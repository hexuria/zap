## 2026-06-03T13:03:52Z
You are a worker subagent implementing CLI commands for schema management in `tmp`.

Please perform the following steps:

1. Modify `tmp/Cargo.toml` if needed to include `ureq` as a dependency (same version as `tmp-core`: `ureq = { version = "2.9", features = ["json"] }`), OR implement the schema import/share/keywords logic inside `tmp-core` and expose them to `tmp`. Adding `ureq` to `tmp/Cargo.toml` directly is recommended for simplicity of CLI subcommand implementation.

2. Modify `tmp/src/main.rs`:
   Update the `SchemaSubcommands` enum to include:
   - `Share { tool: String }`
   - `Import { source: String }`
   - `Keywords { tool: String, words: Vec<String> }`
   Update `run_cli` to route these subcommands to `commands::schema::share`, `commands::schema::import`, and `commands::schema::keywords` respectively.

3. Modify `tmp/src/commands/schema.rs`:
   Implement:
   - `pub fn share(tool: &str, custom_config_path: Option<&str>) -> Result<(), Box<dyn std::error::Error>>`
     This must:
     a. Load the config to find the schema directory.
     b. Find `<tool>.json` in `~/.config/tmp/schemas/` (or the configured schema directory). If not found, return an error.
     c. Parse it using `tmp_core::schema::Schema::from_json`.
     d. Call `export_shareable()` on it.
     e. Serialize back (recommend `serde_json::to_string_pretty`) and write to `<tool>.json` in the current working directory.
   - `pub fn import(source: &str, custom_config_path: Option<&str>) -> Result<(), Box<dyn std::error::Error>>`
     This must:
     a. Load the config to find/create the schema directory.
     b. Read the schema content from either a local file or a URL (if starts with "http://" or "https://", fetch using `ureq`). If starts with "file://", strip the prefix and read as local file.
     c. Parse and validate the schema content using `tmp_core::schema::Schema::from_json` (the validation logic runs automatically on deserialization/deserializer helper).
     d. Write the pretty-printed JSON schema to `~/.config/tmp/schemas/<tool>.json` (where `<tool>` is the tool name parsed from the schema itself).
   - `pub fn keywords(tool: &str, words: Vec<String>, custom_config_path: Option<&str>) -> Result<(), Box<dyn std::error::Error>>`
     This must:
     a. Load the config to find the schema directory.
     b. Find `<tool>.json` in the schemas directory. If not found, return an error.
     c. Read and parse it.
     d. If `words` is empty, print/show the current keywords in `meta.keywords` (one per line).
     e. If `words` is not empty, overwrite `meta.keywords` with `words`, serialize and write back to the local schema file.

4. Modify `tests/e2e_tier1.rs`:
   - Remove `#[ignore]` from:
     - `test_tier1_f3_schema_share`
     - `test_tier1_f3_schema_import`
     - `test_tier1_f3_schema_keywords_show`
     - `test_tier1_f3_schema_keywords_set`
   - Modify the tests to perform proper setup:
     - In `test_tier1_f3_schema_share`, write a mock cargo schema in the sandbox schemas directory (e.g. `sandbox.config_dir.join("schemas").join("cargo.json")`) before running `schema share`.
     - In `test_tier1_f3_schema_import`, write a mock valid schema to `custom_schema.json` in the project directory before running `schema import`.
     - In `test_tier1_f3_schema_keywords_show` and `test_tier1_f3_schema_keywords_set`, write a mock cargo schema in the sandbox schemas directory before running `schema keywords`.

5. Modify `tests/e2e_tier2.rs`:
   - Remove `#[ignore]` from:
     - `test_tier2_f3_corrupted_json` (already has test logic, just unignore)
     - `test_tier2_f3_share_nonexistent_dir` (implement: write mock `cargo.json` in schemas, then run in a nonexistent directory using `run_in_dir`, assert it fails)
     - `test_tier2_f3_keywords_special_chars` (implement: write mock `cargo.json` in schemas, run keywords set with special characters, read back and assert they were updated)
     - `test_tier2_f3_import_conflict` (implement: write mock `cargo.json` in schemas with version 1, create file with same tool and version 2, import it, and assert the file on disk was overwritten and has version 2)
     - `test_tier2_f3_share_nonexistent_tool` (already has test logic, just unignore)

6. Run check/test verification:
   - Run `cargo fmt`
   - Run `cargo clippy -- -D warnings`
   - Run `cargo test` and ensure all tests pass.

## 2026-06-03T05:40:18Z
Verify the baseline by running `cargo check` and `cargo test` in the `/Volumes/goldcoders/tmp` workspace. After verifying, add `ratatui = "0.26"` and `crossterm = "0.27"` to `tmp/Cargo.toml`. Run `cargo check` again to ensure the workspace compiles successfully with the new dependencies. Report the results.

MANDATORY INTEGRITY WARNING: DO NOT CHEAT. All implementations must be genuine. DO NOT hardcode test results, create dummy/facade implementations, or circumvent the intended task. A Forensic Auditor will independently verify your work. Integrity violations WILL be detected and your work WILL be rejected.

## 2026-06-03T06:15:06Z
Run `cargo check` and `cargo test` in the `/Volumes/goldcoders/tmp` workspace. Confirm that there are no compilation errors or warnings, and that all active tests (including E2E and unit tests) pass successfully. Report the exact output of the test command.

MANDATORY INTEGRITY WARNING: DO NOT CHEAT. All implementations must be genuine. DO NOT hardcode test results, create dummy/facade implementations, or circumvent the intended task. A Forensic Auditor will independently verify your work. Integrity violations WILL be detected and your work WILL be rejected.

