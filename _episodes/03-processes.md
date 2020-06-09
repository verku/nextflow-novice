---
title: "Processes"
teaching: 30
exercises: 20
questions:
- "How do I run an application using Nextflow?"
- "How do I pass parameters to the application?"
- "How do I access application results?"
objectives:
- "First learning objective. (FIXME)"
keypoints:
- "First key point. Brief Answer to questions. (FIXME)"
---

## Processes

Processes are the Nextflow data structure used to run user scripts.
The default script language is assumed to be Bash script, but
scripts can be written in any interpreted language, e.g. R, python,
or perl.

## The process data structure

A process has five parts:

- Directives: Defines configuration properties
- Input: Defines the input
- Output: Defines the output
- When: Defines when the process should execute (default: true = always when input is available)
- Script: Defines the script to run.

~~~
process < name > {

   [ directives ]

   input:
    < process inputs >

   output:
    < process outputs >

   when:
    < condition >

   [script|shell|exec]:
   < user script to be executed >

}
~~~
{: .source}

The user script is the only required part of the process that needs to be defined.

## Isolated run folder

Each instance of a process is staged in it's own folder.

Example script:
~~~
x = Channel.from( 'a', 'b', 'c')

process echo_string {

    input:
    val x

    script:
    """
    echo "string-$x"
    """

}
~~~
{: .language-groovy}

~~~
$ ls -a -R work/
~~~
{: .language-bash}
~~~
work/:
.  ..  01  40  b6

work/01:
.  ..  8f8eea1b232f6a6218c0b4998fcac2

work/01/8f8eea1b232f6a6218c0b4998fcac2:
.  ..  .command.begin  .command.err  .command.log  .command.out  .command.run  .command.sh  .exitcode

work/40:
.  ..  e920cb5e8d1a63b338f075444ba38c

work/40/e920cb5e8d1a63b338f075444ba38c:
.  ..  .command.begin  .command.err  .command.log  .command.out  .command.run  .command.sh  .exitcode

work/b6:
.  ..  c9801458f3e4924a57eac3e9154105

work/b6/c9801458f3e4924a57eac3e9154105:
.  ..  .command.begin  .command.err  .command.log  .command.out  .command.run  .command.sh  .exitcode
~~~
{: .output}

The hidden file `.command.sh` contains the interpolated script to
execute.

~~~
cat work/b6/c9801458f3e4924a57eac3e9154105/.command.sh
#!/bin/bash -ue
echo "string-b"
~~~
{: .language-bash}

The hidden file `.command.run` contains code that provides the run-time
environment in which the script is run. Running this file, can be used to
help debug processes during the workflow development stage.

## Process input

Since tasks are isolated from each other, input is passed using
channels.

~~~
input:
  <input qualifier> <input name> [from <source channel>] [attributes]
~~~
{: .source}

The input qualifier declares the type of data received.

- val: A named value of any type to be used as input.
- env: A named environment variable to be used as input.
- file: A file, which is soft-linked into the staging folder so it can be accessed properly in the execution context.
- path: A file, which is soft-linked into the staging folder so it can be accessed properly in the execution context (use this).
- stdin: Takes input from stdin
- tuple: Declares input as a group of inputs using the above qualifiers.
- each: Executes a task for each input in the collection.

{% include links.md %}