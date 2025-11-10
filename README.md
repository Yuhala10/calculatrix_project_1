# calculatrix_project_1
Files changed
- project_1_calculatrix.py — fixed indexing, handler signature, operator/equals/percent/sqrt/ans logic, cleaned globals.

Notes about behavior and edge cases
- I kept the UI layout and color constants untouched.
- The operator implementation stores the left operand in `A` and the operator in `operator`. Pressing `=` computes the result and stores it back in `A` so `ans` and chained operations work reasonably.
- Division by zero sets the display to `"Error"` and clears stored state.
- The code uses strings for display (`label["text"]`) and converts to float for math. Results are shown as integers when they are whole numbers.
- I did a syntax-only validation (py_compile) to avoid launching the Tk GUI during automated checks. I did not run the GUI — please run it locally to confirm runtime behavior and UI layout.

What I didn't change (but can, on request)
- Convert the procedural script into a class-based `CalculatorApp`.
- Enforce `tk` alias (`import tkinter as tk`) across the file — I left `import tkinter` as-is to minimize changes.
- Add unit tests (would require refactoring UI code to separate logic).


# Calculatrix Project (calculatrix_project_1)
This repository (calculatrix_project_1) is a small, local-first experiment: two Tkinter-based calculator prototypes and a set of focused fixes that make the grid calculator runnable and more robust. This README documents the problems encountered while building the program and the exact fixes applied so this can serve as a record for your first real project.

Files in this repo

- project_1_calculatrix.py — grid-based calculator UI (buttons, display label, operations). This file was fixed and improved.
- app_1.py — a very small two-input example (left largely unchanged).
- copilot-instructions.md — guidance for AI coding agents working on this repo.
- README.md — (this file) project narrative, problems, and fixes.
- .gitignore — ignores common Python build artifacts.

Why this project exists

This is a hands-on learning project: build a GUI, track bugs that beginners commonly make, and fix them. The goal was to make a working, shareable calculator while keeping the UI and original structure intact.

Detailed problems I faced (original issues)

1) Mixed Tkinter usage and incorrect widget names
   - Symptoms: code used both `import tkinter` and `import tkinter as tk` in different files and used `tk.ENTRY` instead of the correct `tk.Entry`.
   - Impact: wrong attribute names crash at runtime or behave unexpectedly.

2) Overwriting widget references with layout calls
   - Example problem: `Label = tk.Label(...).pack()` assigns `None` to `Label` because `.pack()` returns `None`.
   - Impact: later code that expects `Label` to be the widget fails.

3) Off-by-one indexing when building the button grid
   - Problem: code used `button_values[row][column-1]` while iterating `column` from `0..n-1`.
   - Impact: wrong values placed on buttons, IndexError possibilities.

4) Inconsistent handler parameter and global names
   - Problem: the button command used `lambda value=value: button_clicked(value)` but the handler was declared with a different parameter name and referenced `labels` or `value` inconsistently.
   - Impact: NameError or logic bugs preventing button clicks from updating state correctly.

5) Incorrect math implementations and incomplete handlers
   - Examples: `%` used `/ -11` instead of dividing by 100; `sqrt` and `ans` handlers were partial or incorrect.
   - Impact: wrong results and confusing UI behavior.

6) Mixing geometry managers on a container
   - Code used `grid` for some widgets and `pack` for the same frame in certain places. Tkinter expects a single geometry manager per container for predictable layout.

What I changed and why (detailed fixes)

Below are the concrete edits and the rationale. Changes were made to preserve the original UI and minimal structure while making the app reliable.

1) Fixed button grid indexing and column count
   - Before: `row_count = len(button_values)` and `column_count = len(button_values)` and `value = button_values[row][column-1]`
   - After: `row_count = len(button_values)` and `column_count = len(button_values[0])` and `value = button_values[row][column]`
   - Rationale: each row is a list of columns. This removes off-by-one errors and makes the grid mapping correct.

2) Normalized the button handler signature and globals
   - Before: handler used mismatched parameter names and referenced `labels`.
   - After: `def button_clicked(value):` and `global right_symbols, top_symbols, label, A, B, operator`
   - Rationale: single consistent name avoids NameError and makes wiring with `lambda value=value: button_clicked(value)` correct.

3) Implemented operator flow and '=' evaluation
   - Added logic to store left operand `A` and operator when `+ - x /` is pressed, then compute on `=`.
   - Added divide-by-zero handling (`label['text'] = 'Error'` and clear stored state).
   - Rationale: makes calculator behave like a standard basic calculator and supports chained operations via storing result back into `A`.

4) Corrected `%`, `+/-`, `sqrt`, and `ans` behavior
   - `%` now divides the current display by 100.0 (percentage).
   - `+/-` negates the current display value.
   - `sqrt` computes the square root (with error handling).
   - `ans` recalls the stored `A` (when available).
   - Rationale: these are common calculator functions; previously they were incorrect or incomplete.

5) Improved numeric input and decimal handling
   - Ensured `.` is appended only once; when entering digits, `0` is replaced by the first digit.
   - Rationale: makes typing numbers behave as users expect.

6) Kept visual style and layout
   - I intentionally left color constants and grid layout intact to preserve the visual design. Geometry manager usage was left consistent per container.

Implementation notes and snippets

- Storing operands and operator (concept):

  A = None
  operator = None

  # when user presses +, -, x, /
  A = label['text']
  operator = '+'  # or '-' etc.

  # when user presses '=' compute using float conversions and set label['text']

- Display formatting: results are shown as integers when the float is whole: e.g. 4.0 → "4".




