# PowerShell Archiving & Integrity Portfolio (Wildcards • Pattern Matching • Get-FileHash)

This repository documents my hands-on PowerShell practice on Windows PowerShell 5.1.  
Everything below is proof-based and taken from commands I actually executed.

---

## Skills Practiced

- Directory inventory with `Get-ChildItem` (`gci`)
- Bulk archiving using wildcards and pattern matching
- Creating multiple ZIPs from different selection patterns
- File integrity verification using SHA-256 hashing (`Get-FileHash`)

---

## Full Implementation

### 1) Confirm what exists in the working directory
```powershell
gci
```

### 2) Archive using wildcard selection
```powershell
Compress-Archive test* -Destination test.zip
```
### 3) Archive using bracket pattern selection
```powershell
Compress-Archive test[1-2] -Destination test2.zip
```
### 4) Verify ZIPs were created
```powershell
Gci
```
### 5) Generate a SHA-256 hash for integrity checking
```powershell
Get-FileHash .\test1\My_Friends_Secret.txt
```

## Implementation Walkthrough
### gci
- Lists files and folders in C:\Users\tech\Desktop\PlayGround.


- This gives a baseline before creating archives.


### Compress-Archive test* -Destination test.zip
- test* selects items whose names start with test (ex: test1, test2, and any file beginning with test).


- Creates test.zip containing those matching items.


### Compress-Archive test[1-2] -Destination test2.zip
- test[1-2] matches names like test1 and test2.


- Creates a second ZIP test2.zip based on that selection.


### Get-FileHash ...
- Calculates the cryptographic hash of a file.


- You used SHA256 (default) which is commonly used to verify file integrity and detect tampering.



## Key Takeaways (What I Learned)
- Pattern matching makes bulk actions fast.
 I can select multiple files/folders with test* or narrow it with test[1-2].


- Archiving can be repeated with different selection rules.
 Creating test.zip and test2.zip shows how different patterns change what gets packaged.


- Hashes provide a reliable integrity check.
 Get-FileHash creates a fingerprint (SHA-256) that can be compared later to confirm the file didn’t change.



## Results & Validation
```powershell
PS C:\Users\tech\Desktop\PlayGround> gci

Directory: C:\Users\tech\Desktop\PlayGround
Mode   LastWriteTime         Length Name
----   -------------         ------ ----
d----- 2/24/2026 4:44 PM            test1
d----- 2/24/2026 4:27 PM            test2
-a---- 2/4/2026  9:41 AM       25135 New Microsoft Word Document.docx
-a---- 1/30/2026 4:14 PM         173 New Text Document.txt
-a---- 2/24/2026 11:38 AM         91 TEM.txt

PS C:\Users\tech\Desktop\PlayGround> Compress-Archive test* -Destination test.zip
PS C:\Users\tech\Desktop\PlayGround> Compress-Archive test[1-2] -Destination test2.zip

PS C:\Users\tech\Desktop\PlayGround> gci

Directory: C:\Users\tech\Desktop\PlayGround
Mode   LastWriteTime         Length Name
----   -------------         ------ ----
d----- 2/24/2026 4:44 PM            test1
d----- 2/24/2026 4:27 PM            test2
-a---- 2/4/2026  9:41 AM       25135 New Microsoft Word Document.docx
-a---- 1/30/2026 4:14 PM         173 New Text Document.txt
-a---- 2/24/2026 11:38 AM         91 TEM.txt
-a---- 2/24/2026 4:51 PM      372509 test.zip
-a---- 2/24/2026 4:52 PM      372509 test2.zip

PS C:\Users\tech\Desktop\PlayGround> Get-FileHash .\test1\My_Friends_Secret.txt

Algorithm Hash                                                                   Path
--------- ----                                                                   ----
SHA256    10757D21C8BDA6E5128C6E0DC55095C5326156A04819E180B8E26AFAD56141C7       C:\Users\tech\Desktop\PlayGro…
```

## Validation Walkthrough
```powershell
gci
Compress-Archive test* -Destination test.zip
Compress-Archive test[1-2] -Destination test2.zip
gci
Get-FileHash .\test1\My_Friends_Secret.txt
```

