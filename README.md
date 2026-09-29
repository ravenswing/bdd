# bdd

A Claude Code skill for **benchmark-driven development**: settling a design
decision by measuring the options, instead of taking anyone's word for it,
including Claude's.

A **skill** is a set of instructions Claude follows for a particular kind of
job. This one applies the scientific method to software design and shows its
working.

- **Reproducible.** Every test is a file you can run yourself with one command.
- **Grounded.** The decision comes from measured data, not guesswork.
- **Clear.** A log records the plan, method, and results, so the decision can
  be reviewed at any time.

## When to use

A benchmark costs real time and tokens, so it is for decisions that are worth
it: architectural, hard to reverse, touching a lot of code or data, or relied
on by others. For example:

- choosing a storage engine, database, or file format others will depend on
- choosing or replacing a core library, such as pandas against polars
- picking the concurrency model for a service or pipeline
- checking a design still holds at 10x or 100x the data
- checking a large refactor, migration, or upgrade did not make things worse

It is not for small, local, or easily reverted choices, such as tuning one
function. For those, Claude times it quickly or states its claim as an
assumption.

## How to use

Run `/bdd` followed by the question, for example
`/bdd should the exports be stored as CSV or parquet?`

Claude can also suggest it when it is about to make an unmeasured claim on a
big decision, such as "DuckDB is faster". It suggests it in one line and waits
for your yes.

Use Sonnet or Opus. If a Haiku model picks it up, it stops and asks you to
switch.

## What it does

1. **Plan.** Claude writes down the problem, the candidates with a hypothesis
   for each, measurable decision criteria, the method with its reason, the
   test data, and the command to run. It asks whether real data exists, and
   otherwise generates seeded dummy data. It plans the fewest tests that reach
   a decision. **Stops for your approval**, then the criteria are locked.
2. **Build and run.** It writes the benchmark files, runs them, and pastes the
   output into the log unedited. It stops if a candidate fails, the result is
   a tie, nothing meets the criteria, or the setup looks flawed.
3. **Decide.** It applies the locked criteria and reports the decision, the
   log, and the command to rerun it.

## What you get

A folder per benchmark set:

```text
.bdd/
  YYYY-MM-DD-<name>/
    <name>.md     # the log
    bench.py      # the benchmark files, runnable with one command
```

The log has the same headings every time: the problem, potential solutions and
hypothesis, decision criteria, method, results, and decision made. It records
the exact commands, options, data sizes, seeds, repeat counts, machine, and
library versions, so anyone can rerun the experiment and check the decision.

## Behaviours it encourages

- Decision criteria are agreed **before** anything runs, and not changed after.
- The output makes the winner obvious, with a table marking it per criterion.
- Wrong guesses are recorded, not hidden. Each hypothesis is marked as held or
  not.
- Everything stays inside `.bdd/`. It never edits your project's code.
- Anything not measured is listed, with the reason.
- It never commits. It suggests a commit message and leaves the commit to you.
