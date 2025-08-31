---
marp: true
theme: default
---

# Plain Language Law

### Making Legislation Accessible with AI

**Josh McArthur**
**AI Engineering Meetup**

<!--
Speaker Notes:
- Welcome everyone.
- My name is Josh McArthur.
- Today I'm going to talk about a project called Plain Language Law, where we're using AI to tackle a really old and difficult problem: making law understandable for everyone.
-->

---

## The Problem

Have you ever tried to read a piece of legislation?

**Visual:** A full-bleed, slightly blurred screenshot of an incredibly dense, wall-of-text legal document. Maybe with some complex legal jargon circled or highlighted.

<!--
Speaker Notes:
- Start with a relatable hook. Ask the audience to raise hands.
- It's dense, it's complex, it's intimidating.
- It's written by lawyers, for lawyers. But it affects all of us.
- This complexity isn't just an inconvenience; it's an access to justice issue.
-->

---

## Our Solution: Plain Language Law

A project to make legislative text accessible to everyone using AI, specifically Large Language Models.

**Visual:** A "before and after" slider or side-by-side comparison on the slide.
- **Left side:** The dense legal text from the previous slide.
- **Right side:** The clean, simple, AI-generated "plain language" version of the same text.

<!--
Speaker Notes:
- This is what we built.
- We take that complex source material and, using AI, we translate it into something people can actually understand.
- It's not about "dumbing it down," it's about making it clear.
-->

---

## Why Legislation is a Perfect AI Problem

-   **Huge Corpus:** There's a massive amount of text to work with.
-   **Highly Structured:** Legislation has a predictable structure (sections, clauses, definitions) that machines can parse.
-   **High Value:** The social and democratic value of making law accessible is enormous.
-   **Complex & Layered:** It's a genuinely hard problem that is a great test for modern LLMs.

**Visual:** A simple infographic or a set of 4 icons representing each of these points.

<!--
Speaker Notes:
- We didn't just pick this problem at random. It has the perfect set of characteristics for an AI project.
- It's a large-scale data problem, which is where AI shines.
- The structure gives us handholds to work with the data programmatically.
- And the complexity means that simple rule-based systems would fail. It requires the nuance of LLMs.
-->

---

## The AI-Powered Content Pipeline

**Visual:** A high-level architectural diagram.
- **Boxes:** XML Source -> Ingestion & Transformation -> AI Content Enrichment -> Build & Deploy -> Public Website (S3/CloudFront).
- Keep it simple and clean.

<!--
Speaker Notes:
- So, how did we actually build it?
- Here's a 10,000-foot view of our pipeline.
- It's a multi-stage process that takes raw XML from the source and ends with a fully built, searchable, and accessible static website.
- I'm going to walk through each of these stages.
-->

---

## Stage 1: Ingestion & Transformation

-   **Collation:** Parse source XML into a consistent format.
-   **Content:** Convert XML to clean HTML using XSLT.
-   **Structuring:** Create YAML frontmatter to hold metadata for each document.

**Visual:** A code snippet showing the structure of the YAML frontmatter.
```yaml
# Example: legislation/my-act-2023.html
---
title: "My Awesome Act 2023"
source_url: "..."
topics: [] # To be filled by AI
summary: "" # To be filled by AI
featured_provisions: [] # To be filled by AI
---
<html>...</html>
```

<!--
Speaker Notes:
- The first stage is the "boring" but critical foundation. Garbage in, garbage out.
- We use standard, reliable tools like XSLT to transform the legislative XML into clean HTML.
- Crucially, we create a YAML frontmatter block in each file. This acts as a structured data container that our AI processes will populate later.
-->

---

## Stage 2: AI Content Enrichment

This is where the magic happens. We run a series of AI tasks:

1.  **Plain Language Generation:** `Legalese -> Simple HTML`
2.  **Featured Provision Extraction:** `Identify key sections`
3.  **Topic Classification:** `Tag and categorize the document`

**Visual:** A code snippet of a prompt you use. For example, the prompt for topic classification.
```json
{
  "prompt": "Given the following legal text and a predefined taxonomy of topics [Tax, Health, Environment...], assign the most relevant topics to this document. Text: \"...\""
}
```

<!--
Speaker Notes:
- Once we have clean, structured files, we bring in the AI.
- We have several distinct jobs that the LLMs perform.
- The core one is the plain language translation.
- But we also use it for metadata enrichment - extracting key ideas, and classifying the document against our taxonomy.
- All of this AI-generated data is written back into the YAML frontmatter.
-->

---

## Stage 3: Build & Deploy

-   **Static Site Build:** `Astro` builds the website from the processed HTML files.
-   **Search Index:** `Pagefind` creates a client-side search index.
-   **Automation:** GitHub Actions runs the entire pipeline on a schedule or trigger.
-   **Deployment:** Pushes the final site to S3 with a CloudFront CDN.

**Visual:** A simple diagram of the CI/CD pipeline.
`GitHub Actions Logo -> (Runs Pipeline) -> Astro Logo -> S3 Logo -> CloudFront Logo`

<!--
Speaker Notes:
- The final stage is the assembly and deployment.
- We use Astro as our static site generator. I'll talk more about why in a moment.
- We pre-build a search index so the site is fast and searchable on the client side.
- The whole thing is automated with GitHub Actions, which monitors the source for changes and rebuilds the site automatically.
-->

---

# Challenges & Lessons Learned

**Visual:** A slide with a picture of a winding or difficult road.

<!--
Speaker Notes:
- Of course, it wasn't all smooth sailing.
- This is often the most interesting part of any project: what went wrong and what we learned.
- I want to cover three main challenges we faced.
-->

---

## Challenge 1: The Static Site Generator Saga

-   **The Problem:** `Jekyll` build times were crippling at scale (>30 minutes).
-   **The Journey:** We tried `11ty`, `Next.js`, and `Bridgetown`. Many struggled with the sheer number of files and memory usage.
-   **The Solution:** `Astro` was flexible enough to handle it, but required custom file loading logic.

**Visual:** A simple bar chart comparing build times.
- **Jekyll:** A very tall bar (e.g., "32 mins").
- **Astro:** A very short bar (e.g., "4 mins").
- Or, a code snippet showing the custom file loading logic you had to write for Astro.

<!--
Speaker Notes:
- As we added more and more documents, our Jekyll build times exploded.
- We went on a tour of modern SSGs. We were surprised at how many struggled with our dataset, often crashing with out-of-memory errors.
- Astro was the winner. Its island architecture was a good fit, but the key was its flexibility. We could write our own data loading logic to handle the scale, which wasn't as easy in other frameworks.
-->

---

## Challenge 2: The LLM Trade-off

Quality vs. Cost.

-   `Claude`: Excellent results, but expensive at scale.
-   `Llama`: Much cheaper (running on our own infra), but required more prompt engineering and fine-tuning to match quality.

**Visual:** A simple comparison table.

| Model  | Quality | Cost/Month | Engineering Effort |
| :----- | :------ | :--------- | :----------------- |
| Claude | ★★★★★   | $$$$       | Low                |
| Llama  | ★★★★☆   | $          | High               |

<!--
Speaker Notes:
- This is the classic engineering trade-off.
- We started with Claude via an API, and the quality was fantastic out of the box. But the costs were scaling linearly with our content.
- We moved to running Llama on our own. It's drastically cheaper, but it's not a free lunch. We've had to invest a lot more time in prompt engineering and are now looking at fine-tuning to close the quality gap.
-->

---

## Challenge 3: The QA Bottleneck

How do you quality-assure the output of an LLM on sensitive legal text?

-   Manual review is impossible at scale.
-   We prototyped multilingual support, but had no way to QA the translations in a sustainable way.
-   This remains a largely unsolved problem for us.

**Visual:** A diagram showing a flow: `LLM Output -> [A BIG RED QUESTION MARK] -> Published`.

<!--
Speaker Notes:
- This is the hardest problem we face, and I don't have a perfect answer.
- How do we know the AI isn't misinterpreting a critical legal detail?
- We can't have lawyers review thousands of pages a day.
- This has completely blocked some features, like providing translations into languages our team doesn't speak.
- We're exploring solutions like using a second, different LLM as a "reviewer," but it's a major challenge.
-->

---

# Where We're Heading

**Visual:** A slide with a picture of a road stretching out to the horizon.

<!--
Speaker Notes:
- So that's where we are today. But what's next?
- The next major phase of the project is about moving from simple text generation to deeper semantic understanding, and the key to that is embeddings.
-->

---

## Unlocking Content with Embeddings

-   **The Plan:** Generate vector embeddings for all of our content.
-   This allows us to perform **semantic search** and **conceptual similarity** lookups, not just keyword search.

**Visual:** A simplified diagram showing a sentence being converted into a vector array.
`"The quick brown fox..." -> [TOKENIZE] -> [LLM EMBEDDING MODEL] -> [0.12, -0.45, 0.89, ...]`

<!--
Speaker Notes:
- For those who aren't familiar, embeddings are a way of representing text as a series of numbers—a vector—in a high-dimensional space.
- Documents with similar meanings will have vectors that are "close" to each other.
- This unlocks a whole new set of capabilities beyond what we're doing now. I'll give you two examples.
-->

---

## Use Case 1: Better Summaries with RAG

We can improve Llama's summarization by providing examples of "good" summaries from Claude.

**Visual:** A diagram explaining Retrieval-Augmented Generation (RAG).
1.  User asks for summary of **Document X**.
2.  Use embeddings to find 3 documents conceptually similar to X that already have good Claude-generated summaries.
3.  Inject those examples into the prompt for Llama.
4.  `Prompt: "Here are 3 examples of good summaries. Now, summarize Document X."`
5.  Get a much higher-quality summary from Llama.

<!--
Speaker Notes:
- Our Llama model struggles sometimes to create summaries as good as Claude's.
- With embeddings, we can implement a RAG pattern.
- When we want to summarize a new document, we first do a vector search to find a few existing documents that are conceptually similar and that have high-quality summaries we generated with Claude back in the day.
- We then put those examples directly into the prompt for Llama. This "few-shot" prompting dramatically improves the quality of the output.
-->

---

## Use Case 2: Smarter Content Discovery

Embeddings will power a new generation of features:

-   **Automated FAQ Extraction:** Find clusters of related provisions and generate Q&A sections.
-   **"Related Legislation" Blocks:** Go beyond simple keyword links and find other laws that are conceptually related.
-   **Building Hierarchies:** Automatically group topics and categories into a more intelligent structure.

**Visual:** A mock-up of a webpage for a piece of legislation, with a new "Related Content" sidebar that has links to other laws and a generated FAQ section.

<!--
Speaker Notes:
- The other big area is content discovery.
- Embeddings will let us build much smarter features.
- We can find clusters of related ideas within the law and automatically generate FAQ sections.
- We can build "related content" features that are far more powerful than what you could do with simple tags or keywords.
- It's really about building a semantic web of legal knowledge.
-->

---

## Key Takeaways

1.  **A solid data pipeline is the foundation of any production AI system.** The "boring" tech enables the "smart" tech.
2.  **For production AI, the trade-offs between cost, quality, and engineering effort are where the real work happens.**
3.  **Embeddings are the next step, moving from text generation to genuine semantic understanding.**

**Visual:** A clean, simple slide with just these three points, perhaps with icons for each.

<!--
Speaker Notes:
- So, if you take away three things from this talk, I hope it's these.
- First, don't neglect the data engineering. It's not glamorous, but it's essential.
- Second, be ready to make hard choices about cost vs. quality. There's no magic bullet.
- And finally, if you're working with large text corpora, start thinking about embeddings. It's the key to unlocking the next level of features.
-->

---

# Thank You

**Josh McArthur**

-   Website: plainlanguagelaw.joshmcarthur.com (or your URL)
-   GitHub: github.com/joshmcarthur
-   Twitter/LinkedIn: @yourhandle

**Questions?**

**Visual:** A clean slide with your contact details.

<!--
Speaker Notes:
- That's all I have. Thank you for your time.
- I'd be happy to take any questions.
-->  