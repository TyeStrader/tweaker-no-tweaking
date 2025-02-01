# What this 

A place for me to log my thoughts an explore the universe of tweaks, skizo shit and all. This isn't your end-all be-all for tweaks, nothing here is coherent an there will most likely be conflicting settings among files

## Tools

<p><strong>Custom Resolution Utility 1.5.2</strong><br>
<strong>Scaled Resolution Editor 1.0</strong><br>
<strong>NVCleanstall 1.18.0</strong><br>
<strong>SwitchPowerScheme 1.3</strong><br>

<strong>ProcessExplorer</strong><br>
<strong>NvidiaProfileInspector</strong><br>
<strong>DiscordFixer</strong><br>
<strong>NoSteamWebHelper</strong></p>

## Programs

(Programs may not link to most up to date stuff, check)

<p><strong>7zip</strong> - > https://www.7-zip.org/a/7z2409-x64.exe<br>
<strong>Battle.net</strong> - > https://downloader.battle.net/download/getInstaller?os=win&installer=Battle.net-Setup.exe<br>
<strong>BlackDesert</strong> - > https://naeu-o-dn.playblackdesert.com/UploadData/installer/BlackDesert_Installer_NAEU.exe<br>
<strong>Deluge</strong> - > https://ftp.osuosl.org/pub/deluge/windows/deluge-2.1.1-win64-setup.exe<br>
<strong>Discord</strong> - > https://discord.com/api/downloads/distributions/app/installers/latest?channel=stable&platform=win&arch=x64<br>
<strong>Firefox</strong> - > https://download-installer.cdn.mozilla.net/pub/firefox/releases/134.0.2/win32/en-US/Firefox%20Installer.exe<br>
<strong>Notepad++</strong> - > https://github.com/notepad-plus-plus/notepad-plus-plus/releases/download/v8.7.6/npp.8.7.6.Installer.x64.exe<br>
<strong>Powershell 7</strong> - > https://github.com/PowerShell/PowerShell/releases/download/v7.5.0/PowerShell-7.5.0-win-x64.exe<br>
<strong>System Informer</strong> - > https://sourceforge.net/projects/systeminformer/files/systeminformer-3.2.25011-release-setup.exe/download<br>
<strong>Steam</strong> - > https://cdn.cloudflare.steamstatic.com/client/installer/SteamSetup.exe<br>
<strong>Thunderstore Mod Manager</strong> - > https://download.overwolf.com/install/Download?ExtensionId=ahpflogoookodlegojjphcjpjaejgghjnfcdjdmi&utm_source=web_app_store<br>
<strong>Vencord</strong> - > https://github.com/Vencord/Installer/releases/latest/download/VencordInstaller.exe<br>
<strong>Wootility</strong> - > https://api.wooting.io/public/wootility/download?os=win&version=5.0.0-beta.6</p>

<details open>

<summary>Process Mitigations</summary>

# Research

> HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Session Manager\kernel

  *MitigationOptions*  
  *MitigationAuditOptions*

Using the mask 222222222222222222222222222222222222222222222222 for MitigationOptions might not actually disable them, using (powershell) 'Get-ProcessMitigation -Name discord.exe -RunningProcess' returned that both CFG and ASLR high/bottom was enabled, with process explorer also confirming this. Instead I took a look at what mask is set when using 'Set-ProcessMitigation -System -Disable  <big block of values from microsoft website>' an it returned 222222222222222220020000002000200000002000000000. After a restart, running the Get-ProcessMitigation command for discord now shows that ASLR and CFG is disabled, with process explorer also confirming. This isn't limited to just discord, I checked steam an a couple games with the same results

Another discovery is that if I used -Force On at the end of 'Set-ProcessMitigation -System -Disable <values>' it instead set the mask as 666666666666666660060000006000600000006000000000, which overrides mitigations completely at a system level but made some apps loop opening an closing child processes causing high cpu usage (discord, steam) The reason I can find for this is because apps override some mitigations for its children, for example a value of 2 signals that it should be disabled, but itll allow it if necessary. A value of 6 completely disallows this, causing those child processes to break
the -Force On syntax is to set the override value when doing 'Get-ProcessMitigation -System'


```Powershell
Set-ProcessMitigation -System -Disable DEP, EmulateAtlThunks, SEHOP, ForceRelocateImages, RequireInfo, BottomUp, HighEntropy, StrictHandle, DisableWin32kSystemCalls, AuditSystemCall, DisableExtensionPoints, BlockDynamicCode, AllowThreadsToOptOut, AuditDynamicCode, CFG, SuppressExports, StrictCFG, MicrosoftSignedOnly, AllowStoreSignedBinaries, AuditMicrosoftSigned, AuditStoreSigned, EnforceModuleDependencySigning, DisableNonSystemFonts, AuditFont, BlockRemoteImageLoads, BlockLowLabelImageLoads, PreferSystem32, AuditRemoteImageLoads, AuditLowLabelImageLoads, AuditPreferSystem32, EnableExportAddressFilter, AuditEnableExportAddressFilter, EnableExportAddressFilterPlus, AuditEnableExportAddressFilterPlus, EnableImportAddressFilter, AuditEnableImportAddressFilter, EnableRopStackPivot, AuditEnableRopStackPivot, EnableRopCallerCheck, AuditEnableRopCallerCheck, EnableRopSimExec, AuditEnableRopSimExec, SEHOP, AuditSEHOP, SEHOPTelemetry, TerminateOnError, DisallowChildProcessCreation, AuditChildProcess, UserShadowStack, AuditUserShadowStack, DisableFsctlSystemCalls
```

 <details>
## Get-ProcessMitigations -System
Lets see what output I get.

 **Both MitigationOptions & MitigationAuditOptions keys have been removed beforehand**


```Powershell
PS C:\Program Files\PowerShell\7> Get-ProcessMitigation -System

ProcessName                      : System
Source                           : System Defaults
Id                               : 0

DEP:
    Enable                             : NOTSET
    EmulateAtlThunks                   : NOTSET
    Override DEP                       : False

ASLR:
    BottomUp                           : NOTSET
    Override BottomUp                  : False
    ForceRelocateImages                : NOTSET
    RequireInfo                        : NOTSET
    Override ForceRelocate             : False
    HighEntropy                        : NOTSET
    Override High Entropy              : False

StrictHandle:
    Enable                             : NOTSET
    Override StrictHandle              : False

System Call:
    DisableWin32kSystemCalls           : NOTSET
    Audit                              : NOTSET
    Override SystemCall                : False
    DisableFsctlSystemCalls            : NOTSET
    AuditFsctlSystemCalls              : NOTSET
    Override FsctlSystemCall           : False

ExtensionPoint:
    DisableExtensionPoints             : NOTSET
    Override ExtensionPoint            : False

DynamicCode:
    BlockDynamicCode                   : NOTSET
    AllowThreadsToOptOut               : NOTSET
    Audit                              : NOTSET
    Override DynamicCode               : False

CFG:
    Enable                             : NOTSET
    SuppressExports                    : NOTSET
    Override CFG                       : False
    StrictControlFlowGuard             : NOTSET
    Override StrictCFG                 : False

BinarySignature:
    MicrosoftSignedOnly                : NOTSET
    AllowStoreSignedBinaries           : NOTSET
    EnforceModuleDependencySigning     : NOTSET
    AuditMicrosoftSignedOnly           : NOTSET
    AuditStoreSigned                   : NOTSET
    AuditEnforceModuleDependencySigning: NOTSET
    Override MicrosoftSignedOnly       : False
    Override DependencySigning         : False

FontDisable:
    DisableNonSystemFonts              : NOTSET
    Audit                              : NOTSET
    Override FontDisable               : False

ImageLoad:
    BlockRemoteImageLoads              : NOTSET
    AuditRemoteImageLoads              : NOTSET
    Override BlockRemoteImages         : False
    BlockLowLabelImageLoads            : NOTSET
    AuditLowLabelImageLoads            : NOTSET
    Override BlockLowLabel             : False
    PreferSystem32                     : NOTSET
    AuditPreferSystem32                : NOTSET
    Override PreferSystem32            : False

Payload:
    EnableExportAddressFilter          : NOTSET
    AuditEnableExportAddressFilter     : NOTSET
    Override ExportAddressFilter       : False
    EnableExportAddressFilterPlus      : NOTSET
    AuditEnableExportAddressFilterPlus : NOTSET
    Override ExportAddressFilterPlus   : False
    EAFModules                         : {}
    EnableImportAddressFilter          : NOTSET
    AuditEnableImportAddressFilter     : NOTSET
    Override ImportAddressFilter       : False
    EnableRopStackPivot                : NOTSET
    AuditEnableRopStackPivot           : NOTSET
    Override EnableRopStackPivot       : False
    EnableRopCallerCheck               : NOTSET
    AuditEnableRopCallerCheck          : NOTSET
    Override EnableRopCallerCheck      : False
    EnableRopSimExec                   : NOTSET
    AuditEnableRopSimExec              : NOTSET
    Override EnableRopSimExec          : False

SEHOP:
    Enable                             : NOTSET
    TelemetryOnly                      : NOTSET
    Audit                              : NOTSET
    Override SEHOP                     : False

Heap:
    TerminateOnError                   : NOTSET
    Override HEAP                      : False

Child Process:
    DisallowChildProcessCreation       : NOTSET
    Audit                              : NOTSET
    Override ChildProcess              : False

User Shadow Stack:
    UserShadowStack                    : NOTSET
    UserShadowStackStrictMode          : NOTSET
    AuditUserShadowStack               : NOTSET
    Override UserShadowStack           : False
```
 </details>
</details open>



