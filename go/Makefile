# Go has no install step of its own, so this is the one place to hang the hook
# wiring. Mirrors `npm install` running javascript/package.json's "prepare".
#
# The script path is resolved from THIS makefile's own location rather than the
# caller's cwd, so every invocation form works: `cd go && make setup`,
# `make -C go setup`, `make -f go/Makefile setup`.
# Running it through `bash` also survives a checkout that lost the exec bit
# (zip download, Windows, core.fileMode=false).
HOOKS := $(dir $(lastword $(MAKEFILE_LIST)))../hooks

.PHONY: setup
setup:
	bash "$(HOOKS)/install.sh"
