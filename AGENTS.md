# Agent Operating Guidelines

This document outlines the essential guidelines for agents operating within this repository.

## Build/Lint/Test Commands
- **Verification**: Ensure markdown structure, link validity, and naming conventions are consistent.
- **No Traditional Builds**: This project does not have traditional code build/lint/test commands.

## Code Style Guidelines
- **Markdown Formatting**: Adhere to GitHub Flavored Markdown. Use consistent headers, lists, and code blocks.
- **Naming Conventions**: Follow `notes/XX-topic.md` and `notes/resources/detailed-resource.md` patterns.
- **Link Management**: All URLs must be clickable markdown links with descriptive text.
- **Error Handling**: Clearly document any conversion errors, broken links, or inconsistencies.

## Project Structure Guidelines
- **Course Materials**: Original course files are located in the `course-materials/` directory.
- **Notes**: Organized markdown notes are located in the `notes/` directory.
- **Resources**: Detailed resource files are located in `notes/resources/`.
- **Root Files**: `AGENTS.md` and `README.md` are located in the project root.

## Tool Use Guidelines
### convert_to_markdown
- **Purpose**: Use this tool to convert document like PDF files, presentations, and web pages into structured markdown for easy integration into course notes.
- **Usage**:
  - For PDF or presentation files: Provide the file path (e.g., `course-materials/week1-slides.pdf`).
  - For web pages: Provide the full URL (e.g., `https://example.com/resource`).
- **Best Practices**:
  - Always review the converted markdown for formatting issues or missing content.
  - Extract and preserve all links, images, and headings.
  - If conversion fails or content is missing, clearly document the issue in the notes.
