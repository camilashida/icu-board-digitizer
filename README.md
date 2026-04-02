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

Module 2: AI-Powered Data Extraction
data_extractor.py


This is the core of the system. It takes the images flagged by Module 1, corrects their perspective, and sends them to OpenAI's GPT-4o to extract patient information with high clinical accuracy.

What it does:
- Perspective Normalization: Re-applies the perspective transformation to ensure the AI sees a clean, flat image of the 14 beds.
- Contextual Reference Matching: It searches your reference_database.csv for patients admitted within a +/- 5-day window of the photo's date. This "shortlist" is sent to the AI to prevent transcription errors and ensure the IDs match perfectly.
- Vision-Language Processing: The AI analyzes the handwritten text on the board and compares it against the reference list (Fuzzy Matching).
- Structured Recovery: Parses the AI's natural language response into a clean, bed-by-bed Excel spreadsheet.

How to setup this module:
- OpenAI API Key:
  You must have an API key.
  Security Tip: Do not hardcode your key. Use an environment variable:
  export OPENAI_API_KEY='your_key_here' (Linux/Mac) or setx OPENAI_API_KEY "your_key" (Windows).
- Reference Database (reference_database.csv):
  Ensure your CSV has these exact headers: Date, Name, Patient ID.
  Format for dates should be DD/MM/YYYY.
- Filenames:
  The script expects filenames to start with the date (e.g., 20251011_image.jpg). It uses this to filter the patient database dynamically.

Workflow / How to Use:
- Run Module 1 (change_detector.py) to generate the change_detection_report.xlsx.
- Run Module 2:
  Bash
  python data_extractor.py
- Review the Results: Open extracted_data_results.xlsx. You will find a row for every image that had a detected change, with 14 columns representing each bed in the ICU.


________________________________________________________________________________________________________________________
Project Structure Recap
To keep things running smoothly, organize your folder like this:

Plaintext
├── change_detector.py       # Part 1: Filters changes
├── data_extractor.py         # Part 2: AI Extraction
├── reference_database.csv    # Your patient list
├── input_images/             # Folder with ICU photos
└── outputs/                  # Reports and Excel files

Troubleshooting
- "No images to process": Make sure the change_detection_report.xlsx from Part 1 exists and contains the filenames you want to analyze.
- API Errors: Check if your OpenAI account has credits and if the GPT-4o model is available for your tier.
- Alignment Issues: If the AI is "hallucinating" bed numbers, double-check the SOURCE_POINTS coordinates in both scripts. They must be identical to crop the image correctly.
