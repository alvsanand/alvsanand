---
name: improve-blog-post
description: "Use when reviewing, editing, polishing, or improving an existing blog post in this repository. Preserve the author's viewpoint and voice, identify concrete improvements, then revise the Markdown post in docs/blog/posts/ after confirming the intended scope."
user-invocable: true
---

# Improve a blog post

Review and improve an existing post in `docs/blog/posts/` without replacing the author's ideas with generic prose.

## Start with the post and intent

Read the complete selected post and inspect its frontmatter, structure, citations, links, headings, lists, code blocks, and conclusion. Also read one or two relevant existing posts only when needed to understand repository conventions.

Before editing, collect only the missing intent with the interactive VS Code question prompts. Do not ask these questions as plain chat text when the prompt tool is available. If the user selected a file or named a post, use it as the target without asking them to choose it again.

Use one compact prompt with these questions. Skip an item when the user has already given the answer:

1. What should improve: grammar, clarity, flow, structure, technical accuracy, title, tone, concision, or everything?
2. What must remain unchanged: opinion, jokes, examples, structure, length, terminology, or specific wording?
3. Is the post a draft or already published? If published, should changes stay minimal?
4. Are there facts, sources, links, or dates that need checking or updating?

For the first question, offer selectable choices for grammar, clarity and flow, structure, technical accuracy, title, tone, concision, and everything. Allow multiple selections. For the third question, offer draft and published, and ask whether the edits should be minimal when published. Keep free-text input enabled so the author can provide notes in Spanish or English.

If the user says to improve everything, use a balanced edit: preserve the viewpoint and approximate length, correct clear issues, and improve readability. Do not require polished English from the user. Do not edit until the interactive prompt response is received, unless every required preference was already stated in the user's request.

## Review first

Provide a short review before modifying the file. Group issues by impact:

- **Important**: misleading fact, unsupported claim, broken or missing source, invalid frontmatter, poor structure, or unclear main argument.
- **Useful**: grammar, awkward sentence, repetition, imprecise wording, unclear transition, inconsistent terminology, or weak title.
- **Optional**: subjective stylistic alternatives that are not clearly better.

For factual claims, do not infer or invent evidence. If the post cites a source, use it as context. Flag statements that need verification instead of silently making them more definite. Keep facts separate from the author's opinion.

State the proposed scope, including whether the edit will be light, moderate, or substantial. If the user did not explicitly request edits, wait for confirmation before changing the file.

## Editing rules

- Preserve the author's point of view, real experience, examples, humor, and level of technical confidence.
- Do not add personal claims, measurements, quotes, sources, dates, product behaviour, or test results that the user did not provide or verify.
- Do not turn a personal post into a neutral news report, a marketing article, or a tutorial unless requested.
- Keep the post's natural structure. Remove or merge sections only when this makes the argument clearer.
- Improve headings when they are vague, repetitive, misleading, or inconsistent, but keep good personal headings.
- Maintain the existing frontmatter and filename unless the user asks to change them.
- Preserve valid Markdown links, lists, block quotes, code fences, and source sections.

## Language and voice

The author is a Spanish native speaker writing in English. Use clear, correct, medium-level English. Improve grammar and readability without turning the post into an academic native-English essay. Prefer direct vocabulary, natural phrasing, and a personal voice over sophisticated or overly polished language.

Avoid AI-sounding patterns:

- No generic scene-setting, empty conclusions, motivational language, or corporate phrasing.
- No fixed essay formula or unnecessary headings, lists, bold text, and rhetorical questions.
- No repeated summaries, fake balance, exaggerated certainty, or filler transitions such as "Moreover", "Furthermore", and "In conclusion".
- No em dash, en dash, curly quotes, smart apostrophes, decorative Unicode punctuation, or emojis unless they already appear and the user wants to keep them.
- Use plain Markdown with straight quotes, regular apostrophes, hyphens, and three periods. Do not add HTML, tables, Mermaid, or elaborate formatting unless needed and requested.

## Final checks

After editing:

1. Summarize the concrete changes and call out any fact that still needs user verification.
2. Confirm the file path, title, frontmatter, and approximate word-count change.
3. Check frontmatter, Markdown links, code fences, lists, and source formatting for obvious errors.
4. Verify that the revised post keeps the original message and does not introduce unsupported facts.
5. Run `mkdocs build --strict` when dependencies are available. Report validation limitations honestly.
