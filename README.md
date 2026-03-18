Legacy Esri Migration Tools: MDB to GDB & MXD to APRX

A set of Python 3 utilities designed to migrate legacy Esri Personal Geodatabases (.mdb) and ArcMap Documents (.mxd) into the modern
ArcGIS Pro 64-bit ecosystem (.gdb and .aprx).

The Problem
ArcGIS Pro is a strictly 64-bit application and lacks the 32-bit Microsoft Access libraries required to natively read spatial BLOB fields in legacy 
Esri Personal Geodatabases (.mdb). Standard workflows require an active 32-bit ArcMap installation to export these databases.

Furthermore, using the ArcGIS Pro Data Interoperability Extension (FME) with default settings frequently results in dropped data due to strict 
schema matching, coordinate system discrepancies, and legacy geometry rules (e.g., donut polygon orientation).

The Solution
These scripts bypass the 32-bit limitation by passing specific, UI macros directly to the Data Interoperability extension via arcpy. This forces
the FME engine to merge schemas, ignore coordinate translation panics, and accurately extract legacy geometries into File Geodatabases (.gdb).

A secondary script then consolidates all legacy .mxd map documents into a single "Master" .aprx project, dynamically repairing broken layer 
connections and adjusting for FME-generated geometry suffixes (e.g., _polygon, _line).

Prerequisites
ArcGIS Pro (Tested on 3.5)
Data Interoperability Extension (Must be installed and licensed)
Standard arcpy Python 3 environment

Scripts Included
Phase 1: extract_mdbs.py Recursively scans a target directory for .mdb files and uses the Data Interoperability extension to extract them into 
.gdb files of the same name.
  Utilizes a hardcoded FME macro string (RUNTIME_MACROS and META_MACROS) to suppress schema and projection errors.
  Skips processing if a .gdb of the same name already exists.

Phase 2: update_mxds.pyRecursively scans for .mxd files and imports them into a single, pre-created ArcGIS Pro Master Project (.aprx).
  Map & Layout Renaming: Automatically prefixes imported Maps and Layouts with the original .mxd filename to prevent naming collisions and maintain organization.
  Dynamic Repointing: Identifies layers pointing to legacy .mdb files and dynamically repoints them to the newly generated .gdb files from Phase 1.
  FME Suffix Handling: Automatically detects and handles geometry suffixes appended by FME during Phase 1 (e.g., mapping a legacy Roads
    dataset to the newly created Roads_line feature class).

Usage Instructions
Configure Directory: Open both scripts and update the ROOT_DIR variable to point to your target directory containing the legacy files.
Run Phase 1: Execute extract_mdbs.py from your ArcGIS Pro Python environment or CLI. Wait for all databases to successfully convert.
Create Master APRX: Open ArcGIS Pro, create a completely blank project named Master_Project.aprx (or similar), and save it in your 
target directory. Ensure ArcGIS Pro is completely closed before proceeding to release file locks.
Configure Phase 2: Open update_mxds.py and update the MASTER_APRX variable to match the path of the blank project you just created.
Run Phase 2: Execute update_mxds.py. The script will incrementally save after every successful .mxd import.

Disclaimer
These scripts are provided "as-is" without warranty of any kind. Please ensure you have backups of your original .mdb and .mxd files before 
running automated geoprocessing scripts against your directories.
