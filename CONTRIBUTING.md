# Contributing

## Start here

Pick a `good first issue` or `help wanted` issue. Comment to ask for it. It is yours for 14 days without a draft PR.

Read the "Start here" issue for the suggested learning order.

## Dev setup

You need Go 1.26.10. There is no code yet, so there is no Makefile yet. When it lands, `make dev` will start a regtest lab, `make test` will run unit tests, and `make itest` will run integration tests.

## Style

Run `gofmt`, `go vet`, and `staticcheck`. Keep them clean. Add tests for every behaviour change. Put new user-facing strings in `locales/en.json`.

## Own work

Code, tests and translations must be your own work.

## Commits

Every commit message is one line, in the form `pkg: summary`, as in LND. Examples: `consensus: add compact target decoding`, `multi: rename header store`. No body. No trailers. CI checks this.

One logical change per commit. One topic per PR.

## Translations

You must speak the language you translate. See docs/i18n.md.

## Questions

Ask in the issue or in a draft PR.
