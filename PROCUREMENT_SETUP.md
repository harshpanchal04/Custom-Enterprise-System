# Procurement Configuration Setup

To transform the standard ERPNext into the "Agency Procurement System", follow these locking down procedures.

## 1. Enable Server Scripts (Already Done)
We have already enabled Server Scripts on the backend. If you ever reset the system, run this command to re-enable them:
```powershell
docker compose exec backend bench set-config -g server_script_enabled 1
docker compose restart backend
```

## 2. Server Script: Module Access Control
This script forces the user's "Blocked Modules" list to include everything *except* what is allowed.

1.  Login as **Administrator**.
2.  Search for **Server Script** in the awesomebar.
3.  Click **New Server Script**.
4.  **Name:** `Procurement Lockdown`
5.  **Type:** `DocType Event`
6.  **DocType:** `User`
7.  **Event:** `Before Save`
8.  **Script:**
    ```python
    # Script Type: DocType Event
    # DocType: User
    # Event: Before Save

    def on_update(doc, method):
        # 1. Skip for Administrator
        if doc.name == "Administrator":
            return

        # 2. Define Allowed Modules
        allowed_modules = ["Buying", "Stock", "Quality", "Settings", "Home"]
        
        # 3. Get All Available Modules from System
        all_modules = frappe.get_all("Module Def", pluck="name")
        
        # 4. Filter allowed modules to ensure they actually exist
        valid_allowed = [m for m in allowed_modules if m in all_modules]

        # 5. Calculate Blocked Modules (All - Allowed)
        blocked_list = [m for m in all_modules if m not in valid_allowed]
        
        # 6. Apply to User's Blocked Modules Table
        doc.set("block_modules", [])
        
        for module_name in blocked_list:
            doc.append("block_modules", {
                "module": module_name
            })
    ```
9.  **Save** and verify "Enabled" is checked.

## 3. UI Lockdown: Role Permissions
To hide the "Create Workspace" (+) button and preventing users from editing their workspaces, we rely on standard Role Permissions rather than custom code.

**Instructions:**
1.  Go to **User List**.
2.  Open the User you want to restrict (e.g., a Procurement Manager).
3.  Scroll to **Roles**.
4.  **Uncheck** `System Manager` and `Workspace Manager`.
5.  **Check** `Buying User`, `Stock User`, and `Quality User` (as needed).
6.  **Save**.

**Result:**
*   The **+** button next to "Workspaces" will disappear.
*   The "Edit" button on workspaces will disappear.
*   The user will strictly see only the modules allowed by the Server Script.
