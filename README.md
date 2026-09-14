# Dummy Skills Update Lab

A disposable repository for observing how standalone skills and marketplace plugins update. The current default branch contains **v2.0.0: one unified `dummy-lab` skill**. The `baseline-v1` branch preserves the original four skills.

Each skill reads a small bundled reference and returns a versioned marker. The probes only produce text; they do not need credentials, network calls, or project changes.

| Skill | Prompt | Expected v2 response |
| --- | --- | --- |
| `dummy-lab` | Run the Dummy Skills Update Lab colors probe. | `DUMMY-LAB v2 | colors | cobalt` |
| `dummy-lab` | Run the Dummy Skills Update Lab animals probe. | `DUMMY-LAB v2 | animals | fox` |
| `dummy-lab` | Run the Dummy Skills Update Lab fruit probe. | `DUMMY-LAB v2 | fruit | plum` |
| `dummy-lab` | Run the Dummy Skills Update Lab shapes probe. | `DUMMY-LAB v2 | shapes | triangle` |

## Test standalone installation with npx

For a fresh v2 installation, use a disposable project directory and run the command below. If you already installed v1, complete the update experiment first.

```sh
npx skills@latest add marcelofinamorvieira/dummy-skills-update-lab --skill dummy-lab --agent codex
```

This uses the default project scope. Start a new task in that directory and try the prompts above. For explicit selection, ask: `Use $dummy-lab and run the colors probe.`

Record the installer version and installed list:

```sh
npx skills@latest --version
npx skills@latest list
```

Do not pin the installation to a commit, tag, or baseline branch for this experiment: the update needs to follow changes on the default branch.

## Test marketplace installation in Codex

Test one installation route at a time in the same agent environment. Standalone copies and plugin copies share names, so leaving both active would make the source of a response ambiguous.

For a fresh installation, register the Git-backed marketplace and install its plugin. Existing v1 users should run the update experiment first.

```sh
codex plugin marketplace add marcelofinamorvieira/dummy-skills-update-lab
codex plugin add dummy-skills-update-lab@dummy-skills-lab
codex plugin list
```

Start a new task and run the same probe prompts. If your shell does not have `codex` on its PATH, use the CLI installed with your Codex app.

The marketplace is `dummy-skills-lab`; the installed plugin is `dummy-skills-update-lab`. Its unified skill lives inside that plugin.

## Update an existing v1 installation

Version 2 is now published. The original v1 colors response was `DUMMY-LAB v1 | colors | amber`; the new colors response is `DUMMY-LAB v2 | colors | cobalt`.

The migration:

1. Keeps the repository, marketplace, and plugin identifiers.
2. Bumps the plugin version to `2.0.0`.
3. Removes all four old skill folders.
4. Adds a single `dummy-lab` skill with topic references and `DUMMY-LAB v2` responses.

The `baseline-v1` branch retains the starting files for comparison. Install from the default branch when testing updates.

Observe each route independently:

```sh
# Run interactively; choose the scope where you installed v1:
npx skills@latest update dummy-lab-colors dummy-lab-animals dummy-lab-fruit dummy-lab-shapes

# For the marketplace installation:
codex plugin marketplace upgrade dummy-skills-lab
codex plugin list
```

For a project installation, run the npx command from that project. For a global installation, select global scope or append `--global`. Keep it interactive so any removal prompt is visible.

Inspect the installed names and actual probe responses in a new task. Record whether deleted skills remain, whether cleanup is offered, and whether the new skill appears. Do not run `add` or reinstall before observing the update result: that would test a different operation.

## Cleanup

Remove only the disposable standalone skills that remain installed, using the same scope. Add `--global` if they were installed globally:

```sh
npx skills@latest remove dummy-lab-colors dummy-lab-animals dummy-lab-fruit dummy-lab-shapes --agent codex
```

If the unified skill has been installed, remove it with `npx skills@latest remove dummy-lab --agent codex`.

For the plugin route:

```sh
codex plugin remove dummy-skills-update-lab@dummy-skills-lab
codex plugin marketplace remove dummy-skills-lab
```

## Layout

The single canonical set of skills is in `plugins/dummy-skills-update-lab/skills/`. The npx installer discovers these folders recursively; the marketplace loads the containing plugin through `.agents/plugins/marketplace.json`.
