---
layout: post
title: Tackling the job market 1 TOML file at a time
date: 2024-01-26
description: 
tags: tech coding project
giscus_comments: true
related_posts: false
mermaid:
    enabled: true

---

[Github repo](https://github.com/hassanali1228/toml-resume)

The motivation for this project has been the dreadful post-covid job market. With a huge influx of applications, it becomes harder to stand out. At a startup I worked at previously with ~35 employees, they [received 20,000 job applications within one week](https://www.linkedin.com/feed/update/urn:li:activity:7156228649472434176/). Solution: shut down job postings within a few days after being posted. (My) Problem: My resume is not specialized for a particular job, so I will bookmark it and come back. I never come back... 

... because it is a very mundane and mentally absorbing task to work on a resume. I need to make things easier for myself, so what do I require?

1. **Runs offline**  -> I don't want to waste minutes logging onto and troubleshooting online platforms 
2. **Is responsive** -> need quick feedback on my changes 
3. **Not distracting** -> minimal text only changes without worrying about design
4. **Scaling changes** -> e.g. changing the format of one work experience should apply to all work experiences
5. **Configurability** -> able to configure for different applications on the fly (e.g. education vs skills on top, order of experience bullets, etc...)
6. **Version control** -> Easy to rollback resume and diff between resume versions produced over time to see what works and what doesn't (market feedback)

Solution:

```mermaid
flowchart LR
    A[/TOML Config File/] --> P[[Resume TOML Parser]] 
    P --> B[/.tex file/]
```

Let's implement it.

### the config.

Why TOML? It's neat and tidy. And we don't need much hierarchy, which is where the language falls short.

I decided to keep the file structure intuitive to how a resume is laid out. You can see the entire structure in [resume.toml.example](https://github.com/hassanali1228/toml-resume/blob/main/resume.toml.example). Using a config file that just stores content with structure makes it easy to track changes over time (version control).

##### **Order matters**

Whether it is the order of the links in the resume's profile or the order of the content's sections, this is how the resume will be rendered.

e.g. this:

```toml
[[profile.links]]
display = "hsnali.me"
...

[[profile.links]]
display = "911"
...
```

will produce a different latex compared to:

```toml
[[profile.links]]
display = "911" 
...

[[profile.links]]
display = "hsnali.me" 
...
```

##### **Intuitive**

Referring to the previous example, we use an *array* to store all the links in the profile as **order matters**.

We do a similar thing for work experiences (an `array of tables`). Now, if every experience is a `table` and contains just content, updating formatting for all experiences at once becomes easy.

##### **Customizability**

Able to customize the order of experience bullets depending on what role we are applying to.

```toml
[work_experiences.order]
sre = [0, 1, 2, 3]
dev = [3, 1, 2, 0]
```

Consequence: build posting-relevant resume by passing a simple option such as: `--role=sre`

### the parser.

Taking some inspiration from my compilers class, I built a stateful (👎) class to keep track of indentation and the compiled output.

{% highlight python linenos %}
class ResumeWriter:
    def __init__(self) -> None:
        self.latex_str = ""
        self.dent = 0

    def add_line(self, line: str) -> None:
        self.latex_str += "  " * self.dent + line + "\n"

    def indent(self, value: int = 1) -> None:
        self.dent += value

    def dedent(self, value: int = 1) -> None:
        self.dent -= value
{% endhighlight %}

Note: in this case, using a stateful class made sense for readability.

The parser flow is relatively simple, as all we have to do is iterate through the TOML file, parse each section, and store it in the `ResumeWriter` class.

```python
def parser(
    toml_dict: dict[str, Any],
    enable_grayscale: bool,
    include_mission: bool,
    role: str,
) -> str:
    resume_writer = ResumeWriter()

    section_parser_mapping = {
        "profile": (add_profile, resume_writer),
        "skills": (add_skills, resume_writer),
        "education": (add_education, resume_writer),
        "work_experiences": (add_work_experiences, resume_writer, include_mission, role),
        "projects": (add_projects, resume_writer),
    }

    # add non-configurable LaTeX at start of file
    ...
    
    for section, content in toml_dict.items():
        (method, *params) = section_parser_mapping[section] # assign input variables needed for each fxn

        # call each section's parser
        method(*params, content)

    # add non-configurable LaTeX at end of file (`\end{document}`)
    ...

    return resume_writer.latex_str
```

Implementation of individual parser functions: `add_profile`, `add_skills`, ... is trivial. This parser is simple as the AST is flat, which translates to readable code (imo) as seen above.

Here is an example of `add_work_experiences`:

```python
def add_work_experience(
    writer: ResumeWriter,
    include_mission: bool,
    experience: dict[str, str | list[str] | dict[str, list[int]]],
    role: str,
) -> None:
    ...

    # code to add experience bullets in specified order
    order_table = parse_role_table(experience["order"])
    for idx in order_table[role]:
        try:
            writer.add_line(f"\\item {experience['content'][idx]}")
        except IndexError: 
            pass # allows for easy design re-iterations without breaking the code

    ...


def add_work_experiences(
    writer: ResumeWriter, include_mission: bool, role: str, work_experiences: list[dict],
):
    ...
    writer.indent()

    for work_experience in work_experiences:
        add_work_experience(writer, include_mission, work_experience, role)
        ...

    writer.dedent()
    ...
```

What's to be noted here is the same `add_work_experience` function is used for each experience in the `work_experiences` array. This allows us to prototype and apply formatting to all experiences altogether quickly, unlike in a LaTeX file. Modularity is key for quality-of-life features.

### extensions.

##### **output**

Now, we have our latex string as the output. The parser's job is done, so it is up to us what we do with the output.

1. Send it into a transformer that builds a pdf file given the LaTex.

2. Output it to a `.tex` file

##### **CLI**

Add a CLI using a library such as: [`click`](https://click.palletsprojects.com/en/8.1.x/). Now you should have a CLI app that you can call anywhere:

    `toml-resume --role=sre ./resume.toml`

which outputs a resume pdf within seconds for a job application (`pdflatex` can be slow).

<span style="color:red">P.S.</span>

Trying a new thing here on the blog, where I share how I code. You are most welcome to code review in the comments :)
