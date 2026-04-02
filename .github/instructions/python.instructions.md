---
description: 'Python coding conventions and guidelines'
applyTo: '**/*.py'
---

# Python Coding Conventions

## Python Instructions

- Write clear and concise comments for each function.
- Ensure functions have descriptive names and include type hints.
- Provide docstrings following PEP 257 conventions.
- Use the `typing` module for type annotations (e.g., `List[str]`, `Dict[str, int]`).
- Break down complex functions into smaller, more manageable functions.

## General Instructions

- Always prioritize readability and clarity.
- For algorithm-related code, include explanations of the approach used.
- Write code with good maintainability practices, including comments on why certain design decisions were made.
- Handle edge cases and write clear exception handling.
- For libraries or external dependencies, mention their usage and purpose in comments.
- Use consistent naming conventions and follow language-specific best practices.
- Write concise, efficient, and idiomatic code that is also easily understandable.

## Code Style and Formatting

- Follow the **PEP 8** style guide for Python.
- Maintain proper indentation (use 4 spaces for each level of indentation).
- Ensure lines do not exceed 79 characters.
- Place function and class docstrings immediately after the `def` or `class` keyword.
- Use blank lines to separate functions, classes, and code blocks where appropriate.

## Professional Code Standards

**CRITICAL: Production Code Must Be Professional**

- **NO EMOJIS** in code, print statements, log messages, or comments
- **NO DECORATIVE CHARACTERS** (✓, ✗, ▶, ●, etc.) in production code
- Use plain ASCII text for all code artifacts
- Exception: Emojis are acceptable ONLY in:
  - User-facing UI text (when part of product design)
  - Marketing content
  - Documentation examples showing UI behavior

**Examples:**

❌ **WRONG - Never do this:**
```python
print("✅ Processing complete!")
logger.info("🚀 Starting deployment...")
# ⚠️ Important: Check this value
```

✅ **CORRECT - Professional production code:**
```python
print("Processing complete")
logger.info("Starting deployment")
# Important: Check this value
```

**Rationale:**
- Enterprise code must be universally readable across all terminals, logs, and systems
- Emojis break log parsing, monitoring tools, and text processing pipelines
- Professional codebases maintain consistent, clean ASCII output
- Emojis introduce encoding issues in CI/CD pipelines and legacy systems

## Edge Cases and Testing

- Always include test cases for critical paths of the application.
- Account for common edge cases like empty inputs, invalid data types, and large datasets.
- Include comments for edge cases and the expected behavior in those cases.
- Write unit tests for functions and document them with docstrings explaining the test cases.

## Example of Proper Documentation

```python
def calculate_area(radius: float) -> float:
    """
    Calculate the area of a circle given the radius.

    Parameters:
    radius (float): The radius of the circle.

    Returns:
    float: The area of the circle, calculated as π * radius^2.
    """
    import math
    return math.pi * radius ** 2
```