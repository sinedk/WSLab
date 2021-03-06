<!-- TOC -->

- [Exploring Windows Server 2019 WORK IN PROGRESS](#exploring-windows-server-2019-work-in-progress)
    - [LabConfig](#labconfig)
    - [Comparing features](#comparing-features)
        - [What's new in Windows Server 2019](#whats-new-in-windows-server-2019)
        - [What's now included in 2019 Core (was in Full only in 2016)](#whats-now-included-in-2019-core-was-in-full-only-in-2016)
        - [What's removed from 2019 (both Full and Core)](#whats-removed-from-2019-both-full-and-core)
        - [What's removed from 2019 core](#whats-removed-from-2019-core)
        - [What's removed from 2019 full](#whats-removed-from-2019-full)
    - [Comparing Services](#comparing-services)
        - [What's new in Windows Server 2019](#whats-new-in-windows-server-2019-1)
        - [What's now included in 2019 Core (was in Full only in 2016)](#whats-now-included-in-2019-core-was-in-full-only-in-2016-1)
        - [What's removed from 2019 (both Full and Core)](#whats-removed-from-2019-both-full-and-core-1)
        - [What's removed from 2019 Core](#whats-removed-from-2019-core)
        - [What's removed from 2019 Full](#whats-removed-from-2019-full)

<!-- /TOC -->

# Exploring Windows Server 2019 WORK IN PROGRESS

Following lab will demonstrate comparing services and features in each installation option and version of Windows Server. Run all code from DC or management machine.

## LabConfig

```PowerShell
$LabConfig=@{ DomainAdminName='LabAdmin'; AdminPassword='LS1setup!'; Prefix = 'WSLab-'; SwitchName = 'LabSwitch'; DCEdition='4' ; Internet=$false ;AdditionalNetworksConfig=@(); VMs=@()}

$LabConfig.VMs += @{ VMName = 'Win2016Core'     ; Configuration = 'Simple'; ParentVHD = 'Win2016Core_G2.vhdx' ; MemoryStartupBytes= 512MB }
$LabConfig.VMs += @{ VMName = 'Win2016Full'     ; Configuration = 'Simple'; ParentVHD = 'Win2016_G2.vhdx'     ; MemoryStartupBytes= 512MB }
$LabConfig.VMs += @{ VMName = 'Win2019Core'     ; Configuration = 'Simple'; ParentVHD = 'Win2019Core_G2.vhdx' ; MemoryStartupBytes= 512MB }
$LabConfig.VMs += @{ VMName = 'Win2019Full'     ; Configuration = 'Simple'; ParentVHD = 'Win2019_G2.vhdx'     ; MemoryStartupBytes= 512MB }
 
```

## Comparing features

```PowerShell
$Features2016Full=Get-WindowsFeature -ComputerName Win2016Full
$Features2016Core=Get-WindowsFeature -ComputerName Win2016Core
$Features2019Full=Get-WindowsFeature -ComputerName Win2019Full
$Features2019Core=Get-WindowsFeature -ComputerName Win2019Core

#What's new in Windows Server 2019 Core compared to 2016 Core
$New2019Core2016Core=Compare-Object -ReferenceObject $Features2016Core.Name -DifferenceObject $Features2019Core.Name | where SideIndicator -eq "=>"
#What was removed from Windows Server 2019 Core compared to 2016 Core
$Removed2019Core2016Core=Compare-Object -ReferenceObject $Features2016Core.Name -DifferenceObject $Features2019Core.Name | where SideIndicator -eq "<="
#What's new in Windows Server 2019 Full compared to 2016 Full
$New2019Full2016Full=Compare-Object -ReferenceObject $Features2016Full.Name -DifferenceObject $Features2019Full.Name | where SideIndicator -eq "=>"
#What was removed from Windows Server 2019 Full compared to 2016 Full
$Removed2019Full2016Full=Compare-Object -ReferenceObject $Features2016Full.Name -DifferenceObject $Features2019Full.Name | where SideIndicator -eq "<="

#What's new in 2019
Compare-Object -DifferenceObject $New2019Core2016Core.InputObject -ReferenceObject $New2019Full2016Full.InputObject -IncludeEqual -ExcludeDifferent

#What is newly included in 2019 Core
Compare-Object -DifferenceObject $New2019Core2016Core.InputObject -ReferenceObject $New2019Full2016Full.InputObject | where SideIndicator -eq "=>"

#What's removed from both core and Full
Compare-Object -DifferenceObject $Removed2019Core2016Core.InputObject -ReferenceObject $Removed2019Full2016Full.InputObject -IncludeEqual -ExcludeDifferent

#What's removed from Core
Compare-Object -DifferenceObject $Removed2019Core2016Core.InputObject -ReferenceObject $Removed2019Full2016Full.InputObject | where SideIndicator -eq "=>"

#What's removed from Full
Compare-Object -DifferenceObject $Removed2019Core2016Core.InputObject -ReferenceObject $Removed2019Full2016Full.InputObject | where SideIndicator -eq "<="

 
```

### What's new in Windows Server 2019

![](/Scenarios/Exploring%20Server%202019/Screenshots/FeaturesNewIn2019.png)

### What's now included in 2019 Core (was in Full only in 2016)

![](/Scenarios/Exploring%20Server%202019/Screenshots/FeaturesNewIn2019Core.png)

### What's removed from 2019 (both Full and Core)

![](/Scenarios/Exploring%20Server%202019/Screenshots/FeaturesRemovedFrom2019.png)

### What's removed from 2019 core

![](/Scenarios/Exploring%20Server%202019/Screenshots/FeaturesRemovedFrom2019core.png)

### What's removed from 2019 full

![](/Scenarios/Exploring%20Server%202019/Screenshots/FeaturesRemovedFrom2019full.png)

## Comparing Services

```PowerShell
$Services2016Full=Invoke-Command -ComputerName Win2016Full -ScriptBlock {get-service}
$Services2016Core=Invoke-Command -ComputerName Win2016Core -ScriptBlock {get-service}
$Services2019Full=Invoke-Command -ComputerName Win2019Full -ScriptBlock {get-service}
$Services2019Core=Invoke-Command -ComputerName Win2019Core -ScriptBlock {get-service}


$New2019Core2016Core=Compare-Object -ReferenceObject $Services2016Core.DisplayName -DifferenceObject $Services2019Core.DisplayName |where SideIndicator -eq "=>"
$Removed2019Core2016Core=Compare-Object -ReferenceObject $Services2016Core.DisplayName -DifferenceObject $Services2019Core.DisplayName |where SideIndicator -eq "<="
$New2019Full2016Full=Compare-Object -ReferenceObject $Services2016Full.DisplayName -DifferenceObject $Services2019Full.DisplayName |where SideIndicator -eq "=>"
$Removed2019Full2016Full=Compare-Object -ReferenceObject $Services2016Full.DisplayName -DifferenceObject $Services2019Full.DisplayName |where SideIndicator -eq "<="

#What's new in 2019
Compare-Object -DifferenceObject $New2019Core2016Core.InputObject -ReferenceObject $New2019Full2016Full.InputObject -IncludeEqual -ExcludeDifferent

#What is newly included in 2019 Core
Compare-Object -DifferenceObject $New2019Core2016Core.InputObject -ReferenceObject $New2019Full2016Full.InputObject  | where SideIndicator -eq "=>"

#What's removed from both core and Full
Compare-Object -DifferenceObject $Removed2019Core2016Core.InputObject -ReferenceObject $Removed2019Full2016Full.InputObject -IncludeEqual -ExcludeDifferent

#What's removed from core
Compare-Object -DifferenceObject $Removed2019Core2016Core.InputObject -ReferenceObject $Removed2019Full2016Full.InputObject | where SideIndicator -eq "=>"

#What's removed from full
Compare-Object -DifferenceObject $Removed2019Core2016Core.InputObject -ReferenceObject $Removed2019Full2016Full.InputObject | where SideIndicator -eq "<="
 
```

### What's new in Windows Server 2019

![](/Scenarios/Exploring%20Server%202019/Screenshots/ServicesNewIn2019.png)

### What's now included in 2019 Core (was in Full only in 2016)

![](/Scenarios/Exploring%20Server%202019/Screenshots/ServicesNewIn2019Core.png)

### What's removed from 2019 (both Full and Core)

![](/Scenarios/Exploring%20Server%202019/Screenshots/ServicesRemovedFrom2019.png)

### What's removed from 2019 Core

![](/Scenarios/Exploring%20Server%202019/Screenshots/ServicesRemovedFrom2019core.png)

### What's removed from 2019 Full

![](/Scenarios/Exploring%20Server%202019/Screenshots/ServicesRemovedFrom2019full.png)
