---
title: How to Create Your First PyPI Package
author: Sunny
pubDatetime: 2026-08-16T16:06:31Z
slug: how-to-create-your-first-pypi-package
featured: false
draft: false
tags:
  - Python
  - PyPI
description:
  Publishing your first Python package to the Python Package Index (PyPI) is a major milestone for any developer. It allows anyone in the world to install your code using a simple `pip install your-package` command.
ogImage: https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_4swwd84swwd84sww.png
---

![pypi](https://pub-084cb927976c4020b1cc9f91f5f56f6b.r2.dev/posts/Gemini_Generated_Image_4swwd84swwd84sww.png)

While older tutorials might instruct you to use `setup.py`, the modern, standardized approach uses `pyproject.toml`. This guide will walk you through the modern workflow from project creation to publishing.

---

## How to Create and Publish Your Package

1. **Set Up Your Project Structure:** The foundation.
Create a new directory for your project. The best practice is to put your actual Python code inside a `src` directory to prevent accidental imports during testing.

```text
my_package_project/
├── pyproject.toml
├── README.md
└── src/
    └── my_package/
        ├── __init__.py
        └── example.py

```

Your `__init__.py` can be empty, but it is required for Python to recognize the directory as a package.


2. **Create pyproject.toml:** Replacing setup.py.
The `pyproject.toml` file tells build tools how to package your project and defines metadata like the version, author, and dependencies. Here is a minimal example:

```toml
[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[project]
name = "my_unique_package_name"
version = "0.1.0"
authors = [
  { name="Your Name", email="your.email@example.com" },
]
description = "A short description of my first package"
readme = "README.md"
requires-python = ">=3.8"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

```

*Note: The `name` must be entirely unique across all of PyPI.*


3. **Build the Package:** Generating the distribution archives.
Next, you need to convert your source code into a distribution archive (a `.tar.gz` and a `.whl` file) that PyPI can host.

First, upgrade your build module, then run it:

```bash
python -m pip install --upgrade build
python -m build

```

This will create a `dist/` directory containing two files. These are the files you will upload.


4. **Upload to TestPyPI:** A safe sandbox for testing.
Before publishing to the real PyPI, test the process using **TestPyPI**. This prevents you from permanently burning a version number if you make a mistake.

1. Register an account on [TestPyPI](https://test.pypi.org/account/register/).
2. Go to your Account Settings and create an API token (name it something like "Test Uploads").
3. Install Twine, the official PyPI upload tool:

```bash
python -m pip install --upgrade twine

```

4. Upload your package:

```bash
python -m twine upload --repository testpypi dist/*

```

When prompted for a username, use `__token__`. For the password, paste the API token you generated.


5. **Publish to the Real PyPI:** The final milestone.
Once you have verified that everything looks correct on TestPyPI, repeat the process for the real PyPI:

1. Register on [PyPI.org](https://pypi.org/account/register/).
2. Generate an API token in your account settings.
3. Run the upload command (without the test repository flag):

```bash
python -m twine upload dist/*

```

Congratulations! Your package is now live. Anyone can run `pip install my_unique_package_name`.


---

## Pro Tips and Best Practices

* **Always use Semantic Versioning:** Follow the `MAJOR.MINOR.PATCH` format (e.g., `1.2.4`). Increment the patch number for bug fixes, the minor number for new features, and the major number for breaking changes. Note that once a version is uploaded to PyPI, you can *never* upload that exact version number again, even if you delete it.
* **Write a comprehensive README:** PyPI uses your `README.md` file as the homepage for your package. Include an introduction, installation instructions (`pip install ...`), a quickstart code example, and a link to your issue tracker or GitHub repository.
* **Use `.gitignore`:** If you are using Git, ensure you add `dist/`, `build/`, and `*.egg-info/` to your `.gitignore` file so you don't accidentally commit your build artifacts to version control.
* **Include a License:** If you don't include a license, default copyright laws apply, meaning nobody is legally allowed to use or modify your code. An MIT license is a common choice for open-source Python packages.