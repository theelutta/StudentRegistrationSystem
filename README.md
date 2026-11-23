# Student Registration System

A Java Swing desktop application for the two supplied Advanced Object-Oriented
Programming briefs:

1. the event-driven student information interface; and
2. the integrated JDBC/MySQL student registration system.

The application supports registration, table display, search by admission
number, update, delete with confirmation, clear, and confirmed exit. It uses
all five event mechanisms requested in the briefs.

## Project structure

```text
StudentRegistrationSystem/
├── database/student_registration.sql
├── lib/                          # MySQL Connector/J runtime JAR
├── scripts/build.ps1             # Compile production sources
├── scripts/download-connector.ps1
├── scripts/run.ps1               # Build and launch the Swing application
├── scripts/test.ps1              # Dependency-free regression test
├── src/com/studentregistration/
│   ├── Main.java
│   ├── database/                 # JDBC connection and repository
│   ├── gui/                      # Swing form and JTable model
│   ├── interfaces/               # Searchable contract
│   ├── model/                    # Person, Student, Course, Gender
│   └── service/                  # Validation and business operations
├── test/StudentDomainTest.java
├── REPORT.md
└── TESTING.md
```

## Requirements

- JDK 21 or another JDK that supports the Java language features used here.
- MySQL or MariaDB running on `localhost:3306`.
- MySQL Connector/J 9.1.x in `lib/mysql-connector-j.jar`.
- PowerShell on Windows, or equivalent commands using `javac` and `java`.

XAMPP is suitable for the local MySQL server. The computer used to prepare
this project contains `C:\xampp\mysql\bin\mysql.exe`, but the server still
has to be started manually from the XAMPP Control Panel.

## Database setup

1. Start MySQL in XAMPP (or start your own MySQL/MariaDB service).
2. Import [`database/student_registration.sql`](./database/student_registration.sql).
   With the default XAMPP root account, PowerShell can use:

   ```powershell
   Get-Content .\database\student_registration.sql |
       & 'C:\xampp\mysql\bin\mysql.exe' -u root
   ```

   If your database account has a password, add `-p` and enter it when
   prompted.
3. Download Connector/J into the project:

   ```powershell
   powershell -ExecutionPolicy Bypass -File .\scripts\download-connector.ps1
   ```

   If network policy blocks the helper, download the MySQL Connector/J JAR
   separately and save it as `lib\mysql-connector-j.jar`.

## Database configuration

The password is never stored in the Java source. `DatabaseConnection` reads
these settings from JVM system properties first and environment variables
second:

- `STUDENT_DB_URL` (default: local `student_registration` database)
- `STUDENT_DB_USER` (default: `root`)
- `STUDENT_DB_PASSWORD` (default: empty, matching a typical XAMPP local setup)

For a PowerShell session with a password-protected database:

```powershell
$env:STUDENT_DB_USER = 'root'
$env:STUDENT_DB_PASSWORD = 'your-local-password'
```

## Compile, test, and run

From this project folder:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\build.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\test.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\test-database.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\test-gui.ps1
powershell -ExecutionPolicy Bypass -File .\scripts\run.ps1
```

The run script checks that the Connector/J JAR exists, compiles all production
classes, and launches `com.studentregistration.Main`.

## How to use the application

- Enter a full name, admission number, course, and gender, then choose
  **Register**.
- Enter an admission number and choose **Search**, or press Enter in the
  admission field.
- Click a JTable row to load it into the form, then choose **Update** or
  **Delete**.
- **Clear** resets the form and selection.
- **Exit** and the window close button both ask for confirmation.

The feedback area at the bottom reports database status and operation results.

## Learning guide

The purpose of each major class, the OOP concepts, and the event handling
locations are explained in [`REPORT.md`](./REPORT.md). The tested and
manually-testable behaviours are recorded in [`TESTING.md`](./TESTING.md).

The [`screenshots/`](./screenshots/) folder contains genuine captures produced
by the live Swing acceptance test: the empty window, successful registration,
successful update, and duplicate-admission error dialog.
