# requirements-perf
Demonstrate Python requirements performance challenges

Running `./pants test ::` here is clunky for a few reasons:

1) The dynamic UI is jerky and sometimes frozen, which gives a bad impression.
2) Resolving pants-requirements.txt takes about 4 minutes (on my macbook) even with a fully warm pip http and wheel cache.
3) Making a tiny change to pants-requirements.txt (even just a whitespace change) retriggers that 4 minutes of work.
4) Things are faster with `--intransitive`, although I'm not sure why, given the warm http cache.

We are no worse than pip for a full rebuild (as one can show by running `pip install -r pants-requirements.txt` in a venv.
But the users who provided this example are used to incrementally rebuilding an existing local venv, rather than creating a new isolated one.
So a whitespace change triggers only 3 seconds of work, long enough for pip to verify that all the requirements are already installed 
in the existing venv.

So the challenge is to meet incremental venv building performance...
