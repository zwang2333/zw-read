---
name: zw-read
description: Read a user-specified local scientific paper, approve its standardized filename, and move it with a concise Markdown summary into a configured output folder. Includes per-project first-use setup. Use only when the user explicitly invokes zw-read; never activate automatically for ordinary paper-reading or summarization requests.
license: MIT
---

# zw-read

## Load project settings

For `$zw-read setup`, follow [references/setup.md](references/setup.md) to configure or reconfigure the active project. For a reading request, load `<project>/.zw-read/config.json` using that reference's save/load contract. If settings are missing or invalid, complete setup or repair before processing the selected paper. Ask setup questions individually, and save only after the final configuration review is explicitly confirmed.

Use the configured input folder to resolve relative paper paths and the output folder for final artifacts. An explicitly supplied absolute paper path may be outside the input folder; it does not change the project settings. Use the accepted filename pattern and summary instructions. No project configuration belongs in this skill's installation directory.

## Read and establish the evidence

Accept a user-specified local paper. If the file is missing or the selection is ambiguous, ask the user to identify it; do not select a paper yourself. A configured input folder is not a batch-processing instruction. A title, DOI, or URL alone requires a local file before this workflow begins.

Read the entire document before proposing a filename: main text, methods, figure captions and figures, tables, and embedded supplementary content. For PDFs, extract text and visually inspect figures, tables, or pages whose meaning or reading order is not reliably captured by extraction. Use OCR when necessary and available. If substantive content cannot be read, explain the gap and ask for a readable copy rather than claiming a complete reading.

When `anydoc_enabled` is false, do not invoke AnyDoc or create a retained full-text conversion; use available PDF reading/extraction tools. When true for a PDF, load and use Firecrawl's installed `convert-documents-to-markdown` skill from [firecrawl/anydoc](https://github.com/firecrawl/anydoc), path `skills/convert-documents-to-markdown`. Convert into a unique temporary workspace for reading before filename approval; do not write final artifacts yet. Retain the successful conversion for later saving. Inspect the original PDF's figures and tables as well.

If the enabled dependency or runtime is missing, give the installation source and requirement, and pause until resolved or the user explicitly changes the choice. A failed, empty, or incomplete conversion is not a complete reading: report the failure and pause rather than silently switching converters. Enabling AnyDoc does not authorize hosted OCR or uploading a paper; obtain separate explicit consent before invoking hosted OCR. Clean up only temporary conversion files created by this invocation when finished or cancelled.

Consult official article pages, repositories, or supplementary material when necessary to resolve metadata or substantive gaps. Do not routinely expand into a literature review. Distinguish information in the supplied version from later versions; do not silently replace the supplied paper with a published revision. Flag unavailable information and never invent metadata, sample sizes, access conditions, or findings.

## Propose the filename and wait

Follow the configured basename convention. The default, without literal brackets, is:

`[FirstAuthorLastName]EtAl[LastAuthorLastName]_ShortTitle_ShortJournalName_Year`

- Use the first and last listed authors, not the corresponding authors. Ask about single-author, consortium-only, or otherwise ambiguous author structures rather than inventing exceptions or placeholders.
- Choose a brief descriptive topic title in compact CamelCase, retaining useful scientific acronyms.
- Use familiar journal forms such as `NatureGenetics`, `JASA`, and `PNAS`. Use the preprint venue for a preprint. Resolve uncertain venue or year metadata with the user.
- Use the publication year of the supplied version, not the download date or the year of a different version.
- Preserve the original extension and document contents. The final directory is the configured output folder. A basename must not contain path separators or escape that folder; clarify invalid custom naming requests.

Example: `WainschteinEtAlYengo_MissingHeritability_NatureGenetics_2026.pdf`.

Show the current and proposed full paper paths and the summary path; also show `<approved-basename>.fulltext.md` when a conversion is being retained. Ask for approval to rename/move the paper. **Stop and wait for an explicit answer before moving the paper or writing final artifacts.** Incorporate requested filename changes. If the paper is already at the exact approved destination path, explain that no move/rename is needed and proceed, subject to report collision approval. A correct basename in a different folder still requires approval to move.

Check the paper destination, summary destination, and retained full-text destination when proposing the name and again immediately before writing each file. Never overwrite an existing paper, follow an existing destination symlink to write, or silently add a collision suffix; ask for a different name. If any generated Markdown file exists, ask before replacing that specific file, including the question in the preview where possible. Approval to rename/move is not approval to overwrite either Markdown artifact. If artifacts would share a path (including with a Markdown input paper), ask for a different name or arrangement before proceeding.

## Move without changing contents

After approval, record a SHA-256 digest of the source and use a no-overwrite move operation. Do not rely on a check followed by ordinary overwrite-capable `rename`, `mv`, or `shutil.move`. If the source and destination already identify the same file, avoid deleting or copying it.

For cross-filesystem moves, or when no no-overwrite rename primitive is available, exclusively create the destination, copy the source bytes, close the destination, and verify its SHA-256 digest against the source. Recheck that the source still matches the recorded digest before deleting it. Delete the source only after successful verification. On failure, preserve the source; remove an incomplete destination only if this invocation created it and it has not been replaced or modified by another process. Report any remaining partial artifact.

Verify that the final paper exists with the original contents and that a completed move removed the source. If copying, verification, or source deletion fails, report the exact state and do not claim a completed move or create final notes under an uncompleted target name. Do not roll back by overwriting any file.

## Compose the report

Use the configured summary instructions. The following is the default to show during setup: approximately 200 words across five sections, usually one or two short sentences per section. Allow modest extra space for essential sample sizes or distinct datasets. Use accessible English, precise terminology, and no formulas. Do not trade accuracy for the word target.

Default section order and header (full journal/venue name, verified publication year of the supplied version, original paper title, and clickable paper link):

```markdown
**[Journal], [Year], [Paper name], [Paper link](verified-url)**

**Goal:** Scientific objective and main questions.

**Data:** Data types, key sample sizes and units, dataset/database names, and public availability or access restrictions.

**Method:** Main methods developed or used and how they address the questions.

**Results:** Principal findings with essential qualifications.

**Notes:** Statistical or scientific insights, material limitations, and concrete inspirations for future methodological or applied research.
```

Apply these evidence safeguards even for a customized report format:

- Prefer a verified DOI link, otherwise an official article or preprint page. If none is verifiable, state that the article link is unavailable; never manufacture one. Keep the version/year consistent with the filename.
- Distinguish participants from observations, scans, samples, or other units, and keep separate cohorts and training/validation datasets distinct. Distinguish unrestricted public data from controlled access; say when availability is not stated or could not be verified. For reviews or theoretical papers, describe the evidence base or state when empirical data are not applicable.
- Preserve evidential limits, including association versus causation. Use a broad statistics research lens for insights, limitations, and transferable ideas. Clearly label suggested extensions as possibilities rather than findings. Do not force a speculative inspiration when none is defensible.

## Save and deliver

After the paper is at its completed destination, write the report to UTF-8 `<output-folder>/<approved-basename>.md`. When a successful AnyDoc conversion is being retained, save it as UTF-8 `<approved-basename>.fulltext.md` in that folder. It is the extracted paper text, separate from the summary.

Create new artifacts using exclusive creation. Replace an existing Markdown artifact only after explicit approval for that particular replacement, verifying that the existing file has not changed since the preview; otherwise ask again. Do not modify the paper contents.

Read back each saved artifact and verify its location, basename, and content. Show the same summary text in chat, then provide clickable absolute-path links to the paper, summary, and retained full text outside the summary text. If saving an artifact fails, provide the summary in chat, report which files were actually moved/saved and which failed, and do not claim complete delivery. Preserve successfully moved papers and other successfully saved artifacts.
