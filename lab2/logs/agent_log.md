# Agent Action Log - Lab 2 Refactoring

## [2026-06-13 11:05] FILE_CREATE: Directory structure (input/, output/, src/)

**Status**: ✓ Done
**File affected**: input/, output/, src/
**What was done**: Created three subdirectories for the modular project structure
**Output**: D:\computer vision\lab\lab2\input\, output\, src\
**Notes**: output/ will be auto-populated at runtime; input/ needs user-provided anh.jpg

---

## [2026-06-13 11:05] FILE_CREATE: src/__init__.py

**Status**: ✓ Done
**File affected**: src/__init__.py
**What was done**: Created package init with re-exports of all public functions from all src modules
**Output**: src/__init__.py (7 lines)
**Notes**: Single import point for notebook and main.py consumers

---

## [2026-06-13 11:05] FILE_CREATE: src/utils.py

**Status**: ✓ Done
**File affected**: src/utils.py
**What was done**: Created utility module with load_image(), save_image(), show_images()
**Output**: src/utils.py (52 lines)
**Notes**: Used os.makedirs(exist_ok=True) to auto-create output directories in save_image()

---

## [2026-06-13 11:05] FILE_CREATE: src/point_ops.py

**Status**: ✓ Done
**File affected**: src/point_ops.py
**What was done**: Created Part I module: adjust_brightness(), adjust_contrast(), negative_transform(), binary_threshold()
**Output**: src/point_ops.py (57 lines)
**Notes**: Uses np.float32 intermediate for brightness/contrast to avoid overflow; binary_threshold converts to grayscale internally

---

## [2026-06-13 11:05] FILE_CREATE: src/linear_filters.py

**Status**: ✓ Done
**File affected**: src/linear_filters.py
**What was done**: Created Part II module: median_filter(), gaussian_filter(), sharpen_filter()
**Output**: src/linear_filters.py (52 lines)
**Notes**: median_filter validates odd kernel_size with ValueError; sharpen uses custom 3x3 kernel via cv2.filter2D

---

## [2026-06-13 11:05] FILE_CREATE: src/advanced_filters.py

**Status**: ✓ Done
**File affected**: src/advanced_filters.py
**What was done**: Created Part III module: sobel_edge_detection(), emboss_filter(), compare_filters(), bilateral_filter()
**Output**: src/advanced_filters.py (107 lines)
**Notes**: sobel uses CV_32F for gradient computation; compare_filters renders matplotlib figure to numpy array

---

## [2026-06-13 11:06] FILE_CREATE: main.py

**Status**: ✓ Done
**File affected**: main.py
**What was done**: Created CLI entry point that runs all 3 parts sequentially with [OK]/[FAIL] status
**Output**: main.py (100 lines)
**Notes**: try/except wraps each operation; prints progress for every save

---

## [2026-06-13 11:06] FILE_CREATE: lab2_notebook.ipynb

**Status**: ✓ Done
**File affected**: lab2_notebook.ipynb
**What was done**: Created clean notebook that imports from src/ and calls functions with markdown explanations
**Output**: lab2_notebook.ipynb
**Notes**: Zero logic duplication - all code lives in src/, notebook only imports and displays

---

## [2026-06-13 11:06] FILE_CREATE: README.md

**Status**: ✓ Done
**File affected**: README.md
**What was done**: Created project README with setup instructions, structure diagram, and function reference
**Output**: README.md (51 lines)
**Notes**: Includes one-liner per public function signature

---

## [2026-06-13 11:06] FILE_CREATE: input/anh.jpg

**Status**: ✓ Done
**File affected**: input/anh.jpg
**What was done**: Copied test image from project root to input/ directory
**Output**: input/anh.jpg
**Notes**: Original anh.jpg preserved at project root

---

## [2026-06-13 11:07] FILE_MODIFY: src/utils.py - handle grayscale in save_image()

**Status**: ✓ Done
**File affected**: src/utils.py
**What was done**: Added len(img.shape) == 2 check in save_image() to handle single-channel images (sobel, threshold)
**Output**: src/utils.py (line 30-33 updated)
**Notes**: Grayscale images saved directly without BGR conversion; prevents cv2.cvtColor error on 1-channel arrays

---

## [2026-06-13 11:07] RUN_CODE: python main.py (first run)

**Status**: ✗ Failed (partial)
**File affected**: main.py, src/advanced_filters.py
**What was done**: Ran main.py end-to-end; 13/14 operations passed, compare_filters failed
**Output**: N/A
**Notes**: compare_filters failed: 'FigureCanvasTkAgg' object has no attribute 'tostring_rgb' - matplotlib 3.10 removed tostring_rgb()

---

## [2026-06-13 11:08] FIX_BUG: compare_filters deprecated tostring_rgb()

**Status**: ✓ Done
**File affected**: src/advanced_filters.py
**What was done**: Replaced fig.canvas.tostring_rgb() with fig.canvas.buffer_rgba() for matplotlib 3.10 compatibility
**Output**: src/advanced_filters.py (line 86-89 updated)
**Notes**: buffer_rgba() returns RGBA memoryview; extracted [:,:,:3] to get RGB channels

---

## [2026-06-13 11:08] RUN_CODE: python main.py (second run)

**Status**: ✓ Done
**File affected**: N/A
**What was done**: Re-ran main.py after fixing compare_filters; all 14/14 operations passed
**Output**: 14 output images across part1_point_ops/, part2_linear_filters/, part3_advanced/
**Notes**: All file sizes verified: range 166KB (bilateral) to 486KB (emboss)

---

## [2026-06-13 11:09] FILE_MODIFY: src/advanced_filters.py - cleanup unused variables

**Status**: ✓ Done
**File affected**: src/advanced_filters.py
**What was done**: Removed unused w, h variables from compare_filters() after buffer_rgba() refactor
**Output**: src/advanced_filters.py (line 85-87 simplified)
**Notes**: buffer_rgba() natively returns (h, w, 4) shaped memoryview, no need for manual reshape

---

## [2026-06-13 11:09] RUN_CODE: python main.py (final verification)

**Status**: ✓ Done
**File affected**: N/A
**What was done**: Final clean run to verify all changes; 14/14 operations passed with zero errors
**Output**: All 14 output files confirmed present and correctly sized
**Notes**: End-to-end confirmed: load → process → save pipeline working for all 3 parts

---

## [2026-06-13 11:10] FILE_CREATE: logs/agent_log.md

**Status**: ✓ Done
**File affected**: logs/agent_log.md
**What was done**: Created this agent action log documenting all refactoring steps
**Output**: logs/agent_log.md
**Notes**: Appended summary block below

---

## [2026-06-13 11:12] FILE_MODIFY: lab2_notebook.ipynb - full rebuild

**Status**: ✓ Done
**File affected**: lab2_notebook.ipynb
**What was done**: Rebuilt notebook as presentation-ready: 12 code cells, 16 markdown cells, bilingual headers, 11 feature demos with before/after visuals, Vietnamese descriptions, summary table
**Output**: lab2_notebook.ipynb (27 cells total)
**Notes**: All 11 features displayed with plt.show(); save_image() + print("[OK]") after every cell; brightness/contrast show 3-image comparison (dark/orig/bright); grayscale outputs use cmap="gray"

---

## [2026-06-13 11:15] RUN_CODE: jupyter nbconvert --execute verification

**Status**: ✓ Done
**File affected**: lab2_notebook.ipynb
**What was done**: Ran `jupyter nbconvert --to notebook --execute lab2_notebook.ipynb --output lab2_notebook_executed.ipynb`
**Output**: lab2_notebook_executed.ipynb (6129183 bytes, 12/12 code cells OK, 0 errors)
**Notes**: All 12 code cells executed successfully; cleaned up generated 6MB output file after verification

---

## [2026-06-13 11:15] FILE_DELETE: _check_nb.py, lab2_notebook_executed.ipynb

**Status**: ✓ Done
**File affected**: _check_nb.py, lab2_notebook_executed.ipynb
**What was done**: Removed temporary verification script and 6MB executed notebook artifact
**Output**: N/A
**Notes**: Standard cleanup after verification

---

## ===================== PHASE 2: PER-PART SPLIT =====================

---

## [2026-06-13 11:19] PART I - FILE_CREATE: src/part1_point_ops.md

**Status**: ✓ Done
**File affected**: src/part1_point_ops.md
**What was done**: Created detailed markdown report for Part I (Point Operations) split from lab2_report.md. Covers brightness, contrast, negative, binary threshold. Includes formulas, implementation code, test table.
**Output**: src/part1_point_ops.md (75 lines)
**Notes**: Structure follows User Requirement → Features → Tech → Logic → Implement → Test

---

## [2026-06-13 11:19] PART I - FILE_CREATE: src/part1_point_ops.ipynb

**Status**: ✓ Done
**File affected**: src/part1_point_ops.ipynb
**What was done**: Created self-contained notebook for Part I: Point Operations. 6 code cells + markdown. Inline code extracted from original lab2.ipynb (brightness ±50, contrast ×0.5/×1.5, negative, binary threshold=128). 3-image comparison layout for brightness & contrast.
**Output**: src/part1_point_ops.ipynb (13 cells: 6 code + 7 markdown)
**Notes**: `../input/anh.jpg` path; saves to `output/part1_point_ops/`; summary table with 6 output files

---

## [2026-06-13 11:19] PART II - FILE_CREATE: src/part2_linear_filters.md

**Status**: ✓ Done
**File affected**: src/part2_linear_filters.md
**What was done**: Created detailed markdown report for Part II (Linear Filtering). Covers median filter, Gaussian blur, sharpen filter. Includes convolution formula, kernel matrices, implementation code, test table.
**Output**: src/part2_linear_filters.md (55 lines)
**Notes**: Includes math formulas for convolution and Gaussian distribution

---

## [2026-06-13 11:19] PART II - FILE_CREATE: src/part2_linear_filters.ipynb

**Status**: ✓ Done
**File affected**: src/part2_linear_filters.ipynb
**What was done**: Created self-contained notebook for Part II: Linear Filtering. 5 code cells + markdown. median(kernel_size=3, odd validation), gaussian(kernel_size=5, sigma=0), sharpen(custom 3x3 kernel via cv2.filter2D).
**Output**: src/part2_linear_filters.ipynb (10 cells: 5 code + 5 markdown)
**Notes**: `../input/anh.jpg` path; saves to `output/part2_linear_filters/`; summary table with 3 output files

---

## [2026-06-13 11:20] PART III - FILE_CREATE: src/part3_advanced.md

**Status**: ✓ Done
**File affected**: src/part3_advanced.md
**What was done**: Created detailed markdown report for Part III (Advanced Filters). Covers Sobel edge detection, Emboss custom kernel, filter comparison, bilateral filter. Includes gradient magnitude formula, bilateral dual-weight explanation.
**Output**: src/part3_advanced.md (80 lines)
**Notes**: Explains why bilateral is edge-preserving (spatial + intensity distance weighting)

---

## [2026-06-13 11:20] PART III - FILE_CREATE: src/part3_advanced.ipynb

**Status**: ✓ Done
**File affected**: src/part3_advanced.ipynb
**What was done**: Created self-contained notebook for Part III: Advanced. 6 code cells + markdown. Sobel X/Y gradient magnitude (CV_32F), Emboss custom kernel, filter comparison grid (4-in-1 with figsize=16x4), median(k=5) + bilateral(d=9, σ=75).
**Output**: src/part3_advanced.ipynb (14 cells: 6 code + 8 markdown)
**Notes**: `../input/anh.jpg` path; saves to `output/part3_advanced/`; comparison uses fig.savefig(); summary table with 5 output files

---

## ===================== CLEANUP & VERIFY =====================

---

## [2026-06-13 11:21] FILE_DELETE: src/*.py and main.py

**Status**: ✓ Done
**File affected**: src/__init__.py, src/utils.py, src/point_ops.py, src/linear_filters.py, src/advanced_filters.py, main.py
**What was done**: Removed all 6 Python source files - switched from modular .py approach to notebook-only approach
**Output**: N/A
**Notes**: User requested "không dùng file py"; notebooks now self-contained with inline code

---

## [2026-06-13 11:21] FILE_MODIFY: lab2_report.md → index

**Status**: ✓ Done
**File affected**: lab2_report.md
**What was done**: Converted from full report to index page with table linking to 3 per-part .md and .ipynb files
**Output**: lab2_report.md (45 lines)
**Notes**: Links: part1_point_ops.md/.ipynb, part2_linear_filters.md/.ipynb, part3_advanced.md/.ipynb

---

## [2026-06-13 11:21] FILE_MODIFY: README.md → updated

**Status**: ✓ Done
**File affected**: README.md
**What was done**: Updated setup, usage, and structure sections to reflect notebook-only approach. Added notebook reference table.
**Output**: README.md (38 lines)
**Notes**: No more `main.py` or `pip install` instructions; just open notebook and Run All

---

## [2026-06-13 11:22] FIX_BUG: image path `input/anh.jpg` → `../input/anh.jpg`

**Status**: ✓ Done
**File affected**: src/part1_point_ops.ipynb, src/part2_linear_filters.ipynb, src/part3_advanced.ipynb
**What was done**: Changed relative image path in all 3 notebooks. Jupyter resolves paths relative to the notebook directory (src/), so `../input/anh.jpg` is needed to reach project root.
**Output**: 3 paths fixed (replaceAll each file)
**Notes**: First nbconvert run failed with TypeError: 'NoneType' object is not subscriptable (cv2.imread returned None)

---

## [2026-06-13 11:23] RUN_CODE: PART I - nbconvert --execute

**Status**: ✓ Done
**File affected**: src/part1_point_ops.ipynb
**What was done**: Executed Part I notebook via `jupyter nbconvert --to notebook --execute`
**Output**: 6/6 code cells OK, 0 errors, 2.3MB output with embedded images
**Notes**: Saved 6 images to output/part1_point_ops/; all plt.show() rendered correctly

---

## [2026-06-13 11:24] RUN_CODE: PART II - nbconvert --execute

**Status**: ✓ Done
**File affected**: src/part2_linear_filters.ipynb
**What was done**: Executed Part II notebook via `jupyter nbconvert --to notebook --execute`
**Output**: 5/5 code cells OK, 0 errors, 1.8MB output with embedded images
**Notes**: Saved 3 images to output/part2_linear_filters/; median odd-kernel validation passed

---

## [2026-06-13 11:24] RUN_CODE: PART III - nbconvert --execute

**Status**: ✓ Done
**File affected**: src/part3_advanced.ipynb
**What was done**: Executed Part III notebook via `jupyter nbconvert --to notebook --execute`
**Output**: 6/6 code cells OK, 0 errors, 3.1MB output with embedded images
**Notes**: Saved 5 images to output/part3_advanced/; fig.savefig() for comparison grid worked correctly

---

## [2026-06-13 11:23] FILE_DELETE: temp artifacts (__pycache__, _p*.ipynb, _check_nb.py)

**Status**: ✓ Done
**File affected**: src/__pycache__/, src/_p2.ipynb, src/_p3.ipynb, _check_nb.py
**What was done**: Cleaned up Python cache directory and temporary executed notebook artifacts from verification runs
**Output**: N/A
**Notes**: src/__pycache__/ was leftover from removed .py files; _p*.ipynb from failed nbconvert runs

---

## SUMMARY

| Category       | Count |
|----------------|-------|
| Files created  | 16    |
| Files modified | 10    |
| Files deleted  | 12    |
| Bugs fixed     | 2     |
| Tests run      | 5     |
| Tests passed   | 4     |

**Total actions**: 30
**Start time**: 2026-06-13 11:05
**End time**: 2026-06-13 11:24
**Final status**: ✓ Complete
