# Data management landscape and our niche

Data management and processing is a vast field and/or topic. If we consider any particular
end-to-end system, there can be many systems used to fill specialized roles; on the other hand,
there may also be fewer, generalized systems to satisfy the same needs. Here, I want to elaborate
on a particular ecosystem that I am trying to fit into and how I view our role or niche in relevant
end-to-end systems.

For my purposes here, I think of an ecosystem as a particular set of interchangeable systems that
can be integrated to satisfy a purpose. Any particular composition of such systems would be an
end-to-end system. Each system therein could stand on it's own, but if it is specialized to the
point of depending on other systems, then it may also be considered a sub-system.


# MSG Express and Skytether


## Skytether

Originally, I was trying to determine a reasonable name for my project that was relevant to
`SkyhookDM`. My work was initially imagined as something that would sit on top of--or live as an
extension of--`SkyhookDM`. For this reason, I have been calling my project `Skytether` (meant to
have the connotation of a particular part of a [skyhook][wiki-skyhook]). For a first name, it
seemed reasonable.

Now, beyond names, most of the code that I have written is meant to eventually integrate with
[SkyhookDM][web-skyhookdm], providing the necessary functionality to be able to delegate some
processing to computational devices that a computational storage server may be using. Similarly to
how `SkyhookDM` brings tabular data management to an existing storage system, `Ceph`, I imagine
that `Skytether` would bring the use of **computational storage devices** to an existing
**computational storage system**, `SkyhookDM`.

## MSG Express -- **M**anagement of **S**ingle-cell **G**ene **Express**ion

As I was working on `Skytether`, I began to feel like I should differentiate some of the work I was
doing as a domain-specific extension of `SkyhookDM`. Thus, I initially named the repository
`skytether-singlecell`. However, I recently gave a presentation at a [CROSS][web-cross] to propose
my work for funding as an open source project. In preparing for this presentation, I realized that
my research really should have multiple parts: (1) `Skytether` is an extension and modification of
`SkyhookDM` to leverage computational storage devices, and (2) `MSG Express` is a domain-specific
data management system that uses `Skytether` to support data processing and storage of single-cell
gene expression data. Thus, `MSG Express` will be an open source project that provides benefit to
the bioinformatics community (specifically use cases that use single-cell RNA sequencing),
meanwhile `Skytether` will most likely be an umbrella project that consists of functionality pushed
into other, various open source projects--[arrow][web-arrow] and [SkyhookDM][web-skyhookdm]. I
specifically imagine that `MSG Express` will then provide integration between application-level
libraries, such as [scanpy and anndata][web-scverse], and `Skytether` (or `SkyhookDM`).


# High-level Design

**Work in Progress.**


<!-- Resources -->
[wiki-skyhook]:  https://en.wikipedia.org/wiki/Skyhook_(cable)

[web-skyhookdm]: https://sites.google.com/view/skyhookdm/home
[web-cross]:     https://cross.ucsc.edu/
[web-arrow]:     https://arrow.apache.org/
[web-scverse]:   https://scverse.org/projects/
