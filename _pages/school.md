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

This term was the first term where you had a choice between technical electives, and consequently also project/lab heavy. Though it was nothing as dreadful as [ECE 350](#ece350) labs. I didn't take ECE 320 to avoid taking Yet Another Computer Architecture course, but I heard the course did not let down this term. On the other hand, [ECE 356](#ece356), my alternative choice, was a different story. Overall, if you took the courses you enjoy learning about, it was a pretty fun term despite any other complications.

Difficulty:

    ECE 307 > ECE 356 > ECE 351 > ECE 358

#### Compilers (ECE 351) {#ece351}

This course combines the theory that you learn in [ECE 208](#ece208) with the practical implementation of compilers. You don't need to be an aspiring compiler engineer to like this course, a general passion for programming should be enough. This is because you learn how to design languages (grammars, LL(1), AST, etc...). This comes in pretty handy as a programmer, for example, when writing [idomatic code](https://en.wikipedia.org/wiki/Programming_idiom). You also learn how grammar design decides compiler implementation (e.g. [recursive descent parser possible only for LL(*k*) grammars](https://en.wikipedia.org/wiki/Recursive_descent_parser#:~:text=possible%20only,LL(k)%20grammars)).

> **Practical use**: design a [TOML document that stores resume information](/blog/2024/toml-resume/) and a program that compiles a resume pdf given the TOML document.

You learn about garbage collection, register allocation, and dataflow analysis to perform compiler optimizations. The teaching staff likes leaving learning certain techniques as an exercise to the reader (e.g. performing dataflow analysis with topological sort). These concepts should ideally help you write better code and use the compiler to your advantage (e.g. immutability reduces garbage collection overhead).

The labs really dialed in on teaching compilers as a design pattern, where you have tokens, lexers, parsers and more. You learn about things like the [visitor design pattern](https://en.wikipedia.org/wiki/Visitor_pattern#:~:text=A%20visitor,the%20structures.) and the [interpreter design pattern](https://en.wikipedia.org/wiki/Interpreter_pattern#:~:text=In%20computer,computer%20language.). By the end of the course, you have a (ideally working) compiler for a subset of VHDL with optimizations done using boolean algebra. A major drawback is that the labs use Java, where you are forced to use OOP heavily. The chained polymorphic calls and heavy use of inheritance gets frustrating at times.


