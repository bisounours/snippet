# Snippets for windows console

### Kill task

```bat
taskkill /f /im Spotify.exe  
```

### Rename list of files based on a pattern

```bat
forfiles /S /M *.adoc /C "cmd /c rename @file _@file" 
```