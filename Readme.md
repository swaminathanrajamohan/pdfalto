# pdfalto

**pdfalto** is a command line executable for parsing PDF files and producing structured XML representations of the PDF content in ALTO format. 

pdfalto is a fork of [pdf2xml](http://sourceforge.net/projects/pdf2xml), developed at XRCE, with modifications for robustness, addition of features and output enhanced format in ALTO (including in particular space information, useful for instance for further machine learning processing). It is based on the Xpdf library.  

The latest (non-)stable version is *0.1*. 

# Usage

General usage is as follow: 

```
 pdfalto [options] <PDF-file> [<xml-file>]
  -f <int>               : first page to convert
  -l <int>               : last page to convert
  -verbose               : display pdf attributes
  -noText                : do not extract textual objects
  -noImage               : do not extract Images (Bitmap and Vectorial)
  -noImageInline         : do not include images inline in the stream
  -outline               : create an outline file xml (i.e. a table of content) as additional file
  -annotation            : create an annotations file xml as additional file
  -cutPages              : cut all pages in separately files
  -blocks                : add blocks informations whithin the structure
  -readingOrder          : blocks follow the reading order
  -fullFontName          : fonts names are not normalized
  -nsURI <string>        : add the specified namespace URI
  -opw <string>          : owner password (for encrypted files)
  -upw <string>          : user password (for encrypted files)
  -q                     : don't print any messages or errors
  -v                     : print version info
  -h                     : print usage information
  -help                  : print usage information
  --help                 : print usage information
  -?                     : print usage information
  --saveconf <string>    : save all command line parameters in the specified XML <file>
```

In addition to the ALTO file describing the PDF content, the following files are generated:

* 

# Build

### Linux

* Install libxml2 (development headers). See http://xmlsoft.org/ 

* Install libmotif-dev (development headers) 

* Xpdf 4.00 is shipped as git submodule, to download it: 

> git submodule update --init --recursive

* Build Xpdf 4.00: 

> cd xpdf-4.00

> mkdir build

> cd build

> cmake -DCMAKE_BUILD_TYPE=Release ../

> make

This will create a static library for xpdf-4.00 at the following path `xpdf-4.00/build/xpdf/lib/libxpdf.a`

* Build pdfalto:

> cd ../..

> cmake .

> make

The executable `pdfalto` is generated in the root directory. 

### Windows 

*to be reviewed !*

*NOTE: this version seems to have some problems with certain pdf, we 
recommend you to use the version built using cygwin (same process as Linux).

If you feel like discovering the issue, we would much appreciate it ;-)*
 
This guide compile pdf2xml using the native libraries of Windows:  

* Install the Visual Studio Community edition and the tools to build C/C++ applications under windows. 
To verify make sure the command `cl.exe` and `lib.exe` are found.   

* Download iconv from  https://sourceforge.net/projects/gettext/files/libiconv-win32/1.9.1/

* Download libxml2 from﻿ ftp://xmlsoft.org/libxml2/win32/

* Download the library dirent from﻿ https://github.com/tronkko/dirent

* Download xpdf from  http://www.foolabs.com/xpdf/

* The makefile has been adapted to work with the following directory format
(iconv, libxml2 and dirent root dirs should be at the same level of pdf2xml's sources):  

```bash
drwxr-xr-x 1 lfoppiano 197121 0 lug 28 17:41 dirent/
drwxr-xr-x 1 lfoppiano 197121 0 ago  1 10:38 libiconv-1.9.1/
drwxr-xr-x 1 lfoppiano 197121 0 lug 30 20:02 libxml2-2.7.8.win32/
drwxr-xr-x 1 lfoppiano 197121 0 ago  1 10:44 pdf2xml/ (<- pdf2xml source)
drwxr-xr-x 1 lfoppiano 197121 0 lug 28 09:06 xpdf-3.04/
``` 

* Build xpdf using the windows ms_make.bat.  

* create `libxpdf.a` in `xpdf-XX/xpdf/` with 

> lib /out:libxpdf.lib *.obj

* Compile the zlib and png libraries, under the /images subdirectory in pdf2xml source: 

> make.bat

### MacOS

*to be reviewed !*

* Download the source corresponding to [Xpdf-4.00](https://github.com/kermitt2/xpdf-4.00) shipped as submodule using git:

> git clone --recursive https://github.com/kermitt2/pdfalto.git

* In order to compile Xpdf, we’ll need to have libxml, freestyle, X11 (xquartz on mac) openmotif installed (using home-brew for instance) and added to the environment (accessible from terminal)

* Compile xpdf, then create `libxpdf.a` in `xpdf/xpdf/` with 

> ar -rc libxpdf.a *.o

* Compile the first zlib then png libraries (dependent), under subdirectory `/images`:

> cmake CMakeList.txt

> make -f Makefile 

* Execute the compilation to generate pdfalto using :

> make -f Makefile.macos 

# Future work

- generate metadata information in a separate XML file (as ALTO schema does not support that)

- support unicode composition of characters

- map special characters in secondary fonts to their expected unicode 

- propagate unsolved character unicode value (free unicode range for embedded fonts) as encoded special character in ALTO (so-called "placeholder" approach)

- generalize reading order to all blocks (now it is limited to the blocks of the first page)

- try OCR for unsolved character unicode value based on their associated glyph in embedded font

- try OCR for unsolved character unicode value in context based on their occurences in the document

- try to optimize speed and memory

# Changes

- use the latest version of xpdf, version 4.00.

- add cmake

- pdfalto... 

- encode URI (using `xmlURIEscape` from libxml2) for the @href attribute content to avoid blocking XML wellformedness issues. From our experiments, this problem happens in average for 2-3 scholar PDF out of one thousand.

- output coordinates attributes for the BLOCK elements when the `-block` option is selected,

- add a parameter `-readingOrder` which re-order the blocks following the reading order when the -block option is selected. By default in pdf2xml, the elements follow the PDF content stream (the so-called _raw order_). In pdf2txt from xpdf, several text flow orders are available including the raw order and the reading order. Note that, with this modification and this new option, only the blocks are re-ordered.

From our experiments, the raw order can diverge quite significantly from the order of elements according to the visual/reading layout in 2-4% of scholar PDF (e.g. title element is introduced at the end of the page element, while visually present at the top of the page), and minor changes can be present in up to 100% of PDF for some scientific publishers (e.g. headnote introduced at the end of the page content). This additional mode can be thus quite useful for information/structure extraction applications exploiting pdf2xml output. 

- use the latest version of xpdf, version 3.04.

# Contributors

xpdf is developed by Glyph & Cog, LLC (1996-2017) and distributed under GPL2 or GPL3 license. 

pdf2xml is orignally written by Hervé Déjean, Sophie Andrieu, Jean-Yves Vion-Dury (XRCE) under GPL2 license. 

pdf2xml has been modified and forked by Patrice Lopez (patrice.lopez@science-miner.com) and Achraf Azhar (achraf.azhar@inria.fr).

The windows version has been built originally by [@pboumenot](https://github.com/boumenot) and ported on windows 7 for 64 bit, then for windows (native and cygwin) by [@lfoppiano](https://github.com/lfoppiano) and [@flydutch](https://github.com/flydutch).  

# License

As the original pdf2xml, pdfalto is distributed under GPL2 license. 
