UOCTML was developed within the uocte project as an interchange format for OCT data. As such it is a "greatest common divider".

UOCTML files are a combination of a header file and a set of binary files. The header file is a simple XML file with the extension ".uoctml" that can refer to parts of the binary files. It has the following nesting structure:

```
uoctml     info*,scan*
info       key,value
scan       id,info*,fundus,range,size,tomogram,contour*
fundus     data
tomogram   data
contour    name,data
id         CDATA(string)
key        CDATA(string)
value      CDATA(string)
data       CDATA(string)
name       CDATA(string)
```

The `uoctml` element is the root of the document object model. It has an attribute `version` that currently always has the value `1.0`. Future versions of the file format may have different values signifying additional elements or different interpretation of elements.

`info` elements provide metadata for the file. An `info` element of `uoctml` typically provides information about the subject using keys `name`, `birth date`, `sex`. An `info` element of `scan` typically provides information about one data aquisition using keys `laterality`, `scan date`.

The `scan` element combines information about one scan. Its `id` element's PCDATA must be unique for the scan within the file. UOCTML does not explicitly support structuring the data into sessions or studies. The scan id element can be used to code this information informally.

The `fundus` element describes image data. The image's properties are specified with attributes `channels`, `width`, `height`, and `type` (see below). The image's origin is lower left.

The `range` element specifies the relation of fundus image and tomogram volume. The attributes `minx`, `maxx`, `miny`, and `maxy` specify the integer pixel coordinates within the fundus image corresponding to the area of the tomogram volume.

The `size` element specifies the real-world dimensions of the tomogram volume. The attributes `x`, `y`, and `z` give the dimension in millimeters.

The `tomogram` element specifies volumetric data on a rectangular grid, whose characteristics are given by the attributes `width`, `height`, `depth`, and `type` (see below). The tomogram's x- and z-axis correspond to the fundus image's x- and y-axis respectively.

The `contour` element specifies retinal contours as a depth image. Its characteristics are given by attributes `width`, `height` and `type` (see below).

The `data` element provides reference to binary files where data is actually stored. The CDATA gives the binary file's path relative to the header file. The element has attributes `storage`, which currently only allows the value `raw`, `start` specifying the byte offset within the binary file where the data starts, and `size` specifying the number of bytes the data comprises.

The `type` attribute specifies the data type of image and volume data. Currently only `u8` (unsigned 8-bit integer) is supported for fundus and tomogram data, and `f32` (single precision float, little endian) is supported for contour data.