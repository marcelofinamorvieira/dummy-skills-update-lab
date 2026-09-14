# Dummy Skills Update Lab

A disposable repository for observing how standalone skills and marketplace plugins update. The current default branch contains **v1.0.0: four separate skills**.

Each skill reads a small bundled reference and returns a versioned marker. The probes only produce text; they do not need credentials, network calls, or project changes.

| Skill | Prompt | Expected v1 response |
| --- | --- | --- |
| `dummy-lab-colors` | Run the Dummy Skills Update Lab colors probe. | `DUMMY-LAB v1 | colors | amber` |
| `dummy-lab-animals` | Run the Dummy Skills Update Lab animals probe. | `DUMMY-LAB v1 | animals | otter` |
| `dummy-lab-fruit` | Run the Dummy Skills Update Lab fruit probe. | `DUMMY-LAB v1 | fruit | pear` |
| `dummy-lab-shapes` | Run the Dummy Skills Update Lab shapes probe. | `DUMMY-LAB v1 | shapes | hexagon` |

## Test standalone installation with npx

Use a disposable project directory, then run:

```sh
npx skills@latest add marcelofinamorvieira/dummy-skills-update-lab --skill '*' --agent codex
```

This uses the default project scope. Start a new task in that directory and try the prompts above. For explicit selection, ask: `Use $dummy-lab-colors and run its probe.`

Record the installer version and installed list:

```sh
npx skills@latest --version
npx skills@latest list
```

Do not pin the installation to a commit, tag, or baseline branch for this experiment: the later update needs to follow changes on the default branch.

## Test marketplace installation in Codex

Test one installation route at a time in the same agent environment. Standalone copies and plugin copies share names, so leaving both active would make the source of a response ambiguous.

Register the Git-backed marketplace and install its plugin:

```sh
codex plugin marketplace add marcelofinamorvieira/dummy-skills-update-lab
codex plugin add dummy-skills-update-lab@dummy-skills-lab
codex plugin list
```

Start a new task and run the same probe prompts. If your shell does not have `codex` on its PATH, use the CLI installed with your Codex app.

The marketplace is `dummy-skills-lab`; the installed plugin is `dummy-skills-update-lab`. Its four skills live together inside that plugin.

## The later migration experiment

First install v1 and record its responses. The repository is deliberately staying on v1 until that baseline is ready.

The next test change will:

1. Keep the repository, marketplace, and plugin identifiers.
2. Bump the plugin version to `2.0.0`.
3. Remove all four old skill folders.
4. Add a single `dummy-lab` skill with topic references and `DUMMY-LAB v2` responses.

The `baseline-v1` branch retains the starting files for comparison. Install from the default branch when testing updates.

After the v2 change is published, observe each route independently:

```sh
# Run in the disposable standalone project, interactively:
npx skills@latest update --project

# For the marketplace installation:
codex plugin marketplace upgrade dummy-skills-lab
codex plugin list
```

Inspect the installed names and actual probe responses in a new task. Record whether deleted skills remain, whether cleanup is offered, and whether the new skill appears. Do not run `add` or reinstall before observing the update result: that would test a different operation.

## Cleanup

Remove only these disposable standalone skills from the same project scope:

```sh
npx skills@latest remove dummy-lab-colors dummy-lab-animals dummy-lab-fruit dummy-lab-shapes --agent codex
```

If the later unified skill has been installed, remove it with `npx skills@latest remove dummy-lab --agent codex`.

For the plugin route:

```sh
codex plugin remove dummy-skills-update-lab@dummy-skills-lab
codex plugin marketplace remove dummy-skills-lab
```

## Layout

The single canonical set of skills is in `plugins/dummy-skills-update-lab/skills/`. The npx installer discovers these folders recursively; the marketplace loads the containing plugin through `.agents/plugins/marketplace.json`.
