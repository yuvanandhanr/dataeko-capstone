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
**Symptom:** TODO
**Cause:** TODO
**Fix:** TODO
**Proof:** TODO

## 3. Dockerfile copies source before installing dependencies
**Symptom:** TODO
**Cause:** TODO
**Fix:** TODO
**Proof (build output, before and after):** TODO

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
