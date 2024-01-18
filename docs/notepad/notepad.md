# Snippets for Notepad++

## Remove duplicate lines from notepad++ => replace following regex by nothing (Search mode : regular expression + matches newline) 

```regex
^(.*?)$\s+?^(?=.*^\1$) 
```