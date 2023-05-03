Download Link: https://assignmentchef.com/product/solved-cs3013-project-1-the-process-party
<br>
<span class="kksr-muted">Rate this product</span>







Processes have a strange existence in the Linux or Unix world. The process creation, termination, and execution model may take experience to understand. In this project, we will take a practice-centric approach to exploring how processes work, how they share information, and how they are organized.

Our exploration will involve five short scenarios, each of which explores a di↵erent concept. An overview of the five scenarios is as follows:

<ol>

 <li>The Prolific Parent: A parent process spawns a random number of children (between 10 and 15). Each child introduces itself, waits for a short time, and then terminates (with a specifically chosen exit code). This scenario’s code should be in prolific.c and the Makefile should compile it to an executable named prolific.</li>

 <li>Lifespan Generation: A parent process picks a random number (between 7 and 10) for the number of lifespans that will be explored. It then creates a child. That child decrements the lifespan count, and, if the count is non-zero, creates its own child (which repeats the process) and prints out a statement indicating its process ID and that of its child. If a child decrements the count and it becomes zero (or somehow becomes negative), the child exits with an exit code of 0. All processes wait until their child returns and exits with a value that is one greater than its child’s exit value. When the initial parent process confirms its child has exited, it prints out the exit value of its child and itself exits. This scenario’s code should be in generation.c and the Makefile should compile it to an executable named generation.</li>

 <li>The Explorer: A parent process moves around the file system five times, randomly selecting a directory each time (from among /home, /proc, /proc/sys, /usr, /usr/bin, and /bin). Each time it changes its current working directory (e.g., using chdir()), it indicates which string it chose and outputs the value of the current working directory (using getcwd()). It then forks o↵ a child that runs the ls -alh command. Done correctly, the child’s listing output should match the directory the parent selected. The parent waits on the child to complete before moving on to the next file system location. This scenario’s code should be in explorer.c and the Makefile should compile it to an executable named explorer.</li>

 <li>The Slug: This process randomly selects an amount of time (between 1 and 5 seconds), prints out that time length, then waits that long (it’s a slug, after all). It then randomly runs either the last -d –fulltimes or id -u command (both of which exit after outputting data). This scenario’s code should be in slug.c and the Makefile should compile it to an executable named slug.</li>

 <li>The Slug Race: A parent process creates four children processes that will each run the slug exe- cutable in parallel. The Slug Race parent process will print out a note every 0.25 seconds indicating which children are still competing. When each child completes, it will report the result (with a delay of no more than just over 0.25 seconds). Once all the slug children have exited, the parent prints out a message and exits. The Slug Race parent reports on returning children as they arrive and specifically does not require them to finish in order (what kind of race would that be?). Before creating children, the Slug Race parent should get the current timestamp and, as each child completes, it should indicate how many seconds and milliseconds have elapsed since it started. This scenario’s code should be in slugrace.c and the Makefile should compile it to an executable named slugrace.</li>

</ol>

This project will allow students to learn about process creation, termination, and resource usage in the Linux operating system. All coding is to be done in the C programming language without using any

1

non-standard libraries. We highly recommend implementing or testing the project on a plain Ubuntu 20.04 virtual machine. Projects that do not compile or run correctly on a plain Ubuntu virtual machines without special libraries may be penalized. You may not use the system() system call in any part of Project 1 since it obscures the details involved in process creation.

These projects will be graded in bulk with some scripting support, so it is important that the associated Makefile produces files with the specified executable names and that the source code file names match what is specified. For the components that use randomness, students must read a number from a file, seed.txt, convert it from a string to an integer (using the atoi function from stdlib.h) and use it as the seed value in the srand() function. That will cause the seed to be used for all invocations of the rand() function. Examples below will be provided using the seed value 12345, which can be used for your debugging purposes. A di↵erent seed value will be used for grading. Individual components may require additional seed files, as specified in their scenario details, and will be used in a similar fashion.

We specified the main source code file name and executable target for each of the scenarios in the earlier overview. The source code file will likely be fairly self-contained, but students may use the executable string and a hyphen as a prefix to any other source code files associated with the scenario (e.g., slugrace-header.h, slugrace-helper1.c).

We now describe each of the components in greater detail, along with their expected outputs.

Scenario 1: The Prolific Parent

This process reads the random seed value from seed.txt and uses it to seed the srand() function as described above. It then randomly chooses the number of children using rand() (between 10 and 15, inclusively) and stores that value. In a loop, it then generates a random number for each child and stores it an array. Potential pitfall: It is essential that the parent construct the random numbers in advance since the rand() uses the seed and a global variable to track which numbers have been generated. If the random number is generated by the children after forking, they will all produce the same random number since they will have identical copies of that seed value and global variable.

Once the random number has been created, the parent then forks o↵ the number of children determined earlier, incrementing a counter, which is initialized to 0, upon completing the loop. Each child uses the value of the counter and indexes into the array of random numbers to extract its own random value, which it uses in subsequent steps. The child obtains its own process ID and stores that value. It determines its exit code by using modulo 50 and adding one to the random number it extracted earlier (e.g., ((myRandom % 50) + 1)) and its wait time using modulo 3 and adding one (((myRandom % 3) + 1). The child prints a message revealing its own process ID, the time it will delay, and its ultimate return value. It then waits for the indicated amount of time and exits with the selected exit value.

An example transcript of Prolific Parent with seed value 12345 is below: <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="e093888590819284a08e8f928d818e8499">[email protected]</a>:~&gt; ./prolific

<pre>Read seed value: 12345</pre>

<pre>Read seed value (converted to integer): 12345Random Child Count: 13I’m feeling prolific![Parent]: I am waiting for PID 5924 to finish.</pre>

<pre>   [Child, PID: 5924]: I am the child and I will wait 3 seconds and exit with code 22.</pre>

<pre>   [Child, PID: 5924]: Now exiting...[Parent]: Child 5924 finished with status code 22. Onward![Parent]: I am waiting for PID 5925 to finish.</pre>

<pre>   [Child, PID: 5925]: I am the child and I will wait 3 seconds and exit with code 24.</pre>

<pre>   [Child, PID: 5925]: Now exiting...[Parent]: Child 5925 finished with status code 24. Onward!</pre>

2

<pre>[Parent]: I am waiting for PID 5929 to finish.   [Child, PID: 5929]: I am the child and I will wait 2 seconds and exit with code 2.   [Child, PID: 5929]: Now exiting...</pre>

<pre>[Parent]: Child 5929 finished with status code 2. Onward![Parent]: I am waiting for PID 5935 to finish.</pre>

<pre>   [Child, PID: 5935]: I am the child and I will wait 1 seconds and exit with code 22.</pre>

<pre>   [Child, PID: 5935]: Now exiting...[Parent]: Child 5935 finished with status code 22. Onward![Parent]: I am waiting for PID 5936 to finish.</pre>

<pre>   [Child, PID: 5936]: I am the child and I will wait 1 seconds and exit with code 5.</pre>

<pre>   [Child, PID: 5936]: Now exiting...[Parent]: Child 5936 finished with status code 5. Onward![Parent]: I am waiting for PID 5937 to finish.</pre>

<pre>   [Child, PID: 5937]: I am the child and I will wait 3 seconds and exit with code 38.</pre>

<pre>   [Child, PID: 5937]: Now exiting...[Parent]: Child 5937 finished with status code 38. Onward![Parent]: I am waiting for PID 5943 to finish.</pre>

<pre>   [Child, PID: 5943]: I am the child and I will wait 2 seconds and exit with code 3.</pre>

<pre>   [Child, PID: 5943]: Now exiting...[Parent]: Child 5943 finished with status code 3. Onward![Parent]: I am waiting for PID 5944 to finish.</pre>

<pre>   [Child, PID: 5944]: I am the child and I will wait 2 seconds and exit with code 29.</pre>

<pre>   [Child, PID: 5944]: Now exiting...[Parent]: Child 5944 finished with status code 29. Onward![Parent]: I am waiting for PID 5945 to finish.</pre>

<pre>   [Child, PID: 5945]: I am the child and I will wait 1 seconds and exit with code 29.</pre>

<pre>   [Child, PID: 5945]: Now exiting...[Parent]: Child 5945 finished with status code 29. Onward![Parent]: I am waiting for PID 5948 to finish.</pre>

<pre>   [Child, PID: 5948]: I am the child and I will wait 3 seconds and exit with code 28.</pre>

<pre>   [Child, PID: 5948]: Now exiting...[Parent]: Child 5948 finished with status code 28. Onward![Parent]: I am waiting for PID 5949 to finish.</pre>

<pre>   [Child, PID: 5949]: I am the child and I will wait 1 seconds and exit with code 48.</pre>

<pre>   [Child, PID: 5949]: Now exiting...[Parent]: Child 5949 finished with status code 48. Onward![Parent]: I am waiting for PID 5952 to finish.</pre>

<pre>   [Child, PID: 5952]: I am the child and I will wait 3 seconds and exit with code 22.</pre>

<pre>   [Child, PID: 5952]: Now exiting...[Parent]: Child 5952 finished with status code 22. Onward![Parent]: I am waiting for PID 5953 to finish.</pre>

<pre>   [Child, PID: 5953]: I am the child and I will wait 1 seconds and exit with code 4.</pre>

<pre>   [Child, PID: 5953]: Now exiting...[Parent]: Child 5953 finished with status code 4. Onward!<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="681b000d18091a0c2806071a0509060c11">[email protected]</a>:~&gt;</pre>

In the above transcript, your PID values will likely di↵er (the OS picks those and they are dependent upon the system’s history of processes). However, with the same starting seed value (12345), the wait times and exit codes should match and appear in the same order.

In this scenario, you must use OS system calls to fork() o↵ a child process and to wait on that process. Note that the exit value of a child and wait status code are related, but a macro is needed at the parent for the values to match (consult further: man waitpid).

3

Helpful Hints

A goal in this assignment is for students to learn how to find information in the online documentation of Unix and Linux (called man pages) and, from that documentation, to learn how to invoke the various system facilities from the created program. For example, to learn about the fork() function, type “man fork” in the Unix or Linux shell. Manual pages are organized into sections. Section 1 is for commands to the shell, section 2 is for system calls, and section 3 is for library routines, etc. Some entries are contained in more than one section. For example, “man wait” will provide the manual page for the wait command typed to a shell, while “man 2 wait” will provide the manual page for the wait() system call. Executing “man man” shows how to use the man command to view and/or print manual pages.

For this part of the assignment, the following systems calls and library functions may be needed:

• fork() – create a new process by cloning an existing one • waitpid() – wait for a process to terminate.• getpid() – get current process identifier.

Scenario 2: Lifespan Generation

Note: This scenario is a slight modification of the Prolific Parent scenario. Students may want to start the project by copying prolific.c to generation.c and then customizing generation.c to implement the following.

The parent picks a random number, between 7 and 10, to represent the lifespan count. It then spawns a child. That child decrements the lifespan count, and, if the count is non-zero, creates its own child (which repeats the process) and prints out a statement indicating its process ID and that of its child. If a child decrements the count and it becomes zero (or somehow becomes negative), the child exits with an exit code of 0. All processes wait until their child returns and exits with a value that is one greater than its child’s exit value. When the initial parent process confirms its child has exited, it prints out the exit value of its child and itself exits.

This scenario uses a subset of the same functions as the Prolific Parent, but rather than proceed in an iterative loop, it acts more recursively. Remember, Linux uses copy-on-write when forking, so a parent process and a child process can have di↵erent values in the same variable name if it is changed after the fork occurs.

An example transcript of Lifespan Generation with seed value 12345 is below: <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="90e3f8f5e0f1e2f4d0feffe2fdf1fef4e9">[email protected]</a>:~&gt; ./generation

<pre>Read seed value: 12345</pre>

<pre>Read seed value (converted to integer): 12345Random Descendant Count: 10Time to meet the kids/grandkids/great grand kids/...[Parent, PID: 6091]: I am waiting for PID 6092 to finish.</pre>

<pre>   [Child, PID: 6092]: I was called with descendant count=10. I’ll have 9 descendant(s).[Parent, PID: 6092]: I am waiting for PID 6093 to finish.</pre>

<pre>   [Child, PID: 6093]: I was called with descendant count=9. I’ll have 8 descendant(s).[Parent, PID: 6093]: I am waiting for PID 6094 to finish.</pre>

<pre>   [Child, PID: 6094]: I was called with descendant count=8. I’ll have 7 descendant(s).[Parent, PID: 6094]: I am waiting for PID 6095 to finish.</pre>

<pre>   [Child, PID: 6095]: I was called with descendant count=7. I’ll have 6 descendant(s).[Parent, PID: 6095]: I am waiting for PID 6096 to finish.</pre>

<pre>   [Child, PID: 6096]: I was called with descendant count=6. I’ll have 5 descendant(s).</pre>

4

<pre>[Parent, PID: 6096]: I am waiting for PID 6097 to finish.   [Child, PID: 6097]: I was called with descendant count=5. I’ll have 4 descendant(s).</pre>

<pre>[Parent, PID: 6097]: I am waiting for PID 6098 to finish.   [Child, PID: 6098]: I was called with descendant count=4. I’ll have 3 descendant(s).</pre>

<pre>[Parent, PID: 6098]: I am waiting for PID 6099 to finish.   [Child, PID: 6099]: I was called with descendant count=3. I’ll have 2 descendant(s).</pre>

<pre>[Parent, PID: 6099]: I am waiting for PID 6100 to finish.   [Child, PID: 6100]: I was called with descendant count=2. I’ll have 1 descendant(s).</pre>

<pre>[Parent, PID: 6100]: I am waiting for PID 6101 to finish.   [Child, PID: 6101]: I was called with descendant count=1. I’ll have 0 descendant(s).</pre>

<pre>[Parent, PID: 6100]: Child 6101 finished with status code 0. It’s now my turn to exit.[Parent, PID: 6099]: Child 6100 finished with status code 1. It’s now my turn to exit.[Parent, PID: 6098]: Child 6099 finished with status code 2. It’s now my turn to exit.[Parent, PID: 6097]: Child 6098 finished with status code 3. It’s now my turn to exit.[Parent, PID: 6096]: Child 6097 finished with status code 4. It’s now my turn to exit.[Parent, PID: 6095]: Child 6096 finished with status code 5. It’s now my turn to exit.[Parent, PID: 6094]: Child 6095 finished with status code 6. It’s now my turn to exit.[Parent, PID: 6093]: Child 6094 finished with status code 7. It’s now my turn to exit.[Parent, PID: 6092]: Child 6093 finished with status code 8. It’s now my turn to exit.[Parent, PID: 6091]: Child 6092 finished with status code 9. It’s now my turn to exit.<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="e093888590819284a08e8f928d818e8499">[email protected]</a>:~&gt;</pre>

Scenario 3: The Explorer

In the explorer, we have a parent process that will move about the file system and will create children that execute specific commands in each location. As with the last two scenarios, the parent process will use its seed value to select a di↵erent random number for each of the five locations it will visit. The parent process will use the modulo operator with the selected random number and the number 6 (i.e., (randomNumber % 6)), given the 6 options, to determine which directory to change into. Below are the directories used for the six di↵erent options:

• 0: /home• 1: /proc• 2: /proc/sys • 3: /usr• 4: /usr/bin • 5: /bin

For each of the five locations, it will change its current working directory (e.g., using chdir()), print out the option that was selected based on the random number, and output the value of the current working directory (using getcwd()). It then forks o↵ a child that runs the ls -alh command. Done correctly, the child’s listing output should match the directory the parent selected. The parent process will wait on the child to complete before moving on to the next file system location.

This scenario is most easily created using the Prolific Parent code as a starting point. Students may want to start the project by copying prolific.c to explorer.c and then customizing explorer.c to implement the scenario.

“Not all those who wander are lost.”– J.R.R. Tolkien, The Fellowship of the Ring

5

An example transcript of The Explorer with seed value 12345 is below: <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="02716a6772637066426c6d706f636c667b">[email protected]</a>:~&gt; ./explorer

<pre>Read seed value: 12345</pre>

<pre>Read seed value (converted to integer): 12345It’s time to see the world/file system!Selection #1: /usr [SUCCESS]Current reported directory: /usr</pre>

<pre>[Parent]: I am waiting for PID 11765 to finish.   [Child, PID: 11765]: Executing ’ls -alh’ command...</pre>

<pre>total 116Kdrwxr-xr-x  11 root root 4.0K Aug 26 08:08 .drwxr-xr-x  24 root root 4.0K Jan 10 06:53 ..[... truncated the 9 lines of output here...][Parent]: Child 11765 finished with status code 0. Onward!Selection #2: /bin [SUCCESS]Current reported directory: /bin[Parent]: I am waiting for PID 11766 to finish.</pre>

<pre>   [Child, PID: 11766]: Executing ’ls -alh’ command...total 17M</pre>

<pre>drwxr-xr-x  2 root root  12K Jan 14 06:25 .drwxr-xr-x 24 root root 4.0K Jan 10 06:53 ..-rwxr-xr-x  1 root root 1.1M Jun  6  2019 bash[... truncated the 274 lines of output here...][Parent]: Child 11766 finished with status code 0. Onward!Selection #3: /bin [SUCCESS]</pre>

<pre>Current reported directory: /bin[Parent]: I am waiting for PID 11767 to finish.</pre>

<pre>   [Child, PID: 11767]: Executing ’ls -alh’ command...total 17M</pre>

<pre>drwxr-xr-x  2 root root  12K Jan 14 06:25 .drwxr-xr-x 24 root root 4.0K Jan 10 06:53 ..-rwxr-xr-x  1 root root 1.1M Jun  6  2019 bash[... truncated the 274 lines of output here...][Parent]: Child 11767 finished with status code 0. Onward!Selection #4: /proc [SUCCESS]</pre>

<pre>Current reported directory: /proc[Parent]: I am waiting for PID 11768 to finish.</pre>

<pre>   [Child, PID: 11768]: Executing ’ls -alh’ command...total 4.0K</pre>

<pre>dr-xr-xr-x 313 root            rootdrwxr-xr-x  24 root            rootdr-xr-xr-x   9 root            root[... truncated the 360 lines of output here...][Parent]: Child 11768 finished with status code 0. Onward!Selection #5: /usr [SUCCESS]</pre>

<pre>Current reported directory: /usr[Parent]: I am waiting for PID 11769 to finish.</pre>

<pre>   [Child, PID: 11769]: Executing ’ls -alh’ command...total 116K</pre>

<pre>drwxr-xr-x  11 root root 4.0K Aug 26 08:08 .drwxr-xr-x  24 root root 4.0K Jan 10 06:53 ..[... truncated the 9 lines of output here...]</pre>

6

<pre>   0 Aug 26 08:14 .4.0K Jan 10 06:53 ..</pre>

<pre>0 Aug 26 08:14 1</pre>

<pre>[Parent]: Child 11769 finished with status code 0. Onward!<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="ff8c979a8f9e8d9bbf91908d929e919b86">[email protected]</a>:~&gt;</pre>

For this part of the assignment, the following new systems calls and library functions may be needed:

• chdir() – change the current working directory.• getcwd() – get the current working directory.• execvp() – execute a program (using the path environmental variable to find the executable).

Scenario 4: The Slug

We develop The Slug scenario with Scenario 5 (“The Slug Race”) in mind. The Slug will take a command line argument which can be one of four values: 1, 2, 3, or 4. Based on this value, The Slug will open files seed slug 1.txt, seed slug 2.txt, seed slug 3.txt, or seed slug 4.txt, respectively, to read in its random seed value. This allows multiple Slugs to read in di↵erent random seed values (because otherwise, all the Slugs would behave identically).

The Slug generates two random numbers. The first number represents the amount of seconds, between 1 and 4, that it will wait before proceeding. The second number represents a coin flip on whether it will run the last -d –fulltimes or the id -u command. The Slug is a single process program (i.e., it does not fork a child), so its invocation of those commands will be the end of the process.

An example transcript of The Slug with seed value 12345 stored in seed slug 3.txt is below: <a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="c1b2a9a4b1a0b3a581afaeb3aca0afa5b8">[email protected]</a>:~&gt; ./slug 3

<pre>[Slug PID: 18178] Read seed value: 12345</pre>

<pre>[Slug PID: 18178] Read seed value (converted to integer): 12345[Slug PID: 18178] Delay time is 4 seconds. Coin flip: 1[Slug PID: 18178] I’ll get the job done. Eventually...[ ... a delay of roughly 4 seconds occurs before the next line is output ...][Slug PID: 18178] Break time is over! I am running the ’id -u’ command.</pre>

<pre>1001<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="681b000d18091a0c2806071a0509060c11">[email protected]</a>:~&gt;</pre>

For this part of the assignment, the following new systems calls and library functions may be needed: • sleep() – pauses execution for the specified number of seconds.

Scenario 5: The Slug Race

This scenario is most easily created using the Prolific Parent code as a starting point. Students may want to start the project by copying prolific.c to slugrace.c and then customizing slugrace.c to implement the scenario.

The Slug Race parent process will spawn four children processes, each of which will run The Slug exe- cutable from Scenario 4. The parent will spawn the children using a loop with a counter, providing a di↵erent parameter to each spawned child. The parent will enter a loop, checking to see if any child has finished, and if not, waiting 0.25 seconds before printing out the current racing Slugs. The parent will continue looping until all the children have finished the race. The parent may not force an ordering on the children processes and it may not block waiting for a child to finish.

7

Each time a child finishes, the parent should print a message saying which child finished and how long (in seconds and milliseconds) since the start of the race that it took for the child to finish. Note that this requires the parent to have a timestamp from when the race began.

An example transcript of The Slug Race is below. Each slug has a di↵erent seed value in their respec- tive seed slug x.txt files. These values were 55555, 56789, 12345, and 456 for the seed slug 1.txt, seed slug 2.txt, seed slug 3.txt, and seed slug 4.txt files, respectively.

<pre><a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="5023383520312234103e3f223d313e3429">[email protected]</a>:~&gt; ./slugrace[Parent]: I forked off child 18186.</pre>

<pre>   [Child, PID: 18186]: Executing ’./slug 1’ command...[Parent]: I forked off child 18187.</pre>

<pre>   [Child, PID: 18187]: Executing ’./slug 2’ command...[Parent]: I forked off child 18188.</pre>

<pre>   [Child, PID: 18188]: Executing ’./slug 3’ command...[Parent]: I forked off child 18189.The race is ongoing. The following children are still racing: 18186 18187 18188 18189</pre>

<pre>   [Child, PID: 18189]: Executing ’./slug 4’ command...[Slug PID: 18186] Read seed value: 55555[Slug PID: 18186] Read seed value (converted to integer): 55555[Slug PID: 18186] Delay time is 3 seconds. Coin flip: 0[Slug PID: 18186] I’ll get the job done. Eventually...[Slug PID: 18187] Read seed value: 56789[Slug PID: 18188] Read seed value: 12345[Slug PID: 18187] Read seed value (converted to integer): 56789[Slug PID: 18188] Read seed value (converted to integer): 12345[Slug PID: 18187] Delay time is 2 seconds. Coin flip: 1[Slug PID: 18188] Delay time is 4 seconds. Coin flip: 1[Slug PID: 18187] I’ll get the job done. Eventually...[Slug PID: 18188] I’ll get the job done. Eventually...[Slug PID: 18189] Read seed value: 456[Slug PID: 18189] Read seed value (converted to integer): 456[Slug PID: 18189] Delay time is 4 seconds. Coin flip: 1[Slug PID: 18189] I’ll get the job done. Eventually...The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189The race is ongoing. The following children are still racing: 18186 18187 18188 18189[Slug PID: 18187] Break time is over! I am running the ’id -u’ command.1001Child 18187 has crossed the finish line! It took 2.000000 secondsThe race is ongoing. The following children are still racing: 18186 18188 18189The race is ongoing. The following children are still racing: 18186 18188 18189The race is ongoing. The following children are still racing: 18186 18188 18189[Slug PID: 18186] Break time is over! I am running the ’last -d --fulltimes’ command.The race is ongoing. The following children are still racing: 18186 18188 18189shepard  pts/1   ops.normandy.local  Thu Jan 14 14:49:07 2021   still logged inshepard  pts/0   ops.normandy.local  Thu Jan 14 12:03:58 2021 - Thu Jan 14 14:32:45 2021  (02:28)joker    pts/0  helm.normandy.local  Tue Jan 12 03:54:18 2021 - Tue Jan 12 03:54:24 2021  (00:00)joker    pts/0  helm.normandy.local  Mon Jan 11 13:01:07 2021 - Mon Jan 11 15:14:19 2021  (02:13)wtmp begins Fri Jan  1 15:42:43 2021</pre>

8

<pre>Child 18186 has crossed the finish line! It took 3.000000 secondsThe race is ongoing. The following children are still racing: 18188 18189The race is ongoing. The following children are still racing: 18188 18189The race is ongoing. The following children are still racing: 18188 18189[Slug PID: 18188] Break time is over! I am running the ’id -u’ command.[Slug PID: 18189] Break time is over! I am running the ’id -u’ command.The race is ongoing. The following children are still racing: 18188 1818910011001Child 18188 has crossed the finish line! It took 4.000000 secondsChild 18189 has crossed the finish line! It took 4.000000 secondsThe race is over! It took 4.000000 seconds<a href="/cdn-cgi/l/email-protection" class="__cf_email__" data-cfemail="37445f5247564553775958455a5659534e">[email protected]</a>:~&gt;</pre>

For this part of the assignment, the following new systems calls and library functions may be needed: • usleep() – pauses execution for the specified number of microseconds.

• clock gettime – gets a high resolution clock. Checkpoint Contributions