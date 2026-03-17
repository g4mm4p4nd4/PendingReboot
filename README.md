# PendingReboot

[![Build status](https://ci.appveyor.com/api/projects/status/1vglgjk2d42n9fle/branch/master?svg=true)](https://ci.appveyor.com/project/bcwilhite/pendingreboot/branch/master)

Module to detect Windows OS pending reboots.

This module leverages WMI in order to query the Registry for various pending reboot detections.

## How to use

Deploy the PendingReboot folder from the Release folder to your PSModulePath, i.e. PendingReboot and PowerShell will automatically find/load the module.

This module is also published on the [PowerShell Gallery](https://www.powershellgallery.com/packages/PendingReboot), so installing it via PowerShell is an option:

```PowerShell
Install-Module -Name PendingReboot
```

## Functions

* **Test-PendingReboot** Test the pending reboot status on a local and/or remote computer.

### Test-PendingReboot

* **ComputerName**: A single computer name or an array of computer names.  The default is localhost ($env:COMPUTERNAME).
* **Credential**: Specifies a user account that has permission to perform this action. The default is the current user. Type a username, such as User01, Domain01\User01, or User@Contoso.com. Or, enter a PSCredential object, such as an object that is returned by the Get-Credential cmdlet. When you type a user name, you are prompted for a password.
* **Detailed**: Indicates that this function returns a detailed result of pending reboot information, why the system is pending a reboot, not just a true/false response.
* **SkipConfigurationManagerClientCheck**: Indicates that this function will not test the Client SDK WMI class that is provided by the System Center Configuration Manager Client.  This parameter is useful when SCCM is not used/installed on the targeted systems.
* **SkipPendingFileRenameOperationsCheck**: Indicates that this function will not test the PendingFileRenameOperations MultiString Value property of the Session Manager registry key.  This parameter is useful for eliminating possible false positives. Many Anti-Virus packages will use the PendingFileRenameOperations MultiString Value in order to remove stale definitions and/or .dat files.

## Versions

### 0.9.0.6

* Fixed a bug when querying multiple computers with different values returned unexpected results.
  * Updated Pester test to account for this type of issue.

### 0.9.0.5

* Added appveyor, build script and psdeploy

### 0.9.0.0

* Initial Release with major changes from the TechNet Gallery
  * The function/module received a major overhaul since coming over from the technet gallary.
  * The function is no longer called "Get-PendingReboot", instead "Test-PendingReboot" is more appropriate.
  * **Breaking Change** Test-PendingReboot has all new property names to be more inline with what it is testing.
  * New Parameters were introduced:
    * SkipConfigurationManagerClientCheck - Allows users to skip the CCM Client SDK WMI query, since not all systems will have an SCCM Agent installed.
    * SkipPendingFileRenameOperations - Allows users to skip the PendingFileRenameOperations check, since this MultiString Value generates false-positives.
    * Credential - By popular demand, this parameter will allow the user to connect with alternate credentials.

## Examples

### Test the pending reboot status of the local computer

```PowerShell
PS C:\> Test-PendingReboot

ComputerName IsRebootPending
------------ ---------------
WKS01                   True
```

### Test the pending reboot status of a local computer, returning only a bool value

```PowerShell
PS C:\> (Test-PendingReboot).IsRebootPending
True
```

### Test the pending reboot status of a remote computer called 'DC01' and return detailed information

```PowerShell
PS C:\> Test-PendingReboot -ComputerName DC01 -Detailed

ComputerName                     : dc01
ComponentBasedServicing          : True
PendingComputerRenameDomainJoin  : False
PendingFileRenameOperations      : False
PendingFileRenameOperationsValue :
SystemCenterConfigManager        : False
WindowsUpdateAutoUpdate          : True
IsRebootPending                  : True
```

### Test the pending reboot status of a remote computer called 'DC01', with detialed information, skipping System Center Configuration Manager Agent and PendingFileRenameOperation Checks

```PowerShell
PS C:\> Test-PendingReboot -ComputerName DC01 -SkipConfigurationManagerClientCheck -SkipPendingFileRenameOperationsCheck -Detailed

CommputerName                    : dc01
ComponentBasedServicing          : True
PendingComputerRenameDomainJoin  : False
PendingFileRenameOperations      : False
PendingFileRenameOperationsValue :
SystemCenterConfigManager        :
WindowsUpdateAutoUpdate          : True
IsRebootPending                  : True
```


## Overview & Purpose

This project provides a PowerShell module for determining whether a Windows system has a pending reboot. It allows administrators and automation scripts to quickly detect if a reboot is required after updates, configuration changes, or other maintenance tasks.


## Features & Tech Stack

**Key Features**

- PowerShell functions to test for pending reboots on local or remote computers
- Checks multiple indicators: Component Based Servicing, Computer Rename Domain Join, File Rename operations, System Center Configuration Manager client state, Windows Update Automatic Update status, and registry values
- Ability to skip specific checks for faster execution
- Outputs detailed status information for each check

**Tech Stack**

| Component | Description |
| --- | --- |
| Language | PowerShell |
| Platform | Windows |
| Dependencies | WMI and registry access via PowerShell |


## Installation & Usage

1. Clone or download this repository to your local machine.
2. Import the module into your PowerShell session:

   ```powershell
   Import-Module .\PendingReboot.psm1
   ```
3. Use the `Test-PendingReboot` function to check the pending reboot status. For example:

   ```powershell
   # Check the local machine
   Test-PendingReboot -Detailed

   # Check a remote computer named "DC01"
   Test-PendingReboot -ComputerName DC01 -Detailed
   ```

These commands return a structured object indicating which reboot indicators are present.


## Business & Entrepreneurial Value

An accurate pending reboot detector is valuable for businesses that manage fleets of Windows machines. Integrating this module into automation pipelines allows IT teams to schedule reboots strategically, reducing downtime and improving compliance with patching policies. Companies offering remote management, monitoring, or patching services can bundle this capability as part of a paid subscription, upsell advanced reporting features, or license it for inclusion in larger automation platforms. The solution scales to enterprise environments and can integrate with configuration management systems to drive data‑driven insights about reboot requirements.

## Consumer Value

For system administrators and end users, this module provides an easy way to understand when a reboot is necessary. By consolidating multiple checks into a single command, it saves time compared with manually inspecting registry keys or update logs. Users benefit from more reliable systems, reduced unexpected reboots, and the transparency of knowing exactly why a reboot is required. The script is easy to run, requires no external dependencies beyond PowerShell, and respects privacy by running locally or against trusted remote hosts.
