# Agency Procurement System (ERPNext v15)

A simplified, white-labeled Procurement System built on ERPNext v15.

## 1. Prerequisites
Before starting, ensure you have the following installed:
*   **Docker Desktop**: [Download Here](https://www.docker.com/products/docker-desktop/) (Ensure it is running).
*   **PowerShell** (Windows) or Terminal (Mac/Linux).

## 2. Quick Start
Follow these exact steps to launch the system.

### Step 1: Start the Infrastructure
Open your terminal in this folder and run:
```powershell
docker compose up -d
```
*Wait about 30-60 seconds for the database (MariaDB) to initialize.*

### Step 2: Initialize the Site
Run this command to create the site and install ERPNext. This only needs to be done **ONCE**.
```powershell
docker compose exec backend bench new-site localhost --no-mariadb-socket --db-host db --admin-password admin --db-root-password admin --install-app erpnext --set-default --mariadb-user-host-login-scope='%'
```

### Step 3: Resolution Fix (Important)
Apply the configuration fix to ensure the site loads correctly:
```powershell
docker compose restart frontend
```

### Step 4: Access the System
*   **URL**: [http://localhost:8080](http://localhost:8080)
*   **Login**: `Administrator`
*   **Password**: `admin`

---

## 3. Configuration Setup
After logging in, you need to apply the "Procurement Only" rules.

**Detailed instructions are in [PROCUREMENT_SETUP.md](PROCUREMENT_SETUP.md).**

Summary:
1.  **Server Script**: Copy the script from `PROCUREMENT_SETUP.md` to restrict modules.
2.  **User Roles**: Remove "System Manager" role from agency users to lock down the UI.

## 4. Troubleshooting
**Error: "Internal Server Error" or "Site Not Found"**
Run:
```powershell
docker compose up -d
docker compose restart backend
```

**Error: "Connection Refused" (Redis)**
If you see errors related to Redis in logs:
```powershell
docker compose restart backend
```

**Stop the System**
To stop all containers:
```powershell
docker compose down
```
