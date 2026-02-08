# Testing & Adding Tests to GitHub

## 1. Run tests locally

### Option A: Using the shell script (recommended)

From the project root:

```bash
# Compile (if needed) and run all tests
./run_tests.sh

# With verbose output (see full expected vs actual on failures)
./run_tests.sh --verbose

# Generate a JSON report
./run_tests.sh --report
```

**Windows (PowerShell):** If `./run_tests.sh` doesn't work, use Option B or run in Git Bash/WSL.

### Option B: Using Python directly

```bash
# Compile the COBOL program first (Linux/macOS/Git Bash)
mkdir -p bin
cobc -x -free -o bin/main src/main.cob

# Run all tests
python3 tests/test_runner.py bin/main

# Run with verbose output
python3 tests/test_runner.py bin/main --verbose

# Run only a specific category (e.g. profiles)
python3 tests/test_runner.py bin/main --test-root tests/fixtures/profiles

# Run only user search tests
python3 tests/test_runner.py bin/main --test-root tests/fixtures/main_menu/user_search
```

**Note:** You need GNU COBOL (`cobc`) installed to compile. On Windows without COBOL, use the **Dev Container** (see README) or run tests on GitHub after pushing.

---

## 2. Add the test files to Git and push to GitHub

### Step 1: See what’s new

```bash
git status
```

You should see new/updated files under `tests/fixtures/`, `tests/WEEK3_TEST_CASES.md`, `tests/TESTING_AND_GITHUB.md`, and possibly `README.md`, `tests/user_inputs.md`.

### Step 2: Stage the test files and docs

```bash
# Stage all new and updated test files and docs
git add tests/fixtures/
git add tests/WEEK3_TEST_CASES.md
git add tests/TESTING_AND_GITHUB.md
git add tests/user_inputs.md
git add README.md
```

Or stage everything:

```bash
git add .
```

### Step 3: Commit

```bash
git commit -m "Add Week 3 test cases: profile viewing and user search

- Profile: view_complete_profile, view_required_fields_only, view_after_edit,
  experience_no_description, education_only_no_experience,
  create_then_view_same_session, single_char_name
- Main menu: invalid choices, navigation sequence, job_then_view
- Login: main menu invalid then exit
- User search: search_found, search_not_found, partial name, edge cases,
  search_empty_name, search_case_sensitive, search_multiple_in_session
- Update WEEK3_TEST_CASES.md and user_inputs.md"
```

### Step 4: Push to GitHub

```bash
# If you use main
git push origin main

# If you use a branch (e.g. develop or feature/week3-tests)
git push origin develop
```

---

## 3. How tests run on GitHub

When you push (or open a pull request) to `main` or `develop`:

1. **Actions** runs the workflow in `.github/workflows/test.yml`.
2. It installs GNU COBOL and Python, compiles `src/main.cob` to `bin/main`, then runs:
   ```bash
   python3 tests/test_runner.py bin/main --test-root tests/fixtures --report test-report.json --verbose
   ```
3. All tests under `tests/fixtures/` (including your new ones) are run.
4. A **test report** is uploaded as an artifact and a summary is shown on the workflow run.

**Where to look:**

- **GitHub repo** → **Actions** tab → select the latest run.
- **Summary** shows total/passed/failed and lists failed tests.
- **Run all tests** step shows the full log and any diffs.

**Expected until “Find someone you know” is implemented:**  
User search tests (e.g. `search_found`, `search_not_found`) will **fail** because the app still shows “under construction”. Profile and other tests should **pass**.

---

## 4. Quick reference

| Goal                    | Command / place |
|-------------------------|------------------|
| Run all tests locally   | `./run_tests.sh` or `python3 tests/test_runner.py bin/main` |
| Run only profile tests  | `python3 tests/test_runner.py bin/main --test-root tests/fixtures/profiles` |
| Run only search tests   | `python3 tests/test_runner.py bin/main --test-root tests/fixtures/main_menu/user_search` |
| Add and push new tests  | `git add tests/ README.md` → `git commit -m "..."` → `git push origin main` |
| See results on GitHub   | Repo → **Actions** → latest workflow run → **Summary** and **Run all tests** |
