# Contributing

This is the default contributing guide for every AvengeMedia repo. Some repos ship their own `CONTRIBUTING.md` or `AGENTS.md` with setup and build details; when they do, those win. This file covers what is common.

Repos in the suite:

- [DankMaterialShell](https://github.com/AvengeMedia/DankMaterialShell) - the desktop shell (Go core + Quickshell UI)
- [dank-greeter](https://github.com/AvengeMedia/dank-greeter) - greetd login screen
- [dank-qml-common](https://github.com/AvengeMedia/dank-qml-common) - shared QML widget library, vendored as a submodule by the QML apps
- [dankgo](https://github.com/AvengeMedia/dankgo) - shared Go module (logging, paths, IPC, process lifecycle)
- [dgop](https://github.com/AvengeMedia/dgop) - system metrics CLI and API
- [danksearch](https://github.com/AvengeMedia/danksearch) - file indexing and search
- [dankcalendar](https://github.com/AvengeMedia/dankcalendar), [dankconnect](https://github.com/AvengeMedia/dankconnect)
- [dms-plugins](https://github.com/AvengeMedia/dms-plugins), [dms-plugin-registry](https://github.com/AvengeMedia/dms-plugin-registry) - official plugins and the registry
- [DankLinux](https://github.com/AvengeMedia/DankLinux) - packaging and install automation
- [DankLinux-Docs](https://github.com/AvengeMedia/DankLinux-Docs) - the docs site

All of it is MIT licensed. By opening a pull request you agree your contribution is licensed the same way.

## Before you start

- Read the [code of conduct](CODE_OF_CONDUCT.md).
- File issues in the repo that owns the code. Packaging problems go to DankLinux, docs problems go to DankLinux-Docs, everything about the shell UI or `dms` CLI goes to DankMaterialShell.
- Search existing issues first. Use the issue templates when a repo has them and fill in the version, distro, and compositor.
- For anything larger than a bug fix, open an issue or discussion before writing code. It saves both of us from a PR that cannot merge.

## Pull requests

1. Fork, branch off `master`, make the change.
2. Keep the PR to one topic. Unrelated cleanups go in their own PR.
3. Run the repo's pre-commit hooks. Most repos use [prek](https://prek.j178.dev/): `prek install` once, then they run on every commit. `prek run -a` runs them by hand.
4. Add or update tests for behavior you changed. Tests check inputs and outputs, not that a function exists.
5. Include screenshots or a short video for anything visual.
6. If the change adds user-facing behavior, open a matching PR in DankLinux-Docs.

Small, reviewable PRs merge fast. Large rewrites sit.

## Generative AI

Using an LLM to help write code is fine. Submitting its output unread is not.

- You are responsible for every line you submit. You have read it, tested it, and can explain it in review.
- Say in the PR when a meaningful part of the code was AI generated.
- Write PR descriptions, issues, and comments yourself. No AI-written or AI-rewritten text. A few plain sentences in your own words beat a page of generated summary. PRs and issues with AI-written text get closed without review.
- Do not file issues or leave comments you have not verified yourself. Reports that do not reproduce get closed.
- PRs that read like unreviewed output, with narrating comments, invented APIs, or style that ignores the file they are in, get closed without review.

## Commit messages

Start with the part of the system touched, then a short lowercase description of the work:

```
network: report wifi state from the associated adapter
greeter: let the on-screen keyboard type into the password field
doctor: flag config files that fail to parse
```

Keep the title short. Add a body only when the why is not obvious from the diff, and keep it plain.

## Code style

These apply across languages.

- Keep it simple. Do not overcomplicate things.
- Self-documenting code. Names and structure carry the meaning. A comment survives only for a real trap the code cannot show, and it moves with the code it describes. Do not narrate what the next line does.
- Guard clauses and early returns. Happy path at the leftmost indent, no nested if/else pyramids.
- `switch` over `if/else` chains when dispatching on one value.
- Follow the conventions already in the file you are editing.
- Do not introduce a library for a simple problem. For a complex, common problem, use the modern library everyone else uses.
- Never edit generated code by hand. Regenerate it.
- Resource usage matters. These programs run 24/7 on every machine they are installed on. Audit any change for idle CPU, extra processes, timers, polling, and retained memory.

### Go

- `gofmt -s` and `golangci-lint` are enforced by the hooks. Run `go mod tidy` before pushing.
- Mocks are generated with [mockery](https://vektra.github.io/mockery/) from `.mockery.yml`. Regenerate after changing a mocked interface.
- New exported API needs a test.
- The `ipc` wire format in dankgo is consumed by every QML shell and CLI client in the suite. Do not add, remove, or rename fields without checking every consumer.

### QML

- Use `Theme` tokens for colors, spacing, and radii. No hardcoded values.
- Use the `Dank*` wrappers (`DankListView`, `DankFlickable`, and so on) instead of raw `ListView`, `Flickable`, `ScrollView`.
- Log through the `Log` service. `console.*` is rejected by pre-commit.
- All user-facing text goes through `I18n.tr()` with translator context. Reuse existing catalog terms before adding new ones.
- Never edit or regenerate the translation catalogs (`en.json`, `template.json`). They are synced with POEditor by a maintainer script. Add your `I18n.tr()` calls and leave the catalogs alone.
- Shared widgets live in `dank-qml-common`. Fix shared code there, then bump the submodule in the app repo.

## Translations

Translations are managed in POEditor, not through PRs against the catalogs. Ask in an issue if you want to translate and you will get an invite.

## Security

Do not open a public issue for a security problem. Report it privately through [DankMaterialShell's security advisories](https://github.com/AvengeMedia/DankMaterialShell/security/advisories/new), even if the bug is in another repo in the suite.
