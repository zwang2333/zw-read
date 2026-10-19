# zw-read

A portable, explicitly invoked Codex skill for reading a local scientific paper, approving its filename, and saving a concise English summary. It reads the whole paper, including figures and tables, and keeps scientific claims within the evidence provided.

## Install with Codex

Upload **the contents of this `zw_read` folder** as your GitHub repository. The installable skill is at `skills/zw-read`; its name is `zw-read`, with a hyphen.

In Codex, ask (replace `OWNER/REPOSITORY` with your repository):

```text
Use $skill-installer to install zw-read from the GitHub repository
OWNER/REPOSITORY, path skills/zw-read.
```

For a downloaded or cloned local copy, ask:

```text
Install the skill from /absolute/path/to/zw_read/skills/zw-read into
my Codex user skills directory, preserving its name zw-read and all
supporting files. If zw-read is already installed, ask before replacing it.
```

The user skills destination is `$CODEX_HOME/skills/zw-read` when `CODEX_HOME` is set, otherwise `~/.codex/skills/zw-read`. This package needs no plugin manifest, build step, credentials, or bundled papers. Use a new turn after installation; if your Codex client has not discovered the skill, restart it.

Then open your intended paper project in Codex and run:

```text
$zw-read setup
```

Copying a skill does not execute a setup hook. Setup is a conversation with Codex, run after installation; the first reading request also runs setup if that project has no settings.

## First setup and settings

Codex asks one question at a time:

1. Input folder (default: active project folder).
2. Output folder (default: selected input folder).
3. Filename format (default shown below).
4. AnyDoc PDF conversion (default: false).
5. Summary format (default: approximately 200 English words, with Goal, Data, Method, Results, and Notes).
6. Final confirmation of all settings.

Settings are saved only after final confirmation, in `<project>/.zw-read/config.json`. Relative folders resolve from that project, including `.` for its root. Every project has separate settings. Run `$zw-read setup` again to change them; cancelling retains the previous configuration. No Codex global settings are modified. The setup reference documents the configuration fields.

The default paper basename is:

```text
[FirstAuthorLastName]EtAl[LastAuthorLastName]_ShortTitle_ShortJournalName_Year
```

For example: `WainschteinEtAlYengo_MissingHeritability_NatureGenetics_2026.pdf`.

## Read a paper

```text
$zw-read Read /absolute/path/to/paper.pdf
```

Or identify a file relative to your configured input folder. The skill never chooses a paper or starts a batch merely because that folder contains PDFs.

After reading, Codex shows the current and proposed full paths and waits for filename/move approval. It then moves the unchanged paper into the output folder and saves `<approved-basename>.md`. If both folders are the same, it renames the paper in place. Existing papers are never overwritten, and replacing an existing note requires separate approval. If the paper is already at the correct path, no move is needed.

The default summary has a journal/year/title/link header followed by **Goal**, **Data**, **Method**, **Results**, and **Notes**. It appears both in chat and in the saved Markdown file, with final file links provided separately.

## Optional AnyDoc

AnyDoc is disabled by default and is not bundled. Enabling it uses Firecrawl's skill named **convert-documents-to-markdown** from [firecrawl/anydoc](https://github.com/firecrawl/anydoc), at `skills/convert-documents-to-markdown`. Install it through Codex's skill installer if needed. Its CLI requires Node.js 20 or later; first execution may download the converter.

When enabled, full-paper converted text is retained as `<approved-basename>.fulltext.md` alongside the paper and summary. Conversion supports reading; figures and tables still require inspection of the original PDF. Missing dependencies or failed conversion are reported, with processing paused rather than silently switched to another converter.

AnyDoc's hosted OCR sends the document to an external service. Enabling AnyDoc alone does **not** authorize hosted OCR; Codex must obtain separate explicit consent before using it. Credentials belong in your environment, never in this package or project settings.

## Upload and portability

Upload this folder's contents, including the hidden `.gitignore`. Do not include your project `.zw-read` directory, papers, notes, API keys, or local caches. Each destination project runs its own setup; personal paths are not embedded in the skill.

Skill format and installation background: [OpenAI skill documentation](https://learn.chatgpt.com/docs/build-skills). License: [MIT](LICENSE).
