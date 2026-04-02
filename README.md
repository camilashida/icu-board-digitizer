# icu-board-digitizer
ICU Board Digitizer: High-precision pipeline to automate medical whiteboard data extraction. Uses OpenCV for perspective warping and GPT-4o Multimodal for intelligent OCR across 14 beds. Features SSIM-based Change Detection to optimize API costs and fuzzy logic to cross-reference patient databases into structured Excel reports.

Module 1: Change Detection & Perspective Correction
01_change_detector.py


This script serves as the intelligent filter of the pipeline. Instead of processing every single frame (which would be slow and expensive), this module identifies only the images where the ICU board content has actually changed.

What it does:
- Perspective Rectification (Warping): It takes a distorted photo of the whiteboard and "flattens" it into a perfect top-down rectangle using homography points.
- Sectional Analysis: It divides the board into 14 horizontal sections (one for each bed).
- SSIM Comparison: It uses the Structural Similarity Index (SSIM) to compare the current image against the last "saved" state. If the similarity score falls below 0.72, it flags the image as "changed."
- Reporting: It generates an Excel file listing only the filenames that contain new information.

How to setup this specific script:
To make this work on your computer, you need to adjust the Configuration section at the top of the code:
- INPUT_FOLDER: * Change: INPUT_FOLDER = "your_folder_path_here"
  Why: This is where your raw photos from the ICU are stored.
- SOURCE_POINTS: * Change: These are the (x, y) coordinates of the four corners of your whiteboard in the photo.
  Tip: Use a tool like Paint or an online image inspector to find the pixels for the Top-Left, Top-Right, Bottom-Right, and Bottom-Left corners.
- SIMILARITY_THRESHOLD: * Default is 0.72.
  If the script is too sensitive (saving images with just shadows/glare), increase it to 0.80.
  If it's ignoring real handwriting, decrease it to 0.65.

Execution:
Simply run the script via terminal:
Bash
python change_detector.py

Output: A file named change_detection_report.xlsx will be created with the list of frames ready for the next step (GPT-4o extraction).
