# oracle21c-pluggable-database-And-OEM_project

# Oracle Pluggable Database (PDB) Configuration and OEM Express Setup

## Project Overview
This project demonstrates the setup and configuration of an **Oracle 21c Pluggable Database (PDB)** named `AG_PDB_27320`.  
It covers all stages from creation and user configuration to connecting via **Oracle Enterprise Manager (OEM) Express** for monitoring and management.  
The project also documents key troubleshooting steps, such as port conflicts and listener issues.

---

## System Environment
- **Database Version:** Oracle Database 21c (64-bit)
- **Operating System:** Windows
- **Tools Used:**
  - SQL*Plus
  - Oracle Listener Service
  - Oracle Enterprise Manager (OEM) Express
- **Pluggable Database Name:** `AG_PDB_27320`
- **Users and Credentials:**
  - `SYS / admin` — SYSDBA Role
  - `SYSTEM / 1234567890` — Normal Role
- **OEM Express URL:** `https://localhost:8444/em`

---

## Configuration Steps

### 1. Create the Pluggable Database (PDB)
```sql
ALTER PLUGGABLE DATABASE AG_PDB_27320 OPEN;

Step 2: Verify Database Status
-- Check PDB status
SELECT NAME, OPEN_MODE FROM V$PDBS;


Expected Output:

NAME             OPEN_MODE
---------------  ----------
PDB$SEED         READ ONLY
AG_PDB_27320     READ WRITE


Explanation:
The query verifies that the PDB is created and currently open for use.

Step 3: Configure User Accounts
-- Connect to the PDB
ALTER SESSION SET CONTAINER = AG_PDB_27320;

-- Configure users and passwords
ALTER USER SYSTEM IDENTIFIED BY "1234567890";
ALTER USER SYS IDENTIFIED BY "admin";

-- Unlock accounts
ALTER USER SYSTEM ACCOUNT UNLOCK;
ALTER USER SYS ACCOUNT UNLOCK;


Explanation:
SYSTEM and SYS accounts were configured for OEM and SQL*Plus access. Both accounts were unlocked and granted appropriate privileges.

Step 4: Check and Configure OEM Express Ports
-- Check current ports
SELECT DBMS_XDB_CONFIG.GETHTTPPORT() AS HTTP_PORT FROM dual;
SELECT DBMS_XDB_CONFIG.GETHTTPSPORT() AS HTTPS_PORT FROM dual;

-- Output Example:
-- HTTP_PORT: 8080
-- HTTPS_PORT: 0


Explanation:
The result showed HTTPS port as 0, meaning OEM Express was not yet enabled for this PDB.

Step 5: Set Up OEM Express Secure Port
-- Set HTTPS Port for OEM Express
EXEC DBMS_XDB_CONFIG.SETHTTPSPORT(8444);

-- Disable HTTP for security
EXEC DBMS_XDB_CONFIG.SETHTTPPORT(0);

-- Allow listener local access
EXEC DBMS_XDB_CONFIG.SETLISTENERLOCALACCESS(FALSE);

-- Confirm configuration
SELECT DBMS_XDB_CONFIG.GETHTTPSPORT() AS HTTPS_PORT FROM dual;


Expected Output:

HTTPS_PORT
----------
8444


Explanation:
OEM Express is now accessible through HTTPS port 8444, ensuring secure, conflict-free access for the AG_PDB_27320 PDB.
