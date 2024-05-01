---
title: PowerShell Shows Wi-Fi Passwords History
date: 2020-01-01
categories: DevOps
tags:
  - powershell
github publish: true
---

While this will not help you crack unknown Wi-Fi passwords, it will show you any passwords that are on a computer. So if you can find a computer already on the network and can run this command in PowerShell, you can get access to that password plus any others that computer has in memory.

Simple script to get all Passwords to Wi-Fi you have accessed in the past.

```powershell
(netsh wlan show profiles) | Select-String “\:(.+)$” | %{$name=$_.Matches.Groups[1].Value.Trim(); $_} | %{(netsh wlan show profile name=”$name” key=clear)} | Select-String “Key Content\W+\:(.+)$” | %{$pass=$_.Matches.Groups[1].Value.Trim(); $_} | %{[PSCustomObject]@{ PROFILE_NAME=$name;PASSWORD=$pass }} | Format-Table -AutoSize
```

This script is designed to extract Wi-Fi profile information, including the profile name and password, from a Windows system using PowerShell. Here's a breakdown of what each part of the script does:

1. `(netsh wlan show profiles)`: This command retrieves a list of Wi-Fi profiles configured on the system.
2. `Select-String "\:(.+)$"`: This command selects lines that contain a colon followed by one or more characters until the end of the line. This effectively isolates the Wi-Fi profile names from the output.
3. `%{$name=$_.Matches.Groups[1].Value.Trim(); $_}`: This part of the script assigns the matched Wi-Fi profile names to the variable `$name` and trims any leading or trailing whitespace from the names.
4. `netsh wlan show profile name="$name" key=clear`: This command retrieves detailed information about each Wi-Fi profile, including the password, by specifying the profile name and requesting the key in clear text.
5. `Select-String "Key Content\W+\:(.+)$"`: This command selects lines that contain "Key Content" followed by a colon and one or more characters until the end of the line. This isolates the Wi-Fi passwords from the detailed profile information.
6. `%{$pass=$_.Matches.Groups[1].Value.Trim(); $_}`: This part of the script assigns the matched Wi-Fi passwords to the variable `$pass` and trims any leading or trailing whitespace from the passwords.
7. `[PSCustomObject]@{ PROFILE_NAME=$name;PASSWORD=$pass }`: This creates a custom PowerShell object with two properties: `PROFILE_NAME` (containing the Wi-Fi profile name) and `PASSWORD` (containing the Wi-Fi password).
8. `Format-Table -AutoSize`: This formats the output as a table with automatically adjusted column widths for easier readability.

Overall, this script gathers Wi-Fi profile information, extracts the profile names and passwords, and presents them in a tabular format.
