---
theme: apple-basic
fonts: 
  # basically the text
  sans: 'Noto Serif'
  # use with `font-serif` css class from windicss
  serif: 'Noto Serif'
  # for code blocks, inline code, etc.
  mono: 'Source Code Pro'
---

---
layout: center
---

# AsiaCCS 2023 Tutorial

## Custom Memory Functions Demystified: A tutorial of memory corruptions detection using Goshawk

**Xiang Chen**, Siqi Ma, Zihan Ni, Shangzhi Xu

Shanghai Jiao Tong University 

The University of New South Wales, Australia

CSIRO, AUS

---
layout: intro
---

# Agenda

- Preparation - 5'
- Introducing Goshawk - 20'
- Simple demo using Goshawk - 10'
- Interaction - 30'
- Result Review - 15'
- Q&A

It takes about **80** minutes in total.

---
layout: section
---

# Preparation

---
layout: center
---

# Connect to Goshawk server

Select a username from user001 to user100 and use this command:

```bash
ssh -p 12750 user001@sky.gossip.team # password: asiaccs2023
```

Everyone will have the following directories:
- Goshawk project
- Two C/C++ projects ready to be analyzed
- ...(Other C/C++ projects you like to analyze)

---
layout: section
---

# Introducing Goshawk

---
layout: center
---

# Traditional memory corruption detection

- Create a **CFG** (control flow graph) & Scan the CFG - [ClangStaticAnalyzer MallocChecker](https://clang.llvm.org/doxygen/MallocChecker_8cpp_source.html)

<img src="asiaccs/cfg.png" width="700" class="center" />

---
layout: center
---

# A simple case

<img src="asiaccs/simplecase.png" width="700" class="center" />

---
layout: center
---

# A simple case ✅

<img src="asiaccs/simplecase.png" width="700" class="center" />

PS: more cases can be found at: [ClangStaticAnalyzer testsuite](https://github.com/llvm/llvm-project/blob/main/clang/test/Analysis/malloc.c)

---
layout: center
---

# A complex (real-world) case

<img src="asiaccs/complexcase1-1.png" width="700" class="center" />

---
layout: center
---

# A complex (real-world) case

<img src="asiaccs/complexcase1-2.png" width="700" class="center" />

---
layout: center
---

# A complex (real-world) case ❌

<img src="asiaccs/complexcase1-3.png" width="700" class="center" />

Diving into custom MM functions is not scalable, and in most cases not feasible 🙅

---
layout: center
---

# Another complex (real-world) case ❌

<img src="asiaccs/complexcase2.png" width="800" class="center" />

---
layout: center
---

# Another complex (real-world) case ❌

<img src="asiaccs/complexcase3.png" width="800" class="center" />

---
layout: center
---

# Challenge - A Huge GAP

<img src="asiaccs/gap.png" width="900" class="center" />

---
layout: center
---

# Our solution - MOS model

<img src="asiaccs/sp22.png" width="700" class="center" />

---
layout: center
---

# The MOS model

Below is a MOS model summarized from `Linux/mm/dmapool.c`

<img src="asiaccs/mos.png" width="900" class="center" />

---
layout: image-right
image: asiaccs/complexcase1-5.png
---

# MOS model showcase

Applying MOS model to the previous mentioned complex case:

<img src="asiaccs/complexcase1-4.png" width="600" class="center" />

---
layout: center
---

# Goshawk workflow

<img src="asiaccs/workflow.png" width="700" class="center" />

The last step is the core idea - **simplifying CFG and data-flow analysis**

<img src="asiaccs/workflow-3.png" width="600" class="center" />

---
layout: center
---

# Technical details - NLP-asstisted clasification

<img src="asiaccs/classification.png" width="800" class="center" />

<!--
1. Segment and normalize function prototypes.
2. Convert function prototype into numeric vectors. 
3. Compute the similarity with the reference set.
-->

---
layout: center
---

# Technical details - DFA-based validation

<img src="asiaccs/validation.png" width="800" class="center" />

---
layout: center
---

# Technical details - MOS model generation

<img src="asiaccs/generation.png" width="750" class="center" />

---
layout: center
---

# Technical details - MOS model interpretation

<img src="asiaccs/interpretation.png" width="800" class="center" />

---
layout: center
---

# Technical details - Infeasible paths elimination

<img src="asiaccs/z3.png" width="800" class="center" />

---
layout: center
---

# Goshawk Trophies

By the time of paper publication, Goshask has identified **10K+** custom MM functions, detected **92** new use-after-free and double-freebugs while kept a low FP rate.

<img src="asiaccs/experiment.png" width="900" class="center" />

---
layout: center
---

# Motivation of tutorial

- Goshawk is still evloving
  - ported to Clang-15
  - more user-friendly run script
  - re-train script for other kinds of custom functions
- Goshawk has been applied to a wider range of softwares
  - **19** bugs (13 UAFs and 2 Double Frees) in 90 OpenWrt pacakges are discovered and confirmed
  - the average length of these bugs' data-flow trace is 16.86

---
layout: statement
---

# Simple demo

Apply Goshawk to [redsocks](https://openwrt.org/packages/pkgdata/redsocks)

---
layout: statement
---

# Interaction

Try Goshawk self!

---
layout: center
---

# Software environment

- Ubuntu 22.04.2 LTS
- Python 3.10.6
- Bear 3.1.2
- Clang 15.0.0
- CodeChecker 6.22.1

---
layout: center
---

# Useful Commands

- Get compilation database: `cd <project_path>; bear -- make CC=clang HOSTCC=clang`
- Identify MM functions: `cd <Goshawk_path>; python3 run.py -s=extract <project_path>`
- Dataflow analysis: `cd <Goshawk_path>; python3 run.py -s=analyze <project_path>`

---
layout: statement
---

# Result review

http://code-analysis.net

---
layout: end
---

# Q&A

All the resources has been public available via:

https://github.com/cascades-sjtu/Goshawk-tutorial

Feel free to create an issue about this tutorial

Try other analysis tools made by us: https://code-analysis.org