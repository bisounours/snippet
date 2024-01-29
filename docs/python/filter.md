# Python script to filter a file based on another one

## 1. Description

This script filter a file based on the pattern of another file.  
2 modes are possibles : 
- keep : the lines of the first file which match the patterns of the second are kept
- deleted : the lines of the first file which match the patterns of the second are deleted

The input files are not updated and the result of the filtering is printed in a new output file.  

## 2. Configuration

The parameters of the script can be put directly in the command line : 
```bash
py filter.py [mode] [filter element file] [file to treat] [result file]
```

If the parameters are not given in the command line, they will be asked in the prompt of the script.  

## 3. Script

```python
# coding=UTF-8

import sys

# Main script
####################################################################################################################################################

# Check arguments
if len(sys.argv) == 5 :
	print('Number of argument OK')
	mode = sys.argv[1]
	if mode != 'keep' and mode != 'delete' :
		print('Invalid arguments !')
		print('The mode of treatment can only be "keep" or "delete"')
		sys.exit(1)
	filterFile = open(sys.argv[2],'r')
	workingFile = open(sys.argv[3],'r')
	resultFile = open(sys.argv[4],'w')
elif len(sys.argv) == 1 :
	while 1 :
		mode = input('Mode [keep|delete] : ')
		if mode == 'keep' :
			break
		elif mode == 'delete' :
			break
		else :
			print('Generation mode invalid !')
	filterFileName = input('File of filter element : ')
	filterFile = open(filterFileName,'r')
	workingFileName = input('File to treat : ')
	workingFile = open(workingFileName,'r')
	resultFileName = input('File of result : ')
	resultFile = open(resultFileName,'w')
else :
	print('Invalid arguments !')
	print('Syntax to launch directly : py filter.py [mode] [filter element file] [file to treat] [result file]')
	print('Syntax to add parameter one by one : py filter.py')
	sys.exit(1)

#Convert input files in list
filterList = []
for filterLine in filterFile :
	filterList.append(filterLine.rstrip('\n'))
	
print('Nb filter items : '+ str(len(filterList)))

#Generate result list
for workingLine in workingFile :
	found = False
	for filter in filterList :
		if filter in  workingLine :
			found = True
	if found and mode == 'keep' :
		resultFile.write(workingLine);
	elif not found and mode == 'delete' :
		resultFile.write(workingLine);
	
# Close files
filterFile.close()
workingFile.close()
resultFile.close()

print('Treatment finished !')
```
