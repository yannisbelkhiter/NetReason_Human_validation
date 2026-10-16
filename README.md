# Validation of Net-Reason dataset

Each item is a question about a table, with a reference answer. For each item, an annotator should:

1. **Judge the Q&A pair** against the table: is the question clear, is it answerable from the table alone, and is the reference answer correct?
2. **Score one model reasoning trace** from 1 (poor) to 10 (excellent), and marks whether the model's final answer is correct.

## Quick start

1. Open `https://yannisbelkhiter.github.io/NetReason_Human_validation/index.html` in a browser.
2. Enter your name or ID AS WELL AS YOUR CURRENT ROLE (e.g. PhD student).
3. Load the dataset file (`data/TO_UPLOAD.json`).
4. Click **Start evaluating**.
5. When finished, click **Download results (.json)**.


### How traces are parsed

- Text is split at `</think>`: everything before is shown as **Reasoning**, everything after as **Answer**. A leading `<think>` tag is stripped.
- If there is no `</think>` but a separate `model_final_answer` exists, the whole text is treated as reasoning.
- Items with no trace skip stage 2 and only need the Q&A pair rated.

### Table format

Tables are rendered when the text contains Markdown pipe rows:

```
| Region | Q1 | Q2 |
|--------|----|----|
| EMEA   | 10 | 12 |
```

Anything else is shown as plain text. Tables from automatic extraction are often misaligned, so each item has a **"Table extraction looks broken or misaligned"** checkbox. Use it so broken tables aren't mistaken for bad questions.

## Annotation schema

**Stage 1: Q&A pair** (all required)

| Field | Values |
|---|---|
| `clarity` | `clear`, `ambiguous`, `flawed` |
| `answerable` | `yes`, `partial`, `no` |
| `gt` | `correct`, `partial`, `incorrect`, `unsure` |

**Stage 2: trace** (required only when a trace exists)

| Field | Values |
|---|---|
| `answer_correct` | `correct`, `partial`, `incorrect` |
| `trace_score` | integer 1–10 |

**Always optional:** `table_broken` (checkbox) and `notes` (free text).

## Output

The exported JSON looks like this:

```json
{
  "annotator_id": "yannis",
  "exported_at": "2025-01-01T12:00:00.000Z",
  "n_items": 100,
  "n_answered": 100,
  "annotations": [
    {
      "sample_id": "s001",
      "question_type": "lookup",
      "table_broken": false,
      "trace_score": 8,
      "notes": "",
      "time_ms": 54210,
      "timestamp": "2025-01-01T11:58:03.000Z",
      "clarity": "clear",
      "answerable": "yes",
      "gt": "correct",
      "answer_correct": "correct"
    }
  ]
}
```

- `annotations` follow the order the annotator saw (shuffled if enabled).
- Unanswered items appear as `{ "sample_id", "question_type", "answered": false }`.
- `trace_score` and `answer_correct` are `null` for items without a trace.
- `time_ms` accumulates across revisits to the same item.
- The finish screen shows summary stats (percent clear, answerable, reference correct, tables flagged, mean trace score).

## Repo layout

```
├── data/
├───── TO_UPLOAD.json
├── results_to_upload/
├───── qa_eval_test.json
├── netreason_annotation.html   
└── README.md
```

Collect each annotator's exported JSON separately; files are named `qa_eval_<annotator>.json` (or `_progress.json` for the backup).
