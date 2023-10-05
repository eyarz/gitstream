<p align="center">
  <img src="https://github.com/eyarz/gitstream/assets/19731161/c76a227d-17d7-4a47-91b7-2d25f0dee03e" alt="gitstream-no-bg-white" border="0" />
</p>

<p align="center">
 <img src="https://img.shields.io/badge/License-Apache_2.0-yellow.svg" />
 <img src="https://img.shields.io/badge/Marketplace-gitStream-blue" target="https://github.com/marketplace/gitstream-by-linearb"/>
 <img src="https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Flinear-b%2Fgitstream&count_bg=%2379C83D&title_bg=%23555555&icon=github.svg&icon_color=%23E7E7E7&title=views+%28today+%2F+total%29&edge_flat=false" target="_blank"></a>
</p>

___

`gitStream` is a workflow automation tool to optimize your code review process.  
Add context to Pull Requests, find code experts for reviews, and automate the merge process to maximize developer productivity!

## Table of Contents
- [Why gitStream and not GitHub Actions?](#why-gitstream-and-not-github-actions)
- [Meet gitStream Bot: Your Code Review Sidekick!](#meet-gitstream-bot-your-code-review-sidekick)
- [Users loving gitStream](#users-loving-gitstream)
- [Getting started in two min](#getting-started-in-two-min)
- [The gitStream Dashboard](#the-gitstream-dashboard)
  
## Why gitStream and not GitHub Actions?

Think of gitStream as the Robin to your GitHub Actions Batman. It’s not here to replace GitHub Actions or other CI/CD pals like CircleCI or Jenkins. Instead, gitStream is optimized to enhance the [continuous merge process](https://linearb.io/blog/what-is-continuous-merge) for open-source projects with multiple contributors.

## Meet gitStream Bot: Your Code Review Sidekick!
gitStream bot can _shorten your code review cycles by a 40%_ with [built-in automation](https://docs.gitstream.cm/automations/automation-library/) like:

* **🏷️ Enforce Semantic PR Titles** - enforce pull request naming conventions
* **📊 Calculate percent new code** - post a comment with the percentage of new code
* **🎯 Assign code experts** - assign recommended reviewers based on code components
* **🕰️ Provide estimated time to review** - label PRs estimated number of minutes it would take to review

## Users loving gitStream

From small side projects to complex codebases, all types of open source communities love gitStream:
* [gradle/gradle](https://github.com/gradle/gradle/tree/master/.cm) 15.2k ⭐
* [gitcoinco/grants-stack](https://github.com/gitcoinco/grants-stack/tree/main/.cm) 250 ⭐
* [vim-zz/swordfish-rs](https://github.com/vim-zz/swordfish-rs/tree/main/.cm) 50 ⭐
* [linear-b/gitstream](https://github.com/linear-b/gitstream/tree/main/.cm) (Yes, PRs to gitStream docs are also checked with gitStream 🤯)
  
## Getting started in two min

**1️⃣ Install the GitHub app**  
 gitStream is a verified GitHub app on the [marketplace](https://github.com/marketplace/gitstream-by-linearb), audited and approved by the GitHub Security team. 

**2️⃣ Create a `.cm/gitstream.cm` automations file in your repository**  
This file will contain a YAML configuration that determines the automations that run on every PR to the repository's default branch (usually _master_ or _main_).  
<br>
For example, this automation file will add a label with an [estimated time to review](https://docs.gitstream.cm/automations/provide-estimated-time-to-review/) for every new pull request:
![image](https://github.com/eyarz/gitstream/assets/19731161/a6a2338d-70e7-4d2b-8770-f1145092898e)

  ```yaml
  # -*- mode: yaml -*-
  # This example configuration for provides basic automations to get started with gitStream.
  # View the gitStream quickstart for more examples: https://docs.gitstream.cm/quick-start/
  manifest:
    version: 1.0
  automations:
    # Add a label indicating how long it will take to review the PR.
    estimated_time_to_review: 
      if:
        - true
      run:
        - action: add-label@v1
        # etr is defined in the last section of this example
          args:
            label: "{{ calc.etr }} min review"
            color: {{ 'E94637' if (calc.etr >= 20) else ('FBBD10' if (calc.etr >= 5) else '36A853') }}
    # Post a comment that lists the best experts for the modified files.
    explain_code_experts:
      if:
        - true
      run:
        - action: explain-code-experts@v1 
          args:
            gt: 10 
  # The next function calculates the estimated time to review and makes it available in the automation above.
  calc:
    etr: {{ branch | estimatedReviewTime }}
```
   
**3️⃣ Create a `.github/workflows/gitstream.yml` file to trigger gitStream on every PR**  
Copy-paste the following configuration file (no need to edit) and place it in your repository default branch:
```yaml
# Code generated by gitStream GitHub app - DO NOT EDIT

name: gitStream workflow automation
run-name: |
  /:\ gitStream: PR #${{ fromJSON(fromJSON(github.event.inputs.client_payload)).pullRequestNumber }} from ${{ github.event.inputs.full_repository }}

on:
  workflow_dispatch:
    inputs:
      client_payload:
        description: The Client payload
        required: true
      full_repository:
        description: the repository name include the owner in `owner/repo_name` format
        required: true
      head_ref:
        description: the head sha
        required: true
      base_ref:
        description: the base ref 
        required: true
      installation_id:
        description: the installation id
        required: false
      resolver_url:
        description: the resolver url to pass results to
        required: true
      resolver_token:
        description: Optional resolver token for resolver service
        required: false
        default: ''

jobs:
  gitStream:
    timeout-minutes: 5
    runs-on: ubuntu-latest
    name: gitStream workflow automation
    steps:
      - name: Evaluate Rules
        uses: linear-b/gitstream-github-action@v1
        id: rules-engine
        with:
          full_repository: ${{ github.event.inputs.full_repository }}
          head_ref: ${{ github.event.inputs.head_ref }}
          base_ref: ${{ github.event.inputs.base_ref }}
          client_payload: ${{ github.event.inputs.client_payload }}
          installation_id: ${{ github.event.inputs.installation_id }}
          resolver_url: ${{ github.event.inputs.resolver_url }}
          resolver_token: ${{ github.event.inputs.resolver_token }}

```
<details>
  <summary>How to ensure uniformity across all repositories?</summary>
  <br>
  By creating a special repository named cm within your GitHub organization and placing your automation files there, you'll seamlessly apply the same rules to all repositories under the organization’s umbrella 🌂  
  Check out our docs for more detailed info: https://docs.gitstream.cm/github-installation/
</details>

## The gitStream Dashboard

Imagine having a high-tech control room where you can see every move, every automation executed, and its impact—all in one place. Welcome to the [_gitStream Dashboard!_](https://app.gitstream.cm/)

* 👀 **Get a Bird's-Eye View:** Observe all executed automations and their outcomes from a single, intuitive interface.
* 📊 **Analyze & Optimize:** Delve into detailed reports that help you understand the effectiveness of your automations, enabling you to fine-tune them for even smoother workflows.

<img src="https://github.com/eyarz/gitstream/assets/19731161/560fcfb6-0151-47e5-b937-66868ef793ae" width="80%">

<br><hr>
[🔼 Back to top](#table-of-Contents)
