# Perforce
```bash
# see whether a file needs to be synced:
p4 sync -n FILE

# see whether a file needs to be resolved:
p4 resolve -n FILE

# see whether a file is open:
p4 opened FILE

# see all the revisions of a file:
p4 filelog FILE

# get a giant dump of information about a file (formatted for easy parsing into a dictionary rather than human readability).
p4 fstat <flags> FILE

# see files that are modified but not checked out
p4 diff -se ...
```
