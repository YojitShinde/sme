# Question Submission & Initial Processing

## 1. What are we trying to achieve?

When a user asks a question, we want to make sure that:

1. We can find **similar questions** that may already help them.
2. The question is **appropriate for the platform**.
3. The user gets useful feedback **before continuing to the next stage**.

The user should only need to provide:

- **Title**
- **Description**
- **Tags / Technologies / Topics**

The user does **not** need to understand the platform's internal taxonomy or provide additional metadata.

---

# 2. Example

Imagine a developer submits:

### Title

> Spring Boot API returns 500 when fetching users from MySQL

### Description

> I'm building a REST API using Spring Boot 3 and Java 21.
>
> My `/users` endpoint uses Spring Data JPA to query a MySQL 8 database, but it returns a 500 error.
>
> The error is:
>
> `JDBCConnectionException: Unable to acquire JDBC Connection`
>
> I've checked the database credentials and confirmed that MySQL is running.
>
> How can I identify the cause of this connection failure?

### Tags selected by the user

```text
Java
Spring Boot
MySQL
```

---

# 3. High-level flow

```text
                    ┌───────────────────┐
                    │       USER        │
                    │                   │
                    │ Title             │
                    │ Description       │
                    │ Tags              │
                    └─────────┬─────────┘
                              │
                           SUBMIT
                              │
                              ▼
                    ┌───────────────────┐
                    │  QUESTION RECEIVED│
                    └─────────┬─────────┘
                              │
                 ┌────────────┴────────────┐
                 │                         │
                 ▼                         ▼
        ┌──────────────────┐      ┌───────────────────┐
        │  SIMILAR SEARCH  │      │ APPROPRIATENESS   │
        │                  │      │                   │
        │ BM25             │      │ Rules             │
        │ +                │      │ +                 │
        │ Embeddings       │      │ Lightweight LM    │
        └────────┬─────────┘      └─────────┬─────────┘
                 │                          │
                 ▼                          ▼
        Related questions          Classification signals
                 │                          │
                 └────────────┬─────────────┘
                              ▼
                    ┌───────────────────┐
                    │   REVIEW SCREEN   │
                    │                   │
                    │ Related questions │
                    │ Warnings          │
                    │ User tags         │
                    └─────────┬─────────┘
                              │
                       ┌──────┴──────┐
                       │             │
                       ▼             ▼
                     EDIT         CONTINUE
                                     │
                                     ▼
                              NEXT STAGE
```

---

# 4. Step 1 — User submits the question

The user enters:

```text
Title
Description
Tags
```

There is no need for the user to select:

```text
Project
Role
Expertise
Urgency
```

These can be handled by later stages.

The important principle is:

> **Let the user explain the problem naturally.**

---

# 5. Step 2 — Question is received

Once the user presses **Submit**, the platform receives the complete post.

Conceptually:

```text
                POST
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
      Title  Description  Tags
        │        │        │
        └────────┼────────┘
                 ▼
          Question Object
```

The original question should be preserved.

The system should not modify the user's wording simply because a model is processing it.

---

# 6. Step 3 — Similar Search

## Objective

Answer the question:

> **"Are there existing questions that might already solve this problem?"**

This is a **search/retrieval problem**, not primarily an LLM problem.

We use two complementary techniques:

```text
                 New Question
                      │
             ┌────────┴────────┐
             ▼                 ▼
           BM25            Embeddings
             │                 │
             ▼                 ▼
       Keyword Search    Semantic Search
             │                 │
             ▼                 ▼
          Top-K              Top-K
             │                 │
             └────────┬────────┘
                      ▼
              Merge + Deduplicate
                      │
                      ▼
                Hybrid Ranking
                      │
                      ▼
               Top Related Qs
```

---

## 6.1 BM25 — keyword-based search

BM25 is a traditional information-retrieval algorithm.

The system indexes all public questions.

Our example contains important terms such as:

```text
Spring Boot
MySQL
JDBCConnectionException
REST API
database connection
Spring Data JPA
```

BM25 searches for questions containing relevant terms.

It could find:

```text
Q101
"Spring Boot unable to acquire JDBC connection"

Q245
"MySQL connection error in Spring Boot"

Q381
"Spring Boot REST API database connection failure"

Q412
"How to configure Spring Security in Spring Boot"
```

BM25 ranks them according to textual relevance.

### Why BM25?

Technical questions often contain highly specific terminology.

For example:

```text
JDBCConnectionException
NullPointerException
CUDA out of memory
ORA-00942
React.useEffect
```

Exact terms can be extremely valuable.

BM25 is therefore very good at **lexical matching**.

---

# 7. Embedding Search

BM25 has a limitation.

Two questions can describe the same problem using completely different words.

For example:

### New question

> Unable to acquire a JDBC connection.

### Existing question

> Application cannot establish a database session.

The words are different, but the underlying problem may be similar.

This is where embeddings help.

---

## 7.1 Creating the embedding

An embedding model converts the question into a numerical vector.

```text
Question
   │
   ▼
Embedding Model
   │
   ▼
[0.12, -0.38, 0.71, 0.04, ...]
```

Existing questions have embeddings stored in a vector database/index.

The new question is compared against those vectors.

The system might find:

```text
Q101 → 0.94 similarity
Q245 → 0.91 similarity
Q731 → 0.88 similarity
```

These questions may be semantically similar even if they don't contain exactly the same words.

---

# 8. BM25 + Embeddings together

We don't need to choose one.

Use both.

```text
                  QUESTION
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
       BM25 Search          Embedding Search
          │                       │
      Top 50                    Top 50
          │                       │
          └───────────┬───────────┘
                      ▼
               Merge Results
                      │
                      ▼
                Remove Duplicates
                      │
                      ▼
                Hybrid Ranking
                      │
                      ▼
                  Top 5–10
```

This gives us:

- **BM25 → strong exact-term matching**
- **Embeddings → strong semantic matching**

For the MVP, this is enough.

A more expensive model-based reranker can be added later if evaluation shows that it provides meaningful improvement.

---

# 9. Example Similar Search Result

For our Spring Boot question, the system might produce:

```text
Possible related questions:

1. Spring Boot unable to acquire JDBC connection
   Relevance: High

2. MySQL connection error in Spring Boot
   Relevance: High

3. JDBC connection failure after application deployment
   Relevance: Medium
```

The user can open these questions.

The system should **not automatically say**:

> "Your question is a duplicate."

Instead:

> **"These existing questions may be related to your problem."**

The user can still continue.

---

# 10. Step 4 — Appropriateness Check

This is different from Similar Search.

Similar Search asks:

> **"Is there something similar?"**

Appropriateness asks:

> **"Is this a legitimate post that can proceed?"**

For this we use:

### Lightweight LM + deterministic checks

For the lightweight LM, the proposed model is:

> **Qwen3-4B-Instruct-2507**

The model is used as a **classifier**, not as a chatbot.

---

# 11. Why use a lightweight LM?

We don't need a large reasoning model.

The model only needs to classify the post into a small number of categories.

For example:

```text
Does the post:

✓ contain a genuine question?
✓ contain spam?
✓ contain abuse?
✓ contain unsafe content?
✓ appear off-topic?
✓ contain sensitive information?
```

This is a relatively narrow inference task.

Therefore a smaller model can potentially provide:

- lower latency
- lower compute cost
- higher throughput
- easier deployment
- predictable structured output

---

# 12. Appropriateness processing

The flow is:

```text
             Question
                 │
                 ▼
       ┌───────────────────┐
       │ Deterministic     │
       │ Checks            │
       │                   │
       │ Secrets           │
       │ Basic spam        │
       │ Obvious patterns  │
       └─────────┬─────────┘
                 │
                 ▼
       ┌────────────────────────┐
       │ Lightweight LM         │
       │                        │
       │ Qwen3-4B-Instruct-2507 │
       └────────────┬───────────┘
                    │
                    ▼
          Structured Classification
                    │
                    ▼
             Policy Evaluation
```

---

# 13. Deterministic checks

Some things are better handled without an LM.

For example:

```text
OPENAI_API_KEY=sk-xxxxxxxx
password=xxxxxxxx
AWS_SECRET_ACCESS_KEY=xxxxxxxx
```

A secret-detection mechanism can identify these directly.

Similarly, basic spam patterns can be detected using rules.

This gives us:

```text
Rules → obvious/high-confidence cases
LM    → contextual/ambiguous cases
```

---

# 14. Lightweight LM inference

The LM receives:

```text
Title
+
Description
+
Tags
```

It is given a narrow classification instruction.

Conceptually:

```text
Classify this post.

Return:

has_question
spam
abuse
unsafe
off_topic
sensitive_data

Return structured JSON only.
Do not answer the question.
Do not rewrite the question.
Do not make account decisions.
```

For our example, it could return:

```json
{
  "has_question": true,
  "spam": false,
  "abuse": false,
  "unsafe": false,
  "off_topic": false,
  "sensitive_data": false
}
```

---

# 15. The LM does NOT make the final decision

This is an important architectural principle.

The LM produces:

> **Inference signals**

The application produces:

> **Policy decisions**

For example:

```text
             LM
              │
              ▼
      Classification
              │
              ▼
      Application Logic
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
     Edit   Review  Continue
```

This prevents the model from becoming the authority over moderation.

---

# 16. Example — unclear question

Suppose the user submits:

> "My Spring Boot application is broken. Please help."

The LM could determine:

```json
{
  "has_question": false,
  "spam": false,
  "abuse": false,
  "unsafe": false,
  "off_topic": false,
  "sensitive_data": false
}
```

The platform should **not reject the question**.

Instead, show:

> **Your post contains useful context, but it isn't clear what you want help with. Add the specific problem or error.**

The user edits it.

---

# 17. Example — spam

Suppose someone submits:

> "BUY OUR AMAZING AI COURSE! 90% OFF! CLICK HERE!"

The LM could identify:

```json
{
  "has_question": false,
  "spam": true,
  "abuse": false,
  "unsafe": false,
  "off_topic": true,
  "sensitive_data": false
}
```

The platform can then show a warning or send the post to the moderation path.

---

# 18. Example — legitimate question

Our Spring Boot question produces:

```text
has_question     → TRUE
spam             → FALSE
abuse            → FALSE
unsafe           → FALSE
off_topic        → FALSE
sensitive_data   → FALSE
```

Therefore:

> **No obvious issues detected.**

The user can continue.

---

# 19. Step 5 — Review Screen

The outputs of both systems are presented together.

```text
┌──────────────────────────────────────────┐
│            REVIEW YOUR QUESTION          │
├──────────────────────────────────────────┤
│                                          │
│ YOUR TAGS                                │
│                                          │
│ [Java] [Spring Boot] [MySQL]             │
│ [REST APIs] [Debugging]                  │
│                                          │
├──────────────────────────────────────────┤
│                                          │
│ POSSIBLY RELATED QUESTIONS               │
│                                          │
│ • Spring Boot unable to acquire JDBC     │
│   connection                             │
│                                          │
│ • MySQL connection error in Spring Boot  │
│                                          │
│ • JDBC connection failure after deploy   │
│                                          │
├──────────────────────────────────────────┤
│                                          │
│ ✓ No obvious issues detected             |
│                                          │
├──────────────────────────────────────────┤
│                                          │
│ [ Edit Question ]       [ Continue ]     │
│                                          │
└──────────────────────────────────────────┘
```

This gives the user everything they need without creating a complicated workflow.

---

# 20. Final End-to-End Flow

```text
                         USER
                           │
                           ▼
              ┌────────────────────────┐
              │ Question + Description │
              │ + User-selected Tags   │
              └────────────┬───────────┘
                           │
                         SUBMIT
                           │
                           ▼
                  ┌─────────────────┐
                  │ Question        │
                  │ Received        │
                  └────────┬────────┘
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
     ┌──────────────────┐      ┌─────────────────────┐
     │  SIMILAR SEARCH  │      │  APPROPRIATENESS    │
     └────────┬─────────┘      └──────────┬──────────┘
              │                          │
       ┌──────┴──────┐            ┌──────┴──────┐
       ▼             ▼            ▼             ▼
     BM25       Embeddings      Rules      Qwen3-4B
       │             │                         │
       ▼             ▼                         ▼
     Top-K         Top-K                  Classification
       │             │                         │
       └──────┬──────┘                         │
              ▼                                │
       Merge + Rank                             │
              │                                │
              ▼                                ▼
      Related Questions                  Policy Signals
              │                                │
              └──────────────┬─────────────────┘
                             ▼
                    ┌──────────────────┐
                    │  REVIEW SCREEN   │
                    │                  │
                    │ Related Qs       │
                    │ Warnings         │
                    │ User Tags        │
                    └────────┬─────────┘
                             │
                       ┌─────┴─────┐
                       ▼           ▼
                     EDIT       CONTINUE
                       │           │
                       └─────┐     ▼
                             │  NEXT STAGE
                             │
                             └── Re-check
```

