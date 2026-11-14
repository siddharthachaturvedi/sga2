# Question 2: Login system test case design

These are designed test cases; no login application was provided or executed.

Test fixtures: registered accounts user1 / pass123 and user6 / pass12. unknown_user is not registered. These are fictional test credentials.

Unless a case states otherwise, start with an unlocked account and zero consecutive failures. Click Login for each submission. Cases are independent unless their preconditions explicitly establish prior failures.

Proposed acceptance messages are specified below because the brief does not prescribe exact wording.

Assumptions: A1: only rejected credential checks count toward lockout; empty/short input fails validation before authentication. A2: successful authentication resets the consecutive-failure count. Lock duration, unlock method, whitespace handling, case sensitivity and password complexity are unspecified.

Verify the failure counter through supported application state or observable lockout behavior; the counter need not be visible in the UI.

| Test Case ID | Test Scenario | Input Data | Expected Output |
|---|---|---|---|
| TC_01 | Valid credentials | Username = user1; password = pass123. | Login succeeds. Display "Login successful." Create an authenticated session. |
| TC_02 | Empty username | Username = empty; password = pass123. | Reject submission. Display "Username is required." No authenticated session. |
| TC_03 | Empty password | Username = user1; password = empty. | Reject submission. Display "Password must contain at least 6 characters." No authenticated session. |
| TC_04 | Both inputs empty | Username = empty; password = empty. | Reject submission. Display "Username is required." and "Password must contain at least 6 characters." |
| TC_05 | Password below minimum | Username = user1; password = abc12 (5 characters). | Reject submission. Display "Password must contain at least 6 characters." No authenticated session. |
| TC_06 | Valid password at minimum | Username = user6; password = pass12 (6 characters). | Login succeeds. Display "Login successful." Create an authenticated session. |
| TC_07 | Unknown username | Username = unknown_user (not registered); password = pass123. | Deny login. Display "Invalid username or password." No authenticated session. |
| TC_08 | First incorrect password | Username = user1; password = wrong12; failure count starts at 0. | Deny login. Display "Invalid username or password." Count becomes 1; account remains unlocked. |
| TC_09 | Second consecutive failure | Precondition: user1 has 1 consecutive failure. Submit user1 / wrong12. | Deny login. Display "Invalid username or password." Count becomes 2; account remains unlocked. |
| TC_10 | Third consecutive failure | Precondition: user1 has 2 consecutive failures. Submit user1 / wrong12. | Deny login and lock the account immediately. Display "Account locked after 3 consecutive failed login attempts." |
| TC_11 | Correct credentials after lockout | Precondition: user1 is locked following 3 failures. Submit user1 / pass123. | Deny login despite correct credentials. Display "Account is locked." Account remains locked; no authenticated session. |
| TC_12 | Success after two failures | Precondition: user1 is unlocked with 2 consecutive failures. Submit user1 / pass123. | Login succeeds. Display "Login successful." Reset the consecutive-failure count to 0 (assumption A2). |
| TC_13 | Success interrupts a failure sequence | Submit user1 / wrong12 twice, then user1 / pass123. Sign out; submit user1 / wrong12 once. | First two attempts fail; third succeeds. Final attempt displays "Invalid username or password." Count is 1 and account is unlocked (A2). |
| TC_14 | Six-character invalid password | Username = user1; password = bad123 (6 characters; not the registered password). | Length validation passes, but authentication fails. Display "Invalid username or password." No authenticated session. |

Coverage: username required: TC_02, TC_04; password minimum: TC_03-TC_06, TC_14; valid login: TC_01, TC_06; invalid credentials: TC_07-TC_10, TC_14; lockout at 3 failures: TC_08-TC_11; consecutive-failure interpretation: TC_12-TC_13.
