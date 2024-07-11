---
layout: page
permalink: /school/
title: school
description: reflecting on what I learn at school.
nav: true
nav_order: 3
toc:
    sidebar: left
---

Term-by-term course review of all technical ECE courses (in progress).

## ECE 3B (Fall 2023)

This term was the first term where you could choose your technical electives, and was consequently also project/lab heavy. Though it was nothing as dreadful as [ECE 350](#ece350) labs. I didn't take ECE 320 to avoid Yet Another Computer Architecture course, but I heard the course did not let down this term. On the other hand, there were some complications with [ECE 356](#ece356), my alternative choice. Overall, if you took the courses you enjoy learning about, it was a pretty fun term despite any complications.

### [ECE 351 (Compilers)](#ece351) {#ece351}

This course combines the theory that you learn in [ECE 208](#ece208) with the practical implementation of compilers. You don't need to be an aspiring compiler engineer to like this course, a general passion for programming should be enough. This is because you learn how to design languages (grammars, LL(1), AST, etc...). This comes in pretty handy as a programmer, for example, when writing [idomatic code](https://en.wikipedia.org/wiki/Programming_idiom). You also learn how grammar design decides compiler implementation (e.g. [recursive descent parser possible only for LL(*k*) grammars](https://en.wikipedia.org/wiki/Recursive_descent_parser#:~:text=possible%20only,LL(k)%20grammars)).

> **Practical use**: design a [TOML document that stores resume information](/blog/2024/toml-resume/) and a program that compiles a resume pdf given the TOML document.

You learn about garbage collection, register allocation, and dataflow analysis to perform compiler optimizations. The teaching staff likes leaving learning certain techniques as an exercise to the reader (e.g. performing dataflow analysis with topological sort). These concepts should ideally help you write better code and use the compiler to your advantage (e.g. immutability reduces garbage collection overhead).

The labs really dialed in on teaching compilers as a design pattern, where you have tokens, lexers, parsers and more. You learn about things like the [visitor design pattern](https://en.wikipedia.org/wiki/Visitor_pattern#:~:text=A%20visitor,the%20structures.) and the [interpreter design pattern](https://en.wikipedia.org/wiki/Interpreter_pattern#:~:text=In%20computer,computer%20language.). By the end of the course, you have a (ideally working) compiler for a subset of VHDL with optimizations done using boolean algebra. A major drawback is that the labs use Java, where you are forced to use OOP heavily. The chained polymorphic calls and heavy use of inheritance gets frustrating at times.

### [ECE 356 (Database Systems)](#ece356) {#ece356}

For someone like me, who hasn't touched databases at all in co-op, this course is a Godsend. However, for the students who have taken databases, the formalization of database design and query formulation was an annoyance. The course teaches the following:

1) **Logical Layer**

{: .indent}
You learn a) **DML** (Data Manipulation Language), where you first learn [Relational Algebra](https://en.wikipedia.org/wiki/Relational_algebra) (RA) and then how to derive SQL from the RA queries. b) **DDL** (Data Definition Language) which is your interface to create and modify the database structure. c) **TCL** (Transaction Control Language) which you don't formally learn in the course, but through the development of a project that leverages the MySQL API.

2) **Database Design**

{: .indent}
You learn a) how to logically come up with an ERM (Entity Relationship Model), which accurately represents what the data is supposed to mean. You do this using ERM constructs like Entity Sets, Relationship Sets, Aggregation, etc... b) how to translate an ERM into an RM (Relational Model), so we can construct the database in SQL using the aforementioned RA constructs. c) Normalizing data using functional dependencies to avoid [unstructured duplications](https://en.wikipedia.org/wiki/Database_normalization#:~:text=reduce,integrity). 

3) **Database Performance**

{: .indent}
Was a bit of a rush to get through this content due to some disorganization in the course. We touched on interesting topics like how B+ trees are used for indexing where range-based lookups are done vs. hash maps for direct lookups. Most emphasis was placed on using these concepts to debug query performance and suggest improvements using `explain` and `explain analyze` in MySQL.

The practical portion of the course were 10 assignments, each taking ~5 hours on average, where you were challenged to apply concepts learned in class against an NHL database. They were decently designed, but were not helpful as most of these were not graded until we had written the final exams. The teaching staff was quick to respond on Piazza, but with one TA, they could not grade any deliverables in time.

The project challenged students to design and implement a database, client, and an ML algorithm. With the staff's responsive feedback you could be sure your design was optimal. However, implementing the database and testing it was a nighmare due to the high server load, as a result of the project being released late.

The exam was fair and it tested your understanding of course content well. Overall, the course was very useful and the content was interesting, but it is a shame that it wasn't well organized.

### [ECE 358 (Computer Networks)](#ece358) {#ece358}

The goal of the course is to teach how a packet of data gets from one end to the other, and it does so by iterating through protocols in the [OSI model](https://en.wikipedia.org/wiki/OSI_model). Though the content at some points can be "dry", it can be enjoyable if you justify solutions by drawing intuition from predecessor courses. For example, you are challenged to use [ECE 307](#ece307) concepts to justify random MAC protocols: [CSMA/CD](https://en.wikipedia.org/wiki/Carrier-sense_multiple_access_with_collision_detection). This course also sets up a good foundation for distributed computing, as you are challenged to keep things decentralized and decoupled to build good networks.

The labwork was mainly building realistic simulations for network packet transmissions with statistical distributions, and rebuilding network layers. They weren't challenging if you are familiar with basic stats, binary math, and organizing your code. I found it more fun to structure my code for high reusability, compared to solving any network-related problems. The labs didn't challenge you to optimize, think creatively, etc..., *which I believe is a flaw in the course teaching*.

### [ECE 307 (Probability Theory and Statistics 2)](#ece307) {#ece307}

Easy quizzes, difficult final for realistic averages. Prof ensured everyone passed the class, fair play. Learned lots of important stuff needed to make good decisions, such as hypothesis testing, Bayesian statistics, and more. Did not get to apply knowledge within course, but significantly useful in courses outside.

## ECE 3A (Winter 2023)

A mixed bag. You don't have any choices, but every course is pretty important to understand. The good is you learn important stuff, and for most of the part the teaching methodologies are relevant. The bad is when they are irrelivant and the labs become a burden, and the supposed-to-be-fun courses become a task. Also, I feel like by your 3rd year you should be able to choose electives of your liking.

### [ECE 350 (Real-time Operating Systems)](#ece350) {#ece350}

Before this course, students have a basic understanding of syscalls, trap requests, kernal/user space, multi-processing/threading, context switching, etc... from [ECE 252](#ece252). Now, all these topics are visited in detail, and you learn how the kernel implements security by isolating privelages in kernel mode, drawing inspiration from Unix.

Since it is a _real-time_ OS course, there is a focus on task pre-emption/scheduling policies, priority inversion, lock contention, interrupt servicing, cache coherence, which all work together towards ensuring that the task runtime is **predictable**. It generally is the case that understanding how and when tasks operate within a system often leads to the creation of a more efficient system, where tasks aren't confined to specific runtimes. 

The labs in this course are the most time-consuming task this term. I am bad at keeping track of time but there were a few all-nighters in the lab, spent debugging if it's the board or the OS that's wrong, and rewriting code that others got wrong. Often, you will find code jumping to unexpected locations which is initally confusing. You have to think like an OS to debug these labs, which makes them really good for understanding what an OS does. 

Grading of the lab is dependent on relative performance to the rest of class. The key to getting the extra marks, is not to use the over-engineered solutions, but to rather reason about where the CPU cycles are spent, and what needs to be in memory.
