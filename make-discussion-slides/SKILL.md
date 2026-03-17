---
name: make-discussion-slides
description: Create a Beamer discussion slide deck for a conference paper. Use when the user wants to prepare discussant slides for a paper presentation.
disable-model-invocation: true
argument-hint: [number of content slides, default 5]
allowed-tools: Read, Write, Edit, Bash, Glob, Agent
---

# Discussion Slides Skill

Create a Beamer presentation for discussing an academic paper at a conference. The user should have a comments file (e.g., `Comments.pdf`) and the paper itself in the working directory.

## Steps

1. **Find the inputs:** Look for PDF files in the current working directory. Identify the paper and the comments/discussion file. Read both using the Read tool.

2. **Read both documents carefully.** Extract:
   - The paper's title and author(s)
   - The paper's research question, methodology, and key findings
   - All discussion points, suggestions, and concerns from the comments file

3. **Create a `slides/` subfolder** in the current working directory for the output.

4. **Generate a Beamer `.tex` file** (`slides/discussion_slides.tex`) with the following specifications:

### Beamer Setup
- Use `\documentclass[aspectratio=169]{beamer}` with the Madrid theme
- Remove the default footer; show only slide number as `n/total` at the bottom right
- The title slide should have **no page number** and should **not count** toward the total
- Use this pattern:
```latex
\setbeamertemplate{footline}{
  \hfill
  \usebeamercolor[fg]{page number in head/foot}
  \usebeamerfont{page number in head/foot}
  \insertframenumber/\inserttotalframenumber\kern1em\vskip2pt
}
\setbeamertemplate{navigation symbols}{}
```
- For the title slide, suppress the footer and decrement the frame counter:
```latex
{
\setbeamertemplate{footline}{}
\begin{frame}
\titlepage
\end{frame}
}
\addtocounter{framenumber}{-1}
```

### Title Slide
- Title: `Discussion of "<Paper Title>"`
- Author: Seyed Kazempour
- Institute: Louisiana State University
- Date: Infer the conference name and year from the working directory path or ask the user

### Content Slides
- Default to **15 content slides** (not counting the title). If the user passes an argument, use that number instead via `$ARGUMENTS`.

#### Overall Structure
A discussion slide deck has **two parts**, in this order:

1. **Paper narrative (~60–70% of slides):** Present the paper's content in the discussant's own voice. The discussant may place the paper in the context of the literature, highlight certain results, or reframe the paper's contribution. Use the comments file to guide which aspects to emphasize. Typical narrative slides include:
   - Research question and motivation
   - Classification or key conceptual framework
   - Theoretical framework (if applicable)
   - Empirical design / data
   - Key results (one slide per major result or table/figure)
   - The paper's interpretation / bottom line
   - Strengths of the paper

2. **Comments (~30–40% of slides):** The discussant's critiques and suggestions. Structure as follows:
   - **2–5 major comments**, each on its own slide, ordered by importance or narrative coherence. Each major comment should state the concern clearly and end with a constructive suggestion.
   - **1 slide of minor comments** bundling all remaining smaller points together. This counts as one of the comment slides.

#### Organizing Comments
- When the comments file explicitly organizes points into major and minor, follow that structure.
- When it does not, apply judgment: identify the 2–5 most substantive critiques as major comments and relegate the rest to the minor-comments slide.
- Order major comments by importance or by the order that makes the discussion narrative most coherent.

#### Scaling to Different Slide Counts
- For **15 slides** (default): ~10 narrative + 4 major comments + 1 minor comments
- For **10 slides**: ~6 narrative + 3 major comments + 1 minor comments
- For **5 slides**: ~2 narrative + 2 major comments + 1 minor comments
- Adapt proportions sensibly for other counts, always preserving the two-part structure.

#### Formatting
- Use `\bigskip` for vertical spacing between bullet points on comment slides.
- Keep text concise — these are slides for a ~5 minute discussion.

5. **Compile the presentation** by running `pdflatex` **twice** in the `slides/` directory (two passes are needed to resolve the total frame count):
```bash
cd slides && pdflatex -interaction=nonstopmode discussion_slides.tex > /dev/null 2>&1 && pdflatex -interaction=nonstopmode discussion_slides.tex
```

6. **Report** the final slide structure to the user and link to the compiled PDF.
