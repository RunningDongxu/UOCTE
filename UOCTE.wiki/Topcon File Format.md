Because no specification of this file format was available for the development of uocte, the file format was reverse engineered for interoperability. The information on this page therefore is incomplete and may be incorrect. It only serves to document which parts of the data are interpreted by uocte and which assumptions it makes concerning interpretation.

### High-Level Structure

Topcon is one binary, little endian file that contains both measured data and metadata. Its extension is `fda` and its high-level structure is a header followed by named chunks:

```
u8[4]           "FOCT"
u8[3]           "FDA" or "FAA", denoting "macula" or "external" fixation
u32             "0x2", may be version information
u32             "0x3e8", may be version information
while true
  u8            chunk_name_size
  if chunk_name_size == 0
    eof
  else
    u8[chunk_name_size]   chunk_name
    u32                   chunk_size
    u8[chunk_size]        chunk_data
```

The chunk names start with `@`. They also seem to occur always in the same order, although some chunks are optional. The following list of chunks is sorted by the typical order in which they occur within the file. Strings, including chunk names, are not zero-terminated and ISO8859-1 coded. All images' origins are at the lower left.

```
@FDA_FILE_INFO
u32             "0x2"
u32             "0x3e8"
u8[32]          "8.0.1.20198"
```

```
@HW_INFO_03
u8[16]          model name
u8[16]          serial number
u8[32]          (zeros)
u8[16]          version
u16             build year
u16             build month
u16             build day
u16             build hour
u16             build minute
u16             build second
u8[8]           (zeros)
u8[5][16]       version numbers
```

```
@PATIENT_INFO_02
u8[32]          patient id
u8[32]          patient given name
u8[32]          patient surname
u8[8]           (zeros)
u8              3 or 1, in the latter case birth date is valid
u16             birth year
u16             birth month
u16             birth day
u8[504]         mostly zeros
```

```
@CAPTURE_INFO_02
u16             x
u16[52]         (zeros)
u16             aquisition year
u16             aquisition month
u16             aquisition day
u16             aquisition hour
u16             aquisition minute
u16             aquisition second
```

```
@IMG_JPEG
u8              type
u32             ?
u32             ?
u32             width
u32             height
u32             num_slices
u32             "0xa02"
for i in 1..num_slices
  int32         size
  u8[size]      greyscale tomogram slice image, j2k coded
```

These are the values for `type` found in the wild:

- `0` a line scan
- `2` a rectangular volume scan
- `3` a cylindrical volume scan
- `7` a 7 line scan (horitontal or vertical)
- `11` two 5 line scans put into one 10 slice volume

```
@PARAM_SCAN_04
u16[6]          ?
f64             tomogram x dimension in mm
f64             tomogram z dimension in mm
f64             tomogram y resolution in µm
f64[2]          ?
u8[?]           ?
```

```
@IMG_TRC_02
u32             width
u32             height
u32             bits per pixel
u32             num_slices ("0x2")
u8              "0x1"
for i in 1..num_slices
  u32           size
  u8[size]      greyscale fundus image, j2k coded
```

There are always two images, but these are identical. uocte uses the last image as a fundus image.

```
@PARAM_TRC
u16[2]          ?
```

```
@IMG_FUNDUS (present if @CAPTURE_INFO_02.x & 0x200 != 0)
u32             width
u32             height
u32             bits per pixel
u32             num_slices ("0x1")
u32             "0xa02"
for i in 1..num_slices
  u32           size
  u8[size]      BGR fundus image, j2c coded
```

Note that the blue and red channel are swapped.

```
@PARAM_OBS_02 (present if @CAPTURE_INFO_02.x & 0x200 != 0)
u16             "0x1"
u16[2]          2x "0xffff"
u8[12]          "D7000" (camera model "Kodak ...")
u8[24]          "JPEG Fine"
u16             "0x300"
u16             "0x1"
u16             "0x0"
u8[24]          "Color Temparature"
u16             "0x2014"
u8[12]          (zeros)
f32             ?
```

```
@IMG_MOT_COMP_03 (present if @IMG_JPEG.type == "0x2")
u8              "0x0"
u32             width
u32             height
u32             bits per pixel ("0x10")
u32             num_slices ("0x9")
u16[num_slices][height][width] raw volume image
```

```
@IMG_PROJECTION (present if @IMG_JPEG.type == "0x2")
u32             width
u32             height
u32             bits per pixel ("0x8")
u32             0x1000002
u32             size
u8[size]        greyscale maximum intensity projection of tomogram volume, j2k coded
```

```
@REGIST_INFO
u8              "0x0"
u32[2]          ?
u32[4]          bounding box in fundus pixels (minx, miny, maxx, maxy) or (centerx, centery, radius, 0)
u8[32]          "8.0.1.20198"
u32[4]          bounding boy in trc pixels (minx, miny, maxx, maxy) or (centerx, centery, radius, 0)
f64[4]          4x "0.5"
u8[48]          (zeros)
```

```
@ALIGN_INFO
u16             "0x0"
u32             num_slices
u32[3]          (zeros)
u32             ?
u32             ?
u8[32]          "8.0.1.22004"
```

```
@CONTOUR_INFO
u8[20]          id
u16             type
u32             width
u32             height
u32             size
if type == "0x0"
  u16[height][width]   contour depth in tomogram volume pixels
else if type == "0x100"
  f64[height][width]   contour depth in tomogram volume pixels
u8[32]          "8.0.1.20198"
```

This chunk may appear multiple times with different values for `id`. Contour depth is measured from the top of the tomogram slice images rather than the bottom. If `type` is `0x0`, `id` is typically `RETINA_#` or `FLA_#`, when it is `0x100`, `id` is typically `CORNEA_#`.

```
@FAST_Q2_INFO
f32[6]          various quality statistics
u8[20]          (zeros)
```

```
@THUMBNAIL
u32             size
u8[size]        raw RGB BMP-coded thumbnail image
```

```
@GLA_LITTMANN_01
u32[11]         (zeros)
u32             "0xffff"
u32             "0x1"
```

```
@PATIENTEXT_INFO
u8[128]         (zeros)
```

```
@EFFECTIVE_SCAN_RANGE
u32[4]          fundus bounding box (minx, miny, maxx, maxy)
u32[4]          trc bounding box (minx, miny, maxx, maxy)
```

Note that unlike in `@REGIST_INFO` these bounding boxes are always rectangular. They do not always match the ranges given in `@REGIST_INFO`.

```
@MAIN_MODULE
u8[128]         "3DOCT-2000.exe"
u16[4]          version ("0x8", "0x0", "0x1", "0x5616")
u8[128]         (zeros)
```

```
@RESULT_CORNEA_CURVE (present if @IMG_JPEG.type == "0x3")
u8[20]          id
u32             width
u32             height
u8[32]          "8.0.1.21781"
f64[height]     ?
f64[height][width]   contour data
```

```
@ANTERIOR_CALIB_INFO (present if fixation is external)
u8[16]          "2.1.0"
f64[84]         ?
```

```
@RESULT_CORNEA_THICKNESS (present if @IMG_JPEG.type == "0x3")
u8[32]          "8.0.1.21781"
u8[20]          id
u32             width
u32             height
f64[height][width]   contour data
```

```
@REPORT_INFO
u8[7]           (zeros)
```

Not yet found in the data is the subjects sex and the laterality.