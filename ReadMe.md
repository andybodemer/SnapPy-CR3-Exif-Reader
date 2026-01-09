# SnapPy CR3 Exif Reader

## Purpose

SnapPy CR3 Exif Reader provides Python tools to extract and parse EXIF metadata from Canon CR3 raw image files. The scripts read Canon's proprietary CR3 format (ISO Base Media File Format with embedded TIFF structures) and extract camera settings, lens information, and shooting parameters.

## Features

- **EXIF Extraction**: Reads camera model, lens info, exposure settings, ISO, shutter speed, aperture, focal length, and more
- **Multiple TIFF Structure Parsing**: Navigates through all TIFF structures within Canon's CMT1 UUID box
- **Batch Processing**: Process single files or entire folders of CR3 images
- **Human-Readable Output**: Generates organized sidecar text files with formatted metadata
- **Error Handling**: Validates TIFF structures and skips corrupt data
- **No External Dependencies**: Uses only Python standard library (struct, os, binascii, pathlib, datetime)

## Scripts

### `snappy_cr3_exif_reader_dev.py`
Script for extracting metadata and creating sidecar files.

**Features:**
- Interactive file/folder path input with automatic path cleaning
- CR3 file validation via header inspection
- Skip existing sidecar files to avoid duplicates
- Batch processing support (non-recursive)
- Generates `filename_metadata.txt` sidecar files
- Debug mode to view raw TIFF tag parsing (set `DEBUG_MODE = True`)
- Processes all TIFF structures found in CR3 files (typically 8 structures)
- Filters corrupt TIFF structures automatically
- Extracts 160+ metadata fields including:
  - Camera & date/time information
  - Lens model, specifications, and serial number
  - Exposure settings (time, f-number, ISO, shutter speed)
  - White balance, metering mode, flash settings
  - Image dimensions, orientation, resolution

**Usage:**
```bash
python3 snappy_cr3_exif_reader_dev.py
# Enter file path: /path/to/image.cr3
# or folder path: /path/to/folder/
```

## Output Format

Sidecar files are created with organized sections:

```
============================================================
CR3 METADATA EXTRACTION
Source File: 20240202-__R60073.cr3
Extracted: 2026-01-08 21:13:35
============================================================

CAMERA & DATE/TIME:
------------------------------------------------------------
Make                          : Canon
Model                         : Canon EOS R6
DateTime                      : 2024:02:02 12:35:38

LENS INFORMATION:
------------------------------------------------------------
LensModel                     : RF50mm F1.2 L USM
LensSerialNumber              : 0550000827

EXPOSURE SETTINGS:
------------------------------------------------------------
ExposureTime                  : 1/2500s (0.000400s)
FNumber                       : 1.2
ISOSpeedRatings               : 100
FocalLength                   : 50.0

OTHER METADATA:
------------------------------------------------------------
[Additional technical metadata fields...]
```

## Technical Details

### CR3 File Structure
Canon CR3 files use the ISO Base Media File Format with:
- **ftyp** box containing 'crx' brand identifier
- **moov** container with nested structures
- **uuid** boxes including Canon's CMT1 UUID (`85c0b687820f11e08111f4ce462b6a48`)
- Multiple **TIFF** structures within the CMT1 UUID box containing EXIF data

### Metadata Extraction Process
1. Parse ISO box structure to locate Canon CMT1 UUID box
2. Search for TIFF headers (`II*\x00` - little-endian TIFF magic number)
3. Parse IFD (Image File Directory) entries from each TIFF structure
4. Extract tag values based on TIFF tag types (BYTE, ASCII, SHORT, LONG, RATIONAL, etc.)
5. Follow IFD pointers (ExifIFD, SubIFD, GPS IFD) for additional metadata
6. Validate and filter corrupt TIFF structures
7. Organize extracted fields into categorized output

### Supported EXIF Tags
The scripts support 60+ standard EXIF tags including:
- **TIFF tags**: ImageWidth, ImageLength, Make, Model, DateTime, Artist, Copyright
- **EXIF tags**: ExposureTime, FNumber, ISO, ShutterSpeed, Aperture, FocalLength
- **Lens tags**: LensModel, LensSpecification, LensMake, LensSerialNumber
- **Shooting tags**: ExposureMode, MeteringMode, WhiteBalance, Flash, ColorSpace


## Requirements

- Python 3.x
- No external dependencies (uses only standard library)

## Tested With

- Canon EOS R6 CR3 files
- Canon EOS R5 II CR3 files
- macOS (Darwin 24.6.0)


## Author

Andreas Bodemer