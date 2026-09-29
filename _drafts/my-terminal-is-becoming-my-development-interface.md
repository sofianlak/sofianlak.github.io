---
title: The Terminal I Share With My Agents
date: 2026-09-28 18:00:00 +0200
categories: [DevOps, Development]
tags: [terminal, herdr, hermes, coding-agents]
description: The terminal is becoming the control plane for my agents.
author: sofianlak
image:
  path: /assets/img/headers/bee.jpg
---

## The terminal was already my workspace

The terminal has been my workspace for years. I use `git` to inspect code, `kubectl` to troubleshoot clusters, and `glab` to follow merge requests and pipelines. Even when VS Code is open, a lot of my work happens there.

Coding agents did not bring me to the terminal. They gave me someone else to share it with.

That is what I like about [Herdr](https://herdr.dev/){:target="_blank"}. If you know tmux, the idea of organizing terminals into sessions and panes will feel familiar. Herdr also shows me which coding agents are working or waiting for input.

I can keep a shell, Codex or GitHub Copilot CLI, a running application and its logs around the same project. I can work in one pane while an agent investigates something in another.

Recently, I have also been experimenting with [Hermes Agent](https://github.com/NousResearch/hermes-agent){:target="_blank"}. Herdr gives me the workspace; Hermes can coordinate the investigation inside it. The distinction becomes clearer with a real problem.

![Diagram of voice input, Herdr, coding agents and GitLab in one workspace](/assets/img/ai/herdr.png){: width="672" height="515" style="border-radius: 20px;"}

I had already been using Herdr for a few months when I watched [I Run an AI Civilization in Herdr](https://www.youtube.com/watch?v=AL-PQuB2wy0){:target="_blank"}. The video made me think about what I could do next in a workspace I already knew: give agents distinct responsibilities, then follow how their findings come together. I wanted to try that approach on a problem I actually have.

## An upgrade review with several threads

When we consider a Keycloak upgrade, reading the release notes is only half the job. I need to know whether a change matters for *our* setup. Thanks to my mentor (platform eng tech lead), much of our Keycloak configuration (realms, clients, mappers and so on) is stored as code, so there are files an agent can actually compare with the upstream changes.

I want to give Hermes one request:

> Find the breaking changes that might affect our configuration, show me the evidence, and do not change anything yet.

From there, the work can split:

1. **Upstream researcher:** read the official migration and release notes between the two versions; extract behavior changes with source links.
2. **Repository investigator:** inspect our Keycloak as Code files for the relevant flows, clients, mappers and settings; cite the paths and environments where they appear.
3. **Hermes, the coordinator:** compare both results, discard changes that do not appear relevant, and identify gaps that need a manual check. If an important claim is uncertain, it can ask for a focused follow-up instead of guessing.

The first two investigations can run independently. Their findings only become useful when Hermes joins them: “this upstream behavior changed” plus “this is how we configure it.”

I want the final report to separate **affected**, **probably unaffected** and **unknown**, with a reason for each. An absent string in the repository is not proof that a runtime setting or an application's behavior is absent.

This is the part I was missing when I simply asked one agent to “check the breaking changes.” It could summarize the release notes, but I still had to do the mapping to our configuration myself. Orchestration makes that mapping an explicit task and gives each investigation a narrow question.

This remains an analysis workflow. The agents should not open a merge request or trigger CI; I decide what to test or change after reading their evidence.

## What makes Hermes an orchestrator here

Hermes can use [`delegate_task`](https://hermes-agent.nousresearch.com/docs/user-guide/features/delegation){:target="_blank"} to give separate agents focused work. Each child starts with its own context, so “look into the upgrade” is a poor handoff.

Hermes needs to pass the version range, the repository location, the precise question and the expected evidence to each one. Their results then return to Hermes for synthesis.

I can also shape the main agent through [`SOUL.md`](https://hermes-agent.nousresearch.com/docs/guides/use-soul-with-hermes){:target="_blank"}: for example, I want it to be concise, skeptical of weak evidence and willing to say “I don't know.” `SOUL.md` defines Hermes' general identity and style. Project-specific information, such as where the Keycloak configuration lives and how to read it, belongs in a project context file like `AGENTS.md` or `.hermes.md`.

One detail matters: delegated agents do **not** inherit the parent's `SOUL.md` or conversation. They need a clear goal and context from Hermes; project context files can supply repository conventions. `SOUL.md` does not magically create a team of specialists. The useful part is how Hermes assigns the work, collects the findings and asks the next question.

I can keep Hermes in one Herdr pane, the repository in another, and inspect the paths it cites myself. Herdr makes the work easy to follow; Hermes handles the delegation. I do not have to accept a polished conclusion just because three agents contributed to it.

## Sometimes I talk to them

![Scene from I, Robot with a human standing among humanoid robots](/assets/img/ai/irobot.jpg){: width="672" height="378" style="border-radius: 20px;"}

At home, I sometimes use [OpenWhispr](https://docs.openwhispr.com/guides/dictation){:target="_blank"} to dictate the initial request. I can explain the version change, what worries me and the instruction to stay read-only in one go. At work, with colleagues nearby, I suddenly remember how much I love my keyboard.

Speaking often helps me include context that I would leave out of a hurried typed prompt. That does not mean the transcription is always good.

A [study of learning journals](https://doi.org/10.1016/j.learninstruc.2025.102250){:target="_blank"} found spoken entries were much longer than written ones, while a [study of programming prompts](https://doi.org/10.1145/3803400.3809397){:target="_blank"} found that editing voice transcriptions mattered for task success. I read the result before sending anything precise.

I still use VS Code for a large diff and a full browser when I need one. I also reach for `glab`, terminal-browser or Harlequin when a task calls for them. You can find many [plugins](https://herdr.dev/plugins/) around Herdr.

The terminal was already where I worked. Herdr now gives the agents a place in that workspace, while Hermes lets me split a question, compare the answers and keep the final judgment in my hands.
