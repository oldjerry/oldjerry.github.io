---
title: "Learning CI/CD as an IT Professional"
date: 2026-07-12
draft: false
description: "My notes on learning CI/CD and how my IT support and infrastructure background helps me understand modern software delivery."

tags:
  - CI/CD
  - DevOps
  - GitHub Actions
  - Docker
  - Automation
  - IT Support

categories:
  - DevOps
---

# Learning CI/CD as an IT Professional

## Introduction

Although I have worked in software development, IT support, and infrastructure for many years, CI/CD is an area I have only recently started exploring in depth.

Today, modern software teams rely heavily on automation to build, test, and deploy applications. Understanding how these processes work has become increasingly valuable, even for engineers whose primary role is IT support or infrastructure.

This article summarizes what I have learned so far and why I believe CI/CD is an important skill for today's IT professionals.

---

## What is CI/CD?

CI/CD stands for **Continuous Integration** and **Continuous Delivery (or Continuous Deployment).**

In simple terms:

- **Continuous Integration (CI)** automatically builds and tests code whenever developers submit changes.
- **Continuous Delivery (CD)** prepares applications for deployment automatically.
- **Continuous Deployment** goes one step further by deploying successful builds directly into production without manual intervention.

Instead of relying on manual deployments, the entire process becomes automated and repeatable.

---

## A Typical CI/CD Workflow

Most CI/CD pipelines follow a similar process:

```text
Developer
    │
    ▼
Git Repository
    │
    ▼
Build & Test
    │
    ▼
Create Artifact
    │
    ▼
Deploy
    │
    ▼
Monitoring
```

Automation reduces human error, improves consistency, and allows teams to release software more frequently.

---

## Why Does It Matter for IT Professionals?

At first glance, CI/CD appears to be something only developers need to understand.

However, many problems inside a CI/CD pipeline are operational problems:

- Build failures
- Permission issues
- Networking problems
- Dependency conflicts
- Linux configuration
- Docker issues

These are areas where experienced IT professionals already have strong troubleshooting skills.

While developers focus on writing code, infrastructure engineers help ensure the environments remain stable, reliable, and repeatable.

---

## Skills I'm Learning

To better understand CI/CD, I'm currently focusing on:

- Git and GitHub
- Linux administration
- Docker fundamentals
- GitHub Actions
- YAML configuration
- Cloud fundamentals

My goal isn't simply to learn individual tools, but to understand how they work together in a complete software delivery pipeline.

---

## Looking Ahead

One advantage of maintaining this Hugo website is that it provides a small but practical environment for learning automation.

Today I manually:

- Write Markdown
- Build the site with Hugo
- Commit changes to Git
- Push to GitHub

A natural next step is automating this workflow using GitHub Actions so the website is built and deployed automatically after each push.

It's a simple project, but a great opportunity to learn real CI/CD concepts through hands-on practice.

---

## Final Thoughts

Learning CI/CD has helped me better understand how modern software teams connect development and operations.

Coming from an IT support and infrastructure background, I find that many existing skills—troubleshooting, networking, Linux administration, and system reliability—transfer naturally into this area.

There is still plenty to learn, but that's part of what makes the journey interesting.

I'm looking forward to continuing this learning process and sharing more practical experiences as I build new projects.

---

## References

- https://docs.github.com/en/actions
- https://docs.docker.com/
- https://git-scm.com/doc