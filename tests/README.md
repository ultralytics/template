<br>
<a href="https://www.ultralytics.com/" target="_blank"><img src="https://raw.githubusercontent.com/ultralytics/assets/main/logo/Ultralytics_Logotype_Original.svg" width="320" alt="Ultralytics logo"></a>

# Tests Directory (`tests/`)

This directory houses the tests for our project, providing two options for running tests: using **pytest** or **unittest**.

## Overview

- Contains test scripts written in Python.
- Organized to mirror the structure of the `src/` directory for easy reference.
- Tests should be comprehensive and cover a wide range of cases.

## Running Tests

### Option 1: Using `pytest` (Recommended)

1. **Install pytest:**
   ```bash
   pip install pytest
   ```
2. **Run Tests:**
   ```bash
   python -m pytest tests -v
   ```

### Option 2: Using `unittest` (No Additional Dependencies)

If you'd prefer to avoid external dependencies, you can use Python's built-in `unittest` framework:

1. **Run Tests:**
   ```bash
   python -m unittest discover tests -v
   ```

### Notes
- `pytest` offers more advanced features and better readability, making it the recommended choice.
- `unittest` is built into Python and eliminates the need for additional installations, providing a lightweight alternative.

Make sure to regularly run tests to maintain the reliability and quality of the project.


