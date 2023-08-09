---
title: Helix Editor and Rust Analyzer
slug: rust-helix-lsp-error
date: 2023-08-09
draft: true
categories:
  - Rust
---

[buffer error]: https://github.com/helix-editor/helix/issues/2164
[binary releases]: https://github.com/rust-lang/rust-analyzer/releases

# Helix and 'Language server not active for current buffer' error
I recently switched laptops and started on a clean install of Ubuntu. While installing Rust essentials, including Helix
<!-- more -->

I came across a problem using the LSP while testing out the initial install of the Helix editor and Rust.


1. When installining Helix using apt-get, the initial installation has an issue with the Rust LSP.
2. Go to the Rust analyzer [binary releases] page and download proper binary for OS.
3. Type the following to get location of analyzer
````bash
hx --health rust
````
4. Replace the executable with binary downloaded from github and set as executable
````bash
chmod +x rust-analyzer
````
5. When you first open Helix and run the analyzer with 'space-k' let it run for a bit (minutes in my case) and eventually it will start provided LSP data.

