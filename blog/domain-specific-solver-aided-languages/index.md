# Overview
This blog series will explore the use of solver-aided languages in domain specific
contexts. Some domains that I am specifically interested in are:

* storage systems
* bioinformatics
* distributed systems

While the field of solver-aided languages has been introduced to me via [Lindsey Kuper][blog-lkuper]
at UC Santa Cruz, some of these ideas are motivated by questions I commonly ask myself: "what if we
applied this idea to this topic?" or "what if we combine these ideas which seem related?"

# Thoughts and To-do
Until I fill this series out more, I want to keep a running tally of ideas:

* The application of solvers to help determine where to store data (and maybe which format)
    * This idea led me to swizzles and [Emina Torlak's work on swizzles][paper-swizzle-inventor]
* The application of solvers for deciding a "context." The context I have in mind is somewhat vague:
  consider the questions, "am I working on data that can be weakly consistent?" and "is my network
  well-behaving and I am in the optimistic case, or is it adversarial and a partition currently
  exists?"
    * This idea is a mix of a few things. The [existence of this paper by Lindsey Kuper and Peter Alvaro][paper-ds-solvers]
      (which I have not read, but I think I have heard the gist of); a brief reading of related work
      in [The HoneyBadger of BFT][paper-honeybadgerbft] which mentions the distinction of optimizing
      for the optimistic case versus the worst-case in reference to Clement, et al.'s work; and an
      idea discussed in disorderlylabs group meetings of whether it is possible (or viable) to identify
      a network partition and to propagate such knowledge so that the whole system is _behaving_ "consistently."

<!-- resources -->
[blog-lkuper]:            http://composition.al/about/

[paper-swizzle-inventor]: https://homes.cs.washington.edu/~emina/doc/swizzle.asplos19.pdf
[paper-ds-solvers]:       https://pdfs.semanticscholar.org/fc05/79ace8ae7c73bd9ac479dd42d8635039abd1.pdf
[paper-honeybadgerbft]:   https://eprint.iacr.org/2016/199.pdf
