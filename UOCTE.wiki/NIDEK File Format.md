Because no specification of this file format was available for the development of uocte, the file format was reverse engineered for interoperability. The information on this page therefore is incomplete and may be incorrect. It only serves to document which parts of the data are interpreted by uocte and which assumptions it makes concerning interpretation.

A Nidek dataset is a set of files inside a common directory. There is one XML header file, one binary file for contour information and BMP files for image data. The header file is named `FOOx.xml`, where `FOO` is a basename used for all other files, and has roughly the following nesting structure:

```
NAVIS-EX:       RS,...
RS:             ...,Scan,Information,...
Scan:           ...,ReleaseDate,ScanPattern,ScanType,...,ScanPointA,ScanPointB,...,
                ScanCenterX,ScanCenterY,ScanWidth1,ScanWidth2,...,Eye,...
Information:    ...,OCTDepthResolution,SLOPixelSpacing,CCDPixelSpacing,...
```

uocte assumes that 300µm times `ScanWidth1` and 300µm times `ScanWidth2` give the dimension in x- and z-direction of the tomogram volume. `OCTDepthResolution` gives the y-resolution in µm/pixel. `SLOPixelSpacing` gives the reolution of the fundus image in µm/pixel. Together with `ScanCenterX` and `ScanCenterY` uocte computes the fundus image range corresponding to the tomogram volume. `ScanPointA` and `ScanPointB` give the x- and z- dimensions of the tomogram volume and the x- and y-dimensions of the contour depth images, when `ScanPattern` is `MakulaMap`. In all other cases uocte assumes that only one image is provided and stored in the file named `oct_c_xh1.bmp`.

Images are stored as raw greyscale BMP files. Note that the images' origin is upper left. The tomograms' filenames match `FOOoct_c_###.bmp`, where `###` is the slice number starting from `001`. The fundus image's filename is `FOO.bmp`. 

The contour information resides in `FOOoct_m.dat`. It has the following structure:

```
u32[6]              ?
u32                 num_slices
u32                 size
for i in 1..num_slices
  u8[12]            ?
  for j in 1..num_contours
    u16[width]      i-th slice, j-th contour depth in tomogram pixels
```

Note that `num_contours` is computed as `(size - 12) / width / sizeof(u16)`, and `width` is the x-dimension of the tomogram volume.