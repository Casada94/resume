# Resume TeX review

Reviewed `Clayton Asada's Resume 2026.tex` by compiling it with pdfLaTeX and checking the rendered pages. The design can stay. The real problems are a broken width calculation, a second page the layout did not intend, and a few copy errors.

The layout to keep is page 1: dark name bar, red rules and date badges, left rail for skills and education, jobs on the right, QR code at the bottom of the rail. Page 2 is an accident. It contains only the Marine Corps line, with an empty left column.

Compiled result:

- 2 pages, A4 (595.28 × 841.89 pt)
- PDF is 1.56 MB; the headshot accounts for 1.44 MB
- Log: unused class option `A4`, one 33.22 pt overfull box, one 0.80 pt overfull box, and the same overfull/underfull pattern on every skill, section, and education block
- PDF title, author, and keywords are empty

## Fix these and the design stays

### 1. The last job falls onto a blank second page

The right column is a few lines too long. `\cvevent` always ends with `\vspace{15pt}`, each bullet is a full `itemize` (default `topsep` around a single item), and the icon rows are `table` floats, which add `\intextsep`. The `\mbox{} \vfill` blocks at the bottom of the right column do not pull that line back up.

Paracol does not repeat the sidebar on the next page, so page 2 is a stranded entry: Electrical Systems Technician, United States Marine Corps, and nothing else.

Tightening those three skips, and replacing the floats with a plain `tabular`, is enough to get that line back onto page 1.

### 2. The Raytheon icon row runs into the right margin

The log reports `Overfull \hbox (33.22pt too wide)`. `Linux` sits in the 1 cm margin.

The columns are `p{2.5cm} p{2cm} p{2.5cm} p{2.5cm} p{2.5cm}` plus the default `\tabcolsep` on every side, which is wider than the right column. The DoD row is slightly narrower and fits.

Drop the float, remove the outer `\tabcolsep` with `@{}`, and give the five columns a width that sums to `\linewidth`.

### 3. Every skill, section, and education block is a few points too wide

`\cvtext`, `\cvother`, and `\cvmetaevent` all use the same pattern: a `tabular*` of width `\mpwidth` whose `p` column still has `\tabcolsep`, and an inner `\parbox` of `1\mpwidth` inside a column of `0.98\mpwidth`.

That is the repeated overfull of about 9 pt on the left and 4.6 pt on the right. It is also why the years sit tight against the gutter.

Set the tabular to `\linewidth`, use `@{}`, and drop the extra parbox. The words and colors stay where they are.

### 4. The left column is justified, so narrow lines stretch and hyphenate

“Amazon Web Services (AWS)” breaks as `Amazon Web Ser-` / `vices (AWS)`. The thesis line is `Computer    Science    Mas-` / `ter's Program`. Both are in the log as underfull boxes of badness 10000.

`\RaggedRight` on the left rail and on skill names wraps at spaces and keeps the same type and color. Leave the right column justified.

### 5. The header bar is 0.8 pt too wide

`\fcolorbox{white}{...}` adds a white `\fboxrule` that `\mpwidth` does not subtract. On a white page that rule is invisible, and it is the `Overfull \hbox (0.80pt too wide)` at the contact line. The email also sits on the right edge of the bar.

`\colorbox{darkcol}{...}` matches the current bar and removes the overflow. The contact line still needs a slightly narrower measure, or a non-breaking phone number, so the email stays inside the bar.

### 6. Two icons are squashed

`\includegraphics[width=.5cm, height=.5cm]` forces both dimensions. The AWS mark is 120×80 and the Cucumber mark is 120×134, so both are distorted. Use one dimension and `keepaspectratio`.

### 7. The PDF is heavy because the headshot is embedded at full size

`images/headshot.jpg` is 2539×2539 and accounts for 1.44 MB of the 1.56 MB file, placed at about 1220 ppi. A version around 800 px on the long side looks the same at this print size and drops the file to a normal resume weight. The QR code is fine.

## Copy, same layout

These are visible on the page and do not move anything:

- `Gradel 2 to Gradle 8` → Gradle
- `lead to implementation` → led
- `United State Marine Corps` → United States
- `dropout out technique` → dropout technique
- `Javascript`, `Intellij`, `VsCode`, `MySql` → JavaScript, IntelliJ, VS Code, MySQL
- `B.S. Computer Science.` has a stray period
- Phone `(916) 801 6154` reads more clearly as `(916) 801-6154`
- Raytheon (`06/2024 – Present`) and DoD (`07/2024 – 09/2025`) overlap for fourteen months, and the DoD description repeats the Raytheon sentence. Readers will ask which job was current. Say they were concurrent, or correct the dates.
- The TCS entry is only a title, a date, and one bullet. The Marine Corps entry has no detail. That is why the bottom of page 1 looks unfinished compared with the two jobs above it.

## Same pixels, cleaner source

None of this changes the output once the width fix above is in:

- `\documentclass[10pt,A4]` does nothing. `article` does not recognize `A4`, and the log says so. The page is A4 only because `\usepackage[a4paper]{geometry}` sets it. Change the class option to `a4paper`.
- Unused: `fontawesome` and `\icon`, `\icontext`, `\iconhref`, `\iconemail`; `tikz` plus the `shapes`, `backgrounds`, `mindmap`, and `trees` libraries (only the unused `\cvskill` draws anything); `fancyhdr`; `xstring`; `transparent`. The commented certifications block is also stale. It still says you are enrolling in the master’s program.
- `\cvlist` is `\begin{itemize}{#1}\end{itemize}`. The extra braces and the space from the newline in the definition are accidental. Most command definitions are missing `%` at the end of the line, which inserts glue.
- `\cvevent` puts only the title and summary in the `\parbox`. The bullets sit outside it, so a page break can split a job from its title. That is the page-2 split.
- `hidelinks` is already on, but the email, phone, and QR code are not links. `mailto:`, `tel:`, and the LinkedIn URL add no ink.
- PDF title, author, and keywords are empty.
- The apostrophe in the filename makes `latexmk` and `hyperref` look for `"Clayton Asada's Resume 2026".out`, which is the rerun warning at the end of the log.

## Leave these alone

- The short red rules are `0.1\textwidth` on purpose, so they match in both columns.
- The negative skips (`\\[-24pt]`, `\\[-7pt]`, the `-25pt` / `27pt` pair in `\cvmetaevent`) are the current rhythm. Replacing them with different spacing will move headings and badges.
- `\cvskill`’s progress bars are not on the page. Adding them is a different resume.
- Oldstyle Raleway figures are what this file loads (`T1/Raleway-OsF`). Lining figures would change every date and year.
- Switching the page to US letter, or moving to LuaLaTeX, will reflow the page.
