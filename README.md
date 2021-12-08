# requirements-perf
Demonstrate Python requirements performance challenges

Running `./pants test ::` here is very clunky and slow, for a few reasons:

1) Resolving pants-requirements.txt takes about 8 minutes (on my macbook) cold, or 4 minutes with a fully warm pip http and wheel cache.
2) Extracting requirements and building pytest_runner.pex for each target takes about another 8 minutes.
3) Making a tiny change to pants-requirements.txt (even just a whitespace change) retriggers all that work.
4) The dynamic UI is jerky and frequently freezes, much more so than we see in other repos, despite the number of tests not being unusual.
5) Because of contention, the dynamic UI shows trivial work like determining python imports from a single file appearing to take several seconds.

Things improve somewhat with the new `--python-run-against-entire-lockfile` option. Running `./pants --python-run-against-entire-lockfile test ::`
cuts out all the subsetting and pytest_runner.pex building. But resolving pants-requirements.txt still takes 4 minutes.

Contrast this with a pre-pants user workflow, where you build the full venv once (4-8 minutes), but then run everything against that full venv. 
On changes to pants-requirements.txt you rerun pip/poetry against the existing venv, so you get incremental builds. For example, 
a whitespace change triggers only 3 seconds of work, long enough for pip to verify that all the requirements are already installed 
in the existing venv.

We are no worse than pip for a full rebuild (as one can show by running `pip install -r pants-requirements.txt` in a venv).
But we are much, much worse than the incremental workflow described.

So the challenge is to meet incremental venv building performance...
