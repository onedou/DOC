    find . -type f -print0 | xargs -0 dos2unix

Will recursively find all files inside current directory and call for these files dos2unix command
