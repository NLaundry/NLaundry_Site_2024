+++
title = "How to Write your own HCI Related Works Section Template"
date = 2022-02-24
draft = false
tags = ['Academia', 'Writing']
+++

What you’ll find here

* My process for figuring out what to include in a literature review
* The template I built for writing future literature reviews

So recently I had to write a literature review for one of my classes and I wanted to formalize my approach to doing so. I’ve been working on it for the past few days and thought I’d share what I’ve done so any new graduate students, like myself, can learn from it and hopefully share their own tips and tricks.

Before we get started, a brief disclaimer. I’m talking about the “related work” section of a paper, not systematic literature reviews. These, I believe, are structured differently. The TLDR is that a related work section has the goal of establishing the value of your research by identifying a problem, a gap in the existing work, and proposing your Research Question as the missing puzzle piece. Systematic literature reviews are larger projects focused on categorizing and building perspective about the current state of a field of research.
The Process: Finding out what to include in your Literature Review

Writing styles vary from one domain of research to another. A good literature review and writing style in Engineering may be laughed out of a social science paper review process and vice versa. So how do we determine what’s good in our field, or any field for that matter? Like great creatives before us, we “steal”.
Borrowing from the Greats: How to Extract the Essence of the Literature Review

As a computer scientist and programmer, problem decomposition is a core skill. I view everything I do through the lens of these two questions “what are the parts of this problem?” and “how can I solve them independently?”

When it comes to any creative process (and make no mistake, research is as much a creative process as it is a technical one) we should start by analyzing what other people have done. So step 1 is to find high-quality papers in your field of interest and read them. Google Scholar or whatever tools your university has available to you are great and easy places to start. You can also ask your advisor for their papers and anything they’re currently reading.

Once we’ve familiarized ourselves with the feel of a good paper, we can begin to break them down and build up what makes them good from the bottom up. I like to decompose things to the smallest useful component, summarize it, identify its goal, and how achieving this goal adds value.

When talking about literature reviews, I think the smallest useful component is a paragraph. Remember in grade school when our teachers taught us every paragraph should deliver a single idea. We should identify what that idea is so we can figure out how it contributes to the paper as a whole.

Some common topics I identified while reading are:

    Introducing the history of the field your research exists within
    Establishing a problem within the field. This could be:
    an algorithm isn’t fast enough to satisfy a niche use case
    students don’t learn a specific skill effectively on their own
    facial recognition software is disproportionately accurate on certain demographics
    Exploring a related problem
    Explaining a solution that is applicable to your problem and how it was used in another domain

Once you’ve established what each paragraph does, examine the value that it brings to the paper:

    Does this provide confidence that this is a long-standing or growing field?
    Does this establish a problem that the research solves?
    Does this demonstrate that previous research has been done in similar spaces?

Next up, by identifying the purpose of each paragraph in a section, we can surmise the larger purpose of that section. Some insights I had while working include:

    “all of these paragraphs work to establish the field and an open problem”
    “This whole section is about problems in other domains with solutions that could be applied to the author’s research question”
    “The related problems demonstrate similar ideas but show that this particular problem is still unsolved”

Lastly, we can create a framework from the insights we found while breaking down good literature reviews. Simple frameworks can be things like checklists that make sure you’ve shown there is a problem worth solving, identified quality research that relates to your work briefly talked about the history of the field, etc. The best part about simple checklists is the next time you write a paper, you can reuse them to make sure you’ve written a complete lit review. If you identify a research problem early, you can tailor your paper search to find other works that will help you tick things off your checklist.
My Literature Review Template

Alright, now we’ve discussed HOW you can build your own lit review template. Let’s take a look at what I came up with and how I fleshed it out for my most recent review in the field of Technology Enhanced Learning.

I began by breaking apart the example review provided by Professor Joe Sawada on Pivot Gray Codes for the spanning trees of a graph. This is the general structure I noticed.
Introduction

    should establish:
    the value and a brief history of the field in general.
    This might include notable names in the field, their early contributions, and how the field is growing in recent years
    the problem and why it hasn’t been solved
    Introduce a problem in the field that hasn’t been solved or could use a better solution.
    Talk about how the problem fits into the field, (in my case how this is a Technology Enhanced Learning Problem)
    the value of a solution to the problem
    Show that by creating a solution to this problem you’re contributing to the field of research and/or improving lives in some way.
    Should introduce the research question
    should be clear that, if answered, the RQ solves the problem

Related problems and Solutions
Purpose

    Should:
    cover related problems and solutions (duh)
    Spend extra time on solutions you plan on using and how they’ve been successful in other domains
    demonstrate the gap in the research
    It should feel like your research is THE missing puzzle piece in this field
    The reader should see a clear path to addressing your research question based on the solutions and technologies you’ve introduced.

Using the Template

Once I had this template built up I used it to flesh out and develop my own ideas for my lit review. I’ll show you what that looks like.
Introduction

    The field
    TEL
    integrates new technologies with the goal of improving learning, teaching efficiency, etc.
    List some uses of how TEL improves learning and its history, plus pioneers of the space
    The problem
    Computational thinking + problem decomposition for CS
    one area where students struggle in particular is Computational Thinking … definition here
    POTENTIALLY: Show some citations about how CT is becoming a very hot field
    Computational thinking is a particularly poignant skill for Novice Programmers to develop …
    A key part of CT is problem decomposition, the ability to break down a problem into its component parts to simplify problem-solving. Solving each smaller component problem leads to a solution to the larger initial problem.
    The specific problem: Non-Scalability of Intervention + Feedback
    Students often struggle to develop Computational Thinking themselves and programming assignments are often graded on their functionality not how they’re developed — cite
    Intervention during the problem decomposition process helps but is not scalable — cite the intervention helps part
    not scalable because individual intervention in large classes is ridiculously time-consuming
    Students don’t learn computational thinking on their own very effectively and it’s not taught very well
    Problem decomposition specifically
    Introductory CIS assignments require that students accomplish a set of functional or algorithmic requirements and those requirements are often embedded in dense assignment descriptions — a clear opportunity for the development of problem decomposition skills
    This begs the question, how do we scale individual guidance to better teach students?
    RQ — Solves this problem by giving students one on one help decomposing assignments
    Can we create a formal component structure for CIS assignment descriptions and automatically identify these components in an assignment?
    If we can do this, we’re automatically creating teachable examples of problem decomposition

Related Problems and Solutions

    Other formalizations of Software requirements decompositions
    Agile software development practices make use of the following formalized problem decompositions:
    Software requirements documents
    UML diagrams
    User stories
    Ontologies
    A method of creating formal structures and storing examples to create knowledge bases
    List the properties of ontologies: formal, explicit, shared
    Ontology-based software requirements and user stories citation
    UMLS medical Ontology
    FIBO financial Ontology
    TRANSITION:
    once we’ve formalized the components the problem can be decomposed into, we can automate that decomposition.
    Developing Knowledge bases and identifying Ontological entities
    The automatic population of knowledge bases
    NLP Entity recognition techniques
    An example of using popular NLP techniques to populate knowledge bases from unstructured text- that one Russian paper + building structured KBs for trustable NLP apps
    In other fields of research automatically tagging ontological entities for categorization is a well-explored area of research
    Medical MetaMap example — automatically tagging UMLS ontological entities in near plain text documents.

With this template filled out, completing my lit review has been a fairly simple process of turning bullet points into paragraphs, finding missing citations, and formating. The hard work is forming good ideas, so focus on that first. Develop the skeleton of your paper with good ideas and real value and the words will come naturally.

So there it is — my lit review framework and how I used it to write my own. The assignment is due Monday at 11:59 pm. I’ll upload it here when it’s done so you can see my finished product. Thanks for reading, feel free to reach out if you have any questions, want to chat, or have some tips of your own for me.

Cheers,

Nathan Laundry

February 24, 2022

Catch my blogposts earlier at my personal blog
Check out my Email Newsletter #GuidingQuestions
