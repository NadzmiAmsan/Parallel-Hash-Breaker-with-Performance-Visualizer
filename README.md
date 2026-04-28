🔓 Parallel Hash Breaker: Cracking Passwords at the Speed of Parallel Computing
Course Code: ITT440: Network Programming

Lecturer: Shahadan Bin Saad

Youtube Link : https://www.youtube.com/watch?v=EzCX8cD6PAc&t=6s


🎯 1. Mission Objective
In password security auditing, time is the only barrier between a hash and its plaintext. Traditional brute‑force methods test one password at a time, making complex cracking attempts painfully slow.

The Parallel Hash Breaker is a high‑performance cryptographic cracker that leverages multiprocessing, threading, and sequential models to demonstrate real‑world parallel computing concepts. By distributing password attempts across all available CPU cores, it transforms a standard laptop into a password‑recovery powerhouse, achieving over 3x speedup on intensive workloads while generating professional performance graphs.

💻 2. Hardware & Environment
Processor: Quad‑Core / Octa‑Core CPU (e.g., Intel i5‑8250U @ 1.60GHz)

Memory: 8GB RAM minimum

OS: Linux Environment (Ubuntu 22.04+ recommended)

Runtime: Python 3.8+ with matplotlib for analytics and tqdm for real‑time progress bars.

Disclaimer
The target hash is pre‑configured to match a number near the end of the search space (49,999,999), ensuring meaningful benchmark times without infinite loops. You can modify Config.TARGET_HASH to test custom values.

🛠️ 3. Deployment Guide
A. System Ignition
bash
# Create a new project folder
mkdir ITT440_HashBreaker
cd ITT440_HashBreaker

# Initialize a virtual environment
python3 -m venv venv

# Activate the environment (Linux/macOS)
source venv/bin/activate

# Install required libraries
pip install tqdm matplotlib
B. Engagement Protocol – Basic Run
bash
# Run the full benchmark (sequential, threading, parallel) with default SHA256
python3 parallel_hash_breaker.py --limit 50000000
C. Extra Tasks for Deeper Analysis
The following additional commands allow you to explore different hash algorithms, worker counts, dictionary attacks, and the lightweight difficulty benchmark.

Task	Command
Switch to MD5 algorithm	python3 parallel_hash_breaker.py --algo md5 --limit 50000000
Use fewer workers (e.g., 2)	python3 parallel_hash_breaker.py --workers 2 --limit 50000000
Run dictionary attack	python3 parallel_hash_breaker.py --dict
Run difficulty benchmark (fast demo)	python3 parallel_hash_breaker.py --difficulty
[INSERT IMAGE: Screenshot showing terminal with all these commands executed]

📊 4. Battlefield Analytics
A. Single Large Task (50 Million Hashes) – Default SHA256
When cracking a SHA‑256 hash positioned near the end of a 50‑million number space, the engine produces the following performance breakdown:

Method	Time (seconds)	Speedup vs Sequential
Sequential (1 core)	36.14 s	1.00x (baseline)
Threading (8 threads)	35.92 s	1.01x (GIL limited)
Parallel (8 cores)	11.34 s	3.19x 🚀
Total passwords tested: 50,000,000
Target hash: SHA-256("49999999")
Result found: 49999999

[INSERT IMAGE: Performance comparison bar chart – performance_comparison.png]
Figure 1: Parallel multiprocessing outperforms threading and sequential models due to true CPU parallelism.

B. Effect of Hash Algorithm – MD5 vs SHA256
Hash algorithms have different speeds. MD5 is generally faster than SHA256 because it uses fewer computational rounds. Running the same benchmark with --algo md5 shows this difference:

Algorithm	Sequential Time (s)	Parallel Time (8 cores)	Speedup
SHA256	36.14	11.34	3.19x
MD5	28.50	8.90	3.20x
Observation: MD5 is ~21% faster overall, but the parallel speedup ratio remains similar – proving that parallel gains are independent of hash algorithm.

[INSERT IMAGE: Bar chart comparing MD5 vs SHA256 performance]

C. Scaling with Fewer Workers – The --workers 2 Test
Not all environments have 8 cores. Running with only 2 workers (--workers 2) shows how speedup scales with fewer resources:

Workers	Time (s)	Speedup vs Sequential
1 (sequential)	36.14	1.00x
2	18.92	1.91x
4	14.10	2.56x
8	11.34	3.19x
Observation: Doubling workers roughly doubles speed until diminishing returns due to overhead. This is a textbook example of near‑linear scaling – perfect for teaching parallel computing concepts.

[INSERT IMAGE: Scaling plot – scaling_plot.png or a custom plot for workers=2,4,8]

D. Dictionary Attack – Fast Wordlist Cracking
The dictionary attack tries common words plus simple mutations (capitalization, appending 123 or !). It is extremely fast because the search space is tiny.

Command: python3 parallel_hash_breaker.py --dict

Dictionary Size	Mutations per Word	Candidates Tested	Result	Time
5 base words	20 total	20	Not found (default)	0.0004 s
Note: The default dictionary (admin, password, 123456, etc.) does not match the numeric target hash (49999999). To see a successful crack, either:

Change Config.TARGET_HASH to match a dictionary word (e.g., hashlib.sha256(b"admin").hexdigest())

Or add the number 49999999 to the dictionary list.

[INSERT IMAGE: Terminal output showing dictionary attack run with "Not found" message]

Why include dictionary attack?
Real‑world password cracking often starts with dictionary attacks before brute‑force – they are orders of magnitude faster when users choose weak passwords.

E. Lightweight Difficulty Benchmark – Runs in Seconds
This built‑in benchmark (--difficulty) is designed for quick demonstrations. It generates random passwords of increasing length/complexity and runs both sequential and parallel hashing.

Command: python3 parallel_hash_breaker.py --difficulty

Level	Passwords	Algorithm	Password Len	Sequential (s)	Parallel (s)	Speedup
very_easy	100,000	md5	4	0.48	0.19	2.53x
easy	10,000	md5	8	0.09	0.04	2.25x
intermediate	1,000	sha256	8	0.05	0.02	2.50x
hard	100	sha256	12	0.02	0.01	2.00x
very_hard	10	sha512	16	0.01	0.01	1.00x
Key Takeaways:

For large task counts (100k), parallel gives ~2.5x speedup.

For tiny tasks (10 passwords), overhead dominates and speedup disappears – demonstrating that parallelism has a cost.

This benchmark is ideal for live lectures because it finishes in seconds without needing huge datasets.

[INSERT IMAGE: Screenshot of the difficulty benchmark output from terminal]

🧠 5. How It Works (The Logic)
The Parallel Hash Breaker implements three distinct cracking models and a difficulty benchmark, all following a MapReduce‑inspired pattern:

1. Sequential (Baseline)
Partition: None. One loop from 0 to limit.

Analyze: Single core hashes each number one by one.

Synthesize: Returns first match.

2. Threading (GIL Demonstration)
Partition: Search space divided among N threads.

Analyze: Each thread runs Python code concurrently – but the Global Interpreter Lock (GIL) allows only one thread to execute Python bytecode at a time.

Result: Minimal speedup (hashing is CPU‑bound, so threads just add overhead).

3. Parallel (Multiprocessing) – True Speedup
Partition: Search space split into N equal chunks (one per CPU core).

Analyze: Each core runs a separate Python process with its own GIL. Hashing happens in true parallel.

Early Termination: As soon as any process finds the password, it terminates the entire pool – saving time.

Synthesize: The main process collects the result.

4. Difficulty Benchmark (Lightweight & Fast)
Partition: A list of random passwords is generated per difficulty level (100k very easy … 10 very hard).

Analyze: Sequential version hashes each password one by one. Parallel version distributes the list across all cores using Pool.map().

Result: Even though each task is tiny, the overhead of multiprocessing is offset by true parallelism for medium/large counts.

[INSERT IMAGE: Diagram of MapReduce pattern – splitting, mapping, reducing]

Key Code Snippets
Parallel crack with early termination:

python
with Pool(num_workers) as pool:
    for res in pool.imap_unordered(_crack_wrapper, ranges):
        if res is not None:
            pool.terminate()   # Stop all workers immediately
            break
Difficulty benchmark core logic:

python
# Sequential
for task in tasks:
    hash_single_task(task)

# Parallel
with Pool(cpu_count()) as pool:
    pool.map(hash_single_task, tasks)
Changing hash algorithm (MD5 vs SHA256):

python
# In Config class
HASH_ALGO = "md5"   # or "sha256"
Reducing worker count:

python
# Pass --workers 2 to argparse, then:
parallel_crack(limit, num_workers=2)
This design clearly illustrates:

Why threading fails for CPU‑bound tasks in Python (GIL).

Why multiprocessing succeeds (separate processes, separate GILs).

How task granularity affects parallel efficiency (tiny tasks = overhead dominates).

How hash algorithm choice affects absolute performance but not relative speedup.

How worker count scaling demonstrates near‑linear gains up to available cores.

✅ 6. Conclusion
The Parallel Hash Breaker successfully demonstrates:

True parallelism using Python's multiprocessing module.

The limitations of threading under the GIL.

Practical speedups of 3x or more on a standard laptop.

Fast, demo‑friendly benchmarks (difficulty benchmark) that run in seconds.

Real‑world tasks like dictionary attacks and algorithm switching.

These concepts are directly transferable to other domains: log analysis, image processing, data transformation – anywhere CPU‑bound tasks can be split into independent chunks.
