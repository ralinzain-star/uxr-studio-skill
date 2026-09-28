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

## Option 2 — install from a local clone (if you are editing the plugin)

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

Everything product-specific lives in one file. Skills look for it in this order:

1. `.claude/uxr-product.md` in the project you are working in (recommended — survives plugin
   updates and works with any install option)
2. `plugins/uxr-studio/context/product.md` in a local clone (gitignored, so real company data
   never gets committed)

Create it in your project from the template:

```bash
mkdir -p .claude && curl -fsSL https://raw.githubusercontent.com/ralinzain-star/uxr-studio-skill/main/plugins/uxr-studio/context/product.template.md -o .claude/uxr-product.md
```

Then fill it in for your product. No skill needs editing — every agent and skill reads this one
file. Keep the section headings, since the skills read them by name.

If neither file exists, the skills still run: they say so once and ask only for the product
details the task at hand needs.

## First things to try

    /uxr-studio:method-selector
    /uxr-studio:research-intake-triage
    /uxr-studio:workflow-conversion-diagnosis

Or address an agent directly, for example uxr-intake or uxr-quant.
