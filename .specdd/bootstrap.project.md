# Story Maker

Rust 2021. One binary named `story-maker`.
The terminal UI is ratatui on crossterm. Arguments use clap. JSON uses serde. The settings file uses the toml crate.
ffmpeg and ffprobe must be on PATH. Shell out to them. Do not bind libav.
Tests run with `cargo test`.
No database, no web server, no tokio runtime, and no Publish crate.
Do not add a second UI toolkit.

`README.md` is the overview for the band. When a spec change alters behavior a person can see, update `README.md` in the same change.

Do not scaffold implementation code unless the user asks to build. Specs may name files that do not exist yet.
