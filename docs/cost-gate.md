# Cost Gate & Budget Checklist

This document verifies that the AWS environment has been audited for cost management and that standard Free Tier thresholds are understood prior to deploying resources in the Asia Pacific (Mumbai) `ap-south-1` region.

## 📌 Cost Check Status
- **Status:** PASS
- **Verified On:** 2026-10-30 UTC

## 💰 Live Asia Pacific (Mumbai) Pricing Reference
- **On-Demand Compute (t3.micro / Linux):** $0.0112 per hour
- **EBS Volume Storage (gp3 SSD):** $0.0912 per GB-month
- **Public IPv4 Address (In-Use):** $0.0050 per hour
- **Data Transfer (Out to Internet):** $0.1093 per GB (Note: First 100 GB per month is free)

## 🛡️ Cost Safeguards Implemented
1. **Zero Active Cost Baseline:** Verified via Billing Console that month-to-date spending is under control, with standard Free Tier discounts successfully offsetting compute services.
2. **Resource Hygiene Audit:** Confirmed 0 active/running instances, 0 orphaned EBS volumes, and 0 idle Elastic IP addresses before starting work.
3. **Credit Specification Safeguard:** Standard credit specification (rather than Unlimited) enforced during instance launch to prevent automatic CPU burst charges on t3.micro.
4. **Active Cleanup Strategy:** All created resources (instances, peering connections, subnets, route tables, and VPCs) will be completely terminated and deleted immediately upon project sign-off.
