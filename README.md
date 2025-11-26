# DualogIMAPMigration
The **Dualog IMAP Migration Tool** is designed to modify ownership of email folders and messages within Dualog IMAP mail store backup files. This tool is typically used when migrating vessel email systems and user accounts need to be remapped to new IDs.
### Key Features:
- Automatically requests Administrator privileges if needed
- Processes multiple backup files in batch
- Creates backups of original files before modification
- User-friendly color-coded output
- Self-contained - all files in one location

---

## Prerequisites

### System Requirements:
- **Operating System**: Windows 10 or later
- **PowerShell**: Version 5.1 or later (built into Windows)
- **Permissions**: Administrator privileges (script will auto-request if needed)
- **Disk Space**: Sufficient space for temporary file extraction

### Required Files:
All these files must be in the same folder as the script:

| File Name | Description |
|-----------|-------------|
| `ChangeBackupMessageOwners.ps1` | Main PowerShell script |
| `7z.exe` | 7-Zip command line executable |
| `7z.dll` | 7-Zip library file |
| `DualogUserMap.exe` | User ID mapping processor |
| `DualogUserMap.ini` | User ID mapping configuration |

---

## Installation

1. **Extract all files** to a dedicated folder (e.g., `C:\DualogTools\Vessel IMAP Migration Tool`)

2. **Verify all required files** are present in the same folder

3. **Enable PowerShell script execution** (if not already enabled):
   - Open PowerShell as Administrator
   - Run: `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`
   - Confirm by typing `Y` when prompted

---

## Folder Structure

After setup, your directory structure should look like this:

```
Vessel IMAP Migration Tool/
├── ChangeBackupMessageOwners.ps1    (Main script)
├── 7z.exe                            (7-Zip executable)
├── 7z.dll                            (7-Zip library)
├── DualogUserMap.exe                 (Mapping processor)
├── DualogUserMap.ini                 (User ID mappings)
├── USER_GUIDE.md                     (This file)
└── Dualog IMAP Backup/               (Auto-created folder for backup files)
    └── imapmailstore.dualogbackup*   (Place your backup files here)
```

---

## Configuration

### User ID Mapping (DualogUserMap.ini)

The `DualogUserMap.ini` file contains mappings in the format:

```
[Old User ID] [New User ID]
```

**Example:**
```
936010 1620791
936011 1620792
936012 1620793
```

**To modify mappings:**
1. Open `DualogUserMap.ini` in a text editor (Notepad, Notepad++, VS Code)
2. Update the user ID pairs as needed
3. Save the file
4. Each line should contain exactly two numbers separated by a space

### Archive Password

The script uses the password `G4VESSEL` to open backup archives. If your archives use a different password, you must edit the script:

1. Open `ChangeBackupMessageOwners.ps1` in a text editor
2. Find line 34: `$archivePassword = "G4VESSEL"`
3. Change `G4VESSEL` to your password
4. Save the file

---

## How to Use

### Quick Start:

1. Place your IMAP backup files (named `imapmailstore.dualogbackup*`) in the **"Dualog IMAP Backup"** folder
2. Right-click `ChangeBackupMessageOwners.ps1`
3. Select **"Run with PowerShell"**
4. Click **"Yes"** on the UAC prompt (if shown)
5. Wait for processing to complete
6. Review the output for any errors

---

## Step-by-Step Instructions

### Step 1: Prepare Backup Files
- Copy your Dualog IMAP backup files into the `Dualog IMAP Backup` subfolder
- Files should match the pattern: `imapmailstore.dualogbackup*`
- Example: `imapmailstore.dualogbackup.2024.01.15`

### Step 2: Verify User Mappings
- Open `DualogUserMap.ini` and verify the user ID mappings are correct
- Ensure old user IDs match the IDs in your backup files
- Ensure new user IDs match your target system

### Step 3: Run the Script
- Navigate to the tool folder
- Right-click `ChangeBackupMessageOwners.ps1`
- Select **"Run with PowerShell"**

### Step 4: Grant Administrator Access
- If prompted by UAC, click **"Yes"** to allow administrator privileges
- The script will restart automatically with elevated permissions

### Step 5: Monitor Processing
The script will display:
- Script and folder locations
- List of detected backup files
- Processing status for each file
- Completion message

**Sample Output:**
```
==============================================================================================
Dualog IMAP Mail Store Backup / Change Folder and Message Ownership / Version 2.0.0
PowerShell Version - Running as Administrator
==============================================================================================

This Folder (Script) : C:\DualogTools\Vessel IMAP Migration Tool\
Dualog Backup Folder : C:\DualogTools\Vessel IMAP Migration Tool\Dualog IMAP Backup\
Temp/Working Folder  : C:\DualogTools\Vessel IMAP Migration Tool

List of Detected Dualog IMAP Mail Store Backup Files...

imapmailstore.dualogbackup.vessel01
imapmailstore.dualogbackup.vessel02

Starting Main Process...

Processing File : imapmailstore.dualogbackup.vessel01

Replacing Modified File in Archive (This Will Take Some Time - Please Wait)...

Processing of File Completed (imapmailstore.dualogbackup.vessel01)

=================
Process Completed
=================
```

### Step 6: Verify Results
- Check the output for any error messages
- Original FOLDERS files are backed up with prefix `Original-Folders-File-From-*`
- Modified backup files are ready for use

---

## What the Script Does

### Detailed Process Flow:

1. **Privilege Check**
   - Verifies if running as Administrator
   - Auto-elevates if necessary

2. **File Validation**
   - Checks for required files (7z.exe, 7z.dll, DualogUserMap.exe, DualogUserMap.ini)
   - Creates "Dualog IMAP Backup" folder if it doesn't exist

3. **Backup File Detection**
   - Scans for files matching `imapmailstore.dualogbackup*`
   - Excludes `imapmailstore.dualogbackup.archive`
   - Lists all detected files

4. **For Each Backup File:**
   - Extracts the `FOLDERS` file from the 7z archive (using password `G4VESSEL`)
   - Creates backup copies of the original `FOLDERS` file
   - Runs `DualogUserMap.exe` to remap user IDs according to `DualogUserMap.ini`
   - Removes the old `FOLDERS` file from the archive
   - Adds the modified `FOLDERS` file back to the archive
   - Cleans up temporary files

5. **Completion**
   - Displays summary and completion message
   - Waits for user to press a key before closing

---

## Troubleshooting

### Error: "File [name] not detected"
**Problem:** Required files are missing from the script folder

**Solution:**
- Ensure all required files are in the same folder as the script
- Re-extract from the original package if files are missing

---

### Error: "No Dualog IMAP mail store backup files were detected"
**Problem:** No backup files found in the "Dualog IMAP Backup" folder

**Solution:**
- Check that backup files are placed in the `Dualog IMAP Backup` subfolder
- Verify files match the pattern: `imapmailstore.dualogbackup*`
- Ensure the backup folder was created in the correct location

---

### Error: "Failed to Extract File Named 'FOLDERS' From Archive!"
**Problem:** Cannot extract FOLDERS file from backup archive

**Possible Causes:**
1. Incorrect password (default is `G4VESSEL`)
2. Corrupted backup file
3. Archive is not a valid 7z file
4. FOLDERS file doesn't exist in the archive

**Solution:**
- Verify the archive password matches the script configuration
- Test the backup file by opening it manually with 7-Zip
- Restore from a known good backup

---

### Script Closes Immediately
**Problem:** PowerShell window closes without showing output

**Solution:**
- Right-click the script and select "Run with PowerShell" (not "Run")
- Or open PowerShell first, navigate to the folder, then run: `.\ChangeBackupMessageOwners.ps1`

---

### UAC Prompt Doesn't Appear
**Problem:** Script doesn't request administrator privileges

**Solution:**
- Right-click the script → "Run as Administrator"
- Check if UAC is disabled in Windows settings

---

### Processing Takes a Long Time
**Problem:** Archive modification is slow

**Explanation:**
- This is normal for large backup files
- 7-Zip operations on large archives can take several minutes
- The message "This Will Take Some Time - Please Wait" appears during this phase

**Solution:**
- Be patient and wait for completion
- Do not close the window during processing

---

## Important Notes

### Safety and Backups

- **Always create backups** of your original files before processing
- Original `FOLDERS` files are automatically backed up with the prefix `Original-Folders-File-From-*`
- Keep these backups until you verify the migration was successful

### Archive Password

- The default password is hardcoded as `G4VESSEL`
- If your archives use a different password, you must modify the script
- Never share the password or modified script with unauthorized users

### File Naming

- Backup files must follow the pattern: `imapmailstore.dualogbackup*`
- The file `imapmailstore.dualogbackup.archive` is automatically excluded from processing
- File names are case-insensitive on Windows

### User ID Mappings

- Ensure your `DualogUserMap.ini` file is accurate before processing
- Incorrect mappings can result in emails being assigned to wrong users
- Test with a single backup file before processing multiple files

### Administrator Privileges

- Administrator privileges are required for:
  - Creating folders in system directories
  - Modifying archive files
  - Running the DualogUserMap.exe processor
- The script automatically requests elevation if needed

### Logs and Temporary Files

- Temporary files are created in the script directory during processing
- These include: `folders`, `7z_extract.log`, `7z_delete.log`, `7z_add.log`
- These files are automatically cleaned up after processing
- If you see leftover files, they can be safely deleted after the script completes

---

## Support and Additional Information

For technical support or questions about this tool:
- Contact your Dualog system administrator
- Refer to the original documentation: `IMAP Migration Tool.docx`
- Review log files generated during processing for detailed error information

---

## Version History

**Version 2.0.0** (PowerShell Edition)
- Converted from batch script to PowerShell
- Added automatic administrator privilege elevation
- Changed to self-contained folder structure
- Added color-coded output
- Improved error handling and user feedback
- Auto-creates backup folder if missing

**Version 1.0.0** (2021-11-15)
- Original batch script version
- Basic functionality for IMAP backup processing

---

**End of User Guide**
