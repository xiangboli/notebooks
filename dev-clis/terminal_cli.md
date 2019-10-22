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

## Sed & Awk
[sed introduction](http://www.grymoire.com/Unix/Sed.html)
[awk introduction](http://www.grymoire.com/Unix/Awk.html)


