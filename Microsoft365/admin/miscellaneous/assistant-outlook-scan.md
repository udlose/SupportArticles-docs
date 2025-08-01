---
title: Outlook scan with Microsoft Support and Recovery Assistant
description: Describes available switches and conditions when using the Enterprise version of Microsoft Support and Recovery Assistant to scan Outlook.
author: Cloud-Writer    
ms.author: meerak
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
ms.custom: 
  - CSSTroubleshoot
ms.reviewer: gregmans; zebamehdi; meerak
appliesto: 
  - Microsoft 365
search.appverid: MET150
ms.date: 05/09/2025
---
# Scenario: Outlook Scan

[!INCLUDE [Microsoft Support and Recovery Assistant note](../../../includes/sara-note-new-outlook.md)]

The Outlook Scan scenario scans your device and creates a detailed report about Microsoft Outlook, Microsoft Office, Windows, and mailbox configurations.

In the full version of Microsoft Support and Recovery Assistant, the equivalent entry point for the scenario is *Advanced diagnostics \ Outlook \ Create a detailed report of my Outlook, Office, Windows, and mailbox configuration*.

**Note:** This scenario doesn't require that you use an elevated Command Prompt window.

## Download the Enterprise version of the Assistant

Select the following button:

> [!div class="nextstepaction"]
> [Download the Assistant](https://aka.ms/SaRA_EnterpriseVersionFiles)

For complete details about how to run the Enterprise version of the Assistant, see [Enterprise version of Microsoft Support and Recovery Assistant](sara-command-line-version.md).

## Script the Outlook Scan scenario

You can automate some of the tasks to run the scenario. The following sample Windows PowerShell script can help you get started. You can also edit it to better fit your requirements.

**Note**: Detailed instructions for the script are provided in the included comments. 

[Download the Outlook Scan scenario script](https://aka.ms/SaRAEnterpriseOutlookAdvancedDiagnostic)

The script performs the following basic tasks.

1. Copy the files for the Enterprise version of the Assistant to the client from the location specified in the script.

   The default download location that's referenced in the script is [https://aka.ms/SaRA_EnterpriseVersionFiles](https://aka.ms/SaRA_EnterpriseVersionFiles). This is the internet download location for the Assistant. However, you can modify the script to use either a path to the .zip file for the Assistant download or a folder location that contains the files that are extracted from the Assistant download.

2. Start SaraCmd.exe to run the Outlook Scan scenario by using the required switches and parameters.

3. Gather the log files generated when the scenario runs.

   The script collects all the log files that are generated by the Assistant.

4. Create a consolidated .zip file that includes all the logs.

   All the log files that are generated by the Assistant are compressed into a .zip file. The name of this .zip file is customizable.
   
## Available switches for the Outlook Scan scenario

The following switches are available for this scenario. They aren't case-sensitive. Unless noted as optional, the switches are required to run the scenario. You can use more than one optional switch.

|Switch \<parameter\>|Details|Required/Optional|
|---|---|---|
|`-S <scenarioname>`|Specify this switch and `ExpertExperienceAdminTask` as the value for the `scenarioname` parameter to run this scenario.|Required|
|`-AcceptEula`|Specify this switch to accept the End User License Agreement (EULA) and to run this scenario.|Required|
|`-LogFolder <Output Path>`|Specify this switch to force *SaraCmd.exe* to output scenario-specific logs to the folder that's specified by the `<Output Path>` parameter.|Optional|
|`-HideProgress`|Specify this switch to hide the progress display for this scenario. By default, the Assistant always displays the progress of tasks in the console.|Optional|
|`-OfflineScan`|Specify this switch to run an offline scan of Outlook while the Outlook application is running.|Optional|

## Sample commands

Here are some sample combinations of switches to run this scenario.

- Sample 1

  To scan Outlook, run the following command in a non-elevated Command Prompt window.

  ```console
  SaRAcmd.exe -S ExpertExperienceAdminTask -AcceptEula
  ```

- Sample 2

  To scan Outlook in an offline mode when the Outlook application is running, and hide the progress display in the console, run the following command in a non-elevated Command Prompt window.

  ```console
  SaRAcmd.exe -S ExpertExperienceAdminTask -AcceptEula -OfflineScan -HideProgress
  ```

- Sample 3

  To scan Outlook and output the log files to the *C:\temp* folder, run the following command in a non-elevated Command Prompt window.

  **Note:** The folder that's specified by the `-LogFolder` switch must exist and be writeable.

  ```console
  SaRAcmd.exe -S ExpertExperienceAdminTask -AcceptEula -LogFolder C:\temp
  ```

- Sample 4 

  To run an offline scan of Outlook, output the log files to the *C:\temp* folder, and hide the progress display in the console, run the following command in a non-elevated Command Prompt window.

  ```console
  SaRAcmd.exe -S ExpertExperienceAdminTask -AcceptEula -LogFolder C:\temp -HideProgress -OfflineScan
  ```

## Detected conditions and results

When you run the Outlook Scan scenario by using the Enterprise version of the Assistant, you don't receive any prompts. This is a different experience from the full version of the Assistant. The following table describes the actions that the Enterprise version of the Assistant takes for each condition that's encountered by this scenario, and the corresponding output that's displayed.

|Condition|Action taken by the Enterprise version|Output shown in the Command Prompt window|
|---|---|---|
|Outlook isn't running|Run an Offline scan of Outlook|01: An Offline scan was performed because Outlook is either not running or it is running elevated (as Administrator). See \<filename\> in *%localappdata%\saralogs\UploadLogs*.|
|Outlook is running|Run a full scan of Outlook|02: A Full scan was performed. See \<filename\> in *%localappdata%\saralogs\UploadLogs*.|
|More than one Outlook version is detected|Run a scan of the latest version of Outlook|(Depending on the situation, this output could be *01*, *02*, *04*, or *05*)|
|Both Outlook and the Assistant's Command Prompt window are elevated|Run a full scan of Outlook|02: A Full scan was performed. See \<filename\> in *%localappdata%\saralogs\UploadLogs*.|
|Outlook is running as elevated but the Assistant's Command Prompt window isn't elevated|Run an offline scan of Outlook|01: An Offline scan was performed because Outlook is either not running or it's running as elevated. See \<filename\> in *%localappdata%\saralogs\UploadLogs*.|
|Outlook isn't running as elevated but the Assistant's Command Prompt window is elevated|None|04: Outlook isn't running elevated. Don't use an elevated command-prompt.|
|Failure to run a scan for reasons such as:<ul><li>Outlook isn't installed.</li><li>The Outlook version detected is older than 2007.</li><li>An exception occurs during the scan.</li></ul>|Scan initiated but not completed|05: An error occurred while performing a scan of Outlook. You might be able to perform an Offline scan if you exit Outlook and rerun this scenario. You can also try using the full SaRA version.|
