---
title: "Use Self-Hosted Agent in Azure DevOps"
date: 2021-12-18T11:10:36+08:00
draft: false
language: en
featured_image: ../assets/images/featured/Use-Self-Hosted-Agent-in-Azure-DevOps.png
summary: Learn how to use self-hosted agents in Azure DevOps to run your pipelines on your infrastructure, ensuring greater control over the build process.
description: This blog post explains the benefits and step-by-step process to set up a self-hosted agent in Azure DevOps for custom or enterprise environments, providing flexibility and resource control.
author: Aman Gupta - DevOps Insiders
authorimage: ../assets/images/global/author.webp
categories: blog
tags: Azure DevOps, DevOps, Self-Hosted Agent, CI/CD, Automation
---

# Introduction

Azure DevOps offers hosted agents to run your CI/CD pipelines. While these are convenient, there are times when you need more control over the build environment. In such cases, a **self-hosted agent** is the perfect solution. Self-hosted agents allow you to run your pipeline on your infrastructure, using custom hardware or specific environments, providing flexibility and security.

In this post, I’ll walk you through the process of setting up a self-hosted agent in Azure DevOps, highlighting key use cases and benefits.

## Why Use Self-Hosted Agents?

Self-hosted agents provide several advantages over Microsoft-hosted agents:

1. **Resource Control**: You manage the hardware and software. This is useful when you need to test on specific operating systems or hardware configurations.
2. **Cost Efficiency**: No limits on build time or concurrency. While hosted agents have limits, self-hosted agents are bound only by your own infrastructure capacity.
3. **Custom Environments**: Install specific software or custom libraries that your project requires without worrying about Azure's pre-defined agent environments.
4. **Network Access**: Direct access to on-premises or private resources that are not publicly accessible.

## Prerequisites

Before setting up a self-hosted agent, ensure you have the following:

1. An Azure DevOps account with a project setup.
2. A machine (physical or virtual) with network access to Azure DevOps.
3. Required permissions in Azure DevOps to add an agent.
4. Supported OS: Windows, Linux, or macOS.
5. Sufficient resources (CPU, RAM, disk space) to run builds or deployments.

## Steps to Set Up a Self-Hosted Agent in Azure DevOps

### Step 1: Create a Personal Access Token (PAT)
To register an agent with Azure DevOps, you need a PAT to authenticate. Follow these steps:

1. Go to Azure DevOps.
2. Click on your profile in the upper-right corner and select **Personal Access Tokens**.
3. Click **New Token**.
4. Select the appropriate scopes like **Agent Pools (read, manage)**, **Project and Team (read)**, etc.
5. Save the PAT for later use.

### Step 2: Download the Agent

1. Navigate to your Azure DevOps organization.
2. Go to **Project Settings** > **Agent pools** > **New Agent**.
3. Choose your OS (Windows, Linux, or macOS).
4. Download the appropriate agent package for your machine.

### Step 3: Configure the Agent

Once the agent is downloaded, follow these steps to configure it:

#### Windows:
1. Extract the downloaded zip file.
2. Open Command Prompt as an Administrator.
3. Navigate to the extracted folder and run:
   ```bash
   config.cmd
   ```
4. Provide the URL of your Azure DevOps organization and the PAT created earlier.
5. Complete the configuration by following the prompts.

#### Linux/macOS:
1. Extract the tar.gz file:
   ```bash
   tar zxvf agent.tar.gz
   ```
2. Open a terminal and navigate to the extracted folder.
3. Run the configuration command:
   ```bash
   ./config.sh
   ```
4. Provide the Azure DevOps URL and PAT.
5. Complete the setup.

### Step 4: Run the Agent

After configuration, you need to start the agent:

#### Windows:
1. Run the agent by using the command:
   ```bash
   run.cmd
   ```

#### Linux/macOS:
1. Start the agent with:
   ```bash
   ./svc.sh install
   ./svc.sh start
   ```

The agent should now be running and ready to pick up jobs from Azure DevOps.

## Assigning Pipelines to Self-Hosted Agents

To make Azure DevOps use your self-hosted agent, follow these steps:

1. Open your pipeline YAML file or go to the pipeline settings in the Azure DevOps portal.
2. Specify the agent pool where your self-hosted agent is registered.
   ```yaml
   pool:
     name: Default
   ```

Replace `Default` with the name of the agent pool where your self-hosted agent is listed.

## Monitoring and Scaling Self-Hosted Agents

Self-hosted agents run on your infrastructure, so it's crucial to monitor their performance. Here are some best practices:

- **Scaling**: If your build queues grow, consider adding more agents to your pool.
- **Monitoring**: Regularly check agent logs for any issues.
- **Maintenance**: Keep the underlying hardware or virtual machines updated to ensure performance.


## Conclusion

Self-hosted agents in Azure DevOps offer enhanced control, cost efficiency, and the ability to tailor the build environment to specific needs. Whether you're working with private networks, need specialized hardware, or have custom software requirements, a self-hosted agent gives you the flexibility you need. Follow the steps outlined in this guide to get your agent up and running today.
