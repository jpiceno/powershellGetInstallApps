# Get Installed Applications Script

This PowerShell script retrieves a list of installed applications from the Windows registry, extracting their name, installation date, version, and publisher. The results are sorted by installation date in descending order and displayed in a table format.

## How It Works

1. **Scans Registry Paths:**
   - `HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*` → 64-bit applications
   - `HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*` → 32-bit applications on a 64-bit system
   - `HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*` → Applications installed for the current user

2. **Extracts Application Details:**
   - `DisplayName` (Application Name)
   - `InstallDate` (Formatted as a readable date if available)
   - `DisplayVersion` (Application Version)
   - `Publisher` (Application Publisher)

3. **Formats and Outputs Data:**
   - Sorts applications by `InstallDate` in descending order.
   - Displays results in a formatted table.

## Usage

Run the script in PowerShell:

```powershell
.\Get-InstalledApps.ps1
```

Ensure you have appropriate permissions to access the registry keys.

## Script Code

```powershell
# Define registry paths for installed applications
$registryPaths = @(
    "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*",
    "HKLM:\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*",
    "HKCU:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*"
)

# Create an array to store results
$installedApps = @()

# Loop through each registry path
foreach ($path in $registryPaths) {
    if (Test-Path $path) {
        $apps = Get-ItemProperty -Path $path | Where-Object { $_.DisplayName -and $_.InstallDate }

        foreach ($app in $apps) {
            $installedApps += [PSCustomObject]@{
                Name        = $app.DisplayName
                InstallDate = if ($app.InstallDate -match "^\d{8}$") { 
                                [datetime]::ParseExact($app.InstallDate, "yyyyMMdd", $null) 
                              } else { 
                                "Unknown" 
                              }
                Version     = $app.DisplayVersion
                Publisher   = $app.Publisher
            }
        }
    }
}

# Output results in a table format
$installedApps | Sort-Object InstallDate -Descending | Format-Table -AutoSize
```

## Example Output

```
Name                  InstallDate  Version   Publisher
----                  -----------  -------   ---------
Google Chrome        2023-09-15   117.0.0   Google LLC
Microsoft Edge       2023-08-20   115.2.1   Microsoft Corporation
Zoom                 2023-07-10   5.15.0    Zoom Video Communications
```

## Requirements
- Windows PowerShell
- Administrator privileges (recommended for full registry access)

## License
This script is provided under the MIT License. Feel free to use, modify, and distribute it.

---
For any issues or contributions, please submit a pull request or open an issue in the repository!

