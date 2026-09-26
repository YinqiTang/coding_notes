

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