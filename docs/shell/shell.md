# Snippets for shell

### File .bashrc
```shell
PS1='[\h][\e[0;36m\u\e[m]\e[0;35m$PWD\e[m\n\$ '
alias ll='ls -altr'
alias less='less -R'
```

### Zip list of file based on a pattern

```shell
find . -name "*.[ch]" -print | zip source -@ 
```

### Deletion of files (one by one) 

Deletion of files by pattern : 
```shell
find . -name "*csv" -exec rm {} \; 
```

Deletion of files by modification dates : 
```shell
find . -mtime +30 -exec rm {} \; 
```

### Empty files when disk full 

```shell
find . -type f -exec sh "echo ' ' > $1" {} \; 
```
  

taskkill /f /im Spotify.exe  

forfiles /S /M *.adoc /C "cmd /c rename @file _@file" 

for file in * ; do mv $file ${file//kor0f/kor0z} ; done 

