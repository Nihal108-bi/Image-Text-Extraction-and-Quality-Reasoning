
# Image Text Extraction & Quality Reasoning Pipeline

## Overview

This project implements an **end-to-end image analysis pipeline** that combines:

* OCR-based text extraction
* Classical computer vision feature analysis
* Lightweight LLM-based reasoning (Gemini Flash)

to produce a **structured, explainable JSON assessment** of image quality.

The focus of this project is **robust engineering, explainability, and real-world constraints**, not just model size or accuracy.

---

## Key Objectives

* Extract readable text from images using OCR
* Analyze image quality using classical CV techniques
* Reason over structured features using an LLM
* Produce clean, machine-readable JSON output
* Handle API limitations and noisy LLM outputs gracefully

---

## Pipeline Architecture

```
Image
  ↓
Preprocessing
  ↓
OCR Text Extraction
  ↓
Classical CV Feature Extraction
  ↓
LLM-Based Reasoning (Gemini Flash)
  ↓
Structured JSON Output
```

⚠️ The LLM **does not see the image directly**.
It reasons only over **explicit, structured features**, improving interpretability and control.

---
## Repository Structure

├── notebook/
│   └── Extracting_text_from_images.ipynb
├── images/
│   ├── Army_Reserves_Recruitment_Banner_MOD_45156284.jpg
│   ├── sample.jpg
├── outputs/
│   ├── Army_Reserves_Recruitment_Banner_MOD_45156284.json
│   ├── sample.json
├── Technical_Writeup_Image_Quality_Pipeline.pdf
├── README.md


## Features Extracted

### OCR

* Detected text content (used as a supporting signal, not ground truth)

### Computer Vision Features

* **Blur detection** (Laplacian variance)
* **Brightness analysis** (under/over-exposure)
* **Edge density** (image detail indicator)
* **Text presence metadata**

These features are intentionally **simple and interpretable**.

---

## LLM Reasoning

* Model: **Gemini 2.5 Flash**
* Input: Structured JSON features
* Output: Strict JSON following a predefined schema
* Markdown-wrapped outputs are sanitized before parsing

### Output Schema

```json
{
  "image_quality_score": 0.0,
  "issues_detected": [],
  "positive_signals": [],
  "final_verdict": "",
  "confidence": 0.0,
  "llm_reasoning_summary": ""
}
```

---

## Example Output
![OCR sample image 1](Images\Army_Reserves_Recruitment_Banner_MOD_45156284.jpg)

```json
{'image_quality_score': 0.95,
 'issues_detected': [],
 'positive_signals': ['Image is sharp and not blurry (laplacian_variance: 377.83).',
  'Lighting condition is normal (mean_brightness: 106.8).',
  'High edge density (15.2536) suggests good detail.',
  "Multiple lines of text are clearly detected, indicating good legibility and content clarity (e.g., 'RECRUITING', 'THE ROYAL WESSEX YEOMANRY', 'email: rwXY-bsqn-pSao@moduk')."],
 'final_verdict': 'The image demonstrates excellent technical quality with no blur, normal lighting, and good detail. The detected text is clear and suggests the image is a well-captured informational or recruitment poster.',
 'confidence': 0.98,
 'llm_reasoning_summary': "The analysis of structured features indicates a high-quality image. The blur analysis shows the image is not blurry with a high Laplacian variance (377.83), signifying excellent sharpness. Brightness is normal (106.8 mean brightness), and edge density is good (15.2536), suggesting sufficient detail. The extensive and coherent text detected, including organizational names, recruitment keywords, and contact details, implies the content is highly legible and likely relevant to its apparent purpose (e.g., a recruitment poster for 'THE ROYAL WESSEX YEOMANRY'). No issues were detected, and all signals are positive, leading to a strong confidence in the high quality of the image."}
```

![OCR sample image 2](Images\sample.jpg)
```json
{'image_quality_score': 0.6,
 'issues_detected': ['Image is overexposed, potentially obscuring details.'],
 'positive_signals': ['Image is not blurry, indicating good focus.',
  'Text content was detected, suggesting reasonable clarity in those regions.'],
 'final_verdict': 'The image has moderate quality. While it benefits from good focus and detectable text, the significant overexposure is a notable drawback affecting overall visual fidelity.',
 'confidence': 0.9,
 'llm_reasoning_summary': "The blur analysis clearly indicates the image is not blurry (laplacian_variance: 247.78, is_blurry: false), which is a strong positive signal for image sharpness. However, the brightness analysis shows the image is overexposed (mean_brightness: 216.47, lighting_condition: 'overexposed'), which is a significant issue as it can lead to washed-out areas and loss of detail. Text was detected, implying that at least some content is clear enough to be read. The edge density is noted but without context for what constitutes a 'good' or 'bad' value, it is not used to determine quality score or signals. The overall score reflects a balance between the positive aspect of sharpness and the negative impact of overexposure."}
```
---


## Design Decisions

* **LLM used only for reasoning**, not vision
* **Classical CV preferred** for deterministic quality signals
* **Free-tier compatible** Gemini model selected
* **Fail-fast JSON parsing** for clean outputs
* **No UI / no async** — notebook focused on core logic

---

## Limitations

* OCR accuracy depends heavily on image quality and fonts
* Gemini Flash has strict free-tier rate limits
* Brightness and edge thresholds are heuristic-based
* No multimodal vision-language model is used

---

## Future Improvements

* Integrate a vision-language model for richer reasoning
* Improve OCR preprocessing for noisy images
* Add confidence calibration across multiple samples
* Package as an API or Streamlit demo (if required)

---

## Tech Stack

* Python
* OpenCV
* Tesseract OCR
* NumPy
* Google Gemini API (Flash)

---

## How to Run

1. Open the notebook
2. Set your `GEMINI_API_KEY` in environment / Colab secrets
3. Run cells top-to-bottom
4. Inspect the final JSON output

---

## Author Notes

This project prioritizes **clarity, robustness, and explainability** over raw model complexity.
It is designed to reflect **real-world ML system design**, not just experimentation.

