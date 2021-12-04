# windows-kernel-process-initialization
Debug the early stage of process initialization using kernel mode debugging

The function that interesting us for process initialization debugging is the first kernel function which called in CreateProcess - NtCreateUserProcess.

In this function all the magic happens. The part the relevant for us is the first thing that happens after that the kernel allocates the EPROCESS and ETHREAD structures.

Therefore, what we need to do is:
1. bp nt!PspInsertProcess
2. g (run your target process)
3. bp /p @rcx nt!PspInsertThread
4. g

Let's explain it, first we break at PspInsertProcess which inserts the process to the process list. The important thing is that this function receives in RCX register the EPROCESS pointer that we need. Now, let's run the target process and hit the breakpoint.

After hitting the breakpoint, we will create a new one that breaks in nt!PspInsertThread *in the context of the new process* by using the EPROCESS from before.

The next breakpoint hitting will be in the new process context with the first thread that going to run and now you can freely debug NTDLL and all the loading phase.

DONE!!!
