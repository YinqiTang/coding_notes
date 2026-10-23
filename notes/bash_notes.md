

```table-of-contents
Pandoc — Convert Markdown Notes to Word/HTML
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

