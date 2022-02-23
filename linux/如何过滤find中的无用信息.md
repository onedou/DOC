How to skip “permission denied” errors when running find in Linux? [duplicate]

you can filter out messages to stderr. I prefer to redirect them to stdout like this.

 find / -name art  2>&1 | grep -v "Permission denied"
Explanation:

In short, all regular output goes to standard output (stdout). All error messages to standard error (stderr).

grep usually finds/prints the specified string, the -v inverts this, so it finds/prints every string that doesn't contain "Permission denied". All of your output from the find command, including error messages usually sent to stderr (file descriptor 2) go now to stdout(file descriptor 1) and then get filtered by the grep command.

This assumes you are using the bash/sh shell.

Under tcsh/csh you would use

 find / -name art |& grep ....
