# Makefile

```bash
# iterating over variable words
MYVAR=file1.c file2.c file3.c
$(foreach filename,$(MYVAR),diff $(filename)_1 $(filename)_2 >> diff.log )
```
