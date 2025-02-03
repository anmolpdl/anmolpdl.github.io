---
layout: post
title:  "Misaligned agents in robust software systems"
subtitle: ""
date:   2025-02-02 15:12:00
categories: []
---

*This post contains a project proposal/kernel for a specific series of experiments that I would like to run (or see further research carried out upon). It is divided into two parts: first, the rationale for a project like this, then, discussion of the experiment setup. Any feedback and/or ideas would be highly welcome.*

# Rationale

The key idea of this project proposal is to build model organisms of misaligned agents executing within robustly controllable and observable software systems. I will try to break down the rationale for this idea with some claims below.

1) **Building [model organisms](https://www.alignmentforum.org/posts/ChDH335ckdvpxXaXX/model-organisms-of-misalignment-the-case-for-a-new-pillar-of-1) seems extremely valuable at this time.**

Model organisms research work is an experimental bridge between demonstrated, replicable cases of misalignment (from cases of [specification gaming](https://deepmind.google/discover/blog/specification-gaming-the-flip-side-of-ai-ingenuity/) to [subterfuge](https://arxiv.org/abs/2406.10162)), and catastrophic risk scenarios and thought experiments.

Right now, capabilities (including complex task execution and tool use) are on the rise; yet, superhuman AI has not arrived and boxed experiments are unlikely to pose major threats of causing catastrophic risk. The case for model organisms is twofold. It will allow us to have a better scientific understanding of misalignment, misaligned agents and possible failure modes. But, it can also raise a fire alarm if things seem to go extremely wrong.


2) **Some early and impactful threat models are likely to crop up when general agents are deployed to systems at scale.**
	
While many elicited behaviors like deception, sycophancy and subterfuge are potentially threatening, occurrence of such behaviors from models that are deployed in environments that are distributed, at scale and connected to hardware are likely to be one of the first truly alarming events. Many systems that are software-automated can be too complex for easy use of scalable oversight methods. In safety-critical systems, misaligned agents manipulating “unsafe” facets of the system could cause catastrophic risk. Some examples could be in large communications, power distribution and industrial systems.

3) **“Safety” seems like a system property, [not a model property](https://www.aisnakeoil.com/p/ai-safety-is-not-a-model-property).**


As mentioned in [Leveson, 2012](https://direct.mit.edu/books/oa-monograph/2908/Engineering-a-Safer-WorldSystems-Thinking-Applied), safety in systems engineering is understood as an emergent property, comprising multiple elements of which technical artefacts are only a part. In particular, having control schemes and scaffolding to prevent misaligned behavior may [improve safety despite intentional subversion](https://arxiv.org/abs/2312.06942). Thus, rather than simply evaluating whether a model is safe or unsafe in a vacuum, we might be better served by placing it in a certain runtime context, then evaluating for safety of the entire system. 

4) **Reliable deployment of AI agents will likely take place in a substrate of highly observable and controllable systems designed for robustness.**
	
With increasing capabilities, AI agents will doubtless be used for executing complex and economically useful tasks. As more control schemes mature, monitoring and failsafes will require observability and controllability methods designed into the execution environment.

# Experiment Discussion

One key research question that arises when following this project idea is: what agentic behavior can we consider as demonstrating misalignment from a system’s perspective?

Many behaviors that could qualify are already being researched upon, such as deception and subterfuge. I am more interested in a class of behaviors that could arise without such deliberation from the agent, but rather might be concrete examples of specification gaming.

As a starting point for a series of experiments, the Erlang ecosystem’s BEAM VM is an interesting testbed. Most current research has Python as a testbed environment. Python is neither particularly fast (having to rely on crutches like JAX and numpy) nor suited to scale and/or real-time applications. In contrast, the BEAM VM (and the Erlang/OTP ecosystem) has been designed to provide service robustly and be highly fault-tolerant. Alternatively, this could also simply be carried out on a general Unix system since what we will be focused upon are system calls.

Currently, I am testing out function calling agents with the [Elixir Langchain library](https://github.com/brainlid/langchain). The idea is to successively provide agents with tools (or system calls) that have escalating potential for misuse. An ordered list of such tools would include:

1. Tools for introspection of the runtime environment: `sys.get_state/1`, `System.get_env/0`, `Process.list/0`, `Process.info/1`, `top`, etc.
2. Tools for control over processes in the runtime environment: `Process.exit()`, `sigkill`, `kill` etc.
3. Tools for permanent storage and/or data store manipulation: a database connection, `mv`, `rm`, `touch`, etc.
4. Tools affecting supervision structures: `systemd`, Erlang supervision trees, etc.
5. Tools allowing an agent the ability to call itself or more instances of the same model.

The agents will be prompted with a computationally difficult or complex task that has to be carried out in [multi-step and potentially multi-turn](https://gorilla.cs.berkeley.edu/blogs/13_bfcl_v3_multi_turn.html) function calling tasks. One example is to instruct an agent to maximize response throughput of a backend system handling synthetic user load. Then, it is instructed to use tools at its disposal to ensure its objective is followed.


*Current update: Among current publicly available models, gpt-4o is near the top in the [BFCL](https://gorilla.cs.berkeley.edu/leaderboard.html), which I have been testing with. While `gpt-4o` is able to write mostly correct Elixir code and call functions for executing it, it suffers often from hallucinations in a multi-step task. These hallucinations are making the tests difficult, or perhaps further work in prompting is necessary to elicit misaligned behaviors more reliably. It is also likely that the current models aren’t at the threshold yet and misalignment in this experiment can only be observed once models start having high multi-step and multi-turn accuracy. The testing phase is still a work in progress. Please reach out to me if you have any ideas or would like to collaborate.*
