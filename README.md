# git-scrub-test

Test-fixture repository for [git-scrub](https://github.com/hyperi-io/git-scrub).

**This repo's git history IS the test fixture.** Do not squash, rebase, or
force-push the seeded history — doing so invalidates the test corpus and
breaks downstream CI.

## What's here

Branches and commits seeded with patterns that exercise git-scrub's
removal paths:

- **AI residue:** `Co-Authored-By: Claude`/`Cursor`/`Copilot`/etc. trailers,
  `.claude/` / `.cursor/` / `.codex/` directory shapes.
- **Spill content:** fake credentials and PII patterns. Every fake value is
  prefixed `TEST-FAKE-` so secret scanners can recognise them as
  intentional test data, not real leaks.
- **Supply-chain residue:** lockfile entries and vendored source for
  fictitious bad packages across all 9 ecosystems git-scrub supports
  (Cargo / npm / pnpm / yarn / bun / uv / pip / go / composer).

## Test harness contract

Consumers (the git-scrub integration test suite, primarily) treat this
repository as an external parameter, never a hard-coded URL:

```bash
# Local checkout
GIT_SCRUB_TEST_REPO=/path/to/clone cargo test --test integration

# Or clone-on-demand from upstream
GIT_SCRUB_TEST_REPO=https://github.com/hyperi-io/git-scrub-test cargo test
```

Tests skip honestly if `GIT_SCRUB_TEST_REPO` is not set. No mocking.

Each test snapshots the fixture into a tempdir before rewriting, so the
original repository state is never mutated by a test run.

## Tagged checkpoints

The test corpus uses lightweight tags as named checkpoints (e.g.
`fixtures/ai-v1`, `fixtures/supply-v1`) so tests can pin to known shapes.

## CI

This repo uses [`hyperi-ci`](https://github.com/hyperi-io/hyperi-ci) for
its own quality gates (`.hyperi-ci.yaml` lands with the first fixture
PR).

## License

Apache-2.0. Same as git-scrub.
