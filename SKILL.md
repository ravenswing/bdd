---
name: bdd
description: Benchmark-driven development, not behaviour-driven development. Settles a choice between solutions, or a claim about one, with runnable measurements instead of assertion.
when_to_use: Only for architectural or hard-to-reverse decisions, or changes with real cost, that would otherwise rest on an unmeasured claim that one approach is faster, smaller, cheaper, more scalable, more accurate, or more reliable, such as "X is faster" or "negligible overhead". Examples include choosing a storage engine, database, file format, or serialisation that others will depend on; choosing or replacing a core library or framework; picking the concurrency model for a service or pipeline; deciding whether a design holds at 10x or 100x the data; and checking that a large refactor, migration, or dependency upgrade did not regress. Not for tuning one function or parameter, small local optimisations, or anything cheap to try and revert.
argument-hint: "<the question to answer>"
---

# Benchmark-driven development

If you are a Haiku model, stop now and recommend the user reruns this with
Sonnet or Opus.

Question: $ARGUMENTS

If no question is given above, take it from the conversation.

Apply the scientific method to a design decision and show the working, so the
answer rests on measurements a human can rerun, not on assertion.

- **Reproducible.** Every test is a file a human can run with one command.
- **Grounded.** The decision comes from measured data, never from guesswork.
- **Clear.** The log records the plan, process, and results, so the decision can
  be reviewed or re-assessed at any time.

> A benchmark without a decision attached is a hobby.

## When to reach for it

A benchmark set costs real time and tokens. Reach for it only when a decision
worth that cost would otherwise rest on reputation, intuition, or "it is well
known that". A decision is worth it when it is architectural, hard to reverse,
touches a lot of code or data, or others will rely on it. Typical questions,
when the decision is that big:

- **Speed.** Which algorithm, data structure, or library is faster here? Does
  vectorising, caching, memoising, or compiling (numba, Cython, Rust) pay off?
- **Concurrency.** Threads, processes, or async? How many workers? What batch
  or chunk size?
- **Data and I/O.** Which file format, compression codec and level, or
  serialiser? Streaming or loading into memory? Does this index or query
  rewrite help? Bulk or row-by-row inserts?
- **Memory and size.** Peak memory, dtype choices, generators against lists,
  memory-mapping, and disk, image, or bundle size.
- **Scale.** Does it still work at 10x or 100x the data, and where does it
  break? Does the claimed big-O show up in practice?
- **Network.** Batching, connection pooling, timeouts, and retry or backoff
  settings.
- **Developer time.** Startup and import time, build time, test suite time,
  and CI caching.
- **Correctness and accuracy.** float32 against float64, approximate against
  exact algorithms, and a regex against a parser on real samples.
- **Reliability.** Is it flaky or deterministic? Is output identical across
  runs, platforms, or versions?
- **Cost.** Cloud spend, API calls, or tokens per run.
- **Regressions.** Did a refactor, optimisation, or dependency upgrade make
  anything worse?

It is not for:

- small, local choices, such as how to write one function or a default that
  is trivial to change later
- cheaply reversible changes, where trying it is quicker than planning it
- questions one quick timing answers. Run that instead, and state the number
  and how it was measured.
- decisions where no measurable difference matters
- questions already measured on comparable data

## If the user did not type `/bdd`

Do not start. If the decision is small, do not suggest it either: state any
unmeasured claim as an assumption and carry on. Otherwise say in one line that
this could be settled by measuring, name the question you would answer, and
suggest `/bdd`. Wait for a yes.

## Where everything goes

Each benchmark set gets its own directory in `.bdd/` at the root of the
repository or project. Create `.bdd/` if it does not exist.

```text
.bdd/
  YYYY-MM-DD-<name>/     # <name> is short and descriptive, e.g. csv-vs-parquet
    <name>.md            # the log
    ...                  # benchmark files and data generators
```

Write nothing outside the set's directory. Never edit the project's own code.
If a candidate needs code, build it as a prototype inside the set's directory.

## Stage 1: Plan, then stop

Plan the fewest tests that reach the decision, and nothing more:

- only candidates that are real contenders, usually two or three
- only metrics named in the decision criteria
- the smallest data that shows the difference at the scale that matters
- only as many repeats as the noise needs
- one script, unless the candidates are architectures or designs

List anything left out under Method as "not measured", with the reason.

Write the log with every heading from The log below, leaving Results
and Decision made empty. Fill in:

1. **The problem.** What is being decided, and why it matters.
2. **Candidates.** Two or more, each with a hypothesis of how it will perform
   and why.
3. **Decision criteria.** Measurable, with thresholds or a ranking, for
   example "fastest median, unless its file is over 2x the smallest". If the
   criteria are unclear, or there is a trade-off between metrics, ask the user
   until they are specific.
4. **Method.** Recommend how to measure, with a one-line reason tied to this
   problem, and list any real alternatives. Timings usually need a warm-up and
   repeats, reported as median and spread. Deterministic metrics, such as file
   size or accuracy on fixed data, usually need one run. Say what difference
   counts as noise, and so as a tie.
5. **Test data.** Ask whether real-world examples exist. If not, generate
   dummy data with a seeded script in the set's directory, so reruns match.
   State the sizes used.
6. **Language.** Use the project's language. If it is obscure or hard to
   benchmark in, you may suggest Python instead, with the reason.
7. **Files and command.** The files you will create and the command that runs
   them. Use one file per candidate only when comparing architectures or
   designs. Otherwise use one script.

Show the user the plan and **stop for approval**. Once approved, the criteria
and method are locked.

## Stage 2: Build and run

- Python files are PEP 723 scripts run with `uv run`, and follow the script
  rules in the python-tooling skill. Other languages use their native tooling.
- The output must make the winner obvious. Print a table with one row per
  candidate, one column per criterion, and the winner marked.
- Record the machine, OS, and language and library versions in Method.
- Run the command exactly as written in the log, and paste its output into
  Results unedited.

Stop and ask the user, instead of working around it, when:

- a candidate fails to run or produces wrong output
- the result is a tie under the method's noise threshold
- no candidate meets the criteria
- a result suggests the setup itself is flawed, such as dummy data that does
  not resemble the real case

If the criteria or method must change, the user decides, and the log records
what changed and why.

## Stage 3: Decide

Apply the locked criteria to the results and write Decision made: the winner,
which criteria decided it, and whether each hypothesis held. Then report to the
user:

- the decision, in one sentence
- the path to the log
- the command to rerun the benchmarks

Never commit. Suggest a one-line commit message instead.

## The log

Write for an experienced developer or agent who was not in the conversation.
Keep it clear, concise, and easy to follow, but explicit: give exact values,
not descriptions. Someone must be able to reproduce the experiment from the
log alone, so record:

- exact commands, flags, and options, such as `usecols` or a compression codec
- data sizes, seeds, and how the data was generated or where it came from
- repeat counts, warm-up runs, and the noise threshold
- the machine, OS, and exact language and library versions
- anything held fixed or deliberately not measured

```markdown
# <Question as a title>

## The problem

## Potential solutions & hypothesis

## Decision criteria

## Method

<how it is measured, and why>
<the machine, OS, and versions>
<the command to run it>

## Results

## Decision made
```

Add headings when the set needs them, such as "Changes to the plan", but keep
these six.
