---
layout: post
title:  "💸 How Duolingo Cut Their AWS Bill by 20%"
date:   2025-04-19 18:19:56 +0530
categories: MCP
timetoread: 8
---

# How Duolingo Cut Their AWS Bill by 20%

Duolingo embarked on a significant cost-cutting journey to reduce their AWS cloud bills by 20%. They achieved this through a series of optimizations addressing various aspects of their cloud infrastructure.

Below are the key **problems faced**, **optimizations done**, and **final approaches adopted**:

---

## Initial Visibility Crisis

- **Problem Faced:**  
  Duolingo initially realized they were “flying blind,” lacking understanding of their cloud spending. A staging environment was costing more than production due to being scaled up for testing and not scaled back down.

- **Optimization Done:**  
  Enlisted **CloudZero**, a third-party service, to gain insight into cloud costs.

- **Final Approach Adopted:**  
  CloudZero converted billing data into **queryable line items**, helping engineers drill down into specific cost categories.

---

## S3 Buckets – Indefinite Revision History

- **Problem Faced:**  
  S3 buckets were storing entire revision history indefinitely, acting like a “digital hoarder.”

- **Optimization Done:**  
  Implemented **lifecycle rules** on the largest buckets.

- **Final Approach Adopted:**  
  Used lifecycle policies to transition data to cheaper tiers (e.g., S3 Standard-IA, Glacier) and delete older versions, controlling storage growth.

---

## DynamoDB Tables – Stale Data Accumulation

- **Problem Faced:**  
  Large volumes of stale data were accumulating, incurring costs without business value.

- **Optimization Done:**  
  Introduced **Time to Live (TTL)** rules.

- **Final Approach Adopted:**  
  TTL automatically removed outdated records. However, legacy data required manual updates.  
  **Lesson:** Design for TTL early in schema planning.

---

## CloudWatch – Extremely Verbose Logs

- **Problem Faced:**  
  Every user action, API call, and system event was being logged in maximum detail, including full stack traces — drastically increasing ingestion and storage costs.

- **Optimization Done:**  
  Removed stack traces from logs.

- **Final Approach Adopted:**  
  Best practices (implied):
  - Log only what’s necessary
  - Set log retention policies
  - Reduce verbosity/log levels

---

## Overprovisioned Resources

- **Problem Faced:**  
  Services were overprovisioned — excess memory, unused CPU, dormant databases, and legacy microservices bloated the cost.

- **Optimization Done:**  
  Focused on **right-sizing** and implementing **autoscaling**.

- **Final Approach Adopted:**  
  Right-sizing just one service led to **hundreds of thousands in annual savings**.

---

## Microservice Communication – Unnecessary API Calls

- **Problem Faced:**  
  A single user request triggered a chain reaction of internal API calls. One legacy service caused 2.1 billion unnecessary API calls/day. Another frequently polled unchanged data due to a 1-minute cache TTL.

- **Optimization Done:**  
  Audited services and analyzed data change patterns.

- **Final Approach Adopted:**  
  - Disabled legacy service triggering excessive calls  
  - Increased cache TTL from 1 minute to 1 hour, reducing traffic by 60%  
  - **Key lesson:** Optimize service communication for cascading savings

---

## Database Optimization – Unused Cost-Saving Features

- **Problem Faced:**  
  Built-in AWS cost-saving features were never activated.

- **Optimization Done:**  
  Applied pricing optimizations for **DynamoDB** and **RDS**.

- **Final Approach Adopted:**  
  Switching one DB to **Aurora IO-optimized config** saved several hundred thousand dollars/year.  
  This pricing model favors high read/write workloads.

---

## Reserved Instances – Inefficient Allocation

- **Problem Faced:**  
  Reserved instance purchases were not aligned with usage patterns.

- **Optimization Done:**  
  Improved analysis and planning for reserved instance purchases.

- **Final Approach Adopted:**  
  Identified baseline usage across EC2, RDS, and ElastiCache.  
  Made bulk reserve purchases to secure long-term discounts.

---

## Ongoing Monitoring & Reporting

- **Problem Faced:**  
  A broader visibility crisis across services.

- **Optimization Done:**  
  Expanded cloud monitoring.

- **Final Approach Adopted:**  
  - Integrated cloud spending metrics (including OpenAI)  
  - Added weekly cost reports  
  - Enabled passive team-wide tracking

---

## Key Takeaways

- **Visibility first** – You can’t optimize what you can’t see  
- **Right-sizing works** – Overprovisioning adds up fast  
- **Design for cost** – Make smart decisions early (TTL, cache, logs)  
- **Microservice traffic matters** – Optimize communication and reduce internal API chatter  
- **Small changes scale** – Lifecycle rules, instance reservations, and config tweaks add up

---

> _Duolingo’s journey highlights how thoughtful engineering and cost awareness can lead to substantial savings—even at scale._
