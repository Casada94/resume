# Clayton Asada's resume

The resume uses pdfLaTeX and Raleway. The source and existing images define
the two-column A4 design; no downloaded fonts or shell escape are needed.

## Build

From the repository root, with TeX Live (validated with 2026) and latexmk:

```sh
latexmk -pdf -interaction=nonstopmode -halt-on-error -file-line-error -outdir=out "Clayton Asada's Resume 2026.tex"
```

This command writes the candidate PDF and auxiliary files to the ignored
`out/` directory. Latexmk automatically reruns pdfLaTeX when needed. Other
editor or background workflows may also refresh the PDF at the repository root.
The pre-refactor PDF is preserved locally as `out/resume-before-refactor.pdf`.

Required packages: `fontenc`, `raleway`, `moresize`, `geometry`, `paracol`,
`array`, `graphicx`, `xcolor`, `enumitem`, `needspace`, `keyval`, `etoolbox`,
and `hyperref`, plus their dependencies. Use pdfLaTeX consistently when
comparing versions; changing engines or font versions can change wrapping.

## Edit and verify

- The preamble contains shared colors, dimensions, contact details, and layout
  commands. Employment entries use named fields; brace values containing commas.
- Technology rows accept five `\cvtech{filename}{label}` cells separated by `&`.
- After edits, check for box warnings, inspect the PDF at normal size, and
  confirm all four jobs still fit on one page.
- `pdfinfo`, `pdfinfo -url`, `pdffonts`, and `pdftotext -layout` can inspect
  page count, metadata, clickable destinations, fonts, and extracted text.
- See [REVIEW.md](REVIEW.md) for the original findings, implementation results,
  and deferred items that require additional judgment.
