---
layout: post
title: Immutability in Infrastructure and Why?
date: 2024-01-20
description: 
tags: infra tech
giscus_comments: true
related_posts: false
mermaid:
    enabled: true

---

Let's first look at what immutability looks like in software.

##### **Immutable**

{::nomarkdown}
{% assign jupyter_path = "assets/jupyter/immutable.ipynb" | relative_url %}
{% capture notebook_exists %}{% file_exists assets/jupyter/immutable.ipynb %}{% endcapture %}
{% if notebook_exists == "true" %}
{% jupyter_notebook jupyter_path %}
{% endif %}
{:/nomarkdown}

Here is an immutable data structure: a tuple. Once created, it can not be modified and therefore the definition is **concrete**. It is this attribute that makes the object hashable. 

##### **Mutable**

{::nomarkdown}
{% assign jupyter_path = "assets/jupyter/mutable.ipynb" | relative_url %}
{% capture notebook_exists %}{% file_exists assets/jupyter/mutable.ipynb %}{% endcapture %}
{% if notebook_exists == "true" %}
{% jupyter_notebook jupyter_path %}
{% endif %}
{:/nomarkdown}

Here is a mutable data structure: a pythonic list. The object can be modified at runtime. It is this attribute that makes the object unhashable. There is no reliable value to calculate a hash from the object's definition. (We are going to ignore `def __hash__(self) => id(self)` can be used in python, as `id` is [guaranteed to be unique and constant for this object during its lifetime](https://docs.python.org/3/library/functions.html#id). This goes further into state invariants and compiler theory.)

## Immutability in Infrastructure

According to [CNCF](https://glossary.cncf.io/immutable-infrastructure), immutable infra is **infra that cannot be changed once deployed**. It is a challenging problem, for example with VM deployments, users can add file system mounts and this couldn't be tracked. Now if the VM goes down, restoration is difficult. True [Infrastructure as Code](https://glossary.cncf.io/infrastructure-as-code/) (IaC) largely solves this problem as each "release" (immutable) is tagged with a version (our `hash`) in version control. We can implement IaC by using containerization where each (docker) image has a tag. We need further steps to ensure users can't ssh and make [breaking changes](https://en.wiktionary.org/wiki/breaking_change#:~:text=Noun,code%20used%20by%20multiple%20applications.). Every change must happen through code.

Referring back to immutability in programming, immutability enables *referential transparency* (fxn `y` always returns `z` for input: `x`) as the value the variable `x` refers to never changes. This in turn enables pure functions (*and by extension, functional programming*). 

In IaC, we see that immutable deployments ensure everything is constant for a deployment ***state***. We can refer to this ***state*** by its release version (or `hash`). Knowing what immutability allows us to do, we can extend beyond just *immutability in infrastruture*. What if we tracked the inputs and outputs for each deployment process? We now enable [local reasoning](https://degoes.net/articles/fp-glossary), where inferring the correctness of the system is not dependent on *prior states* or *all inputs*. This translates to being able to pin down what file is exactly responsible for an issue in your deployment (solving by elimination is now feasible 😧). Tracking inputs in IaC becomes relatively easy when we use config files to store everything and use tools like [pantsbuild](https://www.pantsbuild.org/) that leverage functional programming standards, transitive dependencies, and such. You might also want to look at [NixOS], a declarative operating system based on configuration files.

### Why?

##### **Simpler and Safer deployments**

Now since every change goes through code, we can incorporate CI/CD pipelines to make changes on the fly. The pipelines become easier to write as we don't need to worry about a global state when building, it's just chained functions: (Config A, B, C -> Proc A -> Artifact A, B -> Proc B -> Image-0.0.1).

There also exist tests in the pipelines to ensure deployment safety. Since we can reason about functions locally and all artifacts used to build the images are *constant*, these tests become very simple to write.

##### **Faster deployments**

As we know all config files are constant and every Process will return `B` for every input `A`, we can share artifacts between different image builds. For example:

```mermaid
---
title: Dependency Graph
---
flowchart TD
    IA[Image A] --> A{Proc A}
    IB[Image B] --> A{Proc A}
    IC[Image C] --> B{Proc B}
    A --> CA[Config A] 
    A --> CB[Config B]
    B --> CC[Config C]
```

By caching our output at `Proc A` for its inputs, `Config A` and `Config B`, we speed up our deployment builds.

Furthermore, we can avoid builds that do not change, for example, when only `Config A` is changed, we do not need to build `Image C`. This is a direct consequence of knowing our In's and Out's in the deployment process.

##### **Storage overhead**

We know immutable objects make garbage collection easy in programming languages, due to the elimination of duplicate objects. Similarly here, the artifact created by `Proc A` is not duplicated and hence lowers the storage overhead of the deployment build.

On a large scale, when our images are tagged: ImageA.v0, ImageA.v1, ImageA.v2, ..., we only need to keep a single copy of each. Without immutable deployments, we can not tag images as they can mutate. As a consequence, we do not know what is where and why it is 😨.

##### **Support overhead**

As one might guess, there are going to be a lot fewer support calls now that we can see what broke the build. Ideally, most breaking changes are detected at the CI/CD level. If a new release goes bad, rollbacks are a lot easier since everything is tagged in Git. If a user adds a fs mount that doesn't work, they can't (ideally). 

##### **Engineering overhead**

You might need to hire a few engineers who are specialized in infra and can implement a system like this. But that's a win in my books :)
