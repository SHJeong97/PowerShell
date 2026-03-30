# PowerShell Object Exploration Portfolio (FileInfo • Get-Member • Properties • Methods • OpenRead)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Understanding that `Get-ChildItem` returns objects (not plain text)
- Inspecting object **types**, **properties**, and **methods** with `Get-Member`
- Accessing file metadata through `System.IO.FileInfo`
- Updating file timestamps (`LastWriteTime`) using `[datetime]::ParseExact`
- Opening a file stream with `.OpenRead()` and interpreting stream properties

---

## Full Implementation

### 1) Inspect a file object and its properties
```powershell
gci .\TEM.txt | Get-Member
(gci .\TEM.txt).LastAccessTime
(gci .\TEM.txt).Exists
(gci .\TEM.txt).FullName
```
### 2) Modify file metadata (LastWriteTime)
```powershell
(gci '.\New Text Document.txt').LastWriteTime = [datetime]::ParseExact("01/01/2026", "MM/dd/yyyy", $null)
(gci '.\New Text Document.txt').LastWriteTime
```
### 3) List object methods and open a read-only stream
```powershell
(gci '.\New Text Document.txt') | Get-Member -MemberType Method
(gci '.\New Text Document.txt').OpenRead()
```

##  Implementation Walkthrough
### gci .\TEM.txt | Get-Member
- gci (Get-ChildItem) returns a System.IO.FileInfo object for a file.


- Get-Member shows what that object contains (properties like FullName, Length, LastWriteTime, etc.).


### Accessing properties like .LastAccessTime, .Exists, .FullName
- (gci .\TEM.txt).LastAccessTime reads the file’s last access timestamp.


- (gci .\TEM.txt).Exists returns True/False to confirm the file is present.


- (gci .\TEM.txt).FullName returns the full absolute path.


### Changing .LastWriteTime with ParseExact
- You set LastWriteTime to an exact date (01/01/2026) using:


	- A strict date parser (ParseExact)


	- A format string (MM/dd/yyyy)


- This is useful when you need consistent timestamps (testing, sorting, lab simulations).


### Listing methods + .OpenRead()
- Get-Member -MemberType Method shows actions you can perform on the FileInfo object (CopyTo, MoveTo, Delete, OpenRead, etc.).


- .OpenRead() opens the file as a read-only stream, confirming:


	- CanRead = True


	- CanWrite = False


	- Length matches the file size shown by the stream



##  Key Takeaways (What I Learned)
- PowerShell is object-first.
 Get-ChildItem outputs rich objects (FileInfo), so I can access metadata directly via properties.


- Get-Member is the fastest way to learn what an object can do.
 It shows both properties (data) and methods (actions).


- File timestamps are editable through object properties.
 Setting LastWriteTime is straightforward and reproducible with ParseExact.


- Files can be handled as streams, not just text.
 .OpenRead() returns a stream object with capabilities (read/seek) and metadata (length, position).



##  Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> gci .\TEM.txt | Get-Member

  TypeName: System.IO.FileInfo
Name          MemberType Definition
----          ---------- ----------
... (properties like FullName, Exists, LastWriteTime, Length, etc.)

PS C:\Users\tech\Desktop\PlayGround> (gci .\TEM.txt).LastAccessTime
Wednesday, February 25, 2026 9:06:20 AM

(gci .\TEM.txt).Exists
True

PS C:\Users\tech\Desktop\PlayGround> (gci .\TEM.txt).FullName
C:\Users\tech\Desktop\PlayGround\TEM.txt

PS C:\Users\tech\Desktop\PlayGround> (gci '.\New Text Document.txt').LastWriteTime = [datetime]::ParseExact("01/01/2026", "MM/dd/yyyy", $null)
PS C:\Users\tech\Desktop\PlayGround> (gci '.\New Text Document.txt').LastWriteTime
Thursday, January 1, 2026 12:00:00 AM

PS C:\Users\tech\Desktop\PlayGround> (gci '.\New Text Document.txt') | Get-Member -MemberType Method
... (methods including OpenRead, CopyTo, MoveTo, Delete, etc.)

PS C:\Users\tech\Desktop\PlayGround> (gci '.\New Text Document.txt').OpenRead()

CanRead  : True
CanWrite : False
CanSeek  : True
Length   : 173
Name     : C:\Users\tech\Desktop\PlayGround\New Text Document.txt
Position : 0
```
...

##  Validation Walkthrough
### Run these in the same directory:
```powershell
gci .\TEM.txt | Get-Member
(gci .\TEM.txt).LastAccessTime
(gci .\TEM.txt).Exists
(gci .\TEM.txt).FullName

(gci '.\New Text Document.txt').LastWriteTime = [datetime]::ParseExact("01/01/2026", "MM/dd/yyyy", $null)
(gci '.\New Text Document.txt').LastWriteTime

(gci '.\New Text Document.txt') | Get-Member -MemberType Method
(gci '.\New Text Document.txt').OpenRead()
```

