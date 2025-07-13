# Troubleshooting Notes: Float to Integer Error in YOLOv8

## 💡 Issue Summary

While annotating and training a custom object detection model using YOLOv8 (Ultralytics), I encountered a runtime error related to data type incompatibility:

> `TypeError: arguments did not match any overloaded call`

After investigation, the root cause was the attempt to use a **float value** where an **integer was required**, specifically in the `drawLine()` and `drawRect()` functions of Qt (used in LabelImg and some visualization tools).

## 🧠 Root Cause

Python's OpenCV or Qt drawing functions (e.g., `p.drawLine(x1, y1, x2, y2)`) require all positional arguments to be of type `int`.  
However, due to previous image zoom/pan operations or annotation scaling, the coordinates were in `float`.

Example error line:
```python
p.drawLine(prev_point_x, prev_point_y, curr_point_x, curr_point_y)

Where :
prev_point_x = float  # Should be int

## ✅ Solution :

I solved the issue by explicitly casting all drawing coordinate values to int(), such as:
p.drawLine(int(prev_point_x), int(prev_point_y), int(curr_point_x), int(curr_point_y))
This fix was applied in the source code file:
labelImg/libs/labelFile.py  # Line ~1026

Lesson Learned :
Minor issues in datatype mismatches can break the UI interaction.
Even non-ML code in annotation tools may require debugging.
Always validate file formats and data types before training.

Tools Used:
Python 3.10
LabelImg (manual install from source)
YOLOv8 (Ultralytics)
