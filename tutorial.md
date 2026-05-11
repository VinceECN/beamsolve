# Workshop tutorial — Building and publishing a Python package

This tutorial guides you through every step covered in the workshop, using **beamsolve** as the working example.

---

## Table of contents

1. [Terminal cheatsheet](#1-terminal-cheatsheet)
2. [VSCode cheatsheet](#2-vscode-cheatsheet)
3. [Virtual environment](#3-virtual-environment)
4. [Install the package in editable mode](#4-install-the-package-in-editable-mode)
5. [Display the package tree](#5-display-the-package-tree)
6. [Run an example](#6-run-an-example)
7. [Build the Sphinx documentation](#7-build-the-sphinx-documentation)
8. [Generate the architecture diagram](#8-generate-the-architecture-diagram)
9. [Set up a GitHub repository](#9-set-up-a-github-repository)
10. [Add and commit files](#10-add-and-commit-files)
11. [Enable online documentation with gh-pages](#11-enable-online-documentation-with-gh-pages)
12. [Create a GitHub release](#12-create-a-github-release)
13. [Archive on Software Heritage](#13-archive-on-software-heritage)
14. [Publish on PyPI (perspective)](#14-publish-on-pypi-perspective)

---

## 1. Terminal cheatsheet

| Command | Description |
|---|---|
| `cd <path>` | Navigate to a directory |
| `.` | Current directory |
| `..` | Parent directory |
| `~` | Home directory of the current user |
| `tree /F /A` | Display directory tree including files (Windows) |
| `python` | Open a Python shell |
| `exit()` | Exit the Python shell |

---

## 2. VSCode cheatsheet

| Shortcut | Description |
|---|---|
| `Ctrl+ù` | Toggle between editor and terminal |
| `Ctrl+Alt+↑/↓` | Duplicate cursor on line above/below |
| `Alt+↑/↓` | Move a line up/down |
| `Ctrl+:` | Comment/uncomment a line |
| `Ctrl+Shift+K` | Delete a line |
| `Ctrl+,` | Open Settings |
| `Shift+Enter` | Run current cell/selection in Interactive Window |
| `F5` | Start debugging |

> **Tip — selecting the Python interpreter.** If you see "import could not be resolved" warnings but your code runs fine, Pylance is looking at the wrong interpreter. Open the Command Palette (`Ctrl+Shift+P`), click *Python: Select Interpreter*, and choose your virtual environment.

---

## 3. Virtual environment

A virtual environment isolates the package dependencies from your system Python.

**Create** a virtual environment named `venv`:
```
python -m venv venv
```

**Activate** it:
```
.\venv\Scripts\activate          # Windows (PowerShell)
source venv/bin/activate         # Linux / macOS
```

If you get a permissions error on Windows, run:
```
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**Deactivate** when you are done:
```
deactivate
```

**Delete** the environment:
```
Remove-Item -Recurse -Force venv    # Windows
rm -rf venv                         # Linux / macOS
```

---

## 4. Install the package in editable mode

From the project root (where `pyproject.toml` is located), run:
```
pip install -e .
```

This installs the package in *editable mode*: any changes you make to the source code are immediately reflected in the active environment — no need to reinstall after each modification.

To install all runtime dependencies from `requirements.txt`:
```
pip install -r requirements.txt
```

To install development tools:
```
pip install -r requirements-dev.txt
```

---

## 5. Display the package tree

From the project root, run:
```
tree /F /A
```
(Windows) or
```
find . -not -path "./.git/*" -not -path "./venv/*" | sort
```
(Linux / macOS)

You should see the full package structure.

---

## 6. Run an example

Open `examples/simply_supported.py` in VSCode and run it with `Shift+Enter` (cell by cell) or `F5` (full script).

You should see:
- Console output indicating the assembly and solve steps.
- A comparison between the FEM and analytical mid-span deflections.
- A plot of the deflected beam.

---

## 7. Build the Sphinx documentation

Go to the `docs/` folder:
```
cd docs
```

Clean any previous build:
```
.\make.bat clean      # Windows
make clean            # Linux / macOS
```

Build the HTML documentation:
```
.\make.bat html       # Windows
make html             # Linux / macOS
```

Open `docs/build/html/index.html` in your browser to view the result.

> **First time?** Install the documentation dependencies first:
> ```
> pip install -r docs/requirements.txt
> ```

---

## 8. Generate the architecture diagram

This requires `pylint` (for `pyreverse`) and `Graphviz` to be installed.

**Step 1 — Generate the `.dot` file.**  
Go to `docs/source/architecture/` and run:
```
pyreverse -o dot -p beamsolve ../../../beamsolve
```
This produces `classes_beamsolve.dot` and `packages_beamsolve.dot`.

**Step 2 — Edit the `.dot` file** to apply colours and grouping:
```
python edit_dot.py
```
This produces `classes_beamsolve_edited.dot`.

**Step 3 — Convert to SVG:**
```
dot -Tsvg classes_beamsolve_edited.dot -o ../_static/architecture/classes_beamsolve.svg
```

Rebuild the documentation to see the diagram included in the *Package architecture* page.

---

## 9. Set up a GitHub repository

1. Go to the project root and initialise Git:
   ```
   git init
   ```

2. Stage and commit all files:
   ```
   git add .
   git commit -m "Initial commit"
   ```

3. Create a new repository on [github.com](https://github.com): click **+** → *New repository*, fill in the details, click *Create repository*.

4. Link your local project to the remote repository (the commands are shown on the GitHub page after creation):
   ```
   git remote add origin https://github.com/<username>/beamsolve.git
   git branch -M main
   git push -u origin main
   ```

---

## 10. Add and commit files

To stage, commit, and push changes:
```
git add .
git commit -m "<descriptive commit message>"
git push origin main
```

To remove a file from the repository but keep it locally (e.g. if you forgot to add it to `.gitignore`):
```
git rm -r --cached <file path>
git commit -m "Remove <file> from tracking"
git push
```

---

## 11. Enable online documentation with gh-pages

The file `.github/workflows/docs.yml` automatically builds and deploys the documentation to GitHub Pages every time you push to `main`.

To activate GitHub Pages:
1. Go to the repository on GitHub → **Settings** → **Pages**.
2. Under *Source*, select **Deploy from a branch**.
3. Select the `gh-pages` branch and the `/ (root)` folder.
4. Click **Save**.

After the next push to `main`, the documentation will be available at:
```
https://<username>.github.io/beamsolve/
```

To force a doc rebuild without any code change:
```
git commit --allow-empty -m "Trigger doc build"
git push
```

---

## 12. Create a GitHub release

A release marks a stable version of the code on GitHub and is the basis for archiving and citation.

1. Update `beamsolve/__version__.py` with the new version number.

2. Run `update_version.py` to propagate the version:
   ```
   python update_version.py
   ```

3. Update `CHANGELOG.md` with the release notes.

4. Commit everything:
   ```
   git add .
   git commit -m "Prepare release v0.1.0"
   ```

5. Create a tag:
   ```
   git tag -a v0.1.0 -m "Release v0.1.0"
   ```

6. Push the tag:
   ```
   git push origin v0.1.0
   ```

7. On GitHub: go to the repository → **Releases** → **Draft a new release** → select the tag `v0.1.0` → add a title and paste the `CHANGELOG.md` content → click **Publish release**.

To delete a tag (if you made a mistake):
```
git tag -d v0.1.0
git push origin :refs/tags/v0.1.0
```

---

## 13. Archive on Software Heritage

[Software Heritage](https://www.softwareheritage.org/) provides long-term archiving of source code with a persistent identifier (SWHID), which can be used for citation.

1. Go to [https://archive.softwareheritage.org/save/](https://archive.softwareheritage.org/save/).
2. Enter the URL of your GitHub repository.
3. Click **Submit**.

Software Heritage will crawl and archive the repository. Once done, you will obtain a SWHID of the form:
```
swh:1:dir:...
```
Add this identifier to your `CITATION.bib` to make your software citable with a persistent archive link.

---

## 14. Publish on PyPI (perspective)

### Build the package
From the project root:
```
python -m build
```
This generates a `dist/` folder containing a `.tar.gz` source archive and a `.whl` wheel.

### Test on TestPyPI
```
twine upload --repository testpypi dist/*
```
This requires an account on [https://test.pypi.org/](https://test.pypi.org/) and an API token.

Test the installation from TestPyPI:
```
pip install --index-url https://test.pypi.org/simple/ --extra-index-url https://pypi.org/simple beamsolve
```

### Publish on PyPI (production)
```
twine upload dist/*
```
This requires an account on [https://pypi.org/](https://pypi.org/).

After publication, anyone can install the package with:
```
pip install beamsolve
```

> **Note on the README.** PyPI renders `.rst` files better than `.md`. Use `README.rst` as the readme in `pyproject.toml`. To test local rendering before uploading:
> ```
> python -m docutils README.rst output.html
> ```
