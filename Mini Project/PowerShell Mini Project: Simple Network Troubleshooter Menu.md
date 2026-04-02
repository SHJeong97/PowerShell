# PowerShell Mini Project: Simple Network Troubleshooter Menu (Ping • IP Config • Flush DNS)

This mini project provides a simple interactive menu to run common network troubleshooting actions:
1) Check internet connectivity  
2) View IP configuration  
3) Flush DNS cache  

Everything below is proof-based and taken from what I ran.

---

## Skills Practiced

- Building interactive menus with `Write-Host`
- User input handling with `Read-Host`
- Branching logic with `switch`
- Connectivity testing with `Test-Connection -Quiet`
- Reading IP configuration with `Get-NetIPConfiguration`
- Flushing DNS cache with `Clear-DnsClientCache`

---

## Full Implementation

### Script: `Network_trobleshooter.ps1`

```powershell
# Display menu options
Write-Host "Simple Network Troubleshooter"
Write-Host "1. Check internet Connection"
Write-Host "2. View IP configuration"
Write-Host "3. Flush DNS"

# Get user input
$user_choice = Read-Host "Enter your chocie (1 - 3)"

# Switch statement to handle user input
switch ($user_choice) {
    "1" {
        $testResult = Test-Connection -ComputerName "www.google.com" -Quiet
        
        if($testResult -eq "True") {
            Write-Host "You have Internet connection"
        }
        else {
            Write-Host "Unable to connect to the Internet"
        }
      }
    "2" {
        $ipAddress = (Get-NetIPConfiguration).IPv4Address.IPAddress
        $DefaultGateway = (Get-NetIPConfiguration).IPv4DefaultGateway.NextHop
        Write-Host "IP address: $ipAddress"
        Write-Host "Default Gateway: $DefaultGateway"
    }
    "3" {
        Clear-DnsClientCache
        Write-Host "DNS cache has been flushed"

    }
    Default {
        # Invalid Selection
        Write-Host "Invalid choice. Please run the script again."
    }
}
```
### Execution
```powershell
.\Desktop\PlayGround\script\Network_trobleshooter.ps1
```

##  Implementation Walkthrough
- The script prints a menu and reads a selection (1–3).


- A switch block runs the correct action based on the selection:


### Option 1 — Check internet connection
- Test-Connection -Quiet returns a boolean-style result.


- The script prints a success/failure message based on that result.


### Option 2 — View IP configuration
- Reads the IPv4 address and default gateway from Get-NetIPConfiguration.


- Prints them to the console.


### Option 3 — Flush DNS
- Runs Clear-DnsClientCache.


- Prints confirmation.


### Default
- Handles invalid input with a message.



##  Key Takeaways (What I Learned)
- Menu + switch is a clean pattern for small troubleshooting tools.


- Test-Connection -Quiet is automation-friendly because it returns a simple True/False-style result.


- Network info is accessible through objects like Get-NetIPConfiguration, making it easy to extract specific fields.



##  Results & Validation
```powershell
PS C:\Users\tech> .\Desktop\PlayGround\script\Network_trobleshooter.ps1
Simple Network Troubleshooter
1. Check internet Connection
2. View IP configuration
3. Flush DNS
Enter your chocie (1 - 3): 1
You have Internet connection
```

##  Validation Walkthrough
```powershell
# Run the script
.\Desktop\PlayGround\script\Network_trobleshooter.ps1

# Choose an option when prompted:
# 1 = ping test
# 2 = show IP + gateway
# 3 = flush DNS
```

