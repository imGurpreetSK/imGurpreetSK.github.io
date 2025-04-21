+++
title = "Prompting 101"
date = "2024-12-17T23:51:03-08:00"
draft = false
author = "Gurpreet"
tags = ["notes", "ai", "prompting"]
+++

Course link: https://learn.deeplearning.ai/courses/chatgpt-prompt-eng/lesson/1/introduction

1. Contents
    - Best practices
    - Common use cases

2. Types of LLMs

    1. Base: Predicts next work based on text training data

    2. Instruction tuned
        - Follows instructions; fine tuned on instructions.
        - Trained on inputs and outputs
        - Uses RLHF - Reinforcement Learning with Human Feedback
            - Helpful, Honest, Harmless
        - Recommended for most practical use cases
        - While using, think of giving instructions to another human
            - Quantity of information tailored to the kind of response expected is optimum
        - Be clear and specific

3. Guidelines for prompting - Principals and Tactics
    - Write clear and specific instructions
    - Doesn’t mean it has to be short - longer prompts can be better and provide more insights.

4. Tactics
    1. Use delimiters to clearly indicate distinct parts of the input
        - Delimiters also help with avoiding prompt injection
    2. Ask for a structured output
        - Provide an output format where feasible
    3. Ask model to check whether conditions are satisfied
        - Conditional prompt (if..else)
        - Any assumptions can yield wrong outcome
        - Exit early after checking conditions
    4. Few-shot prompting
        - Providing successful examples of (part of) tasks to be performed - “What does success look like?”
    5. Give model time to ‘think’
        - Complex tasks can take a long time/computation. Tell the model to take more time to get an answer

5. Tactics
    1. Specify steps required to complete task
        - Ask for output in specific format
    2. Instruct the model to work out it’s own solution before rushing to solution
        - Ask to do it’s own work, then compare and evaluate - “Do not decide if solution is correct until you’ve done the problem yourself”.

6. Model limitations
    1. Hallucinations - making statements that sound plausible but aren’t true
        - Known weakness of models at current time

7. Iterative prompt development
    1. First prompt to solve a problem rarely works the first time
    2. Iterate and get closer to the desired result
        - Refine with a batch of examples
    3. Be precise and clear
    4. Giving a role and task can help

8. Common use cases
    1. Summarizing text
        - Giving purpose helps generate better results (more context)
        - Limit by sentences/words.
            - Doesn’t always adhere to provided limit
            - Character limiting rarely works due to tokenization mechanism
    2. Inferring
        - Making sense of sentiment - whether something is positive or negative
        - LLMs are good at extracting information from a info source
        - “Zero-shot learning”
    3. Transforming
    4. Expanding
        - Temperature
            - Lower temperature (0), more reliability, predictability
            - Higher temp yields more variety (randomness, creativity)

-----

### Self notes

   1. Maybe working backwards from expected result would work coming up with proper requirements
