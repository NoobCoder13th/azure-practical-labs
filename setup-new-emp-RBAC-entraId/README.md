# Microsoft Entra ID and Azure RBAC: Employee Access

This guided project demonstrates how to set up access for a new employee using **Microsoft Entra ID** and **Azure Role-Based Access Control (RBAC)**.

The project follows a least-privilege access model where a new support analyst is added to a security group and the group is assigned the **Reader** role at the resource group scope.

## Learning Objectives

* Create a user and security group in Microsoft Entra ID
* Assign group-based Azure RBAC at resource group scope
* Validate expected access at the correct scope
* Review role assignments and audit events
* Configure and use Temporary Access Pass (TAP)
* Understand how least-privilege access is implemented in Azure
* Clean up users, roles, and resources after completing the lab

## Scenario

A new support analyst is joining the team and requires read-only access to a development resource group.

Instead of assigning permissions directly to the user, a security group is created. The new employee is added to the group, and the **Reader** role is assigned to the group at the resource group scope.

This allows the employee to view resources without having permission to modify or delete them.

## Azure Resources Used

* Microsoft Entra ID
* Microsoft Entra security group
* Microsoft Entra user account
* Azure Resource Group
* Azure Storage Account
* Azure RBAC
* Reader role
* Activity Log
* Temporary Access Pass (TAP)

---

# Exercise 1: Create User and Group

The first exercise establishes the identity foundation that will be used for the RBAC assignment.

The exercise consists of:

1. Create a security group
2. Create a new user account
3. Add the user to the security group

## 1. Create a Resource Group and Storage Account

Before starting the identity exercises, I created a resource group and a storage account to provide resources for the later RBAC access test.

These resources were created multiple times during previous labs, so no screenshot is included for this step.

---

## 2. Create a Security Group

I opened **Microsoft Entra ID** from the Azure Portal search bar.

![Microsoft Entra ID dashboard](images-task-1/entra-id-dash.png)

I selected **New group**.

![Create new group](images-task-1/click-new.png)

I filled in the required information for the security group.

![Security group configuration](images-task-1/fill-new-group-details.png)

After creating the group, Azure displayed the successfully created group.

![Security group created successfully](images-task-1/group-success.png)

The security group will later be used as the principal when assigning the Azure RBAC Reader role.

---

## 3. Create a New User

I returned to **Microsoft Entra ID** and opened **Users**.

![Microsoft Entra ID dashboard](images-task-1/entra-id-dash.png)

I selected the option to create a new user.

![Create new user](images-task-1/create-new-user.png)

I completed the new user form.

![New user form](images-task-1/new-user-form.png)

The user's principal name is important because it is used when signing in.

The test account used for this lab was:

```text
khalidGP@3boodi02002gmail.onmicrosoft.com
```

The initial password was generated during account creation and was intentionally not included in this documentation.


---

## 4. Add the User to the Security Group

I opened the user and selected the option to add the user to a group.

![Add user to group](images-task-1/users-add-to-group.png)

I selected the security group created earlier.

![Select security group](images-task-1/select-group.png)

After adding the user, I opened the user's profile.

![User profile](images-task-1/user-profile.png)

The user's group membership shows that the account belongs to one group.

![User group membership](images-task-1/user-group-member.png)

At this point, the identity structure is ready:

```text
Test User
    │
    ▼
Security Group
    │
    ▼
Azure RBAC assignment
    │
    ▼
Resource Group
```

---

# Exercise 2: Assign RBAC Role at Scope

The second exercise assigns the **Reader** role to the security group at the resource group scope.

Instead of assigning the role directly to the individual user, assigning it to the group allows all members of that group to inherit the same permissions.

The exercise consists of:

1. Open the target resource group
2. Create a Reader role assignment
3. Select the security group
4. Review and confirm the assignment

## 1. Open the Resource Group

I opened the target resource group from the Azure Portal.

![Add role](images-task-2/add-role.png)

From the resource group's access control settings, I started creating a new role assignment.

## 2. Select the Reader Role

I selected the built-in **Reader** role.

![Select Reader role](images-task-2/add-role-reader.png)

The Reader role provides read access to resources within the assigned scope without granting permission to modify or delete them.

## 3. Select the Security Group

In the members section, I selected the security group created during Exercise 1.

![Select group](images-task-2/member-tab.png)

This means the permission is assigned to the group rather than directly to the individual user.

## 4. Review the Role Assignment

I reviewed the configuration before creating the assignment.

![Review role assignment](images-task-2/review-role.png)

The final configuration assigned:

```text
Role:   Reader
Member: Security Group
Scope:  Resource Group
```

I confirmed the role assignment.

![Confirm role assignment](images-task-2/confirm-role.png)

The resulting permission model is:

```text
Security Group
      │
      │ Reader
      ▼
Resource Group
      │
      ├── Storage Account
      └── Other resources
```

Because the assignment is made at the resource group scope, the Reader permission applies to resources contained within that scope.

---

# Exercise 3: Verify Least-Privilege Model

The final exercise verifies that the access model was configured correctly.

The exercise consists of:

1. Review role assignments in IAM
2. Review the audit trail
3. Configure Temporary Access Pass
4. Sign in as the test user
5. Verify Reader access

## 1. Review the User's Role

I reviewed the permissions associated with the test user.

![Review Khalid's role](images-task-3/review-khalid-role.png)

The role assignment confirmed that the user receives access through the security group.

## 2. Review the Activity Log

I reviewed the Activity Log for the role assignment.

![Review role assignment log](images-task-3/review-role-assignment-log.png)

The IAM configuration and Activity Log both showed the Reader role assignment.

This provides both a permission view and an audit trail of the change.

---

## 3. Configure Temporary Access Pass

A **Temporary Access Pass (TAP)** is a time-limited authentication method in Microsoft Entra ID.

For this lab, TAP provides a temporary authentication method that can be used during sign-in without requiring the test account to permanently configure a phone number or authenticator application.

I opened **Authentication methods** from the Azure Portal.

![Navigate to Authentication Methods](images-task-3/nav-to-auth-methods.png)

I opened the Authentication Methods configuration.

![Authentication Methods dashboard](images-task-3/auth-methods-dash.png)

I configured **Temporary Access Pass** for the tenant.

![Temporary Access Pass configuration](images-task-3/TAP-form.png)

---

## 4. Attempt to Sign In as the Test User

I attempted to sign in using the test user's credentials.

During the sign-in process, Microsoft required the user to change the initial password.

![Password update](images-task-3/pass-update.png)

After changing the password, the sign-in process required additional authentication through the Microsoft Authenticator application.

The authentication step could not be completed because the Authenticator setup was not working correctly for the test account.

As a result, I could not complete the final interactive sign-in test as the new user.

### Validation Status

The configuration leading up to the sign-in test was successfully completed:

* User created
* Security group created
* User added to security group
* Reader role assigned to the security group
* Assignment scoped to the resource group
* IAM permissions reviewed
* Activity Log reviewed
* Temporary Access Pass configuration completed

However, **the final firsthand permission test using the new user's session was not completed** because of the authentication issue.

Therefore, the lab confirms the configured RBAC assignment, but does not claim that the interactive Reader-versus-write test was successfully performed.

---

# Access Model

The completed configuration follows a group-based least-privilege model:

```text
┌─────────────────────┐
│     Test User       │
│       Khalid        │
└──────────┬──────────┘
           │
           │ Member of
           ▼
┌─────────────────────┐
│   Security Group    │
└──────────┬──────────┘
           │
           │ Reader role
           ▼
┌─────────────────────┐
│   Resource Group    │
└──────────┬──────────┘
           │
           ├──────────────► Storage Account
           │
           └──────────────► Other Resources
```

The important concept is that permissions are assigned to the **group**, while the user receives those permissions through group membership.

This makes access management easier to maintain when multiple employees require the same level of access.

---

# What I Learned

### Microsoft Entra ID

* Created a user account.
* Created a security group.
* Added a user to a security group.
* Reviewed group membership.
* Worked with authentication methods.

### Azure RBAC

* Assigned a built-in Azure role to a security group.
* Used the **Reader** role.
* Assigned permissions at the **resource group scope**.
* Observed how permissions assigned at a higher scope apply to resources within that scope.

### Access Management

* Used group-based permissions instead of assigning roles directly to individual users.
* Reviewed effective permissions through IAM.
* Reviewed role assignment activity through the Activity Log.
* Configured Temporary Access Pass for authentication testing.
* Applied the principle of least privilege by providing read-only access rather than broader permissions.

### Troubleshooting

The final sign-in test could not be completed because the test account encountered an Authenticator requirement that could not be completed during the lab.

This demonstrated an important distinction between **configuring access permissions** and **successfully authenticating as the target user**. The RBAC configuration was completed and reviewed independently of the unsuccessful sign-in attempt.

---

# Cleanup

After completing the lab, temporary identities, role assignments, and Azure resources should be removed to prevent unnecessary resource usage and avoid leaving unused access configurations behind.

Recommended cleanup:

* Remove the Reader role assignment.
* Remove the test user.
* Remove the security group.
* Delete the resource group created for the lab.
* Verify that temporary Azure resources have been removed.

> **Security note:** Any screenshots containing passwords, TAP codes, access tokens, connection strings, storage keys, or other credentials should be removed or redacted before publishing this project to GitHub.

---

# Summary

This project provided hands-on experience with Microsoft Entra ID and Azure RBAC by building an employee access model from the ground up.

The lab covered:

```text
Create User
     ↓
Create Security Group
     ↓
Add User to Group
     ↓
Assign Reader Role to Group
     ↓
Scope Role to Resource Group
     ↓
Review IAM + Activity Log
     ↓
Configure TAP
     ↓
Attempt User Sign-In
```

The resulting configuration demonstrates how **Microsoft Entra ID provides the identity layer**, while **Azure RBAC controls access to Azure resources**.

The project also demonstrated why assigning permissions to groups can simplify access management and support a least-privilege approach.
