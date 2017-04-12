# loops
```
#!/bin/bash
for i in $( ls ); do
    echo item: $i
done
```

```
 #!/bin/bash
for i in $(seq 1 10);
do
    echo $i
done
```

```
#!/bin/bash 
COUNTER=0
while [  $COUNTER -lt 10 ]; do
    echo The counter is $COUNTER
    let COUNTER=COUNTER+1 
done
```

```
#!/bin/bash
COUNTER=20
until [  $COUNTER -lt 10 ]; do
    echo COUNTER $COUNTER
    let COUNTER-=1
done
```

# rename files
```
for file in *.cc; do mv "$file" "$(basename "$file" .cc).cpp"; done
```

# lowercase filenames
```
for i in *; do mv $i `echo $i | tr [:upper:] [:lower:]`; done
```

# Split files
```
# gcsplit on OSX
csplit --digits=2  --quiet --prefix=outfile infile "/-|/+1" "{*}"
csplit -n2 -s -b outfile infile "/-|/+1" "{*}"
awk '{print $0 " -|"> "file" NR}' RS='-\\|'  input-file
```

# Operate on each line of file
```
for i in $(cat tmp.txt);  do touch $i; done

# may need to set IFS first to $'\n'
```