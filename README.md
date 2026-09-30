# Script-Controlled ACL – Restrict Record Access Based on Field Value

## Project Overview

This project implements a **script-controlled Access Control List (ACL)** in **ServiceNow** to restrict record-level access based on the value of the **Branch** field.

The project uses a custom `u_institution_details` table containing student information and four custom roles (`bb1`–`bb4`) to control **Read, Create, Write, and Delete** operations.

The Read ACL combines:
- A required role (`bb1`)
- A data condition (`Branch is EEE`)
- An advanced server-side script with an administrator override

This ensures that authorised EEE users can view only EEE records, while administrators retain unrestricted access.

## Objectives

- Create test users and custom roles for different privileges.
- Create an Institution Details table for student records.
- Restrict Read access to EEE-branch records.
- Control Create, Write and Delete permissions using separate roles.
- Provide administrator override access.
- Verify the ACL behaviour using ServiceNow's **Impersonate User** feature.

## Technologies Used

- **Platform:** ServiceNow Personal Developer Instance (PDI)
- **Application Scope:** Global
- **Scripting Language:** JavaScript
- **API:** ServiceNow GlideSystem (`gs`)
- **Browser:** Google Chrome / Microsoft Edge
- **Security:** ServiceNow Access Control Lists (ACLs)

## System Requirements

- Intel i3 processor or higher
- 4 GB RAM or higher
- Internet connection
- ServiceNow Personal Developer Instance
- Latest Google Chrome or Microsoft Edge

## Table Design

### `u_institution_details` – Institution Details

| Field | Type |
|---|---|
| Student Roll Number | Auto Number |
| Student Name | Reference – User |
| Faculty Name | Reference – User |
| Branch | Choice – ECE / EEE / CSE |
| Email | String |
| Phone Number | String |
| Description | Multi-line String |

## Role Design

| Role | Permission | Purpose |
|---|---|---|
| `bb1` | Read | View EEE-branch records |
| `bb2` | Create | Create new records |
| `bb3` | Write | Edit existing records |
| `bb4` | Delete | Delete records |
| `admin` | All operations | Unrestricted administrator access |

## ACL Configuration

### 1. Read ACL

- **Table:** `u_institution_details`
- **Operation:** Read
- **Required Role:** `bb1`
- **Data Condition:** Branch is EEE
- **Advanced:** Enabled
- **Script:** Admin override + `bb1` role check + deny by default

```javascript
(function () {
    // Allow admin users full access
    if (gs.hasRole('admin')) {
        return true;
    }

    // Allow EEE branch users to see EEE records
    if (gs.hasRole('bb1')) {
        return true;
    }

    // Deny access for all others
    return false;
})();
```

### 2. Create ACL

- **Operation:** Create
- **Required Role:** `bb2`
- **Data Condition:** None

### 3. Write ACL

- **Operation:** Write
- **Required Role:** `bb3`
- **Data Condition:** None

### 4. Delete ACL

- **Operation:** Delete
- **Required Role:** `bb4`
- **Data Condition:** None

## Security Model

When a user requests a record, ServiceNow evaluates the applicable ACL rules before returning data.

For the Read operation:

1. Check the user's role.
2. Check the record's Branch value.
3. Execute the advanced script.
4. Grant access only when the required checks pass.
5. Deny access by default for users who do not satisfy the rules.
6. Administrator access is allowed through the admin override.

This follows the principles of **least privilege**, **fail-safe defaults**, **data-layer security**, and **separation of duties**.

## Development Process

The project was completed through the following milestones:

1. Creation of test users and roles
2. Creation of the `u_institution_details` table
3. Creation of sample ECE, EEE and CSE records
4. Creation of the script-controlled Read ACL
5. Creation of Create, Write and Delete ACLs
6. Verification using different user/role combinations
7. Documentation
8. Project demonstration

## Sample Data

| Roll No. | Student Name | Faculty Name | Branch |
|---|---|---|---|
| INS0001001 | Yaswanth Nikku | Abraham Lincoln | CSE |
| INS0001004 | Prashanth Reddy | Abel Tuter | EEE |
| INS0001005 | Srinu Nandipalli | ITIL User | ECE |
| INS0001006 | Praneeth | ITIL User | ECE |
| INS0001007 | Guna Sai | Abel Tuter | EEE |
| INS0001008 | Abraham Lincoln | Malla Sravan kumar | CSE |

## Testing

Testing was performed using ServiceNow's **Impersonate User** feature.

| Test Case | User / Role | Expected Behaviour | Result |
|---|---|---|---|
| TC-1 | `bb1` only | View only EEE records; no New button | Pass |
| TC-2 | No role | No records visible | Pass |
| TC-3 | `bb1 + bb2` | View EEE records and create records | Pass |
| TC-4 | `bb1 + bb2 + bb3` | View, create and edit EEE records | Pass |
| TC-5 | `bb1 + bb2 + bb3 + bb4` | View, create, edit and delete EEE records | Pass |
| TC-6 | Administrator | View and manage all branches | Pass |

The project testing confirmed that the ACL combination correctly enforced record-level access for Read, Create, Write and Delete operations.

## Negative Testing

Additional checks confirmed that:

- A user with only `bb2`, `bb3`, or `bb4` cannot bypass the Read ACL.
- A user with no required role cannot access records directly through the table/sys_id URL.
- The restriction is enforced at the platform/data layer rather than only by hiding UI elements.
- Existing ACLs on unrelated tables were not affected.

## Update Set

All configuration changes were captured in the ServiceNow Update Set:

`ACL Project [Global]`

This includes the custom table, fields, custom roles and ACL records.

## Project Demonstration Flow

The demonstration follows increasing levels of privilege:

1. Show the Institution Details table and sample records.
2. Show the four ACL configurations.
3. Impersonate a user with no roles and show the empty list.
4. Add `bb1` and show only EEE records.
5. Add `bb2` and demonstrate record creation.
6. Add `bb3` and demonstrate editing.
7. Add `bb4` and demonstrate deletion.
8. Impersonate System Administrator and show unrestricted access.

## Conclusion

The project demonstrates how ServiceNow ACLs can combine **roles, record data conditions and server-side scripts** to provide record-level security.

The implemented design allows:
- `bb1` → Read EEE records
- `bb2` → Create records
- `bb3` → Edit records
- `bb4` → Delete records
- `admin` → Full access

The design can be extended to other branches, tables and security requirements.

## Future Enhancements

- Extend the same access pattern to ECE and CSE branches.
- Use a dynamic branch value from the user's profile instead of creating separate roles for every branch.
- Add audit logging for denied access attempts.
- Expose the restricted data through a Service Portal widget and verify ACL enforcement there.

## Project Structure

```text
Script-Controlled-ACL/
│
├── README.md
├── Phase-2-Requirement-Analysis.docx
├── Phase-3-Project-Design.docx
├── Phase-4-Project-Planning.docx
├── Phase-5-Project-Development.docx
├── Phase-6-Project-Testing.docx
├── Phase-7-Project-Documentation.docx
└── Phase-8-Project-Demonstration.docx
```

## Keywords

`ServiceNow` `ACL` `Access Control List` `JavaScript` `GlideSystem` `Role-Based Access Control` `Record-Level Security` `Personal Developer Instance` `u_institution_details`
