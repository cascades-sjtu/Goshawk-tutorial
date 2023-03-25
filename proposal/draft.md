# Goshawk

## Abstract

TODO:

## Motivation

Existing static analysis tools for the automated memory-related bug detection are not very effective, especially in highly customized projects and large code bases. This ineffectiveness is mainly caused by two reasons that programmer like to (1) design nested allocator for multi-object struct and (2) follow an unpaired-use model between allocators and deallocators in their developments.

Things get different in 2022, when we implemented and published Goshawk on IEEE S&P 22'. Goshawk has introduced the concept of structure-aware and object-centric Memory Operation Synopsis (MOS). The MOS model abstractly describes the memory objects of a given MM function and how they are managed by the function. By utilizing MOS, we are capable of handling the above two development characteristics while exploring much less code.

We conducted a comprehensive test on OSS projects such as Linux kernel, OpenSSL and IoT SDKs and compared with other SOTA data-flow driven bug detection prototype, such as ClangStaticAnalyzer Malloc checker, K-MELD and SinkFinder. As a result, Goshawk outperforms those tools by an order of magnitude in speed and accuracy. By the time of publication, Goshawk has detected 92 new double-free and use-after-free bugs and reported them with developer-friendly MOS descriptions.

Goshawk is constantly evolving after it has been published. Technically, we have keep maintained Goshawk to Clang-15. Consequently, we have applied Goshawk to a wider range of softwares and get more surprising results.

Based on above introduction, we conclude the following three motivations of our tutorial:

TODO:

### New version of Goshawk

### New findings with Goshawk

TODO:

In a nutshell, we believe it's time for goshawk to show itself again on the global stage!

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
* Shangzhi Xu
TODO:
  * affliation: Shanghai Qizhi Institute
  * biography:
  * past/relavant experience related on the topic of the tutorial:
  * previous tutorial experience:
* Jialiang Dong
TODO:
  * affliation: Shanghai Qizhi Institute
  * biography:
  * past/relavant experience related on the topic of the tutorial:
  * previous tutorial experience:
