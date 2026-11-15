# Java Calculator - DevOps Project

A Spring Boot web calculator supporting addition, subtraction, multiplication and division.

This academic project demonstrates GitHub project planning, feature branches, pull requests, automated testing and continuous integration.

## Features

- Four arithmetic operations with whole and decimal numbers.
- A web interface built with Thymeleaf.
- Server-side validation of missing, invalid and non-finite input.
- Division-by-zero and arithmetic overflow messages.
- Preservation of submitted values and the selected operation.
- Unit tests, HTTP validation tests and an explicit JUnit test suite.
- Automated build and test execution through GitHub Actions.

## Technology

- Java 21
- Spring Boot 3.5.6
- Maven
- Spring MVC
- Thymeleaf
- JUnit Jupiter and JUnit Platform Suite
- Spring MockMvc
- GitHub Actions

## Prerequisites

Install JDK 21, Maven and Git.

Verify the installation:

```shell
java -version
javac -version
mvn -version
git --version
```

Both Java and Maven must use JDK 21. Set `JAVA_HOME` to the JDK installation if Maven uses a different Java version.

The project uses an installed Maven executable; it does not currently include a Maven wrapper.

## Get the project

```shell
git clone https://github.com/DmytroArkhypenko1/devops-calculator devops-calculator
cd devops-calculator
git switch dev
```

`dev` contains integrated development work. `main` receives reviewed releases through a separate release pull request.

## Build

From the directory containing `pom.xml`, run:

```shell
mvn clean verify
```

This cleans previous build output, compiles the application, runs tests and packages an executable Spring Boot JAR.

Generated files are stored in `target/`, which is excluded from Git.

## Run

```shell
mvn spring-boot:run
```

Open [http://localhost:8080](http://localhost:8080).

Alternatively, after building:

```shell
java -jar target/calculator-0.0.1-SNAPSHOT.jar
```

Stop the application with `Ctrl+C`.

## Use the calculator

Enter two numbers, select an operation and click **Calculate**.

| First number | Operation | Second number | Result |
|---|---|---|---|
| 8 | Addition | 2 | 10 |
| 8 | Subtraction | 2 | 6 |
| 8 | Multiplication | 2 | 16 |
| 5 | Division | 2 | 2.5 |
| -3 | Addition | 3 | 0 |
| 2.5 | Multiplication | 1.2 | 3 |

Use a dot as the decimal separator. Scientific notation is supported, for example `1e308`.

The endpoints are:

- `GET /`: display the calculator.
- `POST /`: process form parameters `a`, `b` and `operation`.

Supported operation values are `add`, `subtract`, `multiply` and `divide`.

## Validation and numerical limitations

| Condition | Message |
|---|---|
| Missing or blank operand | Enter both numbers. |
| Non-numeric or non-finite operand | Enter valid finite numbers. |
| Missing or unsupported operation | Select a valid operation. |
| Division by positive or negative zero | Cannot divide by zero. |
| Non-finite calculation result | The result is outside the supported range. |

Validation takes place on the server. Browser-required fields also help prevent empty submissions.

The calculator uses Java `double` arithmetic. Some decimal values cannot be represented exactly, so small rounding differences are expected. This is a learning calculator and is not intended for financial calculations requiring exact decimal arithmetic.

For example, `1e308 + 1e308` exceeds the finite `double` range and displays the result-range error.

Arithmetic methods retain Java floating-point behavior. The web controller detects non-finite results and converts them into user-friendly messages.

## Automated tests

Run the standard tests:

```shell
mvn test
```

Run one test class:

```shell
mvn test "-Dtest=CalculatorTest"
```

Run the explicit suite:

```shell
mvn test "-Dtest=CalculatorTestSuite"
```

Run the complete build verification:

```shell
mvn clean verify
```

| Test class | Responsibility |
|---|---|
| `CalculatorTest` | Arithmetic operations, decimal comparisons, overflow behavior and zero-divisor exceptions |
| `CalculatorControllerTest` | Operation selection, view names and model attributes |
| `CalculatorValidationTest` | HTTP validation, rendered errors, escaped input and recovery after errors |
| `CalculatorTestSuite` | Explicit selection of the three test classes above |

Decimal arithmetic assertions use a tolerance of `0.000000001`.

Surefire does not discover the filename `CalculatorTestSuite` through its default test naming patterns, so the suite is run explicitly. The standard run and suite run execute the same tests; they do not represent two separate sets of unique tests.

Maven is configured to fail when no tests are executed.

Reports are available in `target/surefire-reports/`. Later test runs may overwrite earlier reports.

## Continuous integration

Workflow file: [`.github/workflows/maven-test-1.yml`](.github/workflows/maven-test-1.yml).

The **Calculator CI** workflow contains the **test-main** job and runs on:

- Pull requests targeting `dev` or `main`.
- Pushes to `dev` or `main`.
- Manual dispatch, once the workflow is available on the default branch.

The job:

1. Checks out the repository.
2. Configures Temurin Java 21 and Maven dependency caching.
3. Runs `mvn --batch-mode --no-transfer-progress clean verify`.
4. Runs `CalculatorTestSuite` explicitly.

The workflow uses read-only repository permissions and a 15-minute job timeout. A failed build or test fails the job.

Open **Actions → Calculator CI → a run → test-main** to inspect its output.

CI failure detection was demonstrated by temporarily changing an expected test result on the feature branch, observing a failed run, restoring the assertion and confirming a successful run before merging.

This workflow builds and tests the application; it does not deploy it.

## Branching strategy

The project uses a development branch with short-lived feature branches.

| Branch | Purpose |
|---|---|
| `main` | Stable release branch |
| `dev` | Integration of completed development work |
| `feature/*` | Work on an individual issue |

For each implementation task:

1. Select the Issue and move it from **Todo** to **In Progress**.
2. Update the local `dev` branch.
3. Create the Issue's feature branch from `dev`.
4. Implement the change and run the relevant tests.
5. Commit with Issue reference like `(refs #<actual-issue-number>)` and push the feature branch.
6. Open a pull request targeting `dev`.
7. Reference the Issue with `Refs #<actual-issue-number>`.
8. Review the changes and validation results. Once CI is introduced, require a successful latest run before merging.
9. Squash and merge the feature PR into `dev`.
10. Delete the remote feature branch.
11. Confirm the Definition of Done, close the Issue and move it to **Done**.

Example:

```shell
git switch dev
git pull --ff-only origin dev
git switch -c feature/example-task
```

Changes are delivered through separate PRs rather than one final upload.

Because `main` is the default branch, implementation PRs targeting `dev` do not rely on automatic Issue closure. Issues are closed manually after their acceptance criteria and Definition of Done are satisfied.

A release is prepared through a separate PR from `dev` into `main`. Release verification, tagging and publication are tracked in the release Issue.

## Project planning

[Open the GitHub Project board](https://github.com/users/DmytroArkhypenko1/projects/1).

The initial backlog contains ten Issues, each with a description, tasks, acceptance criteria, Definition of Ready and Definition of Done.

Each Issue is assigned an owner, labels, milestone and sprint.

| Milestone | Scope |
|---|---|
| M1 — Calculator operations | Project setup and four arithmetic operations |
| M2 — Working web calculator | Controller, interface and validation |
| M3 — Tested and documented release | Test suite, CI, documentation and release |

- **Sprint 1:** Project setup and operations.
- **Sprint 2:** Web interface and release.
- **Statuses:** Todo → In Progress → Done.

## Repository structure

```text
.github/
└── workflows/
    └── maven-test-1.yml
src/
├── main/
│   ├── java/com/example/calculator/
│   │   ├── CalculatorApplication.java
│   │   ├── Calculator.java
│   │   └── CalculatorController.java
│   └── resources/templates/
│       └── index.html
└── test/java/com/example/calculator/
    ├── CalculatorTest.java
    ├── CalculatorControllerTest.java
    ├── CalculatorValidationTest.java
    └── CalculatorTestSuite.java
.gitignore
pom.xml
README.md
```

## Troubleshooting

- **Java version mismatch:** check `mvn -version` and ensure `JAVA_HOME` points to JDK 21.
- **Port 8080 is occupied:** stop the other application or run the packaged JAR with `--server.port=8081`.
- **Suite API compilation error:** confirm that the `junit-platform-suite` test dependency is present and reload the Maven project.
- **No tests executed:** check the class or method name. Select `CalculatorTestSuite` explicitly when running the suite.
- **Old page content:** save the source template, stop the application, run `mvn clean verify` and restart it.

## Security

See [SECURITY.md](SECURITY.md) for supported versions and vulnerability reporting instructions.