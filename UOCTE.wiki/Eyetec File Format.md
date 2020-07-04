Because no specification was available for the development of uocte, this file format was reverse engineered for interoperability. The information on this page therefore is incomplete and may be incorrect. It only serves to document which parts of the data are interpreted by uocte.

The files have an `.exd` extension, but are actually renamed ZIP-files. Of the contained files, uocts considers mainly the file `PatientsFiles/DBData.xml` and the binary files it references. Unfortunately the referenced files typically occur before this file, so uocte reads the `.exd` file twice.

`DBData.xml` is an ASCII encoded XML file with a complex structure. The following is simplified

```
ImportExportContainer    PortablePatientInfo
PortablePatientInfo      PatientNameGroup1,PatientBirthDate,PatientSex,EthnicGroup,...,Studies*
Studies                  PortableStudyInfo*
PortableStudyInfo        ...,FileSyncFiles,Series*
Series                   PortableSeriesInfo*
PortableSeriesInfo       ...,Contents*,...
Contents                 PortableContentInfo*
PortableContentInfo      ...,ContentDateTime,...,ContentLaterality,...,FileSyncFiles
FileSyncFiles            FileDetails*
FileDetails              Name,Type
```

The `FileDetails` element refers to binary files storing data. `Name` gives the path relative to the header file and `Type` specifies the type of data the binary file stores. All binary files use little endian storage and all images' origin are lower left. uocte only considers the following file types:

The file of type `Images` is a GZIP-compressed, binary file having the following structure:

```
u32                 ?
u32                 width1
u32                 height1
u32[4]              ?
u8[height1][width1] raw greyscale image of subject's eye
u32[31]             ?
u32                 ?
u32                 width2
u32                 height2
u32[4]              ?
u8[height2][width2] raw greyscale fundus image
u32[31]             ?
u32                 ?
u32                 width3
u32                 height3
u32[4]              ?
u8[height3][width3] raw greyscale maximum intensity projection of tomogram
u32[31]             ?
```

The file of type `Tomograms` stores raw volumetric data and has the following structure:

```
u32                 ?
u32                 width
u32                 height
u32                 num_slices
for i in 1..num_slices
  u32[6]            ?
  u8[height][width] raw tomogram data
  u32[32]           ?
```

The resolution in y direction is assumed to be 1.7µm per pixel. This was inferred from matching with contour data. The dimension in x- and z- direction are 12mm and 9mm, respectively. This matches exactly the dimensions of the fundus image.

The file of type `AnalysedData` contains contour information and has the following structure:

```
for i in 1..10
  u32                 ?
  u32                 width
  u32                 height
  u32[2]              ?
  u16[height][width]  raw contour depth in µm
  u8[height][width]   mask?
  u32[33]             ?
```

The mask data may or may not be an indicator which values are actually valid. They are currently ignored by uocte.