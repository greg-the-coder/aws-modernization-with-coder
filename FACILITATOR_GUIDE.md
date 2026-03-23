---
title: "Facilitator Guide"
chapter: false
weight: 99
hidden: true
---

# Facilitator Guide — AWS and Coder AI-Powered Development Workshop

This guide provides facilitators with everything needed to successfully deliver the workshop. It is hidden from participants and intended only for event organizers and presenters.

---

## Workshop Overview

| Field | Details |
|-------|---------|
| **Title** | AWS and Coder AI-Powered Development Workshop |
| **Duration** | 90 minutes |
| **Level** | 200 (Intermediate) |
| **Target Audience** | Platform Engineers, DevOps Engineers, Engineering Managers, Developers, Solutions Architects |
| **Prerequisites** | 200-level AWS knowledge, basic Kubernetes familiarity |
| **AWS Services Used** | Amazon EKS (Auto Mode), Amazon Aurora PostgreSQL, Amazon Bedrock, AWS CloudFormation, IAM, CloudFront, NLB, EBS |
| **Partner Technology** | Coder Cloud Development Environments, Amazon Kiro CLI, Anthropic Claude Code |
| **Regions Supported** | us-west-2, us-east-1 |

---

## Prerequisites

### Dry run 
- [ ] Verify the CloudFormation template (`static/infrastructure/eks-cluster.yaml`) deploys successfully in target region(s)
- [ ] Test all five Coder templates create workspaces without errors
- [ ] Confirm Amazon Bedrock model access is enabled (Claude Sonnet 4.5, Claude Opus 4.5) in the target region
- [ ] Deploy a test stack end-to-end and walk through all three AI workflows

### 1 Day Before
- [ ] Confirm participant count and provision sufficient Workshop Studio accounts
- [ ] Verify Coder URL, admin credentials, and template availability from CloudFormation outputs
- [ ] Confirm network/firewall at the venue does not block WebSocket connections (required for Coder)
- [ ] Prepare slide deck with event access code and Coder login instructions
- [ ] Pre-stage a backup GitHub repo (`https://github.com/greg-the-coder/ai-dev-workflows.git`) in case participants skip Workflow 1

### Day Of
- [ ] Display the Workshop Studio join URL and event access code on screen
- [ ] Confirm CloudFormation stacks are in `CREATE_COMPLETE` state for all participant accounts
- [ ] Have the Coder admin URL and credentials ready for troubleshooting

---

## Agenda & Timing Guide

| Time | Duration | Module | Activity |
|------|----------|--------|----------|
| 0:00 | 10 min | **Welcome & Introduction** | Introductions, workshop goals, architecture overview |
| 0:10 | 5 min | **Getting Started** | Workshop Studio login, access Coder via CloudFormation outputs |
| 0:15 | 15 min | **Template Engineering** | Explore pre-deployed templates, review architecture patterns, create a test workspace |
| 0:30 | 25 min | **Workflow 1 — AI-Driven Development (Kiro)** | Create Kiro workspace, generate task management app, deploy via CDK, cleanup |
| 0:55 | 15 min | **Workflow 2 — AI-Driven Task Automation (Claude Code)** | Launch Well-Architected review task, observe autonomous remediation tasks |
| 1:10 | 15 min | **Workflow 3 — GenAI RAG Application (Claude Code + pgvector)** | Launch RAG workspace, test document Q&A, prototype web crawler feature |
| 1:25 | 5 min | **Conclusion & Cleanup** | Recap, next steps, cleanup instructions (self-hosted only) |

::alert[Workflow 1 (Kiro) is the longest hands-on section. If running behind schedule, have participants use the pre-built repo (`https://github.com/greg-the-coder/ai-dev-workflows.git`) instead of generating from scratch, and skip the CDK deployment/cleanup steps.]{type="info"}

---

## Module Facilitation Notes

### Getting Started (5 min)

**Key actions:**
- Walk participants through Workshop Studio join flow (sign-in → access code → accept T&C)
- Direct them to the **Event Outputs** sidebar for `CoderURL`, `CoderAdminEmail`, `CoderAdminPassword`
- Confirm everyone can log in to Coder before proceeding

**Common issues:**
- CloudFormation stack still deploying → takes 15–20 min; have participants review Introduction content while waiting
- Coder login fails → verify correct credentials from outputs; default is `admin@example.com` / `admin@coder`

### Template Engineering (15 min)

**Key talking points:**
- Five pre-deployed templates covering different personas (Kiro CLI, Claude Code, RAG, SAM, Windows DCV)
- Templates use Terraform IaC — show the architecture diagram in the module
- Emphasize the GitOps workflow for template management in production

**Hands-on:** Have participants create one workspace from the "Kubernetes with Kiro CLI" template to validate their environment before the AI workflows module.

### Workflow 1 — AI-Driven Development with Kiro (25 min)

**Facilitator walkthrough:**
1. Participants create a Kiro workspace and initialize `kiro-cli chat`
2. Use `/plan` mode for requirements analysis
3. Generate project structure and implementation
4. (Optional) Deploy to AWS and cleanup

**Tips:**
- Kiro will ask for permission before file changes — remind participants to review and approve
- If Kiro gets stuck, suggest participants provide clarifying prompts
- CDK deployment can take 5–10 min — use this time for Q&A
- Remind participants to commit/push to their repo for use in Workflow 2

### Workflow 2 — AI-Driven Task Automation with Claude Code (15 min)

**Facilitator walkthrough:**
1. Participants use the Coder **Tasks** UI (not Workspaces)
2. Select "Kubernetes with Claude Code" template
3. Paste the Well-Architected review prompt
4. Observe Claude Code creating sub-tasks autonomously

**Tips:**
- Bedrock rate limiting may cause intermittent API errors — Claude Code retries automatically
- If the Tasks option doesn't appear in the Coder sidebar, have participants refresh the dashboard
- Participants who didn't complete Workflow 1 should use the fallback repo URL in the prompt

### Workflow 3 — GenAI RAG Application (15 min)

**Facilitator walkthrough:**
1. Use the "Kubernetes with Claude Code RAG" template via Tasks UI
2. Claude Code auto-sets up the Streamlit RAG app
3. Test document ingestion (PDF upload) and chat Q&A
4. (Optional) Prompt Claude Code to add web crawler functionality

**Tips:**
- Aurora PostgreSQL pgvector is pre-provisioned — no manual DB setup needed
- Streamlit preview is on port 8501 in the Task UI Preview tab
- For the optional Kiro IDE step, participants need a Kiro sign-in (AWS Builder ID)

---

## Troubleshooting Guide

| Issue | Resolution |
|-------|------------|
| CloudFormation stack fails | Check the Events tab for the specific error. Common: insufficient IAM permissions, service quota limits on EKS clusters, or Bedrock model access not enabled |
| Coder URL not accessible | Verify CloudFront distribution is deployed and enabled. Check security groups allow inbound HTTPS |
| Workspace fails to start | Check EKS node capacity. Auto Mode should scale automatically but may take 2–3 min for new nodes |
| Kiro CLI login fails | Run `kiro-cli login` again. Ensure the workspace has internet access for authentication |
| Claude Code Bedrock errors | Rate limiting is expected on workshop accounts. Claude Code retries automatically. If persistent, check Bedrock model access in the region |
| Tasks option missing in Coder | Refresh the Coder dashboard. Coder Tasks requires the Claude Code template to be properly deployed |
| Slow workspace provisioning | EKS Auto Mode may need to scale up nodes. First workspace in a new node pool takes longer (~2–3 min) |
| CDK deployment fails in Workflow 1 | Verify AWS credentials are configured in the workspace (`aws sts get-caller-identity`). Check IAM role permissions |

---

## Infrastructure Details

### CloudFormation Stack Outputs (Participant-Visible)

| Output | Description |
|--------|-------------|
| `CoderURL` | CloudFront URL for the Coder web interface |
| `EKSClusterName` | Name of the provisioned EKS cluster |
| `PostgreSQLConnectionURLWithoutPassword` | Aurora PostgreSQL connection string (used by RAG template) |

### Resource Costs (Approximate per Participant)

| Resource | Estimated Hourly Cost |
|----------|-----------------------|
| EKS Cluster (Auto Mode) | ~$0.10/hr |
| EKS Worker Nodes (m5.xlarge) | ~$0.19/hr per node |
| Aurora PostgreSQL Serverless v2 | ~$0.12/hr (0.5 ACU min) |
| CloudFront Distribution | Minimal (data transfer) |
| NLB | ~$0.02/hr |
| **Total (idle, no workspaces)** | **~$0.45/hr** |

Workspace costs vary by template (Kubernetes pods are cheapest, EC2 Windows instances are most expensive).

---

## Cleanup Reminders

**AWS-hosted events:** Resources are automatically cleaned up by Workshop Studio. No action needed.

**Self-paced / own account:** Direct participants to the [Cleanup section](/5_conclusion/52_cleanup.html). Key steps:
1. Delete all Coder workspaces (especially EC2-based ones)
2. Delete the CloudFormation stack
3. Manually delete any remaining IAM roles (`coder-workshop-*`)
4. Disable and delete the CloudFront distribution

---

## Presentation Tips

- **Lead with the problem**: Start by asking participants about their current developer onboarding time and environment setup pain points
- **Live demo first**: Before hands-on, do a quick 2-minute live demo of creating a workspace and using Kiro to generate code
- **Encourage experimentation**: The AI workflows are intentionally open-ended — participants will get different results, and that's expected
- **Manage AI expectations**: Remind participants that AI agents may need redirection. This is normal and part of the human-AI collaboration workflow
- **Time management**: Workflow 1 is the most time-flexible section. Cut the CDK deployment step if running behind
- **Parking lot**: Keep a visible list of questions to address during Q&A or follow up after the event
