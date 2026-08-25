---
name: repo-inventory
description: Read-only inventory of a Python repository — language/version, dependency manager, declared dependencies, entrypoints, test setup, CI config, and anything unusual. Use with fanout in report mode to survey a fleet. Changes nothing.
---

# repo-inventory

Survey this repository WITHOUT modifying any file. Read pyproject.toml, setup.py,
requirements*.txt, Pipfile*, environment.yml, Dockerfile*, CI config (Jenkinsfile,
.github/workflows/*, .gitlab-ci.yml), README*, and the top-level layout. Do not run
the code. Do not install anything.

Write SUMMARY.md at the repo root in exactly this shape, then stop:

~~~
status: complete
items:
  - python: <version constraint or "unknown">
  - deps-manager: <pip | pipenv | poetry | uv | conda | none | mixed>
  - deps-count: <number of direct dependencies>
  - notable-deps: <up to 6 that matter, e.g. snowflake-connector-python, pyspark, kfp, with versions if pinned>
  - entrypoints: <main.py | run.sh | console_scripts | pipeline.py | unknown>
  - tests: <pytest | unittest | none> — <approx count of test files>
  - ci: <jenkins | github-actions | gitlab | none>
  - docker: <base image or "none">
  - flags: <anything a platform engineer should know: old pins, password auth, vendored code, no lockfile>
notes: <2–4 lines: what this repo is and how it runs, in your own words>
~~~

If a field cannot be determined, write "unknown". Never guess versions.
Do not ask questions; this is a survey, and "unknown" is an acceptable answer.