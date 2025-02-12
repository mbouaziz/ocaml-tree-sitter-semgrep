#
# Build support for the programming languages listed in this folder.
#
# ocaml-tree-sitter must have been built and installed for this to work.
#

# List of languages matching a tree-sitter-* repo.
#
# For example, both typescript and tsx are provided by tree-sitter-typescript.
# The list below only shows typescript, not tsx.
#
SUPPORTED_TS_LANGUAGES = \
  bash \
  c \
  c-sharp \
  cpp \
  dart \
  dockerfile \
  elixir \
  go \
  hcl \
  html \
  java \
  julia \
  javascript \
  jsonnet \
  kotlin \
  lua \
  ocaml \
  php \
  python \
  r \
  ruby \
  rust \
  sfapex \
  solidity \
  sqlite \
  typescript \
  vue
# Commented out due to hitting memory limits in CI:
#  hack

# Includes all language variants e.g. typescript and tsx
SUPPORTED_LANGUAGES = $(SUPPORTED_TS_LANGUAGES) tsx

# Languages which are set up to run parsing stats. Ideally, this is all
# the supported languages. See the 'stat' target.
#
STAT_LANGUAGES = \
  bash \
  c \
  c-sharp \
  cpp \
  dockerfile \
  elixir \
  go \
  hack \
  hcl \
  java \
  javascript \
  julia \
  kotlin \
  lua \
  ocaml \
  php \
  python \
  r \
  ruby \
  rust \
  sfapex \
  solidity \
  sqlite \
  tsx \
  typescript

# Build and test each language. Does not run parsing stats (see 'stat:').
#
# This takes a while and is intended for CI. If you're working on a single
# language, better just run ./test-lang.
#
.PHONY: test
test:
	set -e; \
	for lang in $(SUPPORTED_TS_LANGUAGES); do ./test-lang $$lang; done

.PHONY: release
release:
	@echo "'make release' is no longer supported so as to avoid"
	@echo "accidentally pushing changes for other languages."
	@echo "- If you really want to publish changes for all languages,"
	@echo "  use 'make release-all'."
	@echo "- If you want to release just one language named 'foo', run:"
	@echo ""
	@echo "    ./release foo"
	@echo ""
	exit 1

# Place the generated files into their own repo, to be used by an external
# repo as a submodule.
#
.PHONY: release-all
release-all:
	$(MAKE) -C semgrep-grammars
	set -eu; \
        for lang in $(SUPPORTED_LANGUAGES); do \
	  ./release $$lang; \
	done

# Make a pretend release.
.PHONY: dry
dry:
	$(MAKE) -C semgrep-grammars
	set -eu; \
        for lang in $(SUPPORTED_LANGUAGES); do \
	  ./release $$lang --dry-run; \
	done

# Run parsing stats for each language.
# Each needs a list of projects (projects.txt) and file extensions
# (extensions.txt).
#
.PHONY: stat
stat:
	set -eu; \
	for lang in $(STAT_LANGUAGES); do $(MAKE) -C $$lang stat; done
	../scripts/report-stat $(STAT_LANGUAGES) | tee stat.txt
	test -s stat.txt  # check that the pipeline above didn't fail

# Run stats on projects-priv.txt instead of projects.txt.
.PHONY: stat-priv
stat-priv:
	set -e; \
	for lang in $(STAT_LANGUAGES); do $(MAKE) -C $$lang stat-priv; done
	../scripts/report-stat $(STAT_LANGUAGES) | tee stat.txt

.PHONY: clean
clean:
	set -e; \
	for lang in $(SUPPORTED_LANGUAGES); do $(MAKE) -C $$lang clean; done

# Helpers for github PRs using git and hub command-line tools
.PHONY: pr
pr:
	git push origin `git rev-parse --abbrev-ref HEAD`
	hub pull-request -b main

.PHONY: push
push:
	git push origin `git rev-parse --abbrev-ref HEAD`

.PHONY: merge
merge:
	A=`git rev-parse --abbrev-ref HEAD` && git checkout main && git pull && git branch -D $$A
