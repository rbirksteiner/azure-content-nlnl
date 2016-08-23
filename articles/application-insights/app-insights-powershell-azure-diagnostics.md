<properties
    pageTitle="PowerShell gebruiken om diagnostische Azure-gegevens naar Application Insights te verzenden | Microsoft Azure"
    description="Automatiseer het configureren van diagnostische Azure-gegevens, zodat deze worden doorgestuurd naar Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/17/2015"
    ms.author="awills"/>

# PowerShell gebruiken om diagnostische Azure-gegevens naar Application Insights te verzenden

[Microsoft Azure](https://azure.com) kan zo [worden geconfigureerd dat er diagnostische Azure-gegevens worden verzonden](app-insights-azure-diagnostics.md) naar [Visual Studio Application Insights](app-insights-overview.md). De diagnostische gegevens hebben betrekking op Azure Cloud Services en virtuele Azure-machines. Ze vormen een aanvulling op de telemetrie die u vanuit de app verzendt met behulp van de Application Insights-SDK. Als onderdeel van het automatiseringsproces voor het maken van nieuwe resources in Azure kunt u het verzenden van diagnostische gegevens configureren met PowerShell.

## De extensie voor diagnostische gegevens inschakelen als onderdeel van het implementeren van een cloudservice

De cmdlet `New-AzureDeployment` bevat de parameter `ExtensionConfiguration`, die overweg kan met vele configuraties voor diagnostische gegevens. Deze kunnen worden gemaakt met de cmdlet `New-AzureServiceDiagnosticsExtensionConfig`. Bijvoorbeeld:

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## De extensie voor diagnostische gegevens inschakelen voor een bestaande cloudservice

Gebruik `Set-AzureServiceDiagnosticsExtension` voor een bestaande service.

```ps
 
    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## De huidige configuratie van de extensie voor diagnostische gegevens ophalen

```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## De extensie voor diagnostische gegevens verwijderen

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Als u de extensie voor diagnostische gegevens hebt ingeschakeld met `Set-AzureServiceDiagnosticsExtension` of `New-AzureServiceDiagnosticsExtensionConfig`, zonder de parameter Rol te gebruiken, kunt u de extensie verwijderen met `Remove-AzureServiceDiagnosticsExtension`, zonder de parameter Rol. Als de parameter Rol is gebruikt bij het inschakelen van de extensie, moet deze ook worden gebruikt bij het verwijderen hiervan.

De extensie voor diagnostische gegevens verwijderen voor elke afzonderlijke rol:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## Zie ook

* [Azure Cloud Services-apps bewaken met Application Insights](app-insights-cloudservices.md)
* [Diagnostische Azure-gegevens verzenden naar Application Insights](app-insights-azure-diagnostics.md)
* [Het configureren van waarschuwingen automatiseren](app-insights-powershell-alerts.md)




<!--HONumber=Jun16_HO2-->


