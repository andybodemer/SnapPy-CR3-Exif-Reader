# SnapPy CR3 Exif Reader

## Purpose

SnapPy CR3 Exif Reader provides Python tools to extract and parse EXIF metadata from Canon CR3 raw image files. The scripts read Canon's proprietary CR3 format (ISO Base Media File Format with embedded TIFF structures) and extract camera settings, lens information, and shooting parameters.

## Features

- **EXIF Extraction**: Reads camera model, lens info, exposure settings, ISO, shutter speed, aperture, focal length, and more
- **Multiple TIFF Structure Parsing**: Navigates through all TIFF structures within Canon's CMT1 UUID box
- **Batch Processing**: Process single files or entire folders of CR3 images
- **Human-Readable Output**: Generates organized sidecar text files with formatted metadata
- **Error Handling**: Validates TIFF structures and skips corrupt data
- **GUI File Selection**: Native file/folder picker dialogs via tkinter
- **No External Dependencies**: Uses only Python standard library (struct, os, binascii, pathlib, datetime, tkinter)

## Scripts

### `snappy_cr3_exif_reader_dev.py`
Script for extracting metadata and creating sidecar files.

**Features:**
- GUI file/folder selection via tkinter dialogs (with manual path entry fallback)
- CR3 file validation via header inspection
- Skip existing sidecar files to avoid duplicates
- Batch processing support (non-recursive)
- Generates `filename_metadata.txt` sidecar files
- Debug mode to view raw TIFF tag parsing (set `DEBUG_MODE = True`)
- Processes all TIFF structures found in CR3 files (typically 8 structures)
- Filters output to essential metadata only (no raw TIFF noise)
- Extracts key metadata fields including:
  - Camera make/model and firmware version
  - Lens model, serial number, and manufacturing code
  - Exposure settings (time, f-number, ISO, shutter speed)
  - White balance, metering mode, flash settings
  - Image dimensions, orientation, artist/copyright

**Usage:**
```bash
python3 snappy_cr3_exif_reader_dev.py

# Select an option:
#   [1] Select a single CR3 file    → Opens file picker dialog
#   [2] Select a folder of CR3 files → Opens folder picker dialog
#   [3] Enter path manually          → Text input fallback
```

## Output Format

Sidecar files are created with organized sections:

```
============================================================
CR3 METADATA EXTRACTION
Source File: LPST1264.CR3
Extracted: 2026-01-09 13:08:12
============================================================

CAMERA & LENS:
------------------------------------------------------------
Make                          : Canon
Model                         : Canon EOS R5m2
LensModel                     : EF85mm f/1.4L IS USM
LensSpecification             : 85.0

EXPOSURE SETTINGS:
------------------------------------------------------------
ExposureTime                  : 1/125s (0.008000s)
FNumber                       : 4.0
ISOSpeedRatings               : 100
FocalLength                   : 85.0

DATE & TIME:
------------------------------------------------------------
DateTime                      : 2026:01:09 10:27:52
DateTimeOriginal              : 2026:01:09 10:27:52

IMAGE & CREATOR INFO:
------------------------------------------------------------
ImageWidth                    : 8192
ImageLength                   : 5464
Artist                        : Andreas Bodemer
Copyright                     : Andreas Bodemer

SERIAL NUMBERS & FIRMWARE:
------------------------------------------------------------
LensSerialNumber              : 3220000174
LensManufacturingCode         : UQ0060491
FirmwareVersion               : Firmware Version 1.1.1
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