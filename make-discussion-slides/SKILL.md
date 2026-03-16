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
- Default to **5 content slides** (not counting the title). If the user passes an argument, use that number instead via `$ARGUMENTS`.
- Typical structure for 5 slides:
  1. **Summary** — paper's question, method, and key findings
  2. **What I Like** — strengths and contributions
  3. **Main Concern 1** — the most important critique, with a suggestion
  4. **Main Concern 2** — the second most important critique, with a suggestion
  5. **Additional Suggestions** — remaining minor points
- If fewer or more slides are requested, adapt the structure sensibly (e.g., merge concerns or split suggestions).
- Use `\bigskip` for vertical spacing between bullet points on concern/suggestion slides.
- Keep text concise — these are slides for a ~5 minute discussion.

5. **Compile the presentation** by running `pdflatex` **twice** in the `slides/` directory (two passes are needed to resolve the total frame count):
```bash
cd slides && pdflatex -interaction=nonstopmode discussion_slides.tex > /dev/null 2>&1 && pdflatex -interaction=nonstopmode discussion_slides.tex
```

6. **Report** the final slide structure to the user and link to the compiled PDF.
