# Boda Boda Sacco Asset-Financing & Loan Repayment Management System

Makerere University — CS 1301: Practical Skills Oracle Database
End of Recess Term Practical Examination 2025/2026

---

## 1. Software Requirements

| Component | Version Used |
|---|---|
| Oracle Database | Oracle Database Express Edition (XE) 21c |
| Application Platform | Oracle Application Express (APEX) 22.x |
| Web Service Layer | Oracle REST Data Services (ORDS) |
| Java Runtime | Required by ORDS (JDK 11 or later) |
| SQL Client | Oracle SQL Developer |
| Operating System | Windows 10/11 (tested environment) |
| Browser | Any modern browser (Chrome, Edge, Firefox) |

---

## 2. Installation Procedure

### Step 1 — Install Oracle Database XE 21c
1. Download Oracle Database XE 21c from the official Oracle website.
2. Run the installer and set a password for the `SYS` and `SYSTEM` accounts when prompted.
3. Note the listener port configured during installation (default is 1521; in this project the listener was configured on **1522** — confirm your own port using `lsnrctl status` from Command Prompt).
4. Confirm the pluggable database `XEPDB1` is open:
   ```sql
   sqlplus / as sysdba
   SHOW PDBS;
   ALTER PLUGGABLE DATABASE XEPDB1 OPEN;
   ALTER PLUGGABLE DATABASE XEPDB1 SAVE STATE;
   ```

### Step 2 — Install Oracle APEX and ORDS
1. Download Oracle APEX (22.x or later) from the Oracle APEX downloads page.
2. Run the APEX installation script against the `XEPDB1` pluggable database:
   ```sql
   @apexins.sql SYSAUX SYSAUX TEMP /i/
   ```
3. Configure ORDS (Oracle REST Data Services) to serve APEX over HTTP.
4. Start ORDS in standalone mode from its installation folder:
   ```
   java -jar ords.war standalone
   ```
   Leave this terminal window open — closing it stops the application server. ORDS must be manually restarted this way after every system restart.

### Step 3 — Create the Project Schema
Connected as `SYSTEM`/`SYS` in SQL Developer:
```sql
CREATE USER boda_sacco IDENTIFIED BY YourPassword123;
GRANT CONNECT, RESOURCE, DBA TO boda_sacco;
ALTER USER boda_sacco QUOTA UNLIMITED ON USERS;
```

### Step 4 — Build the Database
1. Connect to SQL Developer as `boda_sacco` on service name `XEPDB1`.
2. Open and run `BodaSacco_XE.sql` (included in this submission) as a script (F5), in order:
   - Table creation (`CREATE TABLE` statements)
   - Sequences
   - Indexes
   - Synonyms
   - Sample data (`INSERT` statements)
   - PL/SQL objects: functions, procedures, cursors, package spec + body, triggers (including the primary-key auto-generation triggers and `TRG_GENERATE_SCHEDULE`)

### Step 5 — Set Up the APEX Workspace
1. Go to `http://localhost:8080/ords/apex`, log in to the `INTERNAL` workspace as `ADMIN`.
2. **Administration → Manage Workspaces → Create Workspace**, name it `BODASACCO`, and select **Use Existing Schema → BODA_SACCO**.
3. Set a workspace administrator username and password.
4. Log out of `INTERNAL` and log back in to the `BODASACCO` workspace.

### Step 6 — Import the Application
1. In the `BODASACCO` workspace, go to **App Builder → Import**.
2. Upload the exported application file (`BodaSacco_APEX_App.sql`, included in this submission).
3. Follow the import wizard, confirming the parsing schema is `BODA_SACCO`.
4. Once imported, click **Run** to launch the application.

---

## 3. Login Credentials

### Application Login (Oracle APEX app — role-based, via USERS table)

| Username | Password | Role |
|---|---|---|
| snakato | Passw0rd1 | Branch Officer |
| pmugisha | Passw0rd1 | Branch Officer |
| gauma | Passw0rd1 | Admin |
| dokello | Passw0rd1 | Auditor |

### Database-Level Accounts (Oracle role-based demo accounts)

| Username | Password | Oracle Role Granted |
|---|---|---|
| branch_officer_demo | Passw0rd1 | ROLE_BRANCH_OFFICER |
| admin_demo | Passw0rd1 | ROLE_ADMIN |
| auditor_demo | Passw0rd1 | ROLE_AUDITOR |

### Project Schema Owner

| Username | Password | Purpose |
|---|---|---|
| boda_sacco | (set during Step 3 above) | Owns all tables, sequences, views, and PL/SQL objects |

### APEX Workspace Administrator

| Workspace | Username | Purpose |
|---|---|---|
| BODASACCO | jaymmbe | APEX developer/administrator login for App Builder |

---

## 4. Notes

- All monetary values are recorded in UGX.
- Passwords are never stored in plain text; the `USERS.password_hash` column stores a SHA-256 hash generated via Oracle's `STANDARD_HASH` function.
- Should the listener port differ from 1522 on a fresh install, update all connection strings accordingly (`localhost:<port>/XEPDB1`).
- A full schema backup (`bodasacco_backup.sql`) generated via SQL Developer's Database Export tool is included in this submission as evidence of backup capability (see Part 11.3 of the project report for details).
