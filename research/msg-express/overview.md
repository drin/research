# MSG Express -- **M**anagement of **S**ingle-cell **G**ene **Express**ion

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
