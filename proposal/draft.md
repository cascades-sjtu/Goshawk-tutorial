# Goshawk's glance back

## Abstract

Static analysis, as the technique that can identify bugs without running the code, has been proved effective in artificial test suites. However, our ultimate goal is to find bugs in the real world softwares, which is getting more and more complex nowadays. While modern static analysis tools are also evolving, there still remains a huge gap between tools and its targets.

After our bug detection prototype-Goshawk has been published in 2022, we have been continuously developing and applying it to vairous real-world projects. In this tutorial, we will guide every audience how to leverage Goshawk in C/C++ static analysis and memory-related bug finding.

## Motivation

Existing static analysis tools for the automated memory-related bug (Double Free, Use-After-Free, etc) detection are not very effective, especially in highly customized projects and large code bases. This ineffectiveness is mainly caused by the following two reasons: programmer likes to (1) design non-standard nested allocator for multi-object struct and (2) follow an unpaired-use model between allocators and deallocators.

Things get different in 2022, when we implemented and published the bug detection prototype-Goshawk[^paper] on IEEE S&P 22'. Goshawk has introduced the concept of structure-aware and object-centric Memory Operation Synopsis (MOS). The MOS model abstractly describes the memory objects of a given MM function and how they are managed by the function. By utilizing MOS, Goshawk are capable of handling the above two development characteristics (achieve accuracy) while exploring much less code (achieve efficiency).

We conducted a comprehensive test on OSS projects such as OS kernel, OpenSSL and IoT SDKs and compared with other SOTA data-flow driven bug detection prototype, such as Clang Static Analyzer MallocChecker[^csa], K-MELD[^kmeld] and SinkFinder[^sinkfinder]. As a result, Goshawk outperforms those tools by an order of magnitude in speed and accuracy. By the time of publication, Goshawk has detected 92 new double-free and use-after-free bugs and reported them with developer-friendly MOS descriptions.

Goshawk is constantly evolving after it has been published and it is now public on GitHub[^github] and project website[^website]. While the website comes with a simple tutorial which takes OpenSSL as an example, Goshawk can also handle other C/C++ projects.

Based on the above discussion, we conclude the following three motivations of our tutorial:

### New version of Goshawk

Technically speaking, the initial version of Goshawk has a two-stage working process: (1) memory-management function and MOS annotation and (2) Data-flow analysis based bug detection on those functions. Between the two stage, user must keep the intermediate output and call Codechecker in command line manually, which is not very convinient and error-prone for novices. The latest version of Goshawk combines the two stages into one script [run.py](https://github.com/Yunlongs/Goshawk/blob/master/run.py) and can directly view the analysis results.

We also update its functionality of each stage. As for the first stage, Goshawk has provided some custom functionalities. Users can [re-train Siamese network](https://github.com/Yunlongs/Goshawk#ⅲb-re-train-simaese-network-for-your-customized-target-function-identification-task-egmm-functions-crypto-functions) for their customized target function identification task and use that model for inferring similarities between function prototypes. This work greatly expands the capabilities of Goshawk, making it no longer limited to memory management functions.

As for the second stage, we have ported the [Clang Static Analyzer checker plugin](https://github.com/Yunlongs/Goshawk/tree/master/plugins_src) in Goshawk to Clang-15.0.0 for supporting modern C/C++ projects, and re-organized the four checker into one entry checker [GoshawkAnalyzer](https://github.com/Yunlongs/Goshawk/tree/master/plugins_src/GoshawkAnalyzer).

### New findings with Goshawk

Consequently, we have applied Goshawk to a wider range of softwares and get more surprising results. In addition to the vulnerabilities already listed on the buglist[^buglist], we have discovered UAF bugs in general user programs, like [nasm-2.15.05](https://www.nasm.us/pub/nasm/releasebuilds/2.15.05/nasm-2.15.05.tar.xz), [cairo-1.17.4]( https://cairographics.org/snapshots/cairo-1.17.4.tar.xz), [flite](https://github.com/festvox/flite). We also discovered 15 bugs (13 UAFs and 2 Double Frees) in 90 OpenWrt pacakges, like [libcoap-4.2.1](https://github.com/obgm/libcoap/releases/tag/v4.2.1), [kplex-1.4](https://github.com/stripydog/kplex/releases/tag/v1.4), and we manually confirm them. Generally speaking, the average length of bug's data-flow trace is 16.86, which is hard to explore manually, please visit our website[^website] for details.

We have been continuously finding new bugs with Goshawk and comfirming their detials with developers. Once approved, we can show the step-by-step process of uncovering those vulnerabilities to every audience of this tutorial.

In a nutshell, we believe it's time for goshawk to showcase itself once again on the world stage!

## Objectives

This tutorial is expected to address two technical issues with Goshawk.

### Apply NLP model to bug detection

One of the most innovative idea of Goshawk is the MOS model. From the point of view of its implementation, it is actually a NLP model for identifying function prototypes. While Goshawk just proved its worth in identifying memory functions, we will show in this tutorial how to apply it to other funtions with nested feature, such as cryptography-related and network-related functions. Then we will be able to discover bugs like crypto-misuse or command-injection.

### Apply open-source SAST tool to real-world projects

We already have got a lot of great C/C++ static application security testing (SAST) tools, but as the empirical study[^sast] says, 66% of their invesgated GitHub projects define how to use specific SAST tools, but only 37% enforce their usage for new contributions. This is mainly because of the following two reasons:

1. Open-source SASTs reports a high (47-80%) rate of FP/NP [^issta], increased the burden of developers;
2. Open-source SASTs are not customized for projects, thus it needs to be configured manually in order for the tools to work effectively.

The above two problems are especially obvious when facing large projects, but Goshawk can easily solve them: (1) The MOS model are built automatically, users only need to provide the initial corpus of function prototypes to obtain a project-specific MOS model. (2) Goshawk uses [Clang Static Analyzer](https://clang-analyzer.llvm.org) as its analysis engine, which is part of the Clang compiler infrastructure. (3) Goshawk only performs data-flow analysis on those interested functions and prunes other irrelavant functions. (4) Goshawk uses [Z3 Solver](https://github.com/Z3Prover/z3) to eliminate false warnings due to infeasible paths.

## Target Audience

We assume this tutorial is beneficial for the following three types of audience:

1. Who wants to learn the internal of C/C++ static analysis for memory-related issues;
2. Who wants to get familiar with Goshawk, Clang Static Analyzer and their idiomatic usages;
3. Who wants to integrate security analysis into their development workflow, especially for large project.

## Outline

Below is the timeline of our tutorial, about 90 minutes in total:

| Order | Duration | Activity | Content |
| ----- | -------- | -------- | ------- |
| 1 | 5'  | Preparation  | Distribute our prebuilt Goshawk docker image |
| 2 | 30' | Introduction | Introduce Goshawk and its internal implementation |
| 3 | 10' | Demo         | Show Goshawk's basic usage for finding memory-related bugs |
| 4 | 20' | Interaction  | Ask audiences to try to find memory-related bugs by themselves |
| 5 | 10' | Co-review    | Collect the output of Goshawk from each audience and review them together |
| 6 | 5'  | Comparison   | Compare the results with the Clang Static Analyzer's MallocChecker |
| 7 | 10' | Extension    | Show how to further modify and extend Goshawk to your security research |
| 8 | NaN | Q&A |  |

Below is some detailed description:

1. We will upload docker image to DockerHub. Audiences are required to download it and create its container for later interactive activity;
2. We will mainly introduce Goshawk's principle and Clang Static Analyzer's mechanism;
3. We will show Goshawk's basic usage taking a prepared real-world buggy program as an example. Then we show the intermediate output and analysis results;
4. Audiences will try to find bugs by themselves in our provided about 15 real-world open-source projects, and there will be tutorial assistants in the auditorium to help answer questions;
5. We will analyze those 15 projects in advance. For those easily comfirmed bugs, we will reward their finders; For those implicit bugs, we will confirm them with finders after the tutorial;
6. We will analyze those 15 projects using Clang Static Analyzer's MallocChecker and compare them in advance, only the difference are shown in tutorial;
7. We will show how to design and re-train MOS model for identifying cryptography function, and for helping discovering crypto-misuse issues;
8. We will answer questions about Goshawk's usage and other related topics.

## Speaker

* Xiang Chen
  * affliation: Shanghai Jiao Tong University / Shanghai Qizhi Institute
  * biography: Received a bachelor's degree in information security from Shanghai Jiao Tong University in 2021 and is currently pursuing a master's degree in cyber security. Research interests are static program analysis.
  * past/relavant experience related on the topic of the tutorial:
    * Worked as an intern at Shanghai Feiyu Information Technology Co., Ltd, implementing Clang Static Analyzer checkers for secure coding standards;
    * Worked as a teaching assistant in NIS-7021 and IS-308, instructing students to use Clang Static Analyzer and Goshawk in bug-detection homework;
  * previous tutorial experience: No

## Reference

[^paper]: https://goshawk.code-analysis.org/sp22.pdf
[^csa]: https://clang.llvm.org/doxygen/MallocChecker_8cpp_source.html
[^kmeld]: https://www-users.cse.umn.edu/~kjlu/papers/k-meld.pdf
[^sinkfinder]: https://rucsesec.github.io/papers/FSE2020.pdf
[^github]: https://github.com/Yunlongs/Goshawk
[^website]: https://goshawk.code-analysis.org
[^buglist]: https://github.com/Yunlongs/Goshawk/blob/master/bug_list.md
[^sast]: https://link.springer.com/article/10.1007/s10664-019-09750-5
[^issta]: https://dl.acm.org/doi/10.1145/3533767.3534380
