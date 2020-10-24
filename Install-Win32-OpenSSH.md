_From https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH_

## Install Win32 OpenSSH (test release)

1. Note [these considerations](https://github.com/PowerShell/Win32-OpenSSH/wiki/Various-Considerations) and [project scope](https://github.com/PowerShell/Win32-OpenSSH/wiki/Project-Scope) first.
1. Download the [latest](https://github.com/PowerShell/Win32-OpenSSH/releases/latest) build of OpenSSH.
To get links to latest downloads [this wiki page](https://github.com/PowerShell/Win32-OpenSSH/wiki/How-to-retrieve-links-to-latest-packages).
1. Extract contents of the latest build to `C:\Program Files\OpenSSH` (Make sure binary location has the Write permissions to just to SYSTEM, Administrator groups. Authenticated users should and only have Read and Execute.)
1. In an elevated Powershell console, run the following
    * `powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1`
1. Open the firewall for sshd.exe to allow inbound SSH connections
    * `New-NetFirewallRule -Name sshd -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22`

    Note: `New-NetFirewallRule` is for Windows 2012 and above servers only. If you're on a client desktop machine (like Windows 10) or Windows 2008 R2 and below, try:

    ```
    netsh advfirewall firewall add rule name=sshd dir=in action=allow protocol=TCP localport=22
    ```
1. Start `sshd` (this will automatically generate host keys under %programdata%\ssh if they don't already exist)
    * `net start sshd`
1. Optional
    - To configure a default shell, see [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/DefaultShell)
    - To setup `sshd` service to auto-start 
      * `Set-Service sshd -StartupType Automatic`
    - To migrate sshd configuration from older versions (0.0.X.X), see [here](https://github.com/PowerShell/Win32-OpenSSH/wiki/Migrate-sshd_config-from-older-versions)

## Uninstall Win32 OpenSSH

* Start Windows Powershell as Administrator
* Navigate to the OpenSSH directory
    * `cd 'C:\Program Files\OpenSSH'`
* Run the uninstall script
    * `powershell.exe -ExecutionPolicy Bypass -File uninstall-sshd.ps1`

[Secure file]: https://github.com/PowerShell/Win32-OpenSSH/wiki/Security-protection-of-various-files-in-win32-openssh
[build13]: https://github.com/PowerShell/Win32-OpenSSH/releases/tag/v0.0.13.0