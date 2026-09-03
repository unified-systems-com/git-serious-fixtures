# git-serious-fixtures

A **known-answer fixture repository** for the git-serious product and the `github_core` plugin.
Every file here exists to produce one specific, documented observation on a TAP grid that
collects this organization. Nothing here is real infrastructure, and nothing here runs:
every job carries `if: false`, and the only triggers are ones that never fire on their own.

This repository is marked with the topic `tap-fixture`. A collector or a view that sees that
topic should label the repository as a fixture — **label, not hide**: a fixture that is
filtered out of the picture is the absence-reads-as-evidence trap the product exists to catch.

## Why a separate repository

Putting a deliberately vulnerable reference into a working repository would make the
organization's real security posture read as worse than it is, and the product's own
dashboards would then be wrong about us. A repository whose only purpose is to carry known
shapes is the honest version. Scanners do the same thing: zizmor ships a test-data tree of
workflows that each name the finding they should produce.

## The known answers

| File | Shape it carries | Expected observation |
| --- | --- | --- |
| `.github/workflows/dependabot-actions-alert.yml` | `actions/download-artifact@v3` — a **version-tag** reference to an action with a published advisory (CVE-2024-42471, path traversal in downloaded artifacts, patched in 4.1.7). Inert: the job never runs and downloads nothing. | One Dependabot alert in the **GitHub Actions** ecosystem on this repository. Settles the ecosystem value the REST docs do not list. Lands as a `dependabot_alert` joined to the `github_action` and this workflow (`req-github-core-dependabot-alerts`). |
| `.github/workflows/tag-pin.yml` | `unified-systems-com/git-serious-fixtures/actions/hello@v1` — this repository's own composite action, referenced the way a third party would, at a tag we control. | `USES_ACTION` with `pin_kind: tag`, `resolved_via: config_layer`, `resolved_sha` = the commit `v1` points at. **Moving the `v1` tag later is the tag-drift done-test** (`req-github-core-actions-7`): the edge's field history shows the new SHA under an unchanged `declared_ref`. |
| `.github/workflows/branch-pin.yml` | The same action at `@main`. | `USES_ACTION` with `pin_kind: branch`, resolved from the config layer. |
| `.github/workflows/unresolved-pin.yml` | The same action at `@does-not-exist`. | `USES_ACTION` with `pin_kind: unresolved`, `resolution: unresolved` — found at neither `refs/tags/` nor `refs/heads/`. |
| `.github/workflows/pull-request-target.yml` | `pull_request_target` + checkout of the PR head + event text interpolated into `run:`. The most-cited incident shape. | `workflow_job` with `checkout_ref` set and the `pull_request_target` trigger on the job; zizmor `dangerous-triggers` + `template-injection` findings on the same job, so scanner agreement is a query. |
| `actions/hello/action.yml` | The composite action the pins above point at. | Nothing on its own; it is the target. |

## Rules for this repository

- **Never "fix" a fixture.** Dependabot security updates and Renovate are disabled here on
  purpose; a pull request that bumps `download-artifact` to a patched version deletes the
  known answer. If a fixture must change, change its row in the table above in the same commit.
- **Never add a job that runs.** Every job carries `if: false`. The dependency graph and every
  static scanner read the file; execution is not needed for any observation above.
- **Tags are load-bearing.** `v1` is part of a done-test. Move it deliberately, in a commit
  whose message says which observation it is meant to produce, and record the date.

## Provenance

Created 2026-09-03 to resolve two open questions: the Dependabot-alert done-test on
`tap-plugin-github-core#68` and the tag-drift done-test on `tap-plugin-github-core#45`.
Licensed Apache-2.0, like the rest of the organization.
