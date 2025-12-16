# Press0

**From Zero to Book.**

Press0 is an AI-orchestrated publishing engine that transforms raw Markdown into professional, bookstore-quality PDFs. It bridges the gap between "finished writing" and "ready for print" by automating layout, typography, and asset management.

## The Philosophy

**Single Source Publishing.**
You shouldn't need a separate InDesign file for your book. Press0 treats your web-ready Markdown as the single source of truth, applying a rigorous "Book Publisher Protocol" to render it for physical print (6"x9" Trade).

## Features

-   **Prompt-Driven Architecture**: Built on [Promptware OS](https://shipfail.github.io/promptware/), using natural language prompts as logic.
-   **Remote-First**: Fetches content directly from GitHub URLs or blogs.
-   **Cinematic Layout**: Automatically handles full-bleed "Hero" chapter openers.
-   **Typographic System**: Semantic font mapping using the IBM Plex family.
-   **Clean Room Builds**: Stateless execution using ephemeral environments.

## Usage

Press0 is designed as an Agent Skill.

1.  **Boot Promptware OS**: Ensure your agent is running the Promptware kernel.
2.  **Invoke the Skill**:
    > "Publish this blog post: https://example.com/my-post.md"
3.  **Receive PDF**: The agent will scout, acquire, refine, and synthesize the book.

## License

**Public Prompt License - MIT Variant (PPL-M)**
See [LICENSE](LICENSE) for details.

## Author

Built with ❤️ by **Huan Li** ([Ship.Fail](https://ship.fail)).
