# Useful Terminal Commands

## Look and kill process 
```bash
ps aux | grep jit
```
To list any process listening to the port 8080:
```bash
lsof -i:8080
```
To kill any process listening to the port 8080:
```bash
kill $(lsof -t -i:8080)
```
or more violently:
```bash
kill -9 $(lsof -t -i:8080)
```

## Check cpu & mem usage
```bash
ps -p <pid> -o %cpu,%mem,cmd
```
List all thread:
```bash
ps -e -T | grep <application name or pid>
```
or
```bash
top -H -p pid
```

## Work with jq, grep, sort, uniqe, wc 
```bash
cat test_atom_map | jq '.atom_map | .[] | select(.tick_size != null) | .tick_size | ."1"' | sort -u | wc -l
```
```bash
cat hm_decode.log | grep "QPs" | sort -u | wc -l
```

## Sed & Awk
[sed introduction](http://www.grymoire.com/Unix/Sed.html) <br/>
[awk introduction](http://www.grymoire.com/Unix/Awk.html)


