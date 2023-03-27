# Goshawk

## Abstract

TODO:

## Motivation

Existing static analysis tools for the automated memory-related bug(Double Free, Use-After-Free, etc) detection are not very effective, especially in highly customized projects and large code bases. This ineffectiveness is mainly caused by the following two reasons: programmer likes to (1) design nested allocator for multi-object struct and (2) follow an unpaired-use model between allocators and deallocators.

Things get different in 2022, when we implemented and published the bug detection prototype-Goshawk[^paper] on IEEE S&P 22'. Goshawk has introduced the concept of structure-aware and object-centric Memory Operation Synopsis (MOS). The MOS model abstractly describes the memory objects of a given MM function and how they are managed by the function. By utilizing MOS, Goshawk are capable of handling the above two development characteristics while exploring much less code.

We conducted a comprehensive test on OSS projects such as OS kernel, OpenSSL and IoT SDKs and compared with other SOTA data-flow driven bug detection prototype, such as ClangStaticAnalyzer Malloc checker[^csa], K-MELD[^kmeld] and SinkFinder[^sinkfinder]. As a result, Goshawk outperforms those tools by an order of magnitude in speed and accuracy. By the time of publication, Goshawk has detected 92 new double-free and use-after-free bugs and reported them with developer-friendly MOS descriptions.

Goshawk is constantly evolving after it has been published and it is now public on GitHub[^github] and project website[^website]. While the website comes with a simple tutorial which takes OpenSSL as an example, Goshawk can also handle other C/C++ projects.

Based on the above discussion, we conclude the following three motivations of our tutorial:

### New version of Goshawk

Technically speaking, the initial version of Goshawk has a two-stage working process: (1) memory-management function and MOS annotation and (2) ClangStaticAnalyzer-based bug detection. Between the two stage, user must keep the intermediate output and call Codechecker in command line manually, which is not very convinient and error-prone for novices. The latest version of Goshawk combines the two stages into one script [run.py](https://github.com/Yunlongs/Goshawk/blob/master/run.py) and can directly view the analysis results.

We also update its functionality of each stage. As for the first stage, Goshawk has provided some custom functionalities. Users can [re-train Simaese network](https://github.com/Yunlongs/Goshawk#ⅲb-re-train-simaese-network-for-your-customized-target-function-identification-task-egmm-functions-crypto-functions) for their customized target function identification task and use that model for inferring similarities between function prototypes. This work greatly expands the capabilities of Goshawk, making it no longer limited to memory management functions, but can also handle crypto functions.

As for the second stage, we have ported the [ClangStaticAnalyzer checker plugin](https://github.com/Yunlongs/Goshawk/tree/master/plugins_src) in Goshawk to Clang-15.0.0 for supporting modern C/C++ projects, and re-organized the four checker into one entry checker [GoshawkAnalyzer](https://github.com/Yunlongs/Goshawk/tree/master/plugins_src/GoshawkAnalyzer).

### New findings with Goshawk

Consequently, we have applied Goshawk to a wider range of softwares and get more surprising results. In addition to the vulnerabilities already listed on the buglist[^buglist], we have discovered UAF bugs in [nasm-2.15.05](https://www.nasm.us/pub/nasm/releasebuilds/2.15.05/nasm-2.15.05.tar.xz), [cairo-1.17.4]( https://cairographics.org/snapshots/cairo-1.17.4.tar.xz), [flite](https://github.com/festvox/flite) and two OpenWrt pacakges: [libcoap-4.2.1](https://github.com/obgm/libcoap/releases/tag/v4.2.1), [kplex-1.4](https://github.com/stripydog/kplex/releases/tag/v1.4).

We have been continuously finding new bugs with Goshawk and comfirming their detials with developers. Once approved, we can show the step-by-step process of uncovering those vulnerabilities to every audience of this tutorial.

In a nutshell, we believe it's time for goshawk to showcase itself once again on the world stage!

## Objectives

TODO:

### apply language model to bug detection

### apply open-source tool to real-world bugs

A description of the technical issues that the tutorial will address.

## Target Audience

We assume this tutorial is beneficial for the following three types of audience:

1. Who wants to learn the internal of C/C++ static analysis for memory-related issues
2. Who wants to get familiar with the ClangStaticAnalyzer and its idiomatic usage
3. Who wants to integrate security analysis into their development workflow, especially for large project

## Outline

Below is the timeline of our tutorial, about 90 minutes in total:

1. 5' preparation: distribute our prebuilt Goshawk docker image for later interactive activity
2. 30' introduction: introduce Goshawk and its internal implementation
3. 10' demo: show Goshawk's basic usage for finding memory-related bugs in prepared buggy program
4. 20' interactive activity: ask audiences to try to find memory-related bugs by themselves in our provided 90 projects, there will be tutorial assistants in the auditorium to help answer questions
5. 10' collaborative review: collect the output of Goshawk from each audience and review them together, those who find memory-related bugs can get rewards
6. 5' comparison: compare the results with the results of the static analysis using the vanilla ClangStaticAnalyzer
7. 10' extension: show how to further modify and extend Goshawk to your security research
8. Q&A

## Speaker

* Xiang Chen
  * affliation: Shanghai Jiao Tong University / Shanghai Qizhi Institute
  * biography: Received a bachelor's degree in information security from Shanghai Jiao Tong University in 2021 and is currently pursuing a master's degree in cyber security. Research interests are static program analysis.
  * past/relavant experience related on the topic of the tutorial:
    * Worked as an intern at Shanghai Feiyu Information Technology Co., Ltd, implementing ClangStaticAnalyzer checkers for secure coding standards
    * Worked as a teaching assistant in NIS-7021 and IS-308, instructing students to use ClangStaticAnalyzer and Goshawk in bug-detection homework
  * previous tutorial experience: No

## Reference

[^paper]: https://ieeexplore.ieee.org/document/9833613
[^csa]: https://clang.llvm.org/docs/analyzer/checkers.html#id90
[^kmeld]: https://www.ndss-symposium.org/ndss-paper/detecting-kernel-memory-leaks-in-specialized-modules-with-ownership-reasoning/
[^sinkfinder]: https://dl.acm.org/doi/10.1145/3368089.3409678
[^github]: https://github.com/Yunlongs/Goshawk
[^website]: https://goshawk.code-analysis.org
[^buglist]: https://github.com/Yunlongs/Goshawk/blob/master/bug_list.md
