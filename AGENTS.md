# Workspace Agent Instructions

This workspace is used for creating **Marp presentations** — primarily Thai academic/university content.

## Key Conventions

- All presentations are Marp Markdown files (`.md`) with `marp: true` frontmatter
- Thai fonts: `Noto Sans Thai` and `Sarabun` — always include these in custom CSS
- Logo assets live in `fig/logos/` (e.g., `fig/logos/mahidol.svg`)
- Installed skills are tracked in `skills-lock.json`

## Creating or Editing Slides

Always use the **`marp-slide` skill** when creating or improving Marp presentations.  
The skill provides 7 themes, templates, and best-practice references.

Skill location: [`.agents/skills/marp-slide/SKILL.md`](.agents/skills/marp-slide/SKILL.md)  
Templates: `.agents/skills/marp-slide/assets/template-*.md`  
CSS themes: `.agents/skills/marp-slide/assets/theme-*.css`

## Theme Guidance

| Content type | Recommended theme |
|---|---|
| Academic / university | `default` or `minimal` |
| Business / corporate | `business` |
| Technical / developer | `tech` |
| Creative / event | `colorful` or `gradient` |

## Org Template (New Presentations)

Copy [`template-mahidol.md`](template-mahidol.md) to start any new presentation.

- Theme file: [`themes/theme-mahidol.css`](themes/theme-mahidol.css) (registered in [`.vscode/settings.json`](.vscode/settings.json))
- Use `theme: mahidol` in frontmatter — **no inline `<style>` block needed**
- Set `footer:` in frontmatter once — applies to all slides automatically (hidden on `lead` slides)
- Search for `REPLACE:` to find all placeholders; `ADD SLIDES:` comments mark where to duplicate slide pairs
- PDF export requires internet (Google Fonts CDN): `marp --theme themes/theme-mahidol.css your-file.md --pdf`

## Image Syntax

Use official Marpit syntax:
```md
![bg right:40%](fig/logos/mahidol.svg)   # side image
![w:300px](fig/logos/mahidol.svg)        # sized inline
![bg](image.png)                         # full background
```
