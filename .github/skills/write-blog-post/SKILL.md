---
name: write-blog-post
description: "Use when creating or revising a personal blog post for this repository. Interview the user about the topic, content, type, structure, tone, length, audience, sources, and publication details, then write the Markdown post under docs/blog/posts/."
user-invocable: true
---

# Write a blog post

Create a personal blog post for this repository and save it in `docs/blog/posts/`.

## Required workflow

Do not start drafting until the user has supplied enough content and preferences. Collect missing information through interactive VS Code question prompts, not as plain chat questions when the prompt tool is available. Use one compact prompt and skip questions that the user has already answered. Let the user answer with notes, fragments, links, or an outline; do not require polished English.

Ask for the following information:

1. **Topic and purpose**: What is the post about? Why are you writing it?
2. **Main content**: What experience, opinion, lesson, problem, solution, or examples must be included?
3. **Post type**: For example, personal opinion, tutorial, experience report, technical explanation, review, list, announcement, or comparison.
4. **Audience**: Who should be able to understand and use the post?
5. **Structure**: Does the user want a specific outline? Otherwise, propose a simple outline before drafting.
6. **Tone**: For example, personal, conversational, practical, critical, humorous, serious, or reflective.
7. **Length**: Ask for an approximate word count or a short, medium, or long post.
8. **Technical depth**: Ask how much background, code, configuration, and detail to include.
9. **Sources and facts**: Ask for links, references, dates, quotations, and facts that must be checked or cited.
10. **Publication details**: Ask for the publication date, categories, and any preferred filename or title.
11. **Call to action**: Ask whether the post should end with recommendations, questions, next steps, or no call to action.

Use selectable options where useful: post type, audience, tone, requested length, technical depth, and call to action. Enable multi-select for categories and keep free-text input enabled for every prompt so the author can give details in Spanish or English. For questions about content, sources, title, structure, filename, and publication date, provide free-text input. Use the current date only after the user confirms it is appropriate.

For a revision, first ask what must remain unchanged and what the user wants to improve. Do not overwrite the author's opinion, examples, or voice just to make the article sound smoother.

If the user does not know an answer, suggest a reasonable default and ask for confirmation. For a post based on current events, require sources and clearly separate verified facts from opinion. Never invent sources, quotes, metrics, dates, personal experiences, or technical test results.

## Repository format

Write posts using this frontmatter unless the user requests a different value:

```yaml
---
date: YYYY-MM-DD
authors: [alvsanand]
categories:
  - General
---
```

Use the existing posts in `docs/blog/posts/` as the style and formatting reference. Use a filename beginning with `YYYYMM-`, followed by a short lowercase slug, for example `202608-topic-name.md`. Preserve the repository's Markdown conventions. Add a `## Sources` section when sources are provided or when the post discusses facts that need references.

Before saving, show the proposed title, outline, filename, and frontmatter. If the user has already explicitly asked to create the post, proceed without asking for a second confirmation after the interview. Otherwise, ask before creating or replacing a file.

## Voice and language

The author is a Spanish native speaker writing in English. Use clear, natural English with medium-level grammar. Do not make the prose sound like an academic native-English essay. Keep some simple sentence patterns and a personal, direct voice. Correct grammar, spelling, and unclear sentences, but do not erase the author's personality or add unnecessarily complex vocabulary.

The result must sound like a real person with a point of view, not like generic AI-generated content. Prefer specific observations, concrete examples, honest uncertainty, and direct recommendations. Use first person when the post is personal. Do not add personal claims that the user did not provide.

## Avoid AI-sounding writing

Do not use:

- Generic openings such as "In today's rapidly evolving world" or "As we navigate the ever-changing landscape".
- Empty conclusions such as "embrace the future" or "the possibilities are endless".
- Repetitive summaries of the same point.
- A rigid introduction, fixed number of body sections, or formulaic conclusion unless it genuinely fits the requested post.
- Excessive headings, bullet lists, bold emphasis, rhetorical questions, or motivational language.
- Marketing language, exaggerated certainty, fake balance, or unsupported claims.
- Repeated transition words such as "Moreover", "Furthermore", and "In conclusion".
- Corporate euphemisms and polished wording that does not match the author's direct style.

## Characters and formatting

Use normal Markdown and plain punctuation. Avoid typographic decoration that makes the text look machine-generated. In particular:

- Do not use em dashes or en dashes. Use a period, comma, colon, parentheses, or a hyphen instead.
- Do not use curly quotes, smart apostrophes, ellipses, or other decorative Unicode punctuation. Use straight quotes, apostrophes, and three periods.
- Do not add emojis unless the user requests them.
- Do not use HTML, tables, Mermaid, or elaborate formatting unless the user requests it or the post genuinely needs it.
- Keep paragraphs reasonably short and vary sentence length naturally.

## Final checks

After writing the file:

1. Confirm the path, title, date, categories, and approximate word count.
2. Check that the Markdown frontmatter is valid.
3. Check links and code fences for obvious formatting errors.
4. Check that the post answers its stated purpose, preserves the provided viewpoint, and does not contain invented facts.
5. Run the repository's documentation validation when dependencies are available, such as `mkdocs build --strict`.
6. Report any validation limitation instead of hiding it.
