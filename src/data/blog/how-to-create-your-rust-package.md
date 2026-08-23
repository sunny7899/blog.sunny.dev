---
title: how to create your rust package
author: Sunny
pubDatetime: 2026-08-23T04:06:31Z
slug: how-to-create-your-rust-package
featured: false
draft: false
tags:
  - AI agents
  - LangGraph
  - TypeScript
description:
  In the Rust ecosystem, packages are called crates, and they are managed by Cargo—Rust’s official build system and package manager. Whether you are building a library for others to use or an executable binary, Cargo handles the project creation, building, testing, and publishing processes seamlessly.
---


Here is a step-by-step guide to creating, structuring, and publishing your first Rust library crate to **crates.io** (the official Rust package registry).

---

## How to Create and Publish a Rust Crate

1. **Initialize the Crate:** Use Cargo to scaffold the project.
To create a new library crate, open your terminal and use the `cargo new` command with the `--lib` flag. If you were building an application, you would omit the flag.

```bash
cargo new my_awesome_crate --lib
cd my_awesome_crate

```

This generates a standardized project structure, initializing a Git repository along with it.


2. **Understand the Project Layout:** The heart of your crate.
Cargo generates two primary files:

1. **`Cargo.toml`**: The manifest file containing metadata and dependencies.
2. **`src/lib.rs`**: The root file for your library's code.

Update your `Cargo.toml` to include metadata required for publishing. You must add a description and a license.

```toml
[package]
name = "my_awesome_crate"
version = "0.1.0"
edition = "2021"
description = "A lightweight utility for doing awesome things."
license = "MIT OR Apache-2.0"
repository = "https://github.com/yourusername/my_awesome_crate"

```


3. **Write Code and Tests:** Writing your library code.
Open `src/lib.rs`. You will see that Cargo has already generated a basic test function for you. Replace it with your actual logic.

In Rust, testing is built directly into the language. It is best practice to keep your unit tests in the same file as your code using the `#[cfg(test)]` module.

```rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// let result = my_awesome_crate::add(2, 2);
/// assert_eq!(result, 4);
/// ```
pub fn add(left: usize, right: usize) -> usize {
    left + right
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        let result = add(2, 2);
        assert_eq!(result, 4);
    }
}

```

Run `cargo test` in your terminal to ensure everything works. Cargo will even run the code inside your documentation comments!


4. **Log In to Cargo:** Authenticate with crates.io.
Before publishing, you need an account on crates.io.

1. Go to [crates.io](https://crates.io/) and log in with your GitHub account.
2. Navigate to your Account Settings and generate a new API token.
3. Back in your terminal, authenticate Cargo using the token:

```bash
cargo login <your-api-token>

```

*Note: This saves your token locally in `~/.cargo/credentials`. Do not share it!*


5. **Publish Your Crate:** Sharing your crate with the world.
Before uploading, it is highly recommended to do a dry run to ensure your crate packages correctly and passes all checks:

```bash
cargo publish --dry-run

```

If the dry run is successful and there are no uncommitted changes in your working directory, publish it for real:

```bash
cargo publish

```

Congratulations! Your crate is now live and can be installed by anyone using `cargo add my_awesome_crate`.


---

## Best Practices for Rust Crates

* **Document Everything (Rustdoc):** Rust has a world-class documentation generator. Use `///` (three slashes) above functions, structs, and modules to document them. When you publish your crate, it will automatically be hosted and beautifully formatted on [docs.rs](https://docs.rs/).
* **Follow Semantic Versioning:** Cargo heavily relies on SemVer (`MAJOR.MINOR.PATCH`). Only increment the `MAJOR` version when you make breaking API changes.
* **Include a README:** Cargo will automatically look for a `README.md` file in your project root and display it on your crate's page on crates.io. Make sure it includes a brief overview and a quickstart example.
* **Dual-License Your Code:** The Rust ecosystem standard is to dual-license crates under both the `MIT` and `Apache-2.0` licenses. This provides maximum compatibility for users wanting to integrate your library.