---
title: 'Review: The Book of Why'
description: A review of The Book of Why by Judea Pearl and Dana Mackenzie.
date: 2021-03-07
tags: ['review', 'books']
layout: layouts/post.njk

---


[*The Book of Why: The New Science of Cause and Effect*](http://bayes.cs.ucla.edu/WHY/) is a collaborative effort aimed at a general audience by [Judea Pearl](https://en.wikipedia.org/wiki/Judea_Pearl), a professor of computer science at UCLA, and [Dana Mackenzie](http://danamackenzie.com/), a science writer. As a medical student with a laughably poor formal education in statistics, I believe I qualify as a card-carrying member of the general audience. Here's my review in three parts: i) an overview of topics covered by the book, ii) my thoughts, and iii) links to other reviews and further reading.

The book grounds Pearl's theoretical work on causal inference in a multitude of realistic and real examples, drawn from a range of disciplines including economics, biology, and law. Pearl himself is a pretty [Big Deal](https://amturing.acm.org/award_winners/pearl_2658896.cfm) in computer science. Throughout the book, he demonstrates how his causal methods can be applied to many diverse fields of research, but it's clear that what excites him the most is its potential in artificial intelligence. 

What is causal inference? When we perform scientific studies, we're often interested in the relationship between variables (treatments, risk factors, diseases, outcomes). Scientific studies that investigate these relationships can be divided into two types: observational and interventional. Observational studies can identify associations, but they cannot tell you anything about the direction of that association. There may be other variables that distort your results by concealing a true relationship, or worse, by generating false ones. That's why researchers turn to interventional studies as a more reliable source of evidence&mdash;specifically, randomised controlled trials (RCTs). The problem is, RCTs are not always ethical or feasible, and they require more resources to set up and run. Enter the exciting field of causal inference, which aims to answer questions about cause and effect from observational data.

A brief word on what causal inference is *not*&mdash;this is something that took me a while to wrap my head around. Causal inference doesn't tell us from scratch what the causes of an event are. Nor is it able to "prove" that one variable caused another. Those questions belong to the realm of causal *discovery*. Instead, causal inference aims to draw richer interpretations beyond mere associations from observational data. We can answer questions such as:

- Which of these variables actually affect the outcome?
- What is the magnitude of effect of each individual treatment variable on the outcome?
- What are the mechanisms underlying a complex problem such as cancer?

The catch is that, whether explicitly or implicitly, you must make
assumptions in the first place about the directions of causality among the variables. In many cases, the data can *support* your assumptions, but any
relationships you observe in the data without considering these prior
assumptions are purely correlation. And you know the drill,
*correlation is not causation*...

There are many approaches to causal inference, of which Pearl's is
characterised by two techniques: graphs (DAGs) and *do*-calculus. More
on that in a minute.

## Ladder of causation


> Rung 1: Associations, observational data (seeing)

> Rung 2: Intervention (doing)

> Rung 3: Counterfactuals (imagining)

Pearl proposes this framework which we can use to categorise the various techniques of causal inference. At the bottom lies passive observation of a system,
allowing us to find direction-free associations. The next rung up is
intervention: by explicitly setting the values of treatment variables,
we abolish the effects of confounders. This is even more effective
with randomisation, which is why RCTs are hailed as the gold standard
of scientific studies. 

Going beyond intervention is the idea of the counterfactual, or
*what-if* thinking. If this person had never smoked, would he
still have gotten lung cancer? Counterfactuals are subtly but
crucially different from interventions. These hypothetical questions
pertain to what might have been, and cannot actually happen in the real
world. We can intervene by stopping that person with lung cancer from
smoking now, but we can't erase that lung cancer patient's 50-year history of smoking, which is the counterfactual question we're asking in this
case. Think of counterfactuals as parallel universes. Counterfactuals strengthen the case for a causal relationship:
if $A$ causes $B$, then $B$ shouldn't happen in the abscence of $A$. Of
course, things are rarely this simple in real life, and the authors also discuss *necessary* and *sufficienct* causes.

## DAGs
[DAG](https://en.wikipedia.org/wiki/Directed_acyclic_graph) stands for "directed acyclic graph". A DAG is simply a diagram
containing nodes that are connected by edges (lines) with directions,
and tracing these edges according to their directions should not result in a closed loop. In causal analysis, DAGs are used to represent
the relationships between variables in a system, with the directed
edges indicating the (assumed) direction of causation. 

Pearl is a vocal proponent of graphical models in causal
inference. DAGs, he argues, are much more intuitive than algrebraic
expressions in illustrating complex problems. Importantly, DAGs can
clearly tell us which variables should be controlled for and which
ones should be left as they are. Confounders (common causes) are commonly defined as variables that are associated with both the treatment variable and the outcome variable, and everyone knows we need to control for confounders. However, there are in fact other types of variables that are associated with both the treatment and the outcome that should *not* be controlled for. A common example is collider variables (common effects); controlling for a collider will create a spurious association between treatment and outcome, where there was previously none. DAGs are
invaluable in highlighting these potential pitfalls as our models get
more complex.

See [here](https://cjasn.asnjournals.org/content/clinjasn/12/3/546/F3.large.jpg) for graphical representations of confounders, mediators, and colliders.


## *do*-calculus

*do*-calculus is the other pillar of Pearl's approach to causal
inference, refined over the years by his students. I have to admit
that this entire topic has flown right over my head, and the authors don't
make it easy to follow in the book. Reading about how *do*-calculus
was derived and how it may be used felt a lot like "[How to Draw An
Owl"](https://knowyourmeme.com/memes/how-to-draw-an-owl) material.

At a high level, *do*-calculus is basically a formalised, mathematical
way of expressing interventions in contrast to observations. Conditional probabilities contain no information about causality. $P(A \mid B)$ represents the probability of $A$ given that $B$ is *observed*, a concept that lies at the bottom rung of Pearl's Ladder of Causation. To show the probability of $A$ when you cause $B$ to happen in an experiment, you would write $P[A \mid do(B)]$ instead. When you're able to manipulate a treatment variable statistically, you can also delete arrows pointing towards it in a DAG, which may reveal new confounders, colliders, and mediators. Using [front-door adjustment](https://stats.stackexchange.com/questions/350267/a-layman-understanding-of-the-difference-between-back-door-and-front-door-adjust), we can also control for *unobserved* confounders, which is pretty darn nifty.

## Topics

As its subtitle suggests, *The Book of Why* is ambitious in scope and
touches upon a dizzying array of seminal ideas from the 19th and 20th
century. These are the main topics covered in each chapter (paraphrased by me to be more informative; not actual titles).

- Chapter 1: Pearl's Ladder of Causation
- Chapter 2: Sewall Wright and path analysis, a major
  influence on Pearl
- Chapter 3: Bayes' theorem and Pearl's work on Bayesian
  networks
- Chapter 4: Confounders, mediators, colliders, and back-door adjustment
- Chapter 5: How causal inference could have resolved the
  smoking-and-cancer debate
- Chapter 6: Causal inference applied to statistical paradoxes
  (Monty Hall problem, Simpson's paradox, Berkson's paradox, Lord's
  paradox)
- Chapter 7: *do*-calculus, front-door adjustment, and instrumental variables
- Chapter 8: Counterfactuals, potential outcomes, and structural
  equation modelling
- Chapter 9: Mediation analysis
- Chapter 10: The role of causal inference in Big Data and AI

## My thoughts on *The Book of Why*


Let's start with the obvious. This book dwells on the history of statistics a lot, and statisticians, as the authors would have you believe, are zealots who have conspired to keep causal thinking out of their field right from the start. That is, until Pearl instigated the "Causal Revolution", as he dubs it, the latest and greatest gift to modern science. I have no dog in this fight, but Pearl (whom I assume is the source of most of these opinions put to paper by Mackenzie) often comes across as wildly biased and grandiose. For what it's worth, I doubt that statisticians as a whole are anywhere as malicious or ignorant as they're portrayed in this book.

I'm confused about the intended audience. This book is
simultaneously too simplistic for experts and too technical for
lay people (i.e. me). I suspect the ideal reader lies somewhere in between: someone with a solid grasp of basic statistics and maths who is interested in causal analysis. The authors' contempt and mischaracterisations of the entire field of statistics are unlikely to sit well with said reader, though.

There are many minor errors in the edition I have, including some
inexplicably basic ones that conflate odds and probability, but [this
PDF](http://bayes.cs.ucla.edu/WHY/errata-pages-PearlMackenzie_BookofWhy_Final.pdf) contains the errata.

Overall, I'm pretty satisfied with what I got out of this book, I
think. While
Pearl's opinion of statistics and of his own work are on extreme ends of a
spectrum, and similarly irksome, there is much to celebrate in
this strange tome. I really appreciate the breadth of topics and historical anecdotes that feature here, sprinkled with a healthy dash of philosophy. I'm also very intrigued about the applications of causal inference in biomedical research, and this book has led me to the opening of many tantalising rabbit holes. However, I strongly recommend reading Pearl's technical overview instead (link below), which is very similar to *The Book of Why*, but more concise and palatable.






## Other reviews and associated discussion
- [Lisa Goldberg for the American Mathematical Society](https://doi.org/10.1090/noti1912)
- [Tim Maudlin in the Boston Review](https://bostonreview.net/science-nature/tim-maudlin-why-world),
  [HN discussion](https://news.ycombinator.com/item?id=20889143)
- [Andrew
  Gelman](https://statmodeling.stat.columbia.edu/2019/01/08/book-pearl-mackenzie/),
  [HN discussion](https://news.ycombinator.com/item?id=18871450)
- [Cross Validated discussion](https://stats.stackexchange.com/questions/376920/the-book-of-why-by-judea-pearl-why-is-he-bashing-statistics?noredirect=1&lq=1)


## Further reading
- [Overview of causal inference
  (Pearl)](http://ftp.cs.ucla.edu/pub/stat_ser/r350.pdf)
- [Hernan and Robins](https://www.hsph.harvard.edu/miguel-hernan/causal-inference-book/)
- [This slick flowchart](https://www.bradyneal.com/which-causal-inference-book)

Day 32 of [#100DaysToOffload](https://100daystooffload.com/)
