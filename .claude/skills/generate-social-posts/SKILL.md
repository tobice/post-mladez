# Generate Social Posts from Meeting Schedule

Generate social media post images from a PDF meeting schedule for Post-mládež.

## Input

A PDF file containing the monthly meeting schedule (e.g., `data/program.pdf`)

## Output

PNG images (1600x1600px) for each meeting saved to the `data/` folder.

## Reference Files (in this folder)

- `post-example.png` - Reference example of how the final post should look
- `post-template.pptx` - PowerPoint template with placeholders

## Instructions

When the user provides a PDF meeting schedule, perform these steps:

### 1. Read the PDF and extract meeting data

Extract all meetings from the PDF into a JSON structure. Each meeting has:
- **date**: ISO format `YYYY-MM-DD`
- **location**: Short form from PDF (`u Salvátora` or `u Klimenta`)
- **title**: Meeting title
- **speaker**: Speaker name, or `null` if none (e.g., film screenings)
- **description**: Full description text

**Important - Czech Quotation Marks:** The descriptions often contain Czech quotation marks (`„` and `“`). These characters look similar to ASCII double quotes and will break JSON parsing if written directly. **Always use Python to generate the JSON file** with `json.dump()`:

```python
import json

# Use Unicode escapes for Czech quotes in strings:
# Opening: \u201e for „
# Closing: \u201c for "
desc = "Zpíváme \u201ejeden Pán, jedna víra\u201c..."

data = {
    "month": "únor",
    "year": 2026,
    "meetings": [
        {
            "date": "2026-02-05",
            "location": "u Salvátora",
            "title": "Meeting Title",
            "speaker": "Speaker Name",  # or None for no speaker
            "description": desc
        }
    ]
}

with open('data/program.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, ensure_ascii=False, indent=2)
```

**Do NOT use the Write tool directly** for this file - the Czech quotes will corrupt the JSON.

### 2. Generate the posts

Run the `generate_posts.py` script from the project root:

```bash
python .claude/skills/generate-social-posts/generate_posts.py
```

This will:
- Read `data/program.json`
- Generate PPTX files using the template
- Convert to high-resolution PNG (1600x1600)
- Save to `data/post-XX-YYYY-MM-DD.png`

## Template Details

The `post-template.pptx` contains these placeholders:
- `<title>` - Adumu font, black, 19-26pt (auto-sized)
- `<speaker>` - Titillium Web Bold, 13pt, black
- `<description>` - Titillium Web Light, 11-13pt, black, 1.5 line spacing
- `<date and time>` - Titillium Web Bold, 17pt, black
- `<location>` - Titillium Web Light, 17pt, black

## Location Mapping

- `u Salvátora` → `Evangelický kostel u Salvátora`
- `u Klimenta` → `Fara Klimentská 18, Praha`

## Requirements

- Python: `python-pptx`
- System: `libreoffice`, `pdftoppm` (poppler-utils), `magick` (ImageMagick)
- Fonts: Adumu, Titillium Web family
