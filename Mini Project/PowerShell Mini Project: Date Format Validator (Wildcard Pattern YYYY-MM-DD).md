# PowerShell Mini Project: Date Format Validator (Wildcard Pattern YYYY-MM-DD)

This mini project prompts for a date string and validates whether the input matches the **YYYY-MM-DD** format using wildcard pattern matching.

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- User input collection with `Read-Host`
- Basic type checking using `-as [string]`
- Pattern validation using `-like` with wildcards
- Conditional branching (`if / else`)
- User-friendly output messaging with `Write-Host`

---

## Full Implementation

### Script: `date_validator.ps1`

```powershell
# Take User input
$user_input = Read-Host "Enter a date(format YYYY-MM-DD):"

#check the user input is string
if ($user_input -as [string]) {
    if ($user_input -like "????-??-??") {
    # Use wildcard pattern to check if the input match the YYYY-MM-DD format
    # ???? means years, ?? means months, and days
    Write-Host "Correct formate! The date you entered is: $user_input"
    }
    else {
        Write-Host "Your inpute is a string, but it doesn't match the format!"
    }
}
else {
    Write-Host "The input is not string"
    exit
}

```
### Execution
```powershell
.\Desktop\PlayGround\script\date_validator.ps1
```

##  Implementation Walkthrough
- The script prompts the user for a date string.


- It attempts a simple “string check” using -as [string].


- It validates the format using a wildcard pattern:


	- "????-??-??"


	- ???? = 4 characters (year)


	- ?? = 2 characters (month)


	- ?? = 2 characters (day)


- Based on the match, it prints either:


	- a success message (correct format), or


	- a failure message (string but wrong format)



##  Key Takeaways (What I Learned)
- Wildcard patterns are a quick way to validate input formatting.
 -like "????-??-??" confirms the shape of the string without complex parsing.


- Input can be a string but still be invalid format.
 20000101 is a string, but it doesn’t match YYYY-MM-DD.


- Validation scripts benefit from clear user feedback.
 Printing specific messages makes it obvious why input passed or failed.



##  Results & Validation
```powershell
PS C:\Users\tech> .\Desktop\PlayGround\script\date_validator.ps1
Enter a date(format YYYY-MM-DD):: 20000101
Your inpute is a string, but it doesn't match the format!

PS C:\Users\tech> .\Desktop\PlayGround\script\date_validator.ps1
Enter a date(format YYYY-MM-DD):: 2000-01-01
Correct formate! The date you entered is: 2000-01-01
```

##  Validation Walkthrough
```powershell
# Run the script
.\Desktop\PlayGround\script\date_validator.ps1

# Test inputs:
# Bad:  20000101
# Good: 2000-01-01
```
