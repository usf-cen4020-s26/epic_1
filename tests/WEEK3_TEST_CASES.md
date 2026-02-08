# Week 3 Test Cases - Profile Viewing & User Search

This document describes all test cases added for **Epic #3: User Search & Connections (Part 1)** and enhanced profile viewing. Use this for Jira ticket creation, test execution, and bug reporting.

---

## Test Case Summary

### Core Week 3 Tests

| Category | Test Name | Type | Description |
|----------|-----------|------|--------------|
| Profile Viewing | view_complete_profile | Positive | View profile with all fields (required + About Me + Experience + Education) |
| User Search | search_found | Positive | Search for existing user by exact full name - displays profile |
| User Search | search_not_found | Negative | Search for non-existent user - shows "not found" message |
| User Search | search_partial_name | Negative | Search with first name only - no match (exact match required) |
| User Search | search_partial_last_name | Negative | Search with last name only - no match |
| User Search | search_edge_long_name | Edge | Search for user with long/multi-part name |
| User Search | search_edge_similar_names | Edge | Search "John Doe" when "John Doe" and "John Doe2" exist - finds exact match only |

### Extended Coverage Tests

| Category | Test Name | Type | Description |
|----------|-----------|------|--------------|
| Profile | view_required_fields_only | Positive | View profile with only required fields (no About Me, Experience, Education) |
| Profile | view_after_edit | Positive | Edit profile then view - verify updated data displays; Part 3 verifies persistence |
| Profile | experience_no_description | Positive | One experience entry with description skipped - view omits Description line |
| Profile | education_only_no_experience | Positive | Profile with Education only, no Experience - view shows Education section only |
| Profile | create_then_view_same_session | Positive | Create profile then View (option 2) in same run without logout |
| Profile | single_char_name | Boundary | First name "A", last name "B" - verify display |
| Main Menu | invalid_zero_then_logout | Negative | Post-login invalid choices (0, 7, 9) then valid 6 to logout |
| Main Menu | view_then_find_then_skills_logout | Flow | Create profile, View, Find someone (construction), Skills menu, Go Back, Logout |
| Main Menu | job_search_then_view_profile | Flow | Job search (construction) then View Profile - menu order |
| Login | invalid_then_exit | Negative | Pre-login invalid choices (0, 4, 5) then 3 to exit |
| User Search | search_empty_name | Negative | Search with blank/empty input - expect not found |
| User Search | search_case_sensitive | Negative | Search "JANE SMITH" when user is "Jane Smith" - no match (exact/case-sensitive) |
| User Search | search_multiple_in_session | Positive | Two searches (find Beta Beta, find Alpha Alpha) then search nonexistent (Gamma Gamma) |

---

## 1. Enhanced Self-Profile Viewing

### view_complete_profile (Multi-part)

**Purpose**: Verify "View My Profile" displays ALL profile information reliably and in a readable format.

**Part 1**: 
- Create account `ViewProfileUser`
- Create full profile with: First Name, Last Name, University, Major, Graduation Year, About Me, 2 Experience entries, 1 Education entry
- Logout

**Part 2**:
- Login as `ViewProfileUser`
- Select "View My Profile" (option 2)
- **Expected**: Profile displays with all fields:
  - Name, University, Major, Graduation Year
  - About Me section
  - Experience section with Title, Company, Dates, Description for each entry
  - Education section with Degree, University, Years for each entry
  - Clear separator (--------------------) at end

**Jira User Story**: "As a logged-in user, I want my profile to be displayed in an easy-to-read format."

---

## 2. User Search - Positive Tests

### search_found (Multi-part)

**Purpose**: Successfully search for and find existing user by exact full name.

**Part 1**:
- Create `JohnDoeUser` with profile (John Doe)
- Create `JaneSmithUser` with profile (Jane Smith)
- Exit

**Part 2**:
- Login as `JohnDoeUser`
- Select "Find someone you know" (option 4)
- Enter search: `Jane Smith`
- **Expected**: 
  - Prompt: "Enter the full name of the person you are looking for:"
  - Search input echoed to output
  - Full profile of Jane Smith displayed
  - Return to main menu

**Jira User Story**: "As a user performing a search, I want to see the profile of the user if a match is found."

---

## 3. User Search - Negative Tests

### search_not_found (Multi-part)

**Purpose**: Search for non-existent user returns appropriate message.

**Part 2** (Part 1 creates user):
- Login, select Find someone (4)
- Enter: `Nobody Exists Here`
- **Expected**: "No one by that name could be found." then return to main menu

**Jira User Story**: "As a user performing a search, I want to be informed if no user matches my search query."

### search_partial_name (Multi-part)

**Purpose**: Partial name (first name only) does NOT match - exact match required.

**Part 2**:
- Search for `Robert` when user "Robert Williams" exists
- **Expected**: "No one by that name could be found."

### search_partial_last_name (Multi-part)

**Purpose**: Partial name (last name only) does NOT match.

**Part 2**:
- Search for `Thompson` when user "Michael Thompson" exists
- **Expected**: "No one by that name could be found."

---

## 4. User Search - Edge Cases

### search_edge_long_name (Multi-part)

**Purpose**: Users with long/multi-word names can be found by exact search.

**Setup**: User "Alexander Christopher Montgomery The Third"
**Search**: `Alexander Christopher Montgomery The Third`
**Expected**: Profile displayed

### search_edge_similar_names (Multi-part)

**Purpose**: Exact match only - "John Doe" finds "John Doe" but not "John Doe2".

**Setup**: Two users - "John Doe" (University A) and "John Doe2" (University B) - last name "Doe2"
**Search**: `John Doe`
**Expected**: Profile of "John Doe" (University A) displayed, NOT "John Doe2"

---

## 5. I/O Verification

All tests verify that:
- **Input**: All menu selections and search queries are read from INPUT.TXT
- **Output**: Program output is identical in console (OUTPUT.TXT) and screen
- The test runner compares actual OUTPUT.TXT against expected files

**Jira User Stories**:
- "As a tester, I want the program to read all user inputs for profile viewing and search from a file so I can automate testing."
- "As a tester, I want the program to write all screen output related to profile viewing and search to a file so I can easily verify results."

---

## Running the Tests

```bash
# Run all tests (including Week 3)
python3 tests/test_runner.py bin/main

# Run only profile viewing tests
python3 tests/test_runner.py bin/main --test-root tests/fixtures/profiles/view_complete_profile

# Run only user search tests
python3 tests/test_runner.py bin/main --test-root tests/fixtures/main_menu/user_search

# Verbose output (see full diff on failures)
python3 tests/test_runner.py bin/main --verbose
```

---

## Note for Programmers

The **user search** tests will **FAIL** until the "Find someone you know" option is implemented. The expected output files define the correct behavior. Key implementation details:

- Replace "under construction" message with search flow
- Prompt: "Enter the full name of the person you are looking for:"
- Exact match: Compare `"[First] [Last]"` against stored `"[First] [Last]"` (trimmed)
- Found: Display full profile (reuse 7100-VIEW-PROFILE display logic for another user)
- Not found: "No one by that name could be found."

---

## 6. Extended Test Cases (All Alternatives)

### Profile Viewing Alternatives

- **view_required_fields_only**: User skips About Me and adds no Experience/Education. View must show only Name, University, Major, Graduation Year and separator (no optional sections).
- **view_after_edit**: Parts 1–3. Create profile → edit profile (change name, add experience) → view in same session → logout; Part 3: login again and view to verify persistence of edited data.
- **experience_no_description**: One experience with description left blank. View must show Title, Company, Dates but no "Description:" line.
- **education_only_no_experience**: DONE for experience; one education entry. View must show Education section only.
- **create_then_view_same_session**: Create profile, then immediately select View My Profile (2) in same session.
- **single_char_name**: First name "A", last name "B". Verifies minimal name length and display.

### Main Menu & Navigation

- **invalid_zero_then_logout**: After login, enter 0, 7, 9 (invalid), then 6 (logout). Verifies "Invalid choice. Please try again." and menu re-display.
- **view_then_find_then_skills_logout**: Create profile → View → Find someone (under construction) → Learn skill (5) → Go Back (6) → Logout (6).
- **job_search_then_view_profile**: Create profile → Job search (3, under construction) → View Profile (2). Verifies correct menu flow.
- **invalid_then_exit**: From main (pre-login) enter 0, 4, 5, then 3 to exit.

### User Search Alternatives

- **search_empty_name**: Search with blank line. Expected: "No one by that name could be found."
- **search_case_sensitive**: User "Jane Smith"; search "JANE SMITH". Expected: no match (exact match is case-sensitive).
- **search_multiple_in_session**: Part 1: create Alpha Alpha and Beta Beta. Part 2: search "Beta Beta" (found), "Alpha Alpha" (found), "Gamma Gamma" (not found). Verifies multiple searches in one session.
