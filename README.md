# Goshawk-tutorial

Tutorial of <https://github.com/Yunlongs/Goshawk>, which is a static analyze tool to detect memory corruption bugs in C source codes.

## Summary

The static analysis identifies bugs without running the code. However, to find bugs in real-world complex software, even those modern static analysis tools suffer from scalability issues, and there still remains a huge gap between research-based analysis and practical bug detection. In this tutorial, we demonstrate Goshawk, a static analysis tool to find memory corruption bugs with the help of Natural Language Processing (NLP), and our audiences are expected to

* leverage Goshawk to find memory-related bugs in real-world C/C++ projects in several minutes;
* learn how to utilize latest AI techniques to extend static code analysis.

## Instructions

During the tutorial, you will use the following instructions:

1. Connect to Goshawk server

Select a username from user001 to user100 and use this command:

```bash
# password: asiaccs2023
ssh -p 12750 user001@sky.gossip.team
```

1. Run Goshawk

See commands in https://github.com/Yunlongs/Goshawk#ⅱ-how-to-use

3. View results

After upload, you can visit our Codechecker website: http://code-analysis.net. If you can not open it, then try this command:

```bash
CodeChecker parse ~/<path_to_Goshawk>/temp/analyze_cache
```

## Speakers

* Xiang Chen, Shanghai Jiao Tong University / Shanghai Qizhi Institute, China

  * Biography: Major in cyber security (master’s degree) at Shanghai Jiao Tong University and as a member of G.O.S.S.I.P, Xiang Chen is now focusing on applying static program analysis to find bugs effectively in real-world projects. He is one of the current maintainers of the Goshawk project.

* Siqi Ma, The University of New South Wales, Australia

  * Biography: Siqi Ma is currently a senior lecturer at the University of New South Wales. She mainly works in the area of software security. She has published over 40 papers at the top conferences in the areas of cybersecurity and software engineering, such as Security & Privacy, Usenix Security, International Conference, and Software Engineering.

## Publication

This tutorial has been published on the [ACM AsiaCCS 2023 official website](https://asiaccs2023.org/workshopstutorials/tutorials/). Hope to see you in Melbourne Australia from July 10th to July 14th, 2023.
