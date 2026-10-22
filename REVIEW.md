# Resume source review

Reviewed on 2026-10-20. The baseline is `Clayton Asada's Resume 2026.tex`
and its existing PDF, built with pdfLaTeX / TeX Live 2026.

## Baseline findings

Before implementation, the document compiled but produced two pages: the second
contained only the Marine Corps entry. A second compilation reported 59 overfull/underfull
box warnings, plus the unused `A4` class option. The largest horizontal
overflow is approximately 33 pt in the Raytheon technology stack.

The visual design to preserve is A4 with 1 cm margins, Raleway at 10 pt,
the 29% sidebar, pale column divider, dark name banner, red headings and
date badges, portrait, QR code, and technology icons. Correcting overflow
and fitting the final entry on page one necessarily changes some spacing.

## Findings and completion status

1. [x] **Completed — Pagination:** recover unnecessary vertical space so the short final
   role fits on the first page without shrinking the body font.
2. [x] **Completed — Width arithmetic:** account for table padding, nested text containers,
   and frame borders explicitly. Replace the ambiguous `\mpwidth` expression
   with local, evaluated lengths.
3. [x] **Completed — Text containers:** simplify `\cvtext`, which previously put a full-width
   paragraph box inside a narrower table cell. Preserve its visible inset.
4. [x] **Completed — Technology rows:** replace floating `table[h!]` environments with inline
   rows so stacks cannot drift away from their jobs.
5. [x] **Completed — Spacing:** centralize section, entry, list, and subsection spacing;
   remove compensating negative skips and trailing `\mbox`/`\vfill` workarounds.
6. [x] **Completed — Page breaks:** keep job headings with their opening content and keep
   subsection labels with the following material without boxing entire jobs.
7. [x] **Completed — Header:** subtract both frame padding and border thickness from its width.
8. [x] **Completed — Event interface:** replace seven positional arguments and empty braces
   with named fields; retain a compact heading-only entry variant.
9. [x] **Completed — Education:** omit empty optional fields and their unnecessary spacing.
10. [x] **Completed — Shared formatting:** centralize headings, badges, icon dimensions, and
    technology rows; preserve sidebar/main heading size differences.
11. [x] **Completed — Dead template code:** remove unused icon helpers, skill bars, column
    types, inactive certifications, and obsolete commented alternatives.
12. [x] **Completed — Dependencies:** remove unused Font Awesome, fancyhdr, transparent,
    TikZ/libraries, verbatim, and obsolete logic dependencies. Declare the
    packages used by the replacement implementation directly.
13. [x] **Completed — Paper option:** replace the unrecognized `A4` option with `a4paper`.
14. [ ] **Partial — Links:** email and telephone link targets are completed.
    The clickable QR image remains pending verification of its destination.
15. [x] **Completed — Metadata:** populate PDF title, author, subject, and document language.
16. [x] **Completed — Build:** document the pdfLaTeX engine and provide a reproducible latexmk
    command that writes generated files to the ignored `out/` directory.
17. [x] **Completed — Source hygiene:** normalize line endings and indentation, improve
    comments, and remove obsolete instructions while respecting TeX whitespace.
18. [x] **Completed — Copy corrections:** fix `Gradel`, `dropout out`, `United State`, and
    past-tense `lead`; standardize JavaScript, IntelliJ, VS Code, Neovim, MySQL,
    date separators, sentence punctuation, and thesis quotation marks.
19. [x] **Completed — Approximate revenue:** `~\$800,000` inserts a nonbreaking space, not an
    approximation sign. Express the apparent intended approximation in words.
20. [x] **Completed — Icon proportions:** preserve each image's aspect ratio in a consistent
    bounding box instead of stretching nonsquare logos.

## Items requiring separate judgment or validation

- [ ] **Pending owner clarification — Employment dates:** Raytheon (06/2024–Present) overlaps the Department of
  Defense (07/2024–09/2025). Preserve the dates pending the owner's explanation
  of whether these were concurrent roles or an employer/client relationship.
- [x] **Completed — Preserve other factual claims:** retain experience durations, class ranking,
  revenue figures, and the phrase “customer conversions”; do not infer facts
  or rewrite these claims during source cleanup.
- [ ] **Deferred — Portrait size:** the embedded 2539 × 2539 image renders at about 1,220 DPI
  and accounts for roughly 95% of the 1.56 MB PDF. A separate optimized
  derivative could reduce size while preserving normal viewing quality;
  retain the original and validate a derivative visually before adopting it.
- [ ] **Deferred — Accessibility:** fonts are embedded and have Unicode mappings, and text
  extraction works. However, raw extraction reads the sidebar before the
  name, and the PDF is untagged. Full tagging and reading-order work needs a
  separate compatibility assessment; no universal ATS compatibility is claimed.
- [ ] **Pending owner input — Optional editorial work:** the profile and achievement descriptions could
  be more specific, but new metrics and substantive claims require owner input.

## Verification checklist — completed

- [x] Save the baseline PDF separately before building the revised document.
- [x] Build with latexmk/pdfLaTeX, inspect warnings and page count, and render the
  PDF to check wrapping, alignment, spacing, images, and the final job entry.
- [x] Extract text to verify that every role, technology, education entry, and
  contact detail survives the refactor.
- [x] Inspect PDF metadata, link targets, and font embedding.
- [x] Target builds at `out/` and preserve the pre-existing modified root PDF
  separately so it remains available for comparison.

## Implementation status

The first implementation pass is complete.

- Implemented items 1–13, 15–20, and the email/telephone portion of item 14.
- Reduced the source from 547 to 299 lines, removed inactive template code,
  and normalized the source to LF line endings.
- Kept A4 paper, 1 cm margins, the 29% sidebar, the same Raleway fonts and
  10 pt body size, original colors, 3.5 cm banner, portrait, and QR image.
  Spacing, wrapping, and icon proportions were adjusted deliberately.
- Replaced technology floats with inline rows and seven-argument employment
  calls with named fields. Empty optional fields are omitted cleanly.
- Used ragged-right sidebar text to eliminate stretched word spaces and
  awkward hyphenation; the main narrative remains justified.
- Added `README.md` with the reproducible build and editing instructions.

### Validation results

- `latexmk -pdf -interaction=nonstopmode -halt-on-error -file-line-error
  -outdir=out "Clayton Asada's Resume 2026.tex"` succeeds.
- The final PDF contains **one A4 page**, compared with two previously.
- The final log contains **zero warnings and zero overfull/underfull boxes**,
  compared with 59 box warnings and the unused class option previously.
- Rendered and inspected the candidate against the original page images:
  no clipped technology labels, overlapping sections, or stranded final job.
- Checked extracted text: all four employers, both degrees, both technology
  stacks, skill rows, achievements, dates, and contact details remain present.
- Verified PDF title, author, subject, email and telephone link destinations,
  and embedded Raleway fonts with Unicode mappings. The source sets `en-US`.
- `git diff --check` passes.

The candidate is `out/Clayton Asada's Resume 2026.pdf`. During validation,
the root PDF was also refreshed outside the explicit `out/` build command.
The original PDF bytes were backed up before editing and are preserved as
`out/resume-before-refactor.pdf` for comparison. These `out/` artifacts are
local and ignored by Git.

### Remaining items

- [ ] **Pending — QR link:** the QR image remains unchanged; its destination
  has not been decoded/verified, so no speculative clickable link was added.
- [ ] **Deferred — Image compression and accessibility:** portrait compression and full PDF accessibility tagging remain separate
  follow-ups. The PDF is still approximately 1.56 MB and is not tagged.
- [ ] **Pending owner input — Dates and editorial questions:** overlapping employment dates and other factual/editorial questions above
  remain for the owner to resolve; no dates or metrics were invented.
