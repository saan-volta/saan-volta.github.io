---
title: About Me

menu:
    main: 
        weight: -90
        params:
            icon: user
---


Welcome to my page. I will get this place in order one day.

My name is Anton and I'm an undergrad studying math and compsci at Arizona State university. I focus broadly in probability theory but I like and do all sorts of things.

I occasionally play CTF(Capture the Flag) competitions with our local uni team, CTF Academy, as well as with Shellphish.

I speak Russian and English and at various points have also studied Spanish and Mandarin.


### Here's some stuff I like, in no particular order:

✶ Stochastic processes & models, statistical algos, information theory;

✶ Diffusion models, reinforcement learning;

✶ Random, approximation, and reconstruction algorithms;

✶ Graph theory;

✶ Cryptography & cryptanalysis;

✶ Computational biology;



### What I'm working on right now:

✫ **Minimum entropy coupling algorithms**: this implements some techniques from a series of papers on algorithmic information theory; namely, how to construct a coupling between two probability distribution that has the smallest possible entropy, i.e., "most ordered". In general, the problem is NP-hard, but there are approximation algorithms for different kinds of probability distributions. One interesting use case is in steganography, where you can construct a coupling with an LLM's conditional token distribution and generate covertext that contains hidden data while maintaining provable perfect secrecy.

✫ **Random process algorithms on graphs**: this is a research project for my class on stochastic modelling! I've been spending some time investigating graph algorithms that can be modelled as stochastic processes. Some interesting topics in this area are on random approximations to problems like hypergraph packing, vertex cover search, and graphon estimation.

✫ **DeepMapDB**: this has been a long-running on-and-off r&d project on the following question: How much data can you fit in a neural network? Put more specifically, given a dictionary-format database, what is the smallest NN that can "memorize it" under fixed train time constraints and up to near-perfect accuracy? This task has applications in scenarios in plain data compression, DMBS with high-volume querying requirements, and database deployment on space and memory-constrained hardware. 

✫ **crypto.college**: my university uses pwn.college, a platform for hosting CTF-style challenges to teach its cybersec classes. Check it out, it's brilliant. Though security such as low-level exploitation is not really my forte, I have some knowledge of cryptography from playing CTFs and personal research. I'm currently working with a team on expanding pwn.college to include several modules of cryptography material & challenges, which will be hopefully used one day to teach classes at ASU!






