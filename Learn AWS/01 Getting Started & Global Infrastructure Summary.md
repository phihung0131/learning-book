# 📝 AWS SAA-C03: Getting Started & Global Infrastructure Summary

## 1. AWS Overview & History
*   **What is AWS?** A Cloud Provider offering on-demand servers and services that allow for easy scaling.
*   **Key History Milestones:**
    *   **2002:** Launched internally at Amazon.
    *   **2003:** Idea formed to market Amazon's infrastructure as a service.
    *   **2004:** First public service launched: **SQS** (Simple Queue Service).
    *   **2006:** Relaunched publicly with core services: **SQS, S3, and EC2**.
    *   **2007:** Expansion to Europe.
*   **Market Position:**
    *   **Leader:** AWS has been the leader in the **Gartner Magic Quadrant** for cloud infrastructure for **13 consecutive years**.
    *   **Market Share:** Approx. **31%** (as of Q1 2024), followed by Microsoft (25%).
    *   **Scale:** Over 1 million active users and $90 Billion in annual revenue (2023).
*   **Use Cases:** diverse industries (Netflix, NASA, McDonald's) using AWS for Enterprise IT, Backup, Big Data, Web Hosting, Gaming, etc.

## 2. AWS Global Infrastructure (Exam Critical)
The infrastructure is divided into three specific concepts. You must distinguish between them:

### A. AWS Regions
*   **Definition:** A physical location around the world (cluster of data centers).
*   **Naming Convention:** `us-east-1` (N. Virginia), `eu-west-3` (Paris), `ap-southeast-1` (Singapore).
*   **Scope:** Most AWS services are **Region-scoped** (tied to a specific region).

### B. Availability Zones (AZs)
*   **Structure:** Each **Region** consists of **multiple AZs** (Usually 3, Minimum 3, Maximum 6).
*   **Naming Convention:** Region Code + Letter (e.g., `ap-southeast-2a`, `ap-southeast-2b`).
*   **Definition:** Each AZ is one or more discrete data centers with redundant power, networking, and connectivity.
*   **Key Feature:**
    *   **Isolation:** AZs are separate from each other to prevent disaster spread (fire, flood).
    *   **Connectivity:** Connected via high-bandwidth, ultra-low latency networking.

### C. Points of Presence (Edge Locations)
*   **Scale:** **400+** Points of Presence in 90+ cities across 40+ countries.
*   **Purpose:** To deliver content to end-users with the **lowest latency** possible.
*   **Service:** Primarily used by **Amazon CloudFront** (Content Delivery Network).

## 3. How to Choose an AWS Region
When launching an application, consider these 4 factors:
1.  **Compliance:** Legal requirements (e.g., "Data must stay in France").
2.  **Proximity (Latency):** Choose a region closest to your end-users to reduce lag.
3.  **Available Services:** Not all regions have every AWS service (Check the *Region Table*).
4.  **Pricing:** Costs vary by region (e.g., tax rates, power costs).

## 4. AWS Management Console Tour
*   **UI Updates:** AWS frequently updates the UI (colors, button shapes). Functionality remains the same regardless of visual changes.
*   **Navigation:**
    *   **Region Selector:** Located at the top-right. Select the region geographically closest to you/your customers.
    *   **Search Bar:** The fastest way to find services (search by name or feature).
*   **Service Scope (Crucial for Exam):**
    *   **Global Services:** Content is the same regardless of region selection.
        *   IAM (Identity Access Management)
        *   Route 53 (DNS)
        *   CloudFront (CDN)
        *   WAF (Web Application Firewall)
    *   **Regional Services:** Content is specific to the selected region.
        *   Amazon EC2 (Infrastructure)
        *   Lambda (Compute)
        *   DynamoDB (Database)
        *   Rekognition (AI)
