# Benchmark-driven development (BDD)

## What is bdd?

An impirical testing paradigm.
Apply the scientific method to software design - show your working.
Protects against AI statements being taken at face value.

### Core principles

- reproducible - all tests are explicitly written out and can be run by a human
- grounded - important decisions are based on real data not hallucination or guesswork
- clear - the whole process is documented for future review and can be re-assessed at any time

## When to use bdd?

Whenever there is a testable metric to distinguish between two or more potential solutions to a problem.

Testable metrics can include, but not limited to:

- performance
- disk space or storage
- throughput (e.g. rows/elements/items per second)
- total volume (e.g. ability to handle 1M vs 100B, or 1 kB vs. 30 GB)
- accuracy
- reproducibility

Examples when reproducible testing is better than assumption:

- impact of multi-processing, parallelism or multi-threading
- change in file formats or compression

## How

- write explicit, runnable files that can be run by a human
- print the findings from these scripts in clear, human readable ways - it should be easy to tell which was the best!
- include in your output the command to run these files
- if architectural, make a file per thing/concept under consideration
- if numerical or data files, one script is fine
- create dummy data but ask if real-world examples exist

### "A benchmark without a decision attached is a hobby."

Every test should have an explicit decision criterion/criteria that is defined **BEFORE** any tests are run.
If you are not sure about the criteria or there is a trade-off, ask questions to them narrow down.

## Where

- perform benchmarks in a new (create if needed) `.bdd/` folder in the root of the repository or project.
- for each benchmark set, create a new directory within `.bdd/` with a very short descriptive name in the format "YYYY-MM-DD-<name>.md"
- document the process and results of each benchmarking set in this new directory in a log: "<name>.md"

## Documenting

- the plan, process, and results of a benchmark set should be written up in the log
- the process of the test should be completely reproducible from the information contained in the log
- be as exhaustive as possible, but don't focus on technical details - focus on the what and why less the how.
- this log is technical, meant to be read by more experienced people or agents.
- it should still be clear, concise and easy to follow, but it should contain explicit and exact technical details to reproduce the "experiment".
- the layout of this log should include (but not limited to) these headings:
  1. "## The problem"
  2. "## Potential solutions & hypothesis"
  3. "## Decision criteria"
  4. "## Results"
  5. "## Decision made"
