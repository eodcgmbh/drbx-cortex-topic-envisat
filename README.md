# The DRBX Cortex Topic Definition for Envisat

## Introduction.

The topic definition is a Java package that allows identifying Envisat products by applying regular expressions-like file name matching rules. The central element of this package is the ontology definition file ([cortex-index.owl](src/main/resources/META-INF/cortex-index.owl)) located in the META-INF folder inside the resources directory.

The topic is currently designed to support the following product types from the Envisat satellite mission:

- AATSR sensor
  - ATS\_LST\_2P
- ASAR sensor  
  - ASA\_WSM\_1P
  - ASA\_IMM\_1P
  - ASA\_APM\_1P
- MERIS sensor  
  - MER\_RR\_\_2P
  - MER\_FRS\_2P
- RA2 sensor
  - RA2\_GDR\_2P
  - RA2\_MWS\_2P

For decoding the binary datasets, which is necessary for extracting metadata and the footprint coordinates, product-specific XML schema definitions are contained in the ([xsd](src/main/resources/META-INF/xsd)) folder inside the resources directory. Where available, the metadata information is extracted from the XML metadata files provided within the product package.

The file name matching patterns and the XSD files were created based on the official product specification documents:

- [ENVISAT-1 PRODUCTS SPECIFICATIONS, VOLUME 5: PRODUCT STRUCTURES](https://earth.esa.int/documents/700255/2042507/Vol05_Structures_3f_20150831_v1.pdf)
- [ENVISAT-1 PRODUCTS SPECIFICATIONS, ANNEX A: PRODUCT DATA CONVENTIONS](https://earth.esa.int/documents/700255/2042507/AnnexA_Data_Convention_3g_20150831_v2.pdf)
- [(A)ATSR Land Surface Temperature (LST) Product (UOL_LST_L2) Level 2 User Guide](https://earth.esa.int/documents/10174/1415229/ATSR_UOL_LST_L2_User_Guide_v1-0)
- [ENVISAT-1 PRODUCTS SPECIFICATIONS, VOLUME 7: AATSR PRODUCTS SPECIFICATIONS](https://earth.esa.int/documents/700255/2042507/Vol-07-Aats-4C.pdf/71cd7964-7860-4df7-abe5-5042b76cc31f)
- [VOLUME 8: ASAR PRODUCTS SPECIFICATIONS](https://earth.esa.int/c/document_library/get_file?folderId=231342&name=DLFE-2136.pdf)
- [ENVISAT-1 PRODUCTS SPECIFICATIONS, VOLUME 11: MERIS PRODUCTS SPECIFICATIONS](https://earth.esa.int/documents/700255/707222/Vol11_Meris_6a.pdf/58f5780c-adfd-407f-b924-91603cb5b0d5?version=1.0)
- [ENVISAT-1 PRODUCTS SPECIFICATIONS, VOLUME 14: RA2 PRODUCTS SPECIFICATIONS LEVEL 2](https://earth.esa.int/documents/700255/3528455/Vol14_Ra2mwr_5I_Level2.pdf)

Apache Maven is used for building the Java project. The Project Object Model XML file ([pom.xml](pom.xml)) specifies the overall package configuration.

## Product Format and Composition

### AATSR Sensor

The ATS\_LST\_2P product is provided in terms of netCDF files and corresponding md5 checksum files. It was agreed not the include the md5 files. It is possible, though, to combine these two individual files into one gzipped archive (tarball) and ingest the archive. The filename of the tarball needs to be exactly the same as for the nc/md5 files, with the obvious difference of having the file extension ".TGZ". 

The following is an example illustrating the structure for the ATS\_LST\_2P product, after the previous step of tarball creation:

- ATS\_LST\_2PUUOL20020805\_111312\_000065272008\_00194\_02252\_5972.TGZ
  - ATS\_LST\_2PUUOL20020805\_111312\_000065272008\_00194\_02252\_5972.nc
  - ATS\_LST\_2PUUOL20020805\_111312\_000065272008\_00194\_02252\_5972.md5

Note again that the file that will be ingested in DHuS is the ATS\_LST\_2PUUOL20020805\_111312\_000065272008\_00194\_02252\_5972.TGZ in case both files shall be included. If the checksum file is to be ignored, the ATS\_LST\_2P product is directly the netCDF file:

- ATS\_LST\_2PUUOL20020805\_111312\_000065272008\_00194\_02252\_5972.nc

No tarball is created in this case.

Metadata and footprints are created based on the information contained within the netCDF file.

### ASAR Sensor

The considered products from the Envisat ASAR sensor are provided as single files, i.e. one file per dataset:

- one mixed ASCII/BINARY data file having the extension .N1

Metadata and footprints are created based on the information contained within the N1 file.

An example for the ASA\_WSM\_1P product would be:

- ASA\_WSM\_1PNPDE20070304\_113430\_000001292056\_00080\_26186\_0478.N1

Note that the file that will be ingested in DHuS is the N1 file itself, no tarball or compression is applied.

The same applies for the ASA\_IMM\_1P and ASA\_APM\_1P products.

### MERIS Sensor

Products from the Envisat MERIS sensor are provided in different formats. The currently available release online provides the products in the Envisat MERIS N1 format, i.e. one single file per dataset: 

- one mixed ASCII/BINARY data file having the extension .N1

With the new release (as announced on [14 Jul 2020](https://earth.esa.int/eogateway/news/release-of-envisat-meris-4th-reprocessing-data-new-online-dissemination-service)), the product format has been changed to the Sentinel-3 SAFE format (SEN3) based on a folder of netCDF files including an XML manifest file.

The present package is currently able to handle products in both the N1 and the SEN3 format, however, support for the N1 format will become obsolete.

#### SEN3 Format

The SEN3 products are folders containing netCDF files and an XML manifest file. In order for the DHuS ingest to work, the SEN3 folder needs to be zipped first.

The following is an example illustrating the structure for the MER\_FRS\_2P product as provided to EODC via NAS shipment, after the previous step of ZIP archive creation:

- ENV\_ME\_2\_FRG\_\_\_\_20020518T181911\_20020518T182239\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_0208\_006\_070\_\_\_\_\_\_DSI\_R\_NT\_\_\_\_.SEN3.zip
  - ENV\_ME\_2\_FRG\_\_\_\_20020518T181911\_20020518T182239\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_0208\_006\_070\_\_\_\_\_\_DSI\_R\_NT\_\_\_\_.SEN3
    - M01\_rho\_TOA.nc  
    - M01\_rho\_TOA.nc.md5 
    - M01\_rho\_top.nc
    - ...
    - xfdumanifest.xml

The file that will be ingested is ENV\_ME\_2\_FRG\_\_\_\_20020518T181911\_20020518T182239\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_0208\_006\_070\_\_\_\_\_\_DSI\_R\_NT\_\_\_\_.SEN3.zip.

The exact delivery format of the MER\_RR\_\_2P is not yet clear as shipment to EODC has not been completed yet. An example for an MER\_RR\_\_2P from the ESA Envisat MERIS Online Dissemination Service is:

- EN1\_MDSI\_MER\_RR\_\_2P\_20111224T204833\_20111224T213212\_051350\_0043\_20180901T153523\_0100.ZIP
  - ENV\_ME\_2\_RRG\_\_\_\_20111224T204833\_20111224T213212\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_2618\_110\_043\_\_\_\_\_\_DSI\_R\_NT\_\_\_\_.SEN3
    - chl\_nn.nc
    - chl\_oc4me.nc
    - ...
    - xfdumanifest.xml

The file that will be ingested is EN1\_MDSI\_MER\_RR\_\_2P\_20111224T204833\_20111224T213212\_051350\_0043\_20180901T153523\_0100.ZIP. Note that the identifiers of the SEN3 folder and the ZIP file differ. Nonetheless, the addon is still capable of finding the SEN3 node inside the ZIP file.

In both cases, metadata information is extracted from the xfdumanifest.xml files.

#### N1 Format

The old product versions used the Envisat N1 data format. Metadata and footprints are created based on the information contained within the N1 file.

Examples for the MER\_FRS\_2P and the MER\_RR\_\_2P products would be:

- MER\_FRS\_2PPBCM20020618\_101652\_000000172007\_00008\_01565\_0021.N1
- MER\_RR\_\_2PRBCM20020709\_091801\_000002222007\_00308\_01865\_0002.N1

Note that the file that would be ingested in DHuS is the N1 file itself, no tarball or compression is applied.

### RA2 Sensor

The considered products from the RA2 sensor are provided in the netCDF format, i.e. one file per dataset:

- one netCDF file having the extension .nc

Examples for these products include:

- ENV\_RA\_2\_GDR\_\_\_\_20100915T134832\_20100915T143849\_20170826T150251\_3017\_093\_0049\_\_\_\_PAC\_R\_NT\_003.nc
- ENV\_RA\_2\_MWS\_\_\_\_20041109T091841\_20041109T100858\_20170621T220348\_3017\_032\_0015\_\_\_\_PAC\_R\_NT\_003.nc

No compression is applied in this case and the netCDF files are ingested directly into DHuS.

Metadata and footprints are created based on the information contained within the netCDF file.


## Defined Classes for Envisat

### Class &envisat;productRA2_level2

The &envisat;productRA2_level2 class identifies the products from the RA2 sensor based on file name matching rules. The expected format is netCDF. This class must match the corresponding RA2 class name referenced by the metadata extractor in the Envisat addon (separate Java package). 

### Class &envisat;productASAR_image

The &envisat;productASAR_image class identifies Envisat ASAR Level-1 products. By using the SDF (Structured Data File) implementation for the the DRB (Data Request Broker) it is possible to break down the binary N1 files into a tree of nodes on the basis of XML schema definitions. This class must match the corresponding ASAR class name referenced by the metadata extractor in the Envisat addon (separate Java package). 

### Class &envisat;productMERIS_level2_SEN3

The &envisat;productMERIS_level2_SEN3 class identifies the Envisat MERIS Level-2 SEN3 product folders. This class must match the corresponding MERIS SEN3 class name referenced by the metadata extractor in the Envisat addon (separate Java package). 

This class is also used for the creation of quicklook images. The XML blocks img:descriptor and img:rendering contain settings and code for interacting with the corresponding [Java routines](src/main/java) contained inside this package. The code for generating the images is a copy of the one contained in official [Sentinel-3 addon](https://github.com/SentinelDataHub/dhus-sentinel-3), with the difference that no conversion from radiance to reflectance values is performed, since MERIS Level-2 products already provide those.

### Class &envisat;productMERIS_level2

This class identifies the old product versions of the MERIS Level-2 products, i.e. the binary N1 files. The SDF implementation is again used to decode the binary files based on the XML schema files in this package. Again, the names of the classes in the topic and the addon must match.

### Class &envisat;productATS_LST_2P

The &envisat;productATS_LST_2P class identifies the Envisat AATSR Level-2 product. No particular file extension is expected, because files might be ingested as archives or as pure netCDF files. This is the base class for the corresponding classes and its name must again match the one in the metadta extractor addon.

### Class &envisat;productATS_LST_2P_tgz

The &envisat;productATS_LST_2P_tgz is a subclass of &envisat;productATS_LST_2P and identifies the Envisat AATSR Level-2 product tarball.

### Class &envisat;productATS_LST_2P_netCDF

The &envisat;productATS_LST_2P_netCDF is a subclass of &envisat;productATS_LST_2P and identifies the Envisat AATSR Level-2 product netCDF file.









