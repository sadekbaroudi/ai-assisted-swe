---
applyTo: "**"
---
# Project general coding standards

## Naming Conventions
- Use camelCase for variables, functions, and methods
- Prefix private class members with underscore (_)
- Use ALL_CAPS for constants

## Design Principles and Best Practices

- **SOLID Principles**
  1. Single Responsibility Principle  
  2. Open/Closed Principle  
  3. Liskov Substitution Principle  
  4. Interface Segregation Principle  
  5. Dependency Inversion Principle

- **DRY (Don't Repeat Yourself):**
Avoid code duplication by abstracting common functionality into reusable functions, classes, interfaces, and factories.

## Testing and Quality Assurance

**Testability:**  
- Structure code to facilitate unit, integration, and end-to-end testing.
**Unit Tests and Integration Tests**  
- Write unit tests for all new functions and methods, ensuring they are isolated and testable.
- Update tests with any change to the codebase.
- Run all tests and make sure they all pass before committing code to ensure no regressions.

## Preflight check for tasks
- Review the existing requirements in [.github/instructions/requirements.instructions.md](./requirements.instructions.md) to understand the application's functionality

## Code Structure
- All backend code should be in the [backend](../../backend) directory
- All frontend code should be in the [frontend](../../frontend) directory

## Development Considerations
- Use environment variables for configuration (e.g., database credentials, API keys)
- Ensure that sensitive information is not hardcoded in the codebase
- When creating new files, consider whether there should be any changes to the `.gitignore` file, especially for logs, temporary files, or build artifacts
- As you write code, make sure that you update the [README.md](../../README.md), updating any application server commands, description, dependencies, and installation instructions. This file serves as the main documentation for the project and should always reflect the current state of the application.

## Development validation
- Do not use the browser to test web API endpoints; instead, use cURL and use silent mode to avoid unnecessary output. For example, use `curl -s` to suppress the progress bar.
- Use full paths when running commands instead of relative paths to avoid working in the wrong directory
- For UI testing, please do not test yourself, but instead ask the user to test it. Provide details on what you'd like to test, including specific actions to take and expected outcomes. The user will then run the tests and provide feedback.