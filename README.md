# UiPath RPA Bot - Automated Resume Filtering System

A UiPath RPA workflow that automates end-to-end resume screening, extracting candidate details, matching skills against job requirements, and sorting resumes into shortlisted and rejected folders with a supporting Excel report.

---

## Problem Statement

Manually screening large volumes of resumes is repetitive, time-consuming, and error-prone. This bot automates the entire process, from reading each PDF resume, extracting contact details and skills, making a shortlisting decision, and filing the resume accordingly without any human intervention.

---

## Dataset

- **Format:** PDF resumes
- **Size:** 97 resumes
- **Input folder:** `Resume_CVs/`
- **Output folders:** `Shortlisted_CVs/` and `Rejected_CVs/`

---

## How It Works

The workflow processes each resume in the input folder through the following steps:

1. **Read PDF** - Extracts raw text from each resume using UiPath's `Read PDF Text` activity

2. **Extract Candidate Name** - Derived from the PDF filename using `Path.GetFileNameWithoutExtension`

3. **Extract Email** - Regex pattern matching to find email addresses; defaults to `N/A` if not found

4. **Extract Phone Number** - Regex pattern matching for Indian mobile numbers (`[6-9]\d{9}`); defaults to `N/A` if not found

5. **Skill Matching** - Checks resume text for each required skill (case-insensitive):
   - Required skills: `Python`, `Java`, `SQL`, `Git`, `DSA`
   - Counts matched skills and records which ones were found

6. **Shortlisting Decision**
   - Match count ≥ 3 -> **Shortlisted**
   - Match count < 3 -> **Rejected**

7. **Excel Output** - Adds a row to either `ShortListed.xlsx` or `Rejected.xlsx` with:
   - Candidate Name
   - Email
   - Phone Number
   - Matched Skills
   - Match Count

8. **File Organisation** - Moves each PDF into the appropriate output folder (`Shortlisted_CVs` or `Rejected_CVs`)

---

## Workflow Structure

```
Sequence (Main)
│
├── Assign resume folder path
├── Get all PDF files from folder
├── Build DataTable schema (Name, Email, Phone, Skills, MatchCount)
├── Initialize shortlistedDT and rejectedDT
│
└── For Each resume file:
    ├── Read PDF Text
    ├── Extract candidate name from filename
    ├── Regex match -> Email
    ├── Regex match -> Phone number
    ├── For Each required skill:
    │   └── Check if skill exists in resume text → increment matchCount
    ├── If matchCount >= 3 -> Add to ShortlistedDT -> Write to ShortListed.xlsx
    │   Else -> Add to RejectedDT → Write to Rejected.xlsx
    └── Move PDF to Shortlisted_CVs or Rejected_CVs folder
```

---

## Tools & Technologies

- UiPath Studio
- UiPath Activities: `Read PDF Text`, `For Each`, `Matches`, `Add Data Row`, `Write Range`, `Move File`
- Excel Automation (UiPath Excel Activities)
- Regex (System.Text.RegularExpressions)
- VB.NET expressions

---

## How to Run

1. Clone or download this repository
2. Open `Sequence.xaml` in UiPath Studio
3. Update the folder paths in the workflow variables to match your local machine:
   - `resumeFolder` - path to your input PDF resumes
   - `ShortListed_CVs` — output folder for shortlisted resumes
   - `Rejected_CVs` — output folder for rejected resumes
   - `WorkbookPath` in both Write Range activities — path for Excel output files
4. Place PDF resumes in the `Resume_CVs` folder
5. Run the workflow

---

## Project Structure

```
uipath-resume-filter/
│
├── Sequence.xaml          # Main UiPath workflow
└── README.md
```

---

## Key Takeaway

The bot demonstrates how RPA can eliminate repetitive manual work in HR screening. Regex-based extraction handles unstructured PDF text reliably, while the skill-matching threshold logic (≥3 of 5 required skills) provides a configurable and transparent shortlisting criterion — easily adaptable to different job roles by updating the `requiredSkills` array.
