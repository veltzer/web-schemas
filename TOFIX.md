# TOFIX

Findings from a code scan on 2026-10-04.

## Medium

- `docs/index.html:306` - the Pages front end hardcodes `GITHUB_REPO = "schemas"`, the repo's old name. It only works because GitHub redirects the renamed repo, and it breaks if a new `veltzer/schemas` is ever created. Fix: set it to `"web-schemas"`.
- `scripts/validate_schema.py:75-87` - the `propertyOrdering` check recurses into `properties`, `items` and `allOf/anyOf/oneOf`, but never into `$defs`. Eleven of the schemas keep their object definitions under `$defs` (all of `docs/json/shared/*.json`, plus `docs/json/organizations.json`, which has a `propertyOrdering` inside `$defs`), so those definitions are never checked. Fix: also recurse into `$defs`, `definitions`, `additionalProperties` and `patternProperties`.
- `rsconstruct.toml` - there is no `[processor.tera]`, so nothing renders `tera.templates/.github/dependabot.yml.tera`. The committed `.github/dependabot.yml` is a stale hand-made copy: it lacks the blank line between ecosystem blocks that the template emits. Fix: add `[processor.tera]` with `src_dirs = ["tera.templates"]` and `dep_auto = ["config/project.lua"]` (the only config file present).

## Low

- `pyproject.toml:14` - `pytest` is a dev dependency, but there are no tests and no pytest processor. Either add a test for `scripts/validate_schema.py` (a mismatched and a matching `propertyOrdering`, including the `$defs` case above) with a `[processor.pytest]`, or drop the dependency.
- `pyproject.toml:23` - `mypy_path = "src:python:scripts"` names `src` and `python`, and neither directory exists. Set it to `"scripts"`.
- `doc/TODO.txt:1` - "get ridd of the makefile and make pydmt the only builder" is stale: there is no Makefile and the build is rsconstruct. Remove the item.
- `support/dummy.yaml:1` - a one-line file (`items:`) whose only effect is giving `yamllint` something to check under `support` (`rsconstruct.toml:45`). Delete it and drop `support` from `src_dirs`, or document what it is for.
- `README.md:3` - the README is one line. It should link the published collection at `https://veltzer.github.io/web-schemas/` and describe the `docs/json` layout, including the `shared/` `$defs` modules.
