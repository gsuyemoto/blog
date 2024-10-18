---
title: Helix Editor
slug: helix-editor
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-03-25T22:08:04.000Z
draft: true
---

Just some notes and stuff when using the helix editor, mainly for Rust coding.

Adding a theme in the configuration:
`
    hx
    :config-open
    // in config add this to line 1
    theme="base16_theme"
`
Adding sudo support:

1. Add the following to your .profile or .bash_profile whichever is appropriate for you: `export SUDO_EDITOR="/path/to/target/release/hx"`
2. source ~/.profile
3. on CLI: `sudoedit some_file`
