# Project setup

Read this reference for `$zw-read setup`, missing configuration, or configuration repair. Setup configures this skill only; do not edit Codex global settings or the installed skill's instructions to store personal preferences.

## Project and question sequence

Use the active Codex project directory as the project root. If it is not available, use the current working directory and show that absolute project root with the first question; if neither is identifiable, ask for it. Read settings only from that root's `.zw-read/config.json`; do not search parent projects or borrow another project's settings.

For a new project, ask the following **one question at a time**, waiting for the answer before asking the next. An offered default is not an answer. Users may accept a default explicitly. If a reply supplies several answers voluntarily, retain them and confirm them in order without requiring redundant entry.

1. **Input:** Ask where papers are stored. Show the project root as the default. Resolve relative paths from the project root; expand a leading `~` for the current machine. Require an existing readable directory. Do not create an input folder or select a paper automatically.
2. **Output:** Ask where processed files should go. Propose the selected input folder. Resolve paths the same way. If the requested directory does not exist, explain that it will be created after final confirmation. Do not create it yet. Flag an existing non-directory or inaccessible destination and ask for a correction.
3. **Naming:** Show the default pattern `[FirstAuthorLastName]EtAl[LastAuthorLastName]_ShortTitle_ShortJournalName_Year`, without literal brackets in generated names, and the example `WainschteinEtAlYengo_MissingHeritability_NatureGenetics_2026.pdf`. Ask whether to keep it or supply another format. Resolve unclear custom components now. A pattern describes a basename, not a directory; retain the paper's original extension.
4. **Conversion:** Ask whether to use Firecrawl AnyDoc to convert PDFs to Markdown for reading. Default: **false**. Explain that enabling retains `.fulltext.md`, uses the `convert-documents-to-markdown` skill, and does not authorize hosted OCR. If enabled, inspect that skill's availability and its Node.js/CLI requirements. If missing, give its GitHub source and path and pause for installation or an explicit choice to leave conversion disabled. Do not install it silently or save an enabled, ready-to-use configuration when the required skill is unavailable.
5. **Summary:** Show the complete default header and five-section template from `SKILL.md`, plus approximately 200 words, English, accessible language, and no formulas. Ask whether to keep it or change the structure, length, language, or formatting. Resolve unclear requests. Store the full accepted template and instructions as text, not just a reference to a chat message. Scientific evidence safeguards apply regardless of formatting preferences.
6. **Review:** Show the absolute project root, resolved input and output paths, filename pattern, conversion flag, full summary instructions/template, and settings file path. Ask for explicit confirmation to save these settings (and create the output directory if needed). If the user requests edits, collect them and show the revised review. Do not save until confirmed.

On reconfiguration, show existing values as the proposed answers, retaining the same sequence. Explain the new-project defaults where helpful; do not reset existing answers to defaults silently. Cancellation at any point leaves the previous settings unchanged, and does not start paper processing.

## Save and load contract

Write UTF-8 JSON at `<project>/.zw-read/config.json`, with these fields:

| Field | Value |
| --- | --- |
| `version` | Integer `1` |
| `input_folder` | Nonempty path string |
| `output_folder` | Nonempty path string |
| `filename_pattern` | Nonempty string containing the accepted basename convention |
| `anydoc_enabled` | JSON boolean, initially proposed as `false` |
| `summary_instructions` | Nonempty string containing the complete accepted template and prose requirements |

Prefer project-relative paths for folders inside the project (`.` for the root); use absolute paths for external folders. Relative paths always resolve from the project root, never from the installed skill directory or the paper's directory. Settings contain no API keys or machine-wide options.

After final confirmation, create the output directory if needed, create `.zw-read`, serialize settings to a temporary sibling file, and atomically replace `config.json`. Remove only temporary files created by this attempt. On save failure, keep the old config intact, explain any directory already created, and do not claim setup succeeded. Read back and validate the saved JSON and resolved paths before declaring the skill ready.

On every reading invocation, load this file and validate the version, required fields/types, paths, and optional dependency when enabled. Missing settings trigger setup. Invalid JSON, an unsupported version, incomplete settings, unavailable folders, or ambiguous naming/summary instructions require clarification or repair before processing; do not silently reset settings. Reconfiguration must preserve the old file until the corrected configuration is confirmed.

After successful setup, resume a pending explicitly selected paper request. A setup-only invocation ends with the saved configuration location and a ready-to-use message.
