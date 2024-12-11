---
layout: page
permalink: /work/
title: work
description: detailed work experience descriptions.
nav: true
nav_order: 3
toc:
  sidebar: left
---

## [Databento](https://databento.com "Company website") {#coop4}

**Are the servers running slow or is it just me...**

[company blog feature](https://databento.com/blog/meet-hassan)

I took notice of Databento when I saw the CEO's take on the FTX collapse. I was intrigued by the firm, as their mission to make a "simpler, faster way to get market data" entails many interesting infrastructure challenges. This is the most "full-time-y" internship I have had, where I got to work on tickets (mainly automation and service monitors), along with a fun project.

I was tasked with creating live histograms that measure latency between every colocation centre and the stock exchanges they reach. If I could go back, I would take some inspiration from [twitter's solution](https://blog.twitter.com/engineering/en_us/a/2013/observability-at-twitter) as I really like their sophistication. My approach was to use a reader/writer pattern implemented at each colocation centre. One thread would listen for latencies, and the other would aggregate these latencies into a histogram and store them persistently. As the reader thread only listens for latencies, latency measurements were accurate. Aggregation intervals were configurable to minimize effect of server downtime. Redis was used as the persistent store for fast querying and writes. I liked working on this project as it was a fun and simple introduction to distributed systems. I also learned a lot from the team, who all shared a background and interest in finance.

## [Stealth Media](https://stealthmedia.com "Company website") {#coop3}

**Gaming, cinematics, and everything in between**

I met my to-be-manager on a plane ride back home during vacations. It took courage to start a conversation with someone a seat across, but I found his sketch intriguing and let my curiosity take over. This internship was different in all aspects. I was responsible for engineering a game show suite, encompassing the game itself, network architecture for participants, viewers, and studio equipment, as well as the hardware needed for podiums, and other elements... This was a bit of a squeeze for the 4 months, however my goal was to make everything plug-and-play so adding future functionality _incurs minimal engineering cost_.

Often times, I had to decide between making a feature scalable and easy to maintain, or just functional. There was a rush at the firm to create prototypes for stakeholders, which taught me how make decisions and defend them deductively. The [engineering design process](https://en.wikipedia.org/wiki/Engineering_design_process) really helped in making sound design decisions. In hindsight, this helped me build a more decoupled infrastructure as I reasoned about each problem individually. The most relevant coursework for my work was: systems programming, embedded systems, and linear algebra. Overall, I was pleased with this internship as it was a testament to the practical use of what I have learned so far.

## [Citadel](https://www.citadel.com "Company website") {#coop2}

**Everything is a hashing problem**

[at the bottom of this bloomberg page](https://archive.md/Fw61Z)

After the lenghthiest interview process I have gone through, I was a part of Citadel's compute engineering division. My team was responsible for creating and maintaining all Linux related infrastructure, including the OS images themselves. While they were rapidly building the infra to create and deploy these images, the storage and management overhead for the images was getting out of hand. Hence, I was tasked with making these image deployments immutable, which would solve many issues, including: wasted storage, long rollback times, slow deployments, and [configuration drifts](https://www.opslevel.com/resources/understanding-and-managing-configuration-drift).

The key to understanding the existing infrastructure in place and implementing my solution was _listening_ to the people who created it. Over time, I learned how to solve problems **functionally**, where every problem is a [black box](https://en.wikipedia.org/wiki/Black_box), with its respective input and output. When we understand this, every image deployment can be viewed as a black box process that produces an output: OS image, which can be can cached for a set of inputs: `{os-image-dependency-set: os-image}`. When we detect a change in dependencies, we build a new image tracked with a different version. This opened up many oppurtunities for improvements, such as cached and concurrent builds. I am very grateful for this learning experience, as it taught me how to decompartmentalize and solve problems systematically.

## [Questrade](https://www.questrade.com "Company website") {#coop1}

**Infra is cool?**

After applying for full stack roles all term, I had my first interview for an infra engineering role. Not knowing much about the field, I felt underqualified. There was significant hype around general SWE (backend / frontend / client-facing) roles at the time and not many were interested in infra. It is over the course of the degree that students realize the value of performant and scalable infrastructure (YAML engineer =/= configuration monkey). Luckily for me, my interviewer did not quiz me on arbitrary infra technologies, but he rather showed me that infrastructure problems are mostly intuitive but fruitful. I was able to derive most answers based on patterns learned in any engineering course.

I learned how to fix problems using code. Being an intern, anyone on the team was free to use me as a resource. Hence, I was able to contribute to several high impact projects, teaching me a diverse set of skills. [API Facade](https://en.wikipedia.org/wiki/Facade_pattern) was my favourite thing to learn; this was my introduction to system design. I was able to architect a system for servers to pull confgurations by calling an API endpoint, that fetched configs from a data store (google sheets ;-; at the time). This lowered downtime, as servers would go into "[sunset](<https://en.wikipedia.org/wiki/Sunset_(computing)>)" only when software required updating (e.g. antivirus updates). This internship experience proved invaluable later down the line, as it made infrastructure accessible to me.
