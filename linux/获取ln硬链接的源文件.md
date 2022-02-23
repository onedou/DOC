# How can you see the actual hard link by ls?

You can find inode number for your file with

ls -i
and

ls -l
shows references count (number of hardlinks to a particular inode)

after you found inode number, you can search for all files with same inode:

find . -inum NUM
will show filenames for inode NUM in current dir (.)
