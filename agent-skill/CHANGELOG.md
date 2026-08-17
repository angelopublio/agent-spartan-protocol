# Changelog

All notable changes to the Spartan skill package will be documented in this file.

## [1.0.0](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.6.1...v1.0.0) (2026-08-17)


### ⚠ BREAKING CHANGES

* **spartan:** the `APPROVE` and `CHANGES` review verdicts are renamed to `APPROVED` and `CHANGES_REQUESTED`. Consumers matching the old literals must accept both spellings.

### Features

* **spartan:** name review verdicts by resulting state ([ad1331e](https://github.com/angelopublio/agent-spartan-protocol/commit/ad1331e18582370e21e905b93179a2b00d9a7a5b))

## [0.6.1](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.6.0...v0.6.1) (2026-08-12)


### Bug Fixes

* **spartan:** correct Cursor invocation and generalize host admission ([eb63ade](https://github.com/angelopublio/agent-spartan-protocol/commit/eb63ade2212f874c06a2a5ac6c1d0bce91a29faa))

## [0.6.0](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.5.0...v0.6.0) (2026-08-07)


### Features

* **plugin:** move the skill into skills/spartan and set the marketplace display name ([ca86623](https://github.com/angelopublio/agent-spartan-protocol/commit/ca866235f8f77502022815ab0a134e58138159fd))

## [0.5.0](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.4.1...v0.5.0) (2026-08-05)


### Features

* **spartan:** add handoff envelope identifier (HX-NNN) ([0943941](https://github.com/angelopublio/agent-spartan-protocol/commit/094394198f7864b15c6a467b12e48911bf8b70ea))

## [0.4.1](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.4.0...v0.4.1) (2026-08-03)


### Bug Fixes

* **spartan:** bind invocation to host capability and add Cursor routing ([9caed4e](https://github.com/angelopublio/agent-spartan-protocol/commit/9caed4eefae05fe031712b8e68773715d4b913f9))

## [0.4.0](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.3.1...v0.4.0) (2026-07-31)


### Features

* **protocol:** reserve spartan directories and define design material ([c394a8d](https://github.com/angelopublio/agent-spartan-protocol/commit/c394a8d5b4a1bc0df458c3e01e94a66fb51a88da))

## [0.3.1](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.3.0...v0.3.1) (2026-07-22)


### Bug Fixes

* **spartan:** bind invocation tokens to hosts ([1214edd](https://github.com/angelopublio/agent-spartan-protocol/commit/1214edd145cc61eb68e9ea3ce6ee9750d43cc11d))

## [0.3.0](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.2.0...v0.3.0) (2026-07-21)


### Features

* **distribution:** add public install path via marketplace and skills CLI ([74b4ec3](https://github.com/angelopublio/agent-spartan-protocol/commit/74b4ec317937cbeec1cf7c3fddef9254af372b07))

## [0.2.0](https://github.com/angelopublio/agent-spartan-protocol/compare/v0.1.0...v0.2.0) (2026-07-21)


### Features

* **release:** add automated release policy ([32b7ccc](https://github.com/angelopublio/agent-spartan-protocol/commit/32b7ccc887c7b86d763c00e8e55a4263de291e6a))

## 0.1.0 (2026-07-20)

### Features

- Established the validated static protocol, portable skill, task template, routing guidance, and public task-artifact history.
