+++
date = '2025-07-05T12:28:41-07:00'
draft = false
title = 'Thoughts on working with Agents'
author = "Gurpreet"
tags = ["ai","gen-ai","llms","agents","workflow"]
+++

- Type: Live post
- Last updated: 13 July, 2025

Software engineering is evolving[1](#links), quite rapidly while at it. While I do fear for my job and what impacts AI might have on me, I cannot say that some part of this AI wave haven't been fun.
LLMs and Agents have become a crucial part of my workflow and I've found myself relying on them much more than I imagined I would. I use Claude/Claude Code as my daily driver, with Gemini as fallback for one-off chat scenarios or for rare cases requiring it's generous context window.

This post acts as one where I put my thoughts on things, how I use different tools and what works for me. Expect this to be updated with time, as I learn more.

## Changes I see

### Prototyping is becoming much cheaper

"A prototype is an early sample, model, or release of a product built to test a concept or process." [Wikipedia](https://en.wikipedia.org/wiki/Prototype)

It's becoming much quicker - and [easier](https://www.infoq.com/presentations/Simple-Made-Easy/) - to get to a working prototype. Verifying a hypothesis is becoming cheaper as Vibe coding gains popularity.
For me, vibing practically implements the principle of "Iterate quickly, produce results" and as long as I do not see a Vibe-PR™ raised to prod, I'm all in favor of it.

### Accelerated learning

Whether it's about working on projects that I've wanted to implement or learning about projects I've wanted to explore, learning new things has become easier than it ever has been.

Want synopsis of a project? Here it is. A detailed document on workings of the core rendering engine? Can do. Want to create a game using tech you've never used? Sure thing.

[_Describe_](#context-is-king) what you need Claude Code to do and it'll iterate on it with you until you're satisfied with the results.

## What I've learnt so far

### AI has a multiplicative effect

Agents and LLMs demonstrate a multiplicative effect on an individual's abilities - High ability individuals see their impact increase multifold. With the changes we're seeing, Engineers who

1. Think from first principles
2. Have strong fundamentals
3. Communicate effectively
4. Collaborate efficiently, and
5. Have good intuition

seem to excel at AI adoption and reap most benefits.

### Context is King

The information provided to the LLM during conversation is called the [_Context_](https://docs.anthropic.com/en/docs/build-with-claude/context-windows). The initial prompt, conversation that follows and any additional setup information picked from memory and other files, external links, etc all form the Context for the LLM.

Providing the correct amount of context is critical; provide less details and you won't get the desired outcome, share too much and you risk wasting effort due to [large context-related problems](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html). It's important to say that providing the correct amount of context is quite difficult - iterating on the initial prompt, steering the conversation in the correct direction, even restarting the chat are expected and helped me learn from mistakes.

I like to think of my conversation with an LLM/Agent as one similar to one I might have with an intern on my team - they might understand the intent but they can't do exactly what I need without me telling them. **It's on me to be explicit and empower them to do a task as expected.**

As an example, the following is the prompt I first used to start my recent [pong-wars](https://github.com/imGurpreetSK/pongwars) project. The prompt doesn't define the task very well and the result was way off of what was expected.

```
How can I create something like https://github.com/vnglst/pong-wars using compose multiplatform?
The current project has been created for you as a starter project targeting Android, iOS, desktop and web using compose multiplatform for UI code sharing.
```

Conversely, the following prompt performed much better and generated a project that could be further iterated on:

```
Problem statement:
I want to create a game using kotlin multiplatform as shown in the video here: [link](https://private-user-images.githubusercontent.com/3457693/300288131-4eae12fa-bdc1-49ee-8b39-c94deb7cb2c8.mov?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTE1MjEyNDksIm5iZiI6MTc1MTUyMDk0OSwicGF0aCI6Ii8zNDU3NjkzLzMwMDI4ODEzMS00ZWFlMTJmYS1iZGMxLTQ5ZWUtOGIzOS1jOTRkZWI3Y2IyYzgubW92P1gtQW16LUFsZ29yaXRobT1BV1M0LUhNQUMtU0hBMjU2JlgtQW16LUNyZWRlbnRpYWw9QUtJQVZDT0RZTFNBNTNQUUs0WkElMkYyMDI1MDcwMyUyRnVzLWVhc3QtMSUyRnMzJTJGYXdzNF9yZXF1ZXN0JlgtQW16LURhdGU9MjAyNTA3MDNUMDUzNTQ5WiZYLUFtei1FeHBpcmVzPTMwMCZYLUFtei1TaWduYXR1cmU9YzM0YzQwNjJmYzk1Y2ViNmEwZDZjMmEyYzQ3ZDcyMjA2MTExYzZhODM4M2E4YmYwZDdjMTMyMzEwNzVlOWU2ZCZYLUFtei1TaWduZWRIZWFkZXJzPWhvc3QifQ.xdwdcwuE-Cmi0sxjhbkHhtSA9Lm8WmtJypyFYqYkiDo)
The target platforms are Android, iOS, web and desktop. The UI is to be created using Compose multiplatform.
The game starts with the square area split vertically into two equal parts with two balls, one on each side.

Overview:
Area description:
The area is composed of smaller squares, all of equal size. The squares on the same side are of the same color.
As soon as a ball hits a square, it bounces back to the opposite side. The square is marked claimed by the ball and its
color changes to the opposite color. Additionally, the counter increases by one showing the squares count claimed by
each color.
Each square has a 1px black border.

Ball description:
Each ball is of the same color as the opposite area. As soon as the ball hits a square, it bounces back to the opposite side.
The square is marked claimed by the ball and its color changes to the opposite color. Additionally, the counter increases by one showing the squares count claimed by each color.
The ball moves at a constant speed throughout the area. If the ball hits a wall, it bounces back to the opposite side.

Game termination:
The game keeps on going indefinitely until closed by the user.

Task:
1. Break the task into smaller tasks.
2. Provide a list of tasks to complete. Take approval on execution plan before making any changes.
3. Once each task is complete, commit the changes with a meaningful commit message.
4. Start with the next task only once the commit has been approved by me.
```

### I'm loving planning mode

Imagine conversing with a highly-knowledgeable coworker who understands the problem space, is non-judgmental, is commendably patient and is always available to chat. That's how I feel about planning mode.

I can have long discussions to refine an idea into deliverable milestones and actionable tasks, all while steering the conversation towards my desired outcome. If I feel like, I can later ask the peer to be an expert programmer and pair with me to implement a part or whole of the feature we just discussed at length.

### Do not blindly trust all actions

I've had tests deleted from codebase to achieve 'Ensure all tests pass before committing changes'. LLMs can play dirty to achieve task completion; I'd suggest actively reviewing the changes they make and uphold them to same standards as a developer.

While I understand the lure of `--dangerously-skip-permissions` (or similar) but I'd recommend keeping it only for side projects running in an isolated container.

## How LLMs and Agents improve my productivity

The following is a list of workflows where I see highest positive impact of using LLMs and Agents:

1. **Experiments and spikes**

   This is the one area that has been impacted the most, in a way that looking back at older ways of operation feel _dirty_. I have saved hours while joyfully experimenting as I focus on the what to do than how to do it. To top that, if things don't work out as planned, I can just `/clear` and `git reset` and start again - **the fear of failure has turned into joy of learning by exploration** and I'm thankful for that.

2. **Jira tickets**

   If you haven't tried connecting to Atlassian services via MCP server, I'd highly recommend you do. My recent Jira workflow has essentially stripped down to asking Claude Code to `Come up with a plan to implement Jira ticket PROJ-1234` after I break down the task into smaller, well defined sub-tasks that can be run and committed by agents.

   I also sometimes use [Jules by Google](https://jules.google) for asynchronous work, mainly for project chores.

3. **Project exploration**

   - `/init`, if not done already
      - Tweak as required for best experience
   - `Help me understand the application architecture` or `What does the CoreRepository do?` or `Go through the git history and help me understand how X component evolved over time` or any other project related answers, delivered hot right to your screen.

4. **Side projects**

   I want to work on side projects that help me learn something new. This is much more lucrative now - the speed of execution has increased due to the decrease in cost of bootstrapping. By this I mean I can now focus on learning about the one thing that matters to me the most without having to go through the pain of setting up the project first.

   This simply let's me learn new things more efficiently.

5. **Learning**

   Two tools I use frequently to learn about concepts or systems that I do not work directly with are

   1. DeepWiki

      Mostly to explore libraries used in projects - explore them to have a high level understanding of how they work and itch my 'buy how'.

   2. NotebookLM

      For researching topics and asking questions interactively. I love NotebookLM because I provide the sources, with option to explore similar sources recommended by the book. Currently, I'm creating a notebook to learn more about Gradle and project structuring.

## Yet to explore

- Multi-agent workflows
- Anthropic's Write tests, commit; code, iterate, commit [(TDD) workflow](#links)
- E2E tests with Maestro/Android Studio journeys/playwright

## Footnotes

### Links

1. I'd highly recommend watching Andrej Karpathy's talk [Software is changing (again)](www.youtube.com/watch?v=LCEmiRjPEtQ).
2. Simon Willison's amazing [blog](https://simonwillison.net/tags/ai/).
3. David Crawshaw: [How I program with Agents](https://crawshaw.io/blog/programming-with-agents)
4. Armin Ronacher's [talk on Agentic Coding](https://www.youtube.com/watch?v=nfOVgz_omlU)
5. Claude code [best practices](https://www.anthropic.com/engineering/claude-code-best-practices)
6. In particular: Simon on [future of programming](https://simonwillison.net/2025/Jul/3/table-saws/)

### Notes

1. Regardless of the use case
   - Ask for options, a lot. First iteration is rarely what you want.
   - Always ask for a plan first, and be explicit with this - think, harder, ultra, think step-by-step are your friends.