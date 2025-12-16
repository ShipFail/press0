---
name: press-agent
description: Build a PDF book from a remote Markdown URL (GitHub, etc.). Handles asset fetching, path resolution, and Pandoc generation. Use when the user provides a URL to a markdown file and wants a PDF.
---

# Press Agent

## Capabilities
You are an expert digital publisher. Your goal is to turn a raw Markdown URL into a professional PDF book.

## Workflow

### 1. Scout (Analysis)
- [ ] **Fetch**: Download the content of the provided `BOOK_URL`.
- [ ] **Analyze**: Look for image links (e.g., `![](/assets/img.jpg)` or `![](../img.jpg)`).
- [ ] **Probe**: If paths are relative, use `curl -I` to guess the root URL.
    - Try: `dirname(BOOK_URL) + path`
    - Try: `git_root + path`
    - If all fail, ask the user.

### 2. Acquire (Preparation)
- [ ] **Workspace**: Create a temporary build folder using `mktemp -d`. All intermediate files (markdown, latex) go here.
- [ ] **Cache**: Use `~/.cache/press-agent` for assets.
    - *Fallback*: If `~/.cache` is not writable, use a subfolder in the temp workspace (e.g., `$TEMP_DIR/cache`). Do *not* clutter the project root.
- [ ] **Download**: Fetch all valid images to the cache.
- [ ] **Convert**: Check for `.webp` images. If found, convert them to `.png` using `mogrify -format png *.webp` (LaTeX often fails with WebP).
- [ ] **Rewrite**: Create `book_build.md` in the temp workspace, pointing image paths to the cache (using `.png` extension if converted).

### 3. Refine (Styling)
- [ ] **Metadata**: Ensure the markdown has a YAML frontmatter. If missing, ask the user for Title/Author.
- [ ] **Inject**: Add necessary LaTeX headers for the `press-book` style.
    - *Note*: Ensure `\usepackage{amssymb}` is present in the template to support markdown checkboxes (`[ ]` -> `\square`).
- [ ] **Fonts**: Use available system fonts (e.g., `DejaVu Serif`, `DejaVu Sans`) to avoid missing font errors. Check availability with `fc-list`.

### 4. Synthesize (Build)
- [ ] **Render**: Run `pandoc` using the bundled `templates/book.tex`.
    - Command: `pandoc --pdf-engine=xelatex --template=mvp/press-agent/templates/book.tex --variable=cover-image:path/to/cover.png -o output/book.pdf $TEMP_DIR/book_build.md`
- [ ] **Deliver**: Output the PDF to the `output/` folder in the project root.
- [ ] **Cleanup**: Remove the temporary workspace folder.

## Tools
- Use `curl` for fetching.
- Use `mogrify` (ImageMagick) for image conversion.
- Use `pandoc` for building.
- Use `sed`/`awk` or internal string manipulation for rewriting paths.
