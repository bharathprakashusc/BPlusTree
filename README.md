# Disk-Backed B+Tree Index for Fast Lookups and Range Queries

**DSCI 551 – Database Systems | University of Southern California**  
**Author:** Bharath Prakash (bharathp@usc.edu)

---

## Overview

This project implements a B+Tree index from scratch in Python and benchmarks it against SQLite's native B+Tree index and a forced full table scan. The application uses a synthetic `students` table with 10,000 records to empirically validate B+Tree theory.

**Key results:**
- Python B+Tree point lookup: **~258x faster** than linear scan
- SQLite index point lookup: **~11x faster** than linear scan
- Both implementations return **identical result sets** (verified in notebook)

---

## Repository Contents

| File | Description |
|------|-------------|
| `Bharath_Prakash_Implementation.ipynb` | Main notebook — all code, benchmarks, and verification |
| `students_demo.db` | Pre-generated SQLite database (10,000 student records) |
| `Bharath_Prakash_FinalReport.pdf` | Final project report |
| `README.md` | This file |

---

## Prerequisites

- Python 3.8 or higher
- Jupyter Notebook or JupyterLab

No third-party packages are required. The `sqlite3` module is part of the Python standard library.

---

## Setup & Installation

**1. Clone the repository**
```bash
git clone https://github.com/bharathprakashusc/bplustree.git
cd bplustree
```

**2. Install Jupyter** (skip if already installed)
```bash
pip install jupyter
```

---

## Running the Notebook

```bash
jupyter notebook Bharath_Prakash_Implementation.ipynb
```

Then in Jupyter: **Kernel → Restart & Run All** to execute all cells from top to bottom.

---

## Reproducing Results

The notebook is fully self-contained and runs in two parts:

### Part 1 — SQLite Benchmarking
- Creates `students_demo.db` with the schema below (or reuses it if already present)
- Inserts 10,000 synthetic student records
- Runs and times three query types:
  - **Point lookup** via primary key index: `WHERE student_id = ?`
  - **Range scan** via secondary index: `WHERE gpa BETWEEN ? AND ?`
  - **Forced full table scan** (baseline): `WHERE student_id + 0 = ?`
- Prints `EXPLAIN QUERY PLAN` output to confirm which index path is used

### Part 2 — Python B+Tree from Scratch
- Defines `BPlusNode` and `BPlusTree` classes with `insert()`, `search()`, and `range_scan()`
- Builds two in-memory B+Trees from the same SQLite data (on `student_id` and `gpa`)
- Benchmarks all three methods (SQLite index, Python B+Tree, linear scan) using `time.perf_counter()` averaged over 1000 repetitions
- Verifies correctness: asserts SQLite and B+Tree return identical results for point lookups and range scans
- Visualizes tree structure (BFS level-order traversal + leaf linked list) for a small 16-row example

---

## Dataset Schema

| Field | Type | Description |
|-------|------|-------------|
| `student_id` | `INTEGER PRIMARY KEY` | Sequential integers 1–10,000 (rowid alias) |
| `name` | `TEXT NOT NULL` | Randomly generated first and last names |
| `gpa` | `REAL NOT NULL` | Uniform floats in [2.0, 4.0]; indexed by `idx_gpa` |

The dataset is generated automatically by the notebook. `students_demo.db` is also included so you can skip regeneration.

---

## Expected Output

```
Schema and indexes created successfully
Inserted 10000 students.

Point Lookup (avg over 100 queries x 1000 reps)
  Linear scan:    ~1.55 ms  (baseline)
  Python B+Tree:  ~0.006 ms (~258x faster than linear)
  SQLite index:   ~0.136 ms (~11x faster than linear)

Range Scan Benchmark
GPA Range       Linear (ms)  B+Tree (ms)  SQLite (ms)   Rows
--------------------------------------------------------------
2.0-2.3              ~9.0         ~0.0         ~0.0    ~1500
...

Point lookup verification:
  Match: True

Range scan (gpa 3.5-3.8):
  Results match: True
```

> Exact timings vary by machine. Speedup ratios should be in the same order of magnitude.

---

## No Secret Keys Required

This project uses no API keys, tokens, or environment variables. Everything runs locally using Python's built-in `sqlite3` module.
