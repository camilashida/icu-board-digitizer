# icu-board-digitizer
ICU Board Digitizer: High-precision pipeline to automate medical whiteboard data extraction. Uses OpenCV for perspective warping and GPT-4o Multimodal for intelligent OCR across 14 beds. Features SSIM-based Change Detection to optimize API costs and fuzzy logic to cross-reference patient databases into structured Excel reports.

Key Features:
- Perspective Correction (Warping): Automatically flattens the whiteboard image for better readability.
- Smart Change Detection (SSIM): Only processes images when the content actually changes, saving API costs.
- AI-Powered Matching: Uses GPT-4o to correct handwriting typos by comparing them against a patient CSV.
- Excel Export: Generates a structured report organized by bed number (1 to 14).

Requirements:
Before running the script, ensure you have:
- Python 3.10 or higher installed.
- An OpenAI API Key (with credits available).
- The following Python libraries:
  Bash
  pip install opencv-python pandas scikit-image openai openpyxl

Setup & Usage (Step-by-Step):
1. Prepare your Files
- Patient Database: Create or update a file named "data/reference_list.csv". It must contain the columns: "Data" (date), "Primeiro Nome" (first name), and "Identificação do paciente" (pacient id).
- Images: Place the whiteboard photos (JPG or PNG) in the folder defined in the code (default: data/input_images).
2. Configure the Script:
Open the .py file and update these lines:
- client = OpenAI(api_key="YOUR_KEY_HERE") -> Insert your OpenAI key.
**- PASTA_IMAGENS -> Update to the path where your photos are stored.
**- ARQ_PACIENTES -> Update to the path of your CSV file.
3. Run the Program
Open your terminal or command prompt and run:
Bash
python seu_arquivo.py

Output Data:
After execution, the system will create:
Excel File (quadros_processados_github2.xlsx): A table with all beds filled.
Folder (alteradas_github2): Contains copies of the images that were actually processed (where changes were detected).
