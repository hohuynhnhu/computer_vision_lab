# Lab 2: Xử Lý Ảnh Cơ Bản & Lọc Ảnh

## Setup

```
pip install opencv-python numpy matplotlib jupyter
```

## Usage

1. Place your image at: `input/anh.jpg`
2. Open a notebook from `src/` in Jupyter / VS Code
3. Run **Run All Cells**
4. Results saved to: `output/`

## Structure

```
lab2/
├── input/anh.jpg                  # Test image
├── output/                        # Auto-created at runtime
│   ├── part1_point_ops/           # 6 output images
│   ├── part2_linear_filters/      # 3 output images
│   └── part3_advanced/            # 5 output images
├── src/
│   ├── part1_point_ops.ipynb      # Notebook Part I (self-contained)
│   ├── part1_point_ops.md         # Report Part I
│   ├── part2_linear_filters.ipynb # Notebook Part II (self-contained)
│   ├── part2_linear_filters.md    # Report Part II
│   ├── part3_advanced.ipynb       # Notebook Part III (self-contained)
│   └── part3_advanced.md          # Report Part III
├── lab2.ipynb                     # Original full notebook
├── lab2_report.md                 # Index report
└── README.md
```

## Notebook Reference

| Part | Tác vụ                                    | File                             |
| ---- | ----------------------------------------- | -------------------------------- |
| I    | Brightness, Contrast, Negative, Threshold | `src/part1_point_ops.ipynb`      |
| II   | Median, Gaussian, Sharpen                 | `src/part2_linear_filters.ipynb` |
| III  | Sobel, Emboss, Comparison, Bilateral      | `src/part3_advanced.ipynb`       |
