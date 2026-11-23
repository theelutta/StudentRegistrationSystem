# Testing Evidence

Test date: 2026-11-24  
Runtime: OpenJDK 21.0.12.1 LTS on Windows  
Project: `StudentRegistrationSystem`

## Automated checks executed

| Check | Result | Evidence |
| --- | --- | --- |
| Compile all production Java files | PASS | `scripts/build.ps1` compiled 13 source files. |
| Domain inheritance and overriding | PASS | `StudentDomainTest: PASS`. |
| JTable adapter values and row access | PASS | Included in `StudentDomainTest`. |
| JDBC CRUD and duplicate validation | Run `scripts/test-database.ps1` after MySQL setup | The integration test creates and removes a temporary record. |
| Swing event and interaction acceptance test | Run `scripts/test-gui.ps1` on a graphical desktop | AWT Robot drives the real window and captures actual states. |

Run these checks again from the project folder with:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\build.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\test.ps1
```

## Manual acceptance-test record

The following checks were executed on 2026-11-24 using XAMPP MariaDB 10.4.28,
MySQL Connector/J 9.1.0, and a graphical Windows desktop.

| ID | Scenario | Expected result | Result/date |
| --- | --- | --- | --- |
| DB-01 | Import `database/student_registration.sql` and start MySQL | Database and `students` table are created | PASS — imported and verified with `SHOW TABLES` |
| DB-02 | Launch the app with Connector/J configured | Swing window opens and reports database status | PASS — live window launched |
| CRUD-01 | Register a valid student | Success dialog appears and JTable gains a row | PASS — AWT Robot test |
| CRUD-02 | Register the same admission number twice | Duplicate-number error; second row is not inserted | PASS — JDBC test and real dialog screenshot |
| CRUD-03 | Search by admission number | Matching row loads into the form | PASS — Enter-to-search AWT Robot test |
| CRUD-04 | Edit a selected row and click Update | JTable shows the changed values | PASS — AWT Robot test and screenshot |
| CRUD-05 | Select a row, click Delete, confirm | Row is removed after confirmation | PASS — AWT Robot test |
| GUI-01 | Choose Clear | Form fields, selections, and table selection reset | PASS — explicit Clear assertion |
| GUI-02 | Change course and gender | Feedback area reflects the selected values | PASS — explicit ItemEvent assertion |
| GUI-03 | Type admission input / press Enter | Input feedback updates; Enter performs search | PASS — explicit KeyEvent assertions |
| GUI-04 | Click a JTable row | Row values populate the form | PASS — explicit MouseEvent assertion |
| GUI-05 | Click Exit or the window close button | Confirmation appears; dismissing it keeps app open | PASS — explicit WindowEvent assertion |

## Evidence capture

Real captures are saved in [`screenshots/`](./screenshots/):
`app-empty.png`, `gui-registered.png`, `gui-updated.png`, and
`gui-duplicate.png`.
