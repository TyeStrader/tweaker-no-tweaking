# What this 

A place for me to log my thoughts an explore the universe of tweaks, skizo shit and all. This isn't your end-all be-all for tweaks, nothing here is coherent an there will most likely be conflicting settings among files

<details>

<summary>Process Mitigations</summary>

### My findings

> HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Session Manager\kernel

  *MitigationOptions*  
  *MitigationAuditOptions*

Using the mask 222222222222222222222222222222222222222222222222 for MitigationOptions might not actually disable them, using (powershell) 'Get-ProcessMitigation -Name discord.exe -RunningProcess' returned that both CFG and ASLR high/bottom was enabled, with process explorer also confirming this. Instead I took a look at what mask is set when using 'Set-ProcessMitigation -System -Disable  <big block of values from microsoft website>' an it returned 222222222222222220020000002000200000002000000000. After a restart, running the Get-ProcessMitigation command for discord now shows that ASLR and CFG is disabled, with process explorer also confirming. This isn't limited to just discord, I checked steam an a couple games with the same results

Another discovery is that if I used -Force On at the end of 'Set-ProcessMitigation -System -Disable <values>' it instead set the mask as 666666666666666660060000006000600000006000000000, which overrides mitigations completely at a system level but made some apps loop opening an closing child processes causing high cpu usage (discord, steam) The reason I can find for this is because apps override some mitigations for its children, for example a value of 2 signals that it should be disabled, but itll allow it if necessary. A value of 6 completely disallows this, causing those child processes to break
the -Force On syntax is to set the override value when doing 'Get-ProcessMitigation -System'


```Powershell
Set-ProcessMitigation -System -Disable DEP, EmulateAtlThunks, SEHOP, ForceRelocateImages, RequireInfo, BottomUp, HighEntropy, StrictHandle, DisableWin32kSystemCalls, AuditSystemCall, DisableExtensionPoints, BlockDynamicCode, AllowThreadsToOptOut, AuditDynamicCode, CFG, SuppressExports, StrictCFG, MicrosoftSignedOnly, AllowStoreSignedBinaries, AuditMicrosoftSigned, AuditStoreSigned, EnforceModuleDependencySigning, DisableNonSystemFonts, AuditFont, BlockRemoteImageLoads, BlockLowLabelImageLoads, PreferSystem32, AuditRemoteImageLoads, AuditLowLabelImageLoads, AuditPreferSystem32, EnableExportAddressFilter, AuditEnableExportAddressFilter, EnableExportAddressFilterPlus, AuditEnableExportAddressFilterPlus, EnableImportAddressFilter, AuditEnableImportAddressFilter, EnableRopStackPivot, AuditEnableRopStackPivot, EnableRopCallerCheck, AuditEnableRopCallerCheck, EnableRopSimExec, AuditEnableRopSimExec, SEHOP, AuditSEHOP, SEHOPTelemetry, TerminateOnError, DisallowChildProcessCreation, AuditChildProcess, UserShadowStack, AuditUserShadowStack, DisableFsctlSystemCalls
```

</details>



