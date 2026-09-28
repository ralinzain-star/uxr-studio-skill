# Installing uxr-studio

## Option 1 — install from GitHub

From inside Claude Code:

    /plugin marketplace add ralinzain-star/uxr-studio-skill
    /plugin install uxr-studio@uxr-studio-marketplace

Or from your shell:

    claude plugin marketplace add ralinzain-star/uxr-studio-skill
    claude plugin install uxr-studio@uxr-studio-marketplace --scope user

To pick up new versions later:

    /plugin marketplace update uxr-studio-marketplace

## Option 2 — install from a local clone (recommended if you customise it)

You will want your own `context/product.md` (see below), which is easiest to edit in a clone:

    git clone https://github.com/ralinzain-star/uxr-studio-skill.git
    cd uxr-studio-skill

The repo root is already a marketplace, so from inside Claude Code:

    /plugin marketplace add /path/to/uxr-studio-skill
    /plugin install uxr-studio@uxr-studio-marketplace

## Option 3 — try it for one session, no install

    claude --plugin-dir /path/to/uxr-studio-skill/plugins/uxr-studio

## Check it loaded

    /plugin list
    /reload-plugins

`/reload-plugins` should report 64 skills and 9 agents. To see the components in detail:

    claude plugin details uxr-studio@uxr-studio-marketplace

To validate the structure before installing:

    claude plugin validate /path/to/uxr-studio-skill/plugins/uxr-studio/.claude-plugin/plugin.json

## Point it at your product

Everything product-specific lives in one file:

    plugins/uxr-studio/context/product.md

This file is **not in the repo** — it holds real company data, so it is gitignored. Create it
before first use:

```bash
cp plugins/uxr-studio/context/product.template.md plugins/uxr-studio/context/product.md
```

Then fill it in for your product. No skill needs editing — every agent and skill reads this one
file. Keep the section headings, since the skills read them by name.

If you installed from a local clone (Option 2), edit the file in the clone and re-run
`/plugin marketplace update uxr-studio-marketplace`, or just work from Option 3 while you are
iterating.

## First things to try

    /uxr-studio:method-selector
    /uxr-studio:research-intake-triage
    /uxr-studio:workflow-conversion-diagnosis

Or address an agent directly, for example uxr-intake or uxr-quant.
