# Personal Expense Tracker - Project File Audit Report

A comprehensive review of the files in the **Personal Expense Tracker** project has been conducted. Below is a detailed breakdown of the project's structure, the function of each file, and a list of critical bugs, discrepancies, and unimplemented features that prevent the application from working as intended.

---

## 📂 Project Structure Overview

The project is structured as a standard PHP-based web application with CSS styling, JavaScript libraries, and a local uploads folder.

| File / Folder Path | Type | Description / Responsibility |
| :--- | :--- | :--- |
| [`config.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/config.php) | PHP File | Database connection configuration (MySQL). |
| [`session.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/session.php) | PHP File | Restricts page access to logged-in users, initializes session variables. |
| [`index.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/index.php) | PHP File | Main user dashboard containing summaries and Chart.js visualisations. |
| [`login.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/login.php) | PHP File | User authentication portal. |
| [`logout.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/logout.php) | PHP File | Destroys session variables and redirects to the login portal. |
| [`register.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/register.php) | PHP File | Registration portal for new users. |
| [`add_expense.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/add_expense.php) | PHP File | Handles creating, editing, and deleting individual expenses. |
| [`manage_expense.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/manage_expense.php) | PHP File | Lists user expenses with sorting capabilities. |
| [`expensereport.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/expensereport.php) | PHP File | Generates datewise, monthwise, and yearwise expense reports. |
| [`profile.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/profile.php) | PHP File | Form to update user profile details (first name, last name, profile image). |
| [`change_password.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/change_password.php) | PHP File | Form to change user passwords. |
| [`PersonalExpenseTracker.sql`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/PersonalExpenseTracker.sql) | SQL Script | Contains the database schema and table definitions for MySQL. |
| [`css/`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/css) | Folder | Styling sheets (Bootstrap v4.3.1 and custom CSS rules). |
| [`js/`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/js) | Folder | JavaScript assets (jQuery, Popper, Chart.js, Bootstrap, Feather Icons). |
| [`uploads/`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/uploads) | Folder | Directory to store uploaded user profile pictures. |

---

## 🔍 Detailed Analysis & Core Issues Found

During the file audit, several critical bugs, missing elements, and vulnerabilities were identified.

### 1. 🛑 Critical Connection Name Discrepancy
* **Affected Files:** `config.php` vs. *all other PHP files*.
* **Details:** `config.php` instantiates the database connection as `$conn`:
  ```php
  $conn = mysqli_connect("localhost", "root", "root", "dailyexpense");
  ```
  However, all other files that interact with the database (e.g., `session.php`, `login.php`, `register.php`, `index.php`, etc.) reference the variable as `$con`:
  ```php
  $result = mysqli_query($con, $expenses); // Or $con->query($sql);
  ```
* **Impact:** Every page that queries the database fails immediately with an undefined variable warning or fatal error, rendering the application completely unusable.

### 2. 🗄️ Database Schema Mismatch
* **Affected Files:** [`PersonalExpenseTracker.sql`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/PersonalExpenseTracker.sql) vs. [`profile.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/profile.php).
* **Details:** [`profile.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/profile.php) attempts to update/insert a profile picture path into the `users` table:
  ```php
  $query = "UPDATE users SET profile_path = '$name' WHERE user_id='$userid'";
  ```
  However, the table schema for `users` in [`PersonalExpenseTracker.sql`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/PersonalExpenseTracker.sql) does not contain a `profile_path` column:
  ```sql
  CREATE TABLE `users` (
    `user_id` int(11) NOT NULL,
    `firstname` varchar(50) NOT NULL,
    `lastname` varchar(25) NOT NULL,
    `email` varchar(50) NOT NULL,
    `password` varchar(50) NOT NULL
  ) ...
  ```
* **Impact:** Profile image uploads fail due to database errors.

### 3. 🖼️ Broken Image Upload Interface
* **Affected Files:** [`profile.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/profile.php).
* **Details:** The HTML form designed for uploading profile pictures is completely empty:
  ```html
  <form class="form" method="post" action="" enctype='multipart/form-data'>
      <div class="text-center mt-3">
          <img src="uploads\default_profile.png" class="text-center img img-fluid rounded-circle avatar" width="120" alt="Profile Picture">
      </div>
      <div class="input-group col-md mb-3 mt-3">
          <!-- File input and upload button are missing here! -->
      </div>
  </form>
  ```
* **Impact:** Users are unable to choose or upload files through the user interface.

### 4. 🔗 Hardcoded & Undefined Profile Picture Variables
* **Affected Files:** [`index.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/index.php), [`profile.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/profile.php), [`change_password.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/change_password.php).
* **Details:** 
  - The sidebars in `index.php` and `profile.php` use a hardcoded image source: `src="uploads\default_profile.png"`, which ignores any uploaded image.
  - In `change_password.php`, the navbar tries to fetch `$userprofile`: `src="<?php echo $userprofile ?>"`
  - However, `$userprofile` is never defined or retrieved in [`session.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/session.php).

### 5. 🔑 Unimplemented Password Change Logic
* **Affected Files:** [`change_password.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/change_password.php).
* **Details:** While the user interface renders forms to input the current password, new password, and confirmation, the script contains absolutely no PHP code to process the form when the `updatepassword` button is clicked. It only runs a query to fetch expenses:
  ```php
  <?php
  include("session.php");
  $exp_fetched = mysqli_query($con, "SELECT * FROM expenses WHERE user_id = '$userid'");
  ?>
  ```
* **Impact:** The "Change Password" feature is completely non-functional.

### 6. 📁 Missing Scripts & Dead References
* **Affected Files:** [`register.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/register.php).
* **Details:** The footer of `register.php` includes a script tag pointing to `js/profile-picture.js`:
  ```html
  <script src="js/profile-picture.js"></script>
  ```
  However, this file does not exist in the `js` directory.

---

## 🛠️ Recommended Action Plan

To get the application working properly, the following changes are recommended:

1. **Fix Connection Variable Name:**
   Change `$conn = mysqli_connect(...)` to `$con = mysqli_connect(...)` in [`config.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/config.php).
2. **Update Database Schema:**
   Modify [`PersonalExpenseTracker.sql`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/PersonalExpenseTracker.sql) to add a `profile_path` column to the `users` table:
   ```sql
   ALTER TABLE `users` ADD `profile_path` varchar(255) DEFAULT NULL;
   ```
3. **Fix Profile Upload:**
   - Add `<input type="file" name="file" class="file-upload">` and `<button type="submit" name="but_upload">` inside [`profile.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/profile.php).
   - Retrieve `profile_path` in [`session.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/session.php) and dynamically populate the sidebar/navbar images with the user's custom photo (defaulting to `uploads/default_profile.png` if empty).
4. **Implement Password Change Logic:**
   Add backend validation and database queries in [`change_password.php`](file:///c:/antigravity_miniproject/Personal-Expense-Tracker-main/change_password.php) to verify the current password and update the password column using `md5()` hashing (or ideally upgrade password hashing to secure `password_hash()` methods).
