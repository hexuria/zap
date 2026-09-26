## 2026-06-03T05:57:00Z

Please perform the following tasks:

1. Create a new directory `tmp/src/tui` if it does not exist, and create `tmp/src/tui/mod.rs` with content:
```rust
pub mod verify;
```

2. Create `tmp/src/tui/verify.rs` containing the Ratatui-based Verification TUI. The TUI must support:
- Stdin/stdout interactive terminal check: `if !std::io::stdin().is_terminal() || !std::io::stdout().is_terminal() { println!("Non-interactive shell detected, bypassing verification TUI."); return Ok(()); }`
- Two-pane layout: Left pane displays list of commands; Right pane displays the selected command info, its list of tokens, and details of the selected token.
- Tab/Left/Right arrow switches focus between Commands pane and Tokens pane.
- Up/Down arrows scroll/navigate the active list.
- Pressing `v` toggles the selected command's `verified` status.
- Pressing `V` (shift + v) toggles the schema's meta `verified` status.
- Pressing `t` live-tests the selected token's data source using `tmp_core::resolver::DataResolver::resolve(&token.data_source, &context). Show the resulting values or error in the token detail card.
- Pressing `e` opens an interactive inline editor (like a text prompt or editor) to edit the token's data_source details (like modifying the command or resolver string) and updates the in-memory schema state.
- Pressing `Enter` or `Esc` closes the inline editor.
- Pressing `s` saves the current state (verifies the entire schema, writes it back, and exits).
- Pressing `q` or `Esc` exits the TUI without saving.

3. Create `tmp/src/commands/generate.rs` to implement the `generate` subcommand. The `generate` subcommand must:
- Accept optional arguments: `--help-text <HELP_TEXT_FILE>`, `--provider <PROVIDER>`, `--model <MODEL>`, `--rollback <VERSION>`, `--history`
- If `--history` is specified: list all schema versions (version number and modification timestamp).
- If `--rollback <VERSION>` is specified: rollback the schema to the specified version and write the rolled-back schema to the appropriate directory as a new version.
- If `--help-text` is specified or if help text is supplied: parse the help output. If the input is a file path, read the file; if it's a directory, recursively execute `--help` on the binary (or build target) to gather help text (`parse_recursive_help`).
- If help text is parsed: generate the schema using the LLM clients (using the provider and model overrides if specified). Compare the newly generated schema with the latest existing version (if any). If they differ, output a pretty-printed colorized JSON diff (`generate_diff`) to stdout, prompt the user if they want to save, and if confirmed, run the Verification TUI (if interactive) to allow verification. Finally, save the verified schema as a new version and update the active schema.
- Support a non-interactive flag `--non-interactive` (or check `std::io::stdin().is_terminal()`). In non-interactive mode, bypass the Verification TUI and save the generated schema directly without prompting.

4. Register `tmp::tui` and `tmp::commands::generate` in `tmp/src/main.rs` and `tmp/src/commands/mod.rs`.
5. Update `tmp/src/main.rs` to include the `Generate` variant in the `Commands` enum and hook it up in `run_cli`.
6. Verify your implementation by running `cargo check`. Make sure all changes compile cleanly.
