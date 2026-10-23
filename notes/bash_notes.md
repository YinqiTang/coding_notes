

```table-of-contents
Pandoc — Convert Markdown Notes to Word/HTML
```

## Basic (Probably for Mac)
```bash
# Creates a new folder named `scripts` inside your home directory.
mkdir -p ~/scripts
```
**`-p`**: The "parents" flag. It ensures the command doesn't throw an error if the `scripts` folder already exists. If you were creating a deeper path (like `~/scripts/bash/backups`), it would also create all the missing parent folders along the way.

**Make a bash script:**
- Method 1: use nano
```bash
# Open a text editor right in your terminal so you can write the code
nano ~/scripts/backup.sh
```
- **`nano`**: A built-in, beginner-friendly command-line text editor.
- **`~/scripts/`**: The path to the folder you just created in the previous step.
- **`backup.sh`**: The file you are creating. The `.sh` extension is a standard convention indicating that this file is a shell script.

When you press Enter , your terminal will be replaced by the `nano` interface. You can type your code, press `Ctrl + O` (then Enter) to save it, and `Ctrl + X` to exit back to the normal command line.

- Method 2: make a new file in Positron (recommended)


#### Example case

```bash
# Make a new folder
mkdir -p ~/scripts
cd ~/scripts

# Make the bash script
nano backup.sh

# Make it runnable
chmod +x backup.sh

# Run it
./backup.sh
```

## Backup
### rsync - sync stuff in Mac
```bash
# Preview what would be copied (nothing is changed)
rsync -av --dry-run /source_folder/ /destination_folder/

# Do it for real
rsync -av --progress /source_folder/ /destination_folder/

# Example to skip some folder with specific names at any depth
rsync -avh --delete \
 --exclude '.venv/' \
 --exclude 'XXX/' \
 /source_folder/ /destination_folder/
```

**Key flags:**

- `-a` (archive): Synchronizes directories recursively, transfers special and block devices, and preserves symlinks, modification times, groups, and permissions.
- `-v` (verbose): Shows you exactly which files are being copied.
- `-h` (Human-readable): Displays file sizes in KB/MB/GB instead of plain bytes.
- `--delete` (optional but common): Add this flag if you want to **mirror** the folders. It will delete files in the destination that you have removed from the source.
- `--dry-run` (optional): Add this to see what _would_ happen without actually copying anything. Always use this first when testing.

**Example Bash script (backup.sh) for backup stuff in Mac:**
```bash
#!/bin/bash
SOURCE="/source_folder"
DEST="/destination_folder"

# Stop if the backup drive isn't plugged in
[ -d "$DEST" ] || { echo "Backup drive not connected"; exit 1; }

# --delete: files removed from SOURCE are also removed from DEST
rsync -avh --delete \
 --exclude '.venv' \
 --exclude 'library' \
 --exclude '.DS_Store' \
 --exclude 'repositories' \
 "$SOURCE/" "$DEST/"
```

### Robocopy - sync stuff in Windows

**Robocopy** (Robust File Copy) is a built-in Windows command used to copy and back up files and folders.

#### 1. Basic command

```bash
robocopy "SOURCE" "DESTINATION" /E /XO /R:2 /W:2
```

**Example:**

```bash
# This copies files from the source folder to the backup folder.
robocopy "source_folder" "backup_folder" /E /XO /R:2 /W:2

# The `/L` option shows what Robocopy would do without copying anything.
robocopy "source_folder" "backup_folder" /E /XO /L
```

| Option            | Description                                                |
| ----------------- | ---------------------------------------------------------- |
| `/E`              | Copy all subfolders, including empty ones                  |
| `/S`              | Copy subfolders, excluding empty ones                      |
| `/XO`             | Exclude older source files                                 |
| `/R:2`            | Retry failed copies 2 times                                |
| `/W:2`            | Wait 2 seconds between retries                             |
| `/L`              | Preview operations without copying                         |
| `/Z`              | Use restartable mode for copying                           |
| `/LOG:backup.log` | Save output to a log file                                  |
| `/MIR`            | Mirror folders, including deleting extra destination files |

**Warning:** `/MIR` can delete files from the backup destination. Avoid it if you want to preserve old backup files.

#### 2. Important notes
- Robocopy is already installed on Windows.
- It can copy only missing or changed files on subsequent runs.
- With `/E /XO`, files deleted from the source are not automatically deleted from the backup.
- Robocopy is suitable for regular folder backups.
- Keep important backups on a separate drive, preferably also on an external drive or another device.

#### 3. Potential idea: Create an automatic backup script

Create a text file named `backup.bat` containing:

```bash
@echo off

set "SOURCE=C:\Users\MyName\Documents"
set "DEST=D:\Backup\Documents"

robocopy "%SOURCE%" "%DEST%" /E /XO /R:2 /W:2

echo Backup finished!
pause
```

**How to run:**
1. Replace the source and destination paths.
2. Save the file as `backup.bat` (not `backup.bat.txt`).
3. Double-click the file to start the backup.


### restic (Mac and Window) - save version history
You can not see the data directly. You need to recover the data with a specific version from the repo to another folder, and then you can see your data.
```bash
# 1. Initialize the repository and set a master password
restic init --repo /path/to/my_backup_folder

# 2. Run a Backup
restic -r /path/to/my_backup_folder backup /path/to/source_folder

# 3. View data backup history
restic -r /path/to/my_backup_folder snapshots

# 4. Restore data
# If you lose a file, you can restore a specific snapshot (using its ID from the previous command) to a target folder.
restic -r /path/to/my_backup_folder restore 79766175 --target /path/to/restore/location
```

## Pandoc — Convert Markdown Notes to Word/HTML 

Pandoc is a system-wide CLI tool once installed — works in any folder

Install (macOS)
```bash
brew install pandoc
```

 Basic conversions
```bash
# Markdown -> Word
pandoc "notes/your-note.md" -o "your-note.docx"

# Markdown -> HTML (for email/web, no extra install needed)
pandoc "notes/your-note.md" -o "your-note.html"
```

Useful options
```bash
# Add a title/metadata
pandoc note.md -o note.docx --metadata title="Meeting Notes 2026-09-16"

# Use custom Word styling (create reference.docx once with preferred fonts/headings)
pandoc note.md -o note.docx --reference-doc=reference.docx

# Convert every .md file in a folder to .docx
for f in notes/*.md; do pandoc "$f" -o "${f%.md}.docx"; done
```

