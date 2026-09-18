# Installing uxr-studio locally

Unzip this folder anywhere. The path below is wherever you put it.

## Option 1 — install it properly (recommended)

This folder is already a local marketplace, so from inside Claude Code:

    /plugin marketplace add /path/to/uxr-studio-local
    /plugin install uxr-studio@uxr-studio-marketplace

Or from your shell:

    claude plugin marketplace add /path/to/uxr-studio-local
    claude plugin install uxr-studio@uxr-studio-marketplace --scope user

## Option 2 — try it for one session, no install

    claude --plugin-dir /path/to/uxr-studio-local/plugins/uxr-studio

## Check it loaded

    /plugin list
    /reload-plugins

`/reload-plugins` should report 64 skills and 9 agents. To see the components in detail:

    claude plugin details uxr-studio@uxr-studio-marketplace

To validate the structure before installing:

    claude plugin validate /path/to/uxr-studio-local/plugins/uxr-studio/.claude-plugin/plugin.json

## Point it at a different product

Everything product-specific lives in one file:

    plugins/uxr-studio/context/product.md

This file is **not in the repo** — it holds real company data, so it is gitignored. Create it
before first use:

```bash
cp plugins/uxr-studio/context/product.template.md plugins/uxr-studio/context/product.md
```

Then fill it in for your product. No skill needs editing — every agent and skill reads this one
file. Keep the section headings, since the skills read them by name.

If you installed via the marketplace, edit the file in this folder and re-run
`/plugin marketplace update uxr-studio-marketplace`, or just work from Option 2
while you are iterating.

## First things to try

    /uxr-studio:method-selector
    /uxr-studio:research-intake-triage
    /uxr-studio:workflow-conversion-diagnosis

Or address an agent directly, for example uxr-intake or uxr-quant.
