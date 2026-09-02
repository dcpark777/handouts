# kubeflow-utils — transfer handoff

Notebook<->KFP batch pipeline framework: resolvers absorb every environment
delta; a declarative Python manifest mirrors a plain run_pipeline.py; a
generic runner enforces the storage boundary. Vendor-neutral: all platform
knowledge binds through ONE seam module.

NOTE: import name is `kubeflow_utils`; distribution name `kubeflow-utils`.
(Renamed from the working name to avoid colliding with the existing work
package.)

## Contents
- `package/` — installable library (`uv pip install -e ".[dev]"`),
  13-test suite (all passing), pyproject with lazy extras
  (`[sql]`, `[spark]`, `[snowflake]`).
- `example-repo/` — the authoring reference: steps/ modules (one pattern
  each), lib/ helpers, run_pipeline.py, pipelines/pipeline.py, notebook
  cells, and the two-command surface demo (validate_output.txt).

## The design in six lines
1. `mypackage/steps/` — plain functions, string-path + scalar params;
   `ls steps/` = the component list. Helpers in `lib/`.
2. `run_pipeline.py` — plain-Python local truth; hand-calls the functions.
3. `pipelines/pipeline.py` — declarative manifest (Dataset/Step/parallel/
   Pipeline); list order IS execution order (stage barriers); reads/writes
   drive enforcement + load-time consistency, never ordering.
4. Runner surrounds every step: resolve {param} location templates at
   runtime, pre-check reads, name-match kwargs from the signature, call,
   post-check writes, scalars-only returns, uniform logging.
5. Two commands total: `python run_pipeline.py` (local) and
   `python pipelines/pipeline.py` (validate + DAG + compile; `--check`
   for CI). Checks are ambient: import / compile / runtime.
6. Resolvers: `DataStore(path)` (path+creds; path contract: relative or
   own-URI in, full URI out, foreign URI raises), `Spark().get_session()`
   (layered conf, user wins, protected-key warning, warn-only on active
   session), `Snowflake().get_connection()` (local env token vs
   vault->OAuth, cached). `SqlRunner` = the one optional helper.

## Validated here (don't re-litigate)
- fs_io path contract, classify ordering (LOCAL wins ambiguity),
  relative-path absolutization — tested incl. memory:// remote.
- Manifest load-time checks: order-vs-dataflow, signature name-matching
  (+ per-step `args=` override), duplicate names, template placeholder
  typos, base_path requirement. All raise at import with teaching errors.
- Runner enforcement: pre/post dataset existence, scalar-only returns,
  JSON serde by annotation, CLI output files. Full 3-step pipeline runs
  end-to-end through the real runner in the tests.
- compile_spec(): stages, parallel groups, param/output routing
  (upstream scalar -> downstream signature name).

## Work-machine TODO (in order)
1. **Bind `kubeflow_utils/lake_broker.py`** — six callables, the only
   platform seam. Candidate: adapt from the shared stage-utils package's
   utils/mixin internals (region/proxy/OAuth/vault logic) rather than
   reimplementing; prefer its utils/ functions over instantiating mixins.
2. **Implement `to_kfp()`** in compile.py (only NotImplementedError in
   the package). Container contract is documented in its docstring:
   runner command + param/output wiring + RUNTIME_MODE=kfp +
   KUBEFLOW_UTILS_* env + stage-barrier `.after()` + resources. Wire the
   dev mode to the existing inline compiler; prod = CICD-baked image.
3. **Confirm Snowflake local auth** (env token assumed — swap to the
   real dev mechanism) and the vault client in `_read_vault`.
4. **Validate staging/production-lake DataStore branches** against real
   buckets/datasets (only memory:// + local proven here).
5. Optional next: `kubeflow-utils new step` scaffold (generate function
   skeleton + manifest entry together; include pyproject in repo
   scaffold — the `pip install -e .` papercut), then migrate the first
   real pipeline. The migration is the next teacher.

## Open (deliberately)
- `store.polars_kwargs` sugar — proposed, not ruled on.
- Mirror drift between run_pipeline f-strings and manifest templates is
  an accepted cost; runner postconditions are the net. Opt-in resolve
  helper only if the first migration demands it.

Scrub gate (run before any future export):
grep -rniE "capital|c1[-_]|\bcof\b|badge|bcfg|mlzone|onelake|talos|chamber|bogie|selene|aiml|bank|fraud|neptune" .
