# NixCleaner

NixCleaner is a high-performance Python utility designed to transform complex, deeply nested NixOS configurations into a declarative, flat-attribute module.
By converting hierarchical Nix expressions into a sorted, fully-qualified key-value format, NixCleaner eliminates "brace fatigue" and makes your system configuration as readable as a standard .conf or .ini file.

🚀 Key Features

  * Recursive Path Flattening: Automatically unrolls nested blocks. It converts services = { openssh = { enable = true; }; }; into the clean, single-line services.openssh.enable = true;.
  * Structural Integrity: Uses a stack-based state machine to track nesting depth. It ensures "orphaned" settings (like a lone enable = true;) are correctly rejoined to their parent paths.
  * Syntax-Aware Parsing: Specifically protects Nix-specific multi-line strings ('') and lists ([ ]). It prevents internal equals signs or semicolons from breaking the code.
  * Pre-Flight Validation: Before outputting any code, the script automatically runs nix-instantiate --parse to ensure the generated configuration is 100% syntactically valid for NixOS 2026.
  * Deterministic Sorting: Orders all attributes alphabetically, naturally grouping boot.*, hardware.*, and services.* settings together for easy auditing.

🛠 How It Works
NixCleaner operates in a four-stage pipeline:

  * Tokenization: Uses regex to shield strings and function overrides (like llama-cpp.override) from the flattening process.
  * Reconstruction: Iterates through the file tokens to build absolute attribute paths for every setting.
  * Automated Testing: Generates a temporary file and invokes the Nix parser to verify the result.
  * Standardization: Wraps the flattened output in a standard NixOS function header ({ config, pkgs, lib, ... }:), making it a drop-in replacement for your existing configuration.nix.

📦 Usage
Run with Nix Shell (No Installation)
bash
```
nix shell nixpkgs#python3 --command python3 ./nix_cleaner
```
Use code with caution.
Save and Validate
bash
```
./nix_cleaner > configuration.flat.nix
```
If validation passes, you can safely swap your config
```
sudo mv configuration.flat.nix /etc/nixos/configuration.nix
```
Use code with caution.

⚖️ Why Flatten?

On modern NixOS versions (like 25.11), configurations grow rapidly. Flattening your configuration:

  * Improves Searchability: grep always returns the full context of a setting.
  * Simplifies Diffing: Git diffs become much cleaner as indentation changes no longer trigger "false positive" line changes.
  * Reduces Syntax Errors: Eliminates the risk of mismatched closing braces in files hundreds of lines long.
