# Protocol: The "Architecting a Life" Book Generation Pipeline

**Date:** December 15, 2025
**Context:** Converting a Jekyll Blog Post (Markdown) into a Professional Print PDF (6"x9").
**Target Audience:** Future AI Agents & Human Architects.

---

## 1. Core Philosophy: Single Source Publishing

**The Principle:** Do not maintain separate "Web" and "Print" versions of the content.
**The Implementation:**
*   **Source:** The exact same Markdown file used for the Jekyll website (`docs/_posts/...`).
*   **The Bridge:** Use **Lua Filters** (`filters/strip-liquid.lua`) to sanitize web-specific syntax (Liquid tags, relative URLs) before they reach the print engine.
*   **Asset Strategy:** Use `resource-path` in Pandoc to allow the print build to "see" the web assets without moving them.

## 2. The Typographic System: "Human vs. Machine"

We established a semantic typographic architecture using the **IBM Plex** open-source family to visually distinguish the book's voices.

| Semantic Role | Font Family | Style | Rationale |
| :--- | :--- | :--- | :--- |
| **The Narrator** | `IBM Plex Serif` | Regular | The "Human Voice." Engineered but readable. High x-height requires breathing room. |
| **The Interface** | `IBM Plex Sans` | Bold/Condensed | The "System." Used for Chapter/Part titles. Signals structure and metadata. |
| **The AI Voice** | `IBM Plex Sans` | Italic | The "Machine Whisper." Used for blockquotes (`>`) where the AI speaks. |
| **The Truth** | `IBM Plex Mono` | Regular | The "Code." Used for terminal output, file paths, and front-matter metadata. |

**Critical Configuration:**
*   **Line Spacing:** `\linespread{1.15}`. IBM Plex Serif (and Palatino-likes) has a large x-height. Standard single spacing looks cramped.
*   **Paragraphs:** `\parskip=0.4\baselineskip`. Modern non-fiction benefits from slight separation between paragraphs, unlike dense fiction.

## 3. Layout Engineering (The `memoir` Class)

We rejected standard LaTeX classes in favor of **`memoir`** for trade book production.

### Key Settings
*   **Trim Size:** 6" x 9" (Standard Trade).
*   **Bleed:** 0.125" on all sides. **Crucial:** Images must extend *beyond* the trim line.
*   **Margins:** Inner (Gutter) `1.0in`, Outer `0.7in`. **Twoside** layout is mandatory for print to account for binding glue.
*   **Chapter Starts:** Always on a **Right-Hand (Odd) Page**.
    *   *Command:* `\cleardoublepage` (redefined to ensure blank pages are truly empty).

### The "Hero" Image System
We solved the "Full Bleed Chapter Opener" problem using the `eso-pic` package.
*   **Mechanism:** Place the image on the background layer relative to the *page stock* (not the text block).
*   **Code:** `\AddToShipoutPictureFG*`.
*   **Flow:** The `\chapterhero` command inserts the image, calculates its height, and pushes the chapter title down dynamically.

## 4. Pitfalls & "Do Not Repeat" Errors

### A. The `titlesec` Conflict
*   **Error:** Using `\usepackage{titlesec}` with `memoir`.
*   **Consequence:** Breaks headers, page numbering, and TOC alignment.
*   **Fix:** Use `memoir`'s native `\makechapterstyle` and `\setsecheadstyle`. **Never mix them.**

### B. The "Forced Float" Trap
*   **Error:** Using `\floatplacement{figure}{H}`.
*   **Consequence:** Creates massive, ugly white gaps at the bottom of pages because images refuse to move.
*   **Fix:** Use `\usepackage[section]{placeins}` and allow floats to move (`tbp`).

### C. YAML Front Matter Line Breaks
*   **Error:** Using `\n` or `\\` inside the YAML `author` field for line breaks.
*   **Consequence:** `! Undefined control sequence` or literal backslashes in PDF.
*   **Fix:** Use the Markdown "Hard Line Break" syntax: a backslash at the very end of the line (` \`).

### D. Font Path Resolution
*   **Error:** Downloading fonts to root `assets/` but building from `print/`.
*   **Consequence:** `! Package fontspec Error: The font ... cannot be found.`
*   **Fix:** Fonts must reside in `assets/fonts/` relative to the Makefile execution context.

## 5. Reproducibility Checklist

To rebuild this book from scratch:

1.  **Environment:** Ensure `pandoc`, `xelatex`, and `make` are installed.
2.  **Fonts:** Run the download script to fetch IBM Plex TTFs into `assets/fonts/`.
3.  **Build:**
    ```bash
    cd press-book
    make print-book
    ```
4.  **Artifact:** The output will be named for SEO: `42-degrees-at-dawn-architecting-a-life-with-ai-huan-li.pdf`.

---

*Recorded by ChatGPT (Gemini 3 Pro) & Huan Li*

---

# Appendix: The AI Publisher's Handbook (Legacy Reference)

**Version:** 1.0
**Target Audience:** AI Agents, Automated Publishing Systems, and Solo Founders.
**Objective:** To convert a raw Markdown blog post into a professional, print-ready PDF book with zero human intervention.

## 1. The Philosophy of Algorithmic Publishing

A great book is not just text; it is an **architecture of attention**. When automating book production, we do not aim for "good enough." We aim for a result that feels handcrafted.

### Core Principles
1.  **Respect the Medium**: A PDF for print is not a web page. It has physical dimensions, bleed, and static geometry.
2.  **Legibility is King**: Use classic serif fonts for body text (e.g., TeX Gyre Pagella) and clean sans-serifs for headers.
3.  **Structural Consistency**: Every chapter must start the same way. Every margin must be exact.
4.  **The "Hero" Moment**: Chapter openers are the emotional anchors of the book. They must be visually striking and perfectly aligned.

---

## 2. The Technical Stack

To build a professional book programmatically, use this proven stack:

*   **Source**: Markdown (with YAML frontmatter).
*   **Orchestrator**: `make` (for build automation).
*   **Converter**: `pandoc` (the universal translator).
*   **Typesetter**: `xelatex` (for Unicode support and modern font handling).
*   **Class**: `memoir` (the most robust LaTeX class for book design).

---

## 3. Designing the "Hero" Chapter Opener

The most challenging aspect of automated typesetting is the "Hero Image"—a full-bleed image at the start of each chapter that sits behind the text area but aligns perfectly with the physical page edge.

### The Challenge
Standard LaTeX places images inside the "text block" (inside margins). A Hero Image must ignore margins, touch the physical edge of the paper (bleed), and push the chapter title down by an exact amount.

### The Golden Solution (The "Perfect" Macro)
After extensive experimentation, this is the robust, mathematical approach to placing a hero image. Do not guess coordinates. Measure and calculate.

#### The Algorithm:
1.  **Clear the Page**: Start a new page and remove headers/footers (`\thispagestyle{plain}`).
2.  **Measure the Image**: Load the image into a "savebox" to get its exact natural height when scaled to the page width.
3.  **Calculate Coordinates**:
    *   **Width**: `\stockwidth + 2\bleed` (Full width + bleed on both sides).
    *   **Horizontal Position**: `0pt` (Align to the absolute left edge of the stock).
    *   **Vertical Position**: Calculate the offset from the bottom left corner so the image top touches the physical top edge.
4.  **Place the Image**: Use `eso-pic` to place the image in the background layer.
5.  **Push the Text**: Insert a vertical space equal to the *image height* plus a buffer (`\baselineskip`).

#### The Code Pattern:
```latex
\usepackage{eso-pic}
\newsavebox{\heroimagebox}
\newlength{\herooffset}
\newlength{\herovspace}

\newcommand{\chapterhero}[2][]{%
  \clearpage
  \thispagestyle{plain}
  % 1. Scale image to full stock width + bleed
  \sbox{\heroimagebox}{\includegraphics[width=\dimexpr\stockwidth+2\bleed\relax,keepaspectratio]{#2}}%
  
  % 2. Calculate offset from bottom-left to align image top with page top
  \setlength{\herooffset}{\dimexpr\stockheight-\ht\heroimagebox+\bleed\relax}%
  
  % 3. Place image in background (Foreground layer of shipout)
  \AddToShipoutPictureFG*{\AtPageLowerLeft{%
    \put(0pt,\LenToUnit{\herooffset}){\usebox{\heroimagebox}}%
  }}%
  
  % 4. Push text down
  \setlength{\herovspace}{\ht\heroimagebox}
  \vspace*{\dimexpr\herovspace+\baselineskip\relax}%
  \noindent
}
```

---

## 4. The Workflow: Markdown to Print

### Step 1: Prepare the Markdown
*   **Frontmatter**: Ensure `title`, `author`, and `date` are present.
*   **Image Paths**: **CRITICAL**. Use relative paths (e.g., `assets/img.jpg`), NOT absolute paths (e.g., `/assets/img.jpg`). LaTeX cannot see the root of your drive; it sees the root of the build folder.
*   **Liquid Tags**: If the source is a Jekyll blog, strip Liquid tags (like `{% include %}`) using a Pandoc Lua filter.

### Step 2: The LaTeX Template (`template.tex`)
*   **Geometry**: Set `stocksize` (physical paper) and `trimmedsize` (final book size).
    *   *Example*: 6x9 inch book needs 6.25x9.25 inch stock (0.125" bleed).
*   **Fonts**: Use `fontspec`.
*   **Chapter Styling**: Use `titlesec` to format chapter headings.

### Step 3: The Build Command (`Makefile`)
Use `pandoc` with specific flags to bind it all together:

```makefile
pandoc \
  --from=markdown+yaml_metadata_block-implicit_figures \
  --to=latex \
  --top-level-division=part \
  --pdf-engine=xelatex \
  --template=template.tex \
  --lua-filter=filters/strip-liquid.lua \
  --resource-path=.:..:./assets \
  -o output/book.pdf \
  source.md
```

---

## 5. Pitfalls & Troubleshooting (The "Knowledge Base")

### Issue 1: "Image Top-Left is Out of Page"
*   **Symptom**: The hero image is shifted left, cutting off the content.
*   **Cause**: Using `\hspace*{-\bleed}` inside a `\put` command that is already at `0pt`.
*   **Fix**: When using `\stockwidth` sizing, the horizontal position should be exactly `0pt`. Do not add negative horizontal space if you are positioning from the absolute page edge.

### Issue 2: "Chapter Title Overlaps Image"
*   **Symptom**: The text starts on top of the image.
*   **Cause**: `eso-pic` places images in the background *without* taking up space in the flow.
*   **Fix**: You **must** manually add `\vspace*` equal to the image height. Measuring the image with `\sbox` is the only accurate way to do this dynamically.

### Issue 3: "File Not Found" for Images
*   **Symptom**: `! Unable to load picture...`
*   **Cause**: Markdown often uses `/assets/...` for web compatibility. LaTeX treats this as the root of the filesystem.
*   **Fix**: Use `sed` or a Lua filter to remove the leading slash, or set `--resource-path` correctly in Pandoc.

### Issue 4: "Text Not Visible"
*   **Symptom**: The image covers the text.
*   **Cause**: Using `AddToShipoutPictureBG` (Background) vs `FG` (Foreground) incorrectly, or the image is opaque and placed *over* text.
*   **Fix**: Place the image in the background, or ensure the text is pushed *below* the image using `\vspace`.

---

## 6. Checklist for the AI Agent

Before marking a job as "Success", verify:
1.  [ ] **Bleed Check**: Does the image extend all the way to the edge of the PDF?
2.  [ ] **Alignment Check**: Is the top of the image exactly at the top of the page?
3.  [ ] **Text Flow**: Is the Chapter Title visible immediately below the image?
4.  [ ] **Margins**: Are the left/right margins of the text block consistent?
5.  [ ] **Paths**: Are all image paths relative?

Follow this manual, and you will produce books that look like they took weeks to design, in seconds.
