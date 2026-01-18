# AWS Identity and Access Management (IAM) - Summary

## 1. IAM Overview
*   **Definition:** IAM allows you to manage users and their level of access to the AWS Console.
*   **Scope:** **Global Service** (Users, Groups, and Roles are available in all regions instantly).
*   **Root Account:**
    *   Created by default when you sign up.
    *   Has complete access to all AWS services and billing.
    *   **Best Practice:** Lock it away. Do not use it for daily tasks. Only use it to create the first IAM User.

## 2. IAM Identities (Users & Groups)
*   **IAM Users:** Mapped to a physical person (e.g., Alice, Bob).
*   **IAM Groups:** Contains users only.
    *   **Rule:** Groups **cannot** contain other groups (No nested groups).
    *   A user can belong to multiple groups.
*   **Organization:** One user per physical person. Do not share credentials.

## 3. IAM Policies & Permissions
*   **Definition:** JSON documents that define permissions (Authorization).
*   **Structure:**
    *   **Version:** Always `2012-10-17`.
    *   **Statement:**
        *   **Sid:** Statement ID (Optional).
        *   **Effect:** `Allow` or `Deny`.
        *   **Principal:** The account/user/role to which this policy is applied.
        *   **Action:** List of API calls (e.g., `s3:GetObject`).
        *   **Resource:** The resource list (e.g., `arn:aws:s3:::my_bucket/*`).
*   **Principle of Least Privilege:** Always grant only the minimal permissions required to perform a task.
*   **Inheritance:** A user inherits permissions from all the Groups they belong to + any Inline Policies attached directly.

## 4. IAM Security
### Password Policy
*   Enforce strong passwords (length, characters).
*   Force password expiration (e.g., every 90 days).
*   Prevent password reuse.

### Multi-Factor Authentication (MFA)
*   **Concept:** Password (what you know) + Device (what you own).
*   **Requirement:** Must be enabled for the **Root Account** and IAM Administrators.
*   **MFA Device Options (Exam Topic):**
    1.  **Virtual MFA:** Google Authenticator, Authy (Phone-based).
    2.  **U2F Security Key:** YubiKey (Physical USB). Supports multiple root/IAM users on a single key.
    3.  **Hardware Key Fob:** Gemalto (Physical display).
    4.  **Hardware Key Fob for GovCloud (US):** SurePassID.

## 5. Access Options (How to access AWS)
| Access Method | Used By | Protected By |
| :--- | :--- | :--- |
| **AWS Management Console** | Human Users | Password + MFA |
| **AWS CLI** (Command Line) | Automation / Scripts | Access Keys |
| **AWS SDK** (Code) | Applications | Access Keys |

### Access Keys
*   Consist of **Access Key ID** + **Secret Access Key**.
*   **Secret Access Key** is only shown **ONCE** at creation.
*   **Best Practice:** Never share them, never upload to Git/GitHub.

### AWS CloudShell
*   A browser-based terminal available in the AWS Console.
*   Pre-authenticated with your current credentials.
*   Region-specific availability.
*   Files in `$HOME` directory persist between sessions.

## 6. IAM Roles
*   **Definition:** An identity with specific permissions that does **not** have long-term credentials (no password/keys).
*   **Use Cases:**
    *   **AWS Services:** Assign a role to an EC2 instance or Lambda function to access other AWS services (e.g., S3).
    *   **Cross-Account Access:** Accessing resources in a different AWS account.
*   **Mechanism:** Uses **Temporary Security Credentials**.

## 7. IAM Security Tools (Audit)
| Tool | Scope | Purpose |
| :--- | :--- | :--- |
| **IAM Credentials Report** | **Account-level** | Generates a **CSV** file listing all users and the status of their credentials (MFA, password age, access keys). Used for **auditing**. |
| **IAM Access Advisor** | **User-level** | Shows the **service permissions** granted to a user and **when they last accessed** those services. Used to revise policies (Least Privilege). |

## 8. IAM Best Practices Summary
1.  **Don't use the root account** except for AWS account setup.
2.  **One physical user = One AWS user**.
3.  **Assign users to groups** and assign permissions to groups.
4.  Create a **strong password policy**.
5.  Use and enforce **MFA**.
6.  Create and use **Roles** for giving permissions to AWS Services (e.g., EC2).
7.  Use **Access Keys** for Programmatic Access (CLI / SDK).
8.  Audit permissions using **IAM Credentials Report** & **IAM Access Advisor**.
9.  **Never share IAM users & Access Keys**.