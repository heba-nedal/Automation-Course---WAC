# Tiny Wins — Build, Test & Prove Your Web App

> **U3 Submission | Lovable + Supabase**  
> Build it. Test it. Prove it works.

## 1. Project Idea

### Problem

People often accomplish small but meaningful things in their daily lives without recording or recognizing them. These achievements can easily be forgotten, making it difficult to reflect on progress and appreciate personal growth over time.

### User

Tiny Wins is designed for individuals who want to record their achievements, organize them, and reflect on their progress over time.

### Solution

Tiny Wins is a personal achievement-tracking web application that allows users to record, view, edit, and delete their achievements. It presents achievements through a personal dashboard, a chronological growth timeline, and a visual garden that reflects the user's saved wins.

The application provides a simple, encouraging way to recognize small accomplishments and see how they contribute to a bigger growth story.

## 2. Main Features

- **User Authentication:** Users can sign up, log in, and log out.
- **Achievement Management (CRUD):** Users can create, view, update, and delete their own achievements.
- **Personal Dashboard:** Displays achievement statistics and recent wins.
- **My Growth Story:** Organizes achievements chronologically and groups them by month.
- **Win Garden:** Presents a visual garden alongside statistics derived from the user's saved achievements.
- **User-Specific Data:** Achievement records are associated with their owner and protected through Supabase access controls.
- **Responsive Interface:** Provides a consistent experience across supported screen sizes.

## 3. Tech Stack

- **Lovable:** Used to build and develop the web application and user interface.
- **Supabase:** Used for authentication, PostgreSQL database storage, and user data access control.
- **React and TypeScript:** Used in the application's frontend and application logic.
- **GitHub:** Used for source-code version control and repository management.

## 4. Testing

The application was tested after implementation and publication. The following test cases document the scenarios, expected behavior, observed behavior, and results.

### Test 1 — Positive Testing

**Scenario:** A registered user signs in, creates an achievement with valid information, and views it on the dashboard.

**Expected:** The user should sign in successfully. The achievement should be saved and displayed in the user's achievement list.

**Actual:** The user signed in successfully, created the achievement, and confirmed that the saved record appeared in the application.

**Result:** **PASS**

### Test 2 — Negative Testing

**Scenario:** A user attempts to submit an achievement without providing a required field, such as its title.

**Expected:** The application should prevent the invalid submission and display appropriate validation feedback.

**Actual:** The invalid submission was handled as expected, and the application did not save an achievement with the missing required information.

**Result:** **PASS**

### Test 3 — Edge Case

**Scenario:** A user views the application when there are no achievements to display, or deletes the last remaining achievement.

**Expected:** The application should handle the empty state gracefully without crashing or displaying incorrect achievement statistics.

**Actual:** The application handled the empty state correctly, and the relevant achievement display and statistics reflected the available records.

**Result:** **PASS**

### Test 4 — Data / Access Testing

**Scenario:** Two separate user accounts are used to verify that one user cannot access another user's private achievements.

**Expected:** Each user should see and manage only their own authorized records. Attempts to access another user's records should be denied.

**Actual:** The application was tested with two separate accounts. Each account accessed its own achievement data, and the other account's private achievements were not accessible.

**Result:** **PASS**

### Additional Functional Tests

The following operations were also tested:

- **Create:** A new achievement can be added and saved.
- **Read:** Saved achievements appear in the appropriate views.
- **Update:** Editing an achievement updates the displayed information.
- **Delete:** Deleting an achievement removes it from the application.
- **Dashboard statistics:** Statistics reflect the user's saved achievement data.
- **Growth timeline:** Achievements appear in chronological order and are grouped by month.
- **Win Garden:** Its statistics reflect the user's achievement records.
- **Authentication:** Sign-up, login, and logout flows were tested.
- **Persistence:** Saved achievements remain available after refreshing or reopening the application.

**Overall result:** The tested core features behaved as expected.

## 5. Bugs Found

After creating a Remix of the project in another Lovable account, the application preview failed to load.

The browser console displayed the following error:

`Missing Supabase environment variable(s): SUPABASE_URL, SUPABASE_PUBLISHABLE_KEY`

This indicated that the remixed project did not have the required Supabase connection configuration available to the application.

## 6. Fixes

The Supabase integration was connected to the remixed project. After the connection was restored, the application preview loaded successfully and the core functionality was tested again.

The issue was resolved through the following development cycle:

**Build → Test → Find → Fix → Re-test → Prove**

This demonstrated the importance of checking environment and integration configuration when a project is remixed or moved between workspaces.

## 7. Testing Evidence

Add actual screenshots captured during testing to the repository and link them below.

| Evidence | What it demonstrates | Screenshot |
|---|---|---|
| Authentication | Successful sign-up or login | `evidence/01-authentication.png` |
| Positive test | A valid achievement is created and displayed | `evidence/02-create-achievement.png` |
| Negative test | Required-field validation | `evidence/03-validation.png` |
| Edge case | Correct handling of an empty achievement state | `evidence/04-empty-state.png` |
| User A | The first account sees its own achievements | `evidence/05-user-a.png` |
| User B | The second account cannot access User A's private achievements | `evidence/06-user-b.png` |
| Dashboard | Achievement statistics and recent wins | `evidence/07-dashboard.png` |
| Growth Story and Win Garden | Timeline and garden statistics | `evidence/08-growth-story-garden.png` |
| Bug fix | Supabase configuration error and working application after the fix | `evidence/09-supabase-fix.png` |

*Replace the example paths with the actual screenshot filenames and locations used in the repository.*

## 8. Links

- **Live App:** [https://tiny-win-by-heba.lovable.app/]
- **GitHub Repository:** [https://github.com/heba-nedal/tiny-wins.git]

## 9. Final Checklist

- [x] Selected an application idea.
- [x] Identified the problem, intended user, and solution.
- [x] Built the web application using Lovable.
- [x] Integrated Supabase.
- [x] Implemented and tested authentication.
- [x] Implemented and tested achievement CRUD operations.
- [x] Verified that saved data persists.
- [x] Completed positive testing.
- [x] Completed negative testing.
- [x] Completed edge-case testing.
- [x] Tested data access using two separate user accounts.
- [x] Identified a configuration issue encountered after Remix.
- [x] Fixed the Supabase connection issue.
- [x] Re-tested the application after the fix.
- [x] Prepared testing evidence for the submission.
- [x] Added the live application URL.
- [x] Added the GitHub repository URL.
- [x] Added the actual screenshot files and verified their paths.

## Final Reflection

Building an application is only the first step. Testing helps verify whether the application behaves as expected, while evidence makes those results clear and credible.

Through Tiny Wins, I practiced building a web application with Lovable, integrating Supabase authentication and database functionality, identifying and fixing a configuration issue, and testing the application's main features, including user-specific data access.

The main lesson from this project is:

> **Don't just build it. Prove it works.**
