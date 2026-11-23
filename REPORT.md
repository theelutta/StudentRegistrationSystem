# Technical Report: Student Registration System

## 1. Purpose and brief coverage

This project combines the two supplied assignment briefs. The first brief
requires an event-driven Java Swing student information interface. The second
brief extends that interface into a JDBC/MySQL registration system with CRUD
operations and object-oriented design. The implementation keeps the user
interface, business rules, and database access in separate packages.

## 2. Architecture

```text
Main
  └── StudentRegistrationForm (Swing view + event handlers)
        └── StudentService (validation and application rules)
              └── StudentRepository (PreparedStatement CRUD)
                    └── DatabaseConnection (JDBC configuration)
                          └── MySQL student_registration.students
```

The GUI never writes SQL. It creates a `Student`, calls the service, and then
refreshes the `StudentTableModel` from the service. This makes the code easier
to explain and test during assessment.

## 3. Major classes

| Class | Responsibility |
| --- | --- |
| `Person` | Abstract superclass holding common person data and behaviour. |
| `Student` | Concrete `Person` subclass containing admission number, course, and gender. |
| `Course`, `Gender` | Enumerations used by the Swing selection controls. |
| `Searchable<T>` | Interface defining admission-number search behaviour. |
| `DatabaseConnection` | Reads JDBC settings and creates MySQL connections. |
| `StudentRepository` | Performs parameterized Create, Read, Update, and Delete operations. |
| `StudentService` | Validates input and coordinates repository operations. |
| `StudentRegistrationForm` | Builds the Swing interface and responds to events. |
| `StudentTableModel` | Adapts `Student` objects for `JTable`. |
| `Main` | Creates the object graph and starts Swing on the event-dispatch thread. |

## 4. OOP concepts demonstrated

- **Classes and objects:** Every domain, service, repository, and GUI component
  is represented by a class. `Main` creates the application objects.
- **Constructors:** `Person`, `Student`, `DatabaseConnection`,
  `StudentRepository`, `StudentService`, and the Swing form use constructors to
  establish valid object state and dependencies.
- **Encapsulation:** Student fields are private and exposed through getters.
  The repository hides SQL details, while the service hides validation rules.
- **Inheritance:** `Student extends Person`; the form also extends `JFrame`.
- **Method overriding:** `Student` overrides `getPersonType()` and `toString()`;
  the form overrides listener methods such as `actionPerformed` and
  `windowClosing`.
- **Interfaces:** `StudentService implements Searchable<Student>` and the form
  implements the Swing listener interfaces used by the assignment.
- **Abstraction:** `Person` is abstract and requires subclasses to describe
  their person type. `Searchable` abstracts the search capability from its
  implementation.

## 5. Event handling evidence

| Event mechanism | Working feature | Implementation location |
| --- | --- | --- |
| `ActionEvent` | Register, Clear, Search, Update, Delete, Exit buttons | `StudentRegistrationForm.actionPerformed` |
| `ItemEvent` | Course combo selection and gender radio selection update feedback | `StudentRegistrationForm.itemStateChanged` |
| `KeyEvent` | Admission number character filtering, live character-count hint, Enter-to-search | `keyTyped`, `keyReleased`, `keyPressed` |
| `WindowEvent` | Window close confirmation before `System.exit(0)` | anonymous `WindowAdapter.windowClosing` |
| `MouseEvent` | Clicking a table row loads the selected student into the form | `StudentRegistrationForm.mouseClicked` |

These handlers are registered on actual Swing components; the event classes
are not merely imported.

## 6. Database design and safety

The SQL script creates a `student_registration` database and a `students`
table. `admission_number` has a database-level unique key in addition to the
service/repository duplicate check. This protects the data even if two clients
attempt to register the same number at nearly the same time.

All CRUD statements use `PreparedStatement`. Connections, statements, and
result sets use try-with-resources. The password is read from
`STUDENT_DB_PASSWORD` or a JVM property and is not committed to source code.

## 7. Validation and user feedback

The service rejects short names, names longer than 120 characters, malformed
admission numbers, missing courses, and missing gender selections. The GUI
converts validation, duplicate-number, SQL, and confirmation outcomes into
feedback text and dialogs.

## 8. Testing evidence

The execution results are maintained in [`TESTING.md`](./TESTING.md). The
dependency-free test verifies Student inheritance/overriding and the JTable
adapter. The JDBC integration test and the AWT Robot Swing acceptance test were
also run successfully on the local XAMPP MariaDB and graphical desktop.

## 9. Significant design choices

- A plain JDK/PowerShell build is used instead of Maven so the project can be
  compiled on a machine that has JDK 21 but no Maven installation.
- Connector/J remains an external JAR in `lib` and is downloaded by a helper;
  it is not embedded or hardcoded into the Java sources.
- The interface keeps the form and table in one window so the required
  event-driven interactions are visible during assessment.
