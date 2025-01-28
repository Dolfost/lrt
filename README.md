# latex-report-template
This project is a `CMake` template for latex university reports. It is intended
that it isn't integrated into editor, just pure `CMake`, `latexmk` and
`texlogfilter`. 

### Typical workfrow
Clone repository to your machine and configure project from `src/defines.tex`.
Then and go fill up `src/content` (or `content`, using soft link in repository root).

## CMake targets
Target | Explanation
----|----|
`reoprt_pdf`       | main document 
`report_pdf_clean` | remove all files produced by `latexmk`
`report_log`       | run `texlogfilter` on document logs

Also, after configuration the `build/continious.sh` script is generated as an
alternative to `report_pdf` target.

Project is made in such a way that it could be added as your main project
subdirectory.
## Files
File | Meaning
----|----|
`src/defines.tex` | project-specific definitions
`src/resources/`  | project-wise fonts and images
`src/bibliography.bib` | project-wise bibliography
`src/cmake.tex.in`     | `CMake` variables definitions as tex
