# Triage

> Replace every `TODO`. One section per defect, nine in total.
> For each one: what you saw, why it happened, what you changed, and how you
> proved it is fixed. Paste real output — not a description of output.

## 1. `scripts/ingest.sh` is not executable
**Symptom:** Running `./scripts/ingest.sh data/orders.csv` failed with a permission error.

```bash
PS C:\Users\DE_004\Downloads\Temp> ./scripts/ingest.sh data/orders.csv
./scripts/ingest.sh : The term './scripts/ingest.sh' is not recognized as the name of a 
cmdlet, function, script file, or operable program.
```

**Cause:** The file mode on `scripts/ingest.sh` was not marked executable. In Git, the index was missing the execute bit, so the script could not run as a standalone program.

**Fix:** Set the executable bit on the file and record it in Git:

```bash
git update-index --chmod=+x scripts/ingest.sh
chmod +x scripts/ingest.sh
```

**Proof:** After the fix, the mode and Git index both show it is executable:

```bash
$ git ls-files --stage -- scripts/ingest.sh
100755 156ba558ee754e641b1fc0f8221a0c2ee01cfcd1 0       scripts/ingest.sh

$ ls -l scripts/ingest.sh
-rwxrwxrwx 1 de_004 de_004 764 Sep  9 16:55 scripts/ingest.sh
```

**Why `git update-index --chmod=+x` was also needed:** Changing the file mode in the working tree only fixes the current checkout. Without `git update-index --chmod=+x`, the repository still records the file as non-executable and everyone else cloning the repo would get the same broken permission state.

## 2. Unquoted `$1` in `scripts/ingest.sh`
**Symptom:** A file path containing spaces caused the shell to fail with a binary-operator error.

```bash
$ cp data/orders.csv "data/march orders.csv"
$ bash scripts/ingest.sh "data/march orders.csv"
./scripts/ingest.sh: line 10: [: data/march: binary operator expected
```

**Cause:** The script checked `[ ! -f $1 ]` without quoting `$1`. When the path was `data/march orders.csv`, Bash split it into two arguments and the test expression became invalid.

**Fix:** Quote the argument in the `if` test:

```bash
if [ ! -f "$1" ]; then
```

**Proof:** After the fix, the same path works successfully:

```bash
$ cp data/orders.csv "data/march orders.csv"
$ bash scripts/ingest.sh "data/march orders.csv"
staged march orders.csv — 209 data rows
```

## 3. Dockerfile copies source before installing dependencies
**Symptom:** A one-line Python change triggered a full dependency reinstall during docker build; the build took about 4 seconds instead of reusing the cache.

```bash
# before fix
=> [2/2] RUN pip install --no-cache-dir -r api/requirements.txt
   4.0s
```

**Cause:** The Dockerfile copied the whole repository before installing dependencies. Because the source files changed on every code edit, Docker invalidated the layer that contained the Python package install and had to rerun `pip install` every time.

**Fix:** Install Python dependencies before copying the application source. This keeps the dependency layer cached and rebuilds only when requirements change.

```dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY api/requirements.txt ./api/requirements.txt
COPY ingest/requirements.txt ./ingest/requirements.txt
RUN pip install --no-cache-dir -r api/requirements.txt -r ingest/requirements.txt

COPY . .
```

**Proof (build output, before and after):**

```bash
# project-provided baseline (as shipped)
=> [2/2] RUN pip install --no-cache-dir -r api/requirements.txt
   4s

# after fix (rebuild with no source change)
=> CACHED [2/2] RUN pip install --no-cache-dir -r api/requirements.txt -r ingest/requirements.txt
```

The important change is that Docker now caches the dependency install layer; the source layer is copied last, so a small app edit does not force a reinstall of Python packages.

## 4. No `.dockerignore`
**Symptom:** TODO
**Cause:** TODO
**Fix:** TODO
**Proof (context size, before and after):** TODO

## 5. API key committed to the repository
**Symptom:** TODO
**Cause:** TODO
**Fix:** TODO
**Is the key gone now that you deleted the line?** TODO
**What would you have to do in real life?** TODO

## 6. `requests` call with no timeout
**Symptom:** TODO
**Cause:** TODO
**Fix:** TODO
**Why a hang is worse than an error:** TODO

## 7. Missing index on `orders.customer_id`
**Symptom:** TODO
**Plan before:** TODO
**Plan after:** TODO
**Timings, three runs each:** TODO
**Why the planner changed its mind:** TODO

## 8. SSH open to `0.0.0.0/0`
**Symptom:** TODO
**Why nothing warned you:** TODO
**Fix:** TODO
**What an attacker does with this:** TODO

## 9. `count` instead of `for_each`
**Plan with `count`, after removing `staging`:** TODO
**Plan with `for_each`, same edit:** TODO
**Why this is the most dangerous defect in the list:** TODO
