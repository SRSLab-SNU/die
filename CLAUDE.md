# LaTeX Editing Guide for Claude

Write the latex code as you are Donald Knuth.

---

---

## Build System
- **Build command**: `make`
- **Output**: `p.pdf` in root directory
- **Clean**: `make clean`

## Project Structure
```
/
├── p.tex           # Main document (entry point)
├── conf.bib        # Conference bibliography
├── misc.bib        # Miscellaneous bibliography
├── pkgs.tex        # Package imports
├── cmds.tex        # Custom commands/macros
├── hdr.tex         # Document header
├── abstract.tex    # Abstract section
├── intro.tex       # Introduction
├── *.tex           # Other content sections
├── code/           # Code snippets (auto-converted to .tex)
├── fig/            # Figures (SVG→PDF conversion)
├── sty/            # Style files
└── latex.out/      # Build artifacts
```

## Critical LaTeX Patterns

### 1. Special Characters - ALWAYS ESCAPE
```latex
% Wrong: 
underscore_variable, 50% improvement, C++ code
% Correct:
underscore\_variable, 50\% improvement, \cc{C++} code
```
Common escapes: `_` → `\_`, `%` → `\%`, `&` → `\&`, `#` → `\#`, `$` → `\$`

### 2. Code Formatting
```latex
% Inline code: use custom \cc{} command
\cc{malloc()} not \texttt{malloc()}

% Code blocks: import the code snippet under code/ that are converted by pygment (by make)
\input{code/code-snippet.c}
```

### 3. Cross-References
```latex
% Use \autoref{} for automatic prefixes
\autoref{s:intro}     % produces "§2"
\autoref{f:example}   % produces "Figure 3"
\autoref{t:results}   % produces "Table 1"
```

- Use prefix: s for section, f for figure and t for table

### 4. Custom Commands (from cmds.tex)
- `\sys` → system name with xspace
- `\cc{}` → inline code (preferred over \texttt)
- `\PP{}` → paragraph header with auto-period (plain text only)
- `\PN{}` → paragraph header without auto-period (use when header contains LaTeX commands like `\syslitellm`)
- `\V` → checkmark, `\X` → cross mark
- `\BC{1}` → inline numbered circle (black filled, white text). Use this instead of (1), (2), etc.
- Units: `\MB`, `\GB`, `\KB` (with proper spacing)

**Important**: If your paragraph header contains LaTeX commands (e.g., `\crsbench`, `\syslitellm`), use `\PN{}` instead of `\PP{}` to avoid build issues.

### 5. Multi-File Editing Rules
- NEVER edit included files' \documentclass or \begin{document}
- Each section file should contain only content, no preamble
- Use `\input{}` not `\include{}` (no .tex extension needed)

### 6. Common Mistakes to Avoid
```latex
% Wrong: Breaking compilation
\cite{missing-ref}      % Check conf.bib, misc.bib
\ref{undefined-label}   % Verify label exists
\includegraphics{.png}  % Convert to PDF first

% Wrong: Style violations  
\textit{et al.}         % Use \emph{et al.}
\cite{a} \cite{b}       % Use \cite{a,b}
Figure \ref{fig:x}      % Use \autoref{fig:x}
```

### 7. Build Dependencies
- Code files: `code/*.c` → `code/*.tex` via pygmentize
- Figures: `fig/*.svg` → `fig/*.pdf` via svg2pdf
- Plots: `data/*.gp` → `data/*.tex` via gnuplot

### 8. Commenting
```latex
\begin{comment}
Multi-line comment block
that won't be processed
\end{comment}
```

### 9. Math Mode
```latex
% Inline: $x = y$
% Display: \[ x = y \] or \begin{align}
% Common mistake: Using _ or ^ outside math mode
```

### 10. Bibliography
- Bibliography is split into `conf.bib` (conferences) and `misc.bib` (miscellaneous)
- Use `\cite{}` with BibTeX keys
- Use `{{}}` in the subject field of a citation entry

### 11. Dashes (En Dash and Em Dash)
```latex
% En dash (--): Use for ranges and connections
pages 1--10              % number ranges
2020--2024               % year ranges
client--server model     % connections between equal items

% Em dash (---): Use for parenthetical remarks or breaks in thought
The system---which we describe in detail later---outperforms prior work.
We found a critical bug---a buffer overflow.
```

## Breaking line rules

Break the lines following the "semantic line breaks" rules (https://sembr.org),
making it easier to read the raw text files.

For example, instead of writing below:

```
We discovered a 0-day vulnerability in SQLite3's FTS5 (Full-Text Search 5)
module, specifically within the trigram tokenizer implementation.
%
The trigram tokenizer breaks text into overlapping three-character
sequences for substring matching in full-text searches.
```

I prefer the style below:

```
We discovered a 0-day vulnerability
in SQLite3's FTS5 (Full-Text Search 5) module, 
specifically within the trigram tokenizer implementation.
%
The trigram tokenizer breaks text into overlapping three-character sequences
for substring matching in full-text searches.
%
```

## Convention
- Add packges only to `pkg.tex`
- Use \cc{} instead of \texttt{} for short code snippets
- Locate longer code in the `/code` directory and let make to convert it
- Bibliography files: `conf.bib` and `misc.bib`
- MOST IMPORTANTLY, BREAK the lines based on parts of speech. Put a
  group of related elements together and break them for better readability.
  e.g., right after a verb is pretty good place to break or b/w phrases or clauses.
- Be aware of new lines after `\PP{}` which breaks the lines
  after. Either connect the following sentences with % or start the
  sentence without breaking lines for `\PP{}`
- Once done editing, build it with `make` and fix any latex bugs!
- Be aware of https://read.seas.harvard.edu/~kohler/latex.html
- use \squishlist and \squishend for enumeration list
