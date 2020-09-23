<div align="center">

## How to search a directory tree for files


</div>

### Description

The following code sample illustrates how to search from a given directory downward through the entire directory tree. The sample output from this example is directed to the system debug screen.

For this example, the first class member (below) is called by a menu item. After the search is finished, a message box pops up. http://www.concentric.net/~cgalbrai/dirsrh.shtml
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Found on the World Wide Web](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/found-on-the-world-wide-web.md)
**Level**          |Unknown
**User Rating**    |4.5 (18 globes from 4 users)
**Compatibility**  |C\+\+ \(general\)
**Category**       |[Files](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/files__3-2.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/found-on-the-world-wide-web-how-to-search-a-directory-tree-for-files__3-70/archive/master.zip)





### Source Code

```
void CTestView::OnSearch()
	{
	// szFilename is declared in the header as array of char
	// look for MyFile.txt (or whatever)
		strcpy(szFilename,"MyFile.txt");
	// go to root directory (or to whichever directory that you wish)
		_chdir("C:\\");
	// search for the filename
		SearchDirectory();
	// announce when done
		MessageBox("Done Searching");
	}
SearchDirectory() is called initially from OnSearch(). SearchDirectory() is then called recursively (from itself) until the end of the directory tree is reached and all branches are searched.
	void CTestView::SearchDirectory()
	{
		struct _finddata_t filestruct;
		long hnd;
		char buffer[_MAX_PATH];
	// set _findfirst to find everthing
		hnd = _findfirst("*",&filestruct);
	// if handle fails, drive is empty...
		if((hnd == -1)) return;
	// get first entity on drive - check if it's a directory
		if(::GetFileAttributes(filestruct.name) & FILE_ATTRIBUTE_DIRECTORY
			&& !(::GetFileAttributes(filestruct.name) & FILE_ATTRIBUTE_HIDDEN)) {
	// if so, change to that directory and recursively call SearchDirectory
			if(*filestruct.name != '.') {
				_chdir(filestruct.name);
				SearchDirectory();
	// go back up one directory level
				_chdir("..");
			}
		}
		else {
	// if it's not a directory and it matches what you want...
			if(!stricmp(filestruct.name,szFilename)) {
	// output the filename with path to debugger
				_getcwd(buffer,_MAX_PATH);
				strcat(buffer,"\\");
				strcat(buffer,filestruct.name);
				strcat(buffer,"\r\n");
				OutputDebugString(buffer);
			}
		}
		while(!(_findnext(hnd,&filestruct))) {
			if(::GetFileAttributes(filestruct.name) & FILE_ATTRIBUTE_DIRECTORY
				&& !(::GetFileAttributes(filestruct.name) & FILE_ATTRIBUTE_HIDDEN)) {
				if(*filestruct.name != '.') {
					_chdir(filestruct.name);
					SearchDirectory();
					_chdir("..");
				}
			}
			else {
				if(!stricmp(filestruct.name,szFilename)) {
					_getcwd(buffer,_MAX_PATH);
					strcat(buffer,"\\");
					strcat(buffer,filestruct.name);
					strcat(buffer,"\r\n");
					OutputDebugString(buffer);
				}
			}
		}
		_findclose(hnd);
	}
```

