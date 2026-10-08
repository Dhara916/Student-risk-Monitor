# Student-risk-Monitor
Browser-based student risk dashboard for attendance and marks CSVs, explainable risk flags, recovery estimates and staff email drafts.

Student Risk Monitor is a lightweight prototype for helping academic staff identify students who may need follow-up. Upload attendance and assessment records, review the reasons behind each flag, filter the risk list, and prepare individual alert drafts for the student, subject teacher, and faculty adviser.

The app runs as a static page in a browser. It does not require a database, build step, or package installation.

## Contents

```text
student-risk-monitor-github/
├── README.md
├── .gitignore
├── student-risk-dashboard.html
└── sample-data/
    ├── roster-attendance.csv
    ├── marks.csv
    └── teacher-timetable.csv
```

## Features

- Attendance risk against a configurable required percentage, with a near-threshold buffer.
- Estimate of how many consecutive classes a student needs to attend to recover to the requirement.
- Marks risk signals for scores below the minimum, a downward trend, and a sudden drop.
- A reason and recent assessment evidence for each student flag.
- Student search and filters for department, subject, and risk level.
- Department-level risk summary and priority-sorted student list.
- Teacher availability and appointment request tracking from a timetable CSV.
- Activity log and CSV export of the current student risk review.
- Personalized email drafts for students, subject teachers, and faculty advisers.

## Requirements

- Python 3 (for the local static web server), or another static HTTP server.
- A modern desktop or mobile browser.

No Python packages or JavaScript packages are required.

## Run locally

Open a terminal in this project directory and run:

```powershell
python -m http.server 8000
```

Then open <http://localhost:8000/student-risk-dashboard.html> in a browser. Stop the server with `Ctrl+C`.

## Try the sample data

On the Overview page:

1. Select **Upload data** and upload `sample-data/attendance.csv`.
2. Select **Upload data** again and upload `sample-data/marks.csv`.
3. Select **Send alerts** to calculate risk and review email drafts for students with flags.

To view appointment availability, open the Appointments page and upload `sample-data/teacher-timetable.csv` using **Upload timetable**.

The sample data contains 20 fictional students and uses `example.edu` addresses. It is intended for demonstration only.

## CSV formats

Use UTF-8 CSV files with a header row. Column names below are the expected names.

### Roster and attendance

The roster file must include:

| Column | Meaning |
| --- | --- |
| `student_id` | Unique student identifier; used to match marks rows |
| `student_name` | Student's display name |
| `student_email` | Student email address |
| `department` | Department used in dashboard filters and summaries |
| `adviser_name`, `adviser_email` | Faculty adviser's name and email |
| `subject` | Subject associated with the uploaded attendance record |
| `teacher_name`, `teacher_email` | Subject teacher and the email used to match timetable slots |
| `classes_held` | Total classes held for this subject |
| `classes_attended` | Classes attended by the student |

Each roster row represents a student's attendance in a subject. To track several subjects per student, provide one row per student and subject. The roster upload replaces the current roster in the browser.

### Marks

Required columns:

| Column | Meaning |
| --- | --- |
| `student_id` | Must match a roster `student_id` |
| `subject` | Subject for the assessment |
| `assessment` | Assessment or test name |
| `score` | Student's score |
| `max_score` | Maximum possible score |

An optional `date` column may be included in an uploaded marks file, but it is not required for risk calculations. The risk review export intentionally omits assessment date columns and includes the three most recent assessment names and scores.

### Teacher timetable

Required columns:

| Column | Meaning |
| --- | --- |
| `teacher_email` | Must match the roster's `teacher_email` |
| `teacher_name` | Teacher display name |
| `day` | Day of the week |
| `start_time`, `end_time` | Slot start and end, such as `14:00` and `14:30` |
| `status` | Use `available` or `free` for bookable slots |

## Risk rules

The institution can adjust these settings on the Settings page:

- Required attendance percentage (default: 85%).
- Near-threshold buffer in percentage points (default: 5).
- Minimum mark (default: 50%).
- Sudden-drop threshold (default: 15 percentage points).

Attendance percentage is `classes_attended / classes_held`. The recovery estimate assumes the student attends each next class; it calculates the number of consecutive attended classes required to reach the configured percentage.

Assessment scores are normalized to percentages using `score / max_score`. A low-mark flag is raised when a score falls below the configured minimum. A downward-trend signal uses the ordered assessment scores and identifies a latest result lower than both the first and previous result. A sudden-drop signal compares the latest result with the highest earlier result using the configured drop threshold. The dashboard shows the assessment values behind these signals for staff review.

## Alerts and integrations

**Send alerts** recalculates risk from the data currently loaded in the browser and filters out students without active risk signals. For each flagged student, the app prepares separate drafts addressed to:

- The student, with the student's concern and attendance recovery estimate when relevant.
- The subject teacher, with the subject-specific reason.
- The faculty adviser, with an overview of the student's active concern.

Each draft opens in the user's default email application. The user must review and send each email. The prototype does not send email automatically, confirm delivery, or prevent duplicate messages across separate runs.

Automated phone calls, weekly scheduled summaries, real calendar synchronization, and production appointment booking are not connected. They require a secure backend and institution-approved email, calling, calendar, and scheduling services.

## Data storage and privacy

This is a browser-only prototype. Uploaded records, settings, and appointment requests are stored in browser local storage on the device. The app does not send uploaded files to a server. Clearing the browser's local site data removes the locally stored records.

Do not commit real student, faculty, or staff information to a public repository. Use fictional data for public demos and handle institutional records only in an approved private environment.

## License

No license is included. Add a license before redistributing or reusing the project if needed.
