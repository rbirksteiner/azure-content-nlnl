<properties
   pageTitle="Uw ontwikkelomgeving instellen | Microsoft Azure"
   description="Installeer de runtime, SDK en hulpprogramma's en maak een lokaal ontwikkelcluster. Zodra u dit hebt gedaan, kunt u toepassingen bouwen."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="ryanwi"/>

# Uw ontwikkelomgeving voorbereiden
 Als u [Azure Service Fabric-toepassingen][1] op uw ontwikkelmachine wilt bouwen en uitvoeren, moet u de runtime, de SDK en de hulpprogramma's installeren. U moet er ook voor zorgen dat de Windows PowerShell-scripts die in de SDK zijn opgenomen, kunnen worden uitgevoerd.

## Vereisten
### Ondersteunde versies van besturingssystemen
De volgende versies van besturingssystemen worden ondersteund voor de ontwikkeling:

- Windows 7
- Windows 8/Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Windows 7 bevat standaard alleen Windows PowerShell 2.0. U moet PowerShell 3.0 of hoger installeren om Service Fabric PowerShell-cmdlets te kunnen gebruiken. U kunt [Windows PowerShell 5.0 downloaden][powershell5-download] via het Microsoft Downloadcentrum.

## De runtime, SDK en hulpprogramma's installeren

Het webplatforminstallatieprogramma biedt drie configuraties voor Service Fabric-ontwikkeling:

- [Installeer de Service Fabric-runtime, -SDK en de hulpprogramma's voor Visual Studio 2015][full-bundle-vs2015]
- [Installeer de Service Fabric-runtime, -SDK en de hulpprogramma's voor Visual Studio "15" Preview][full-bundle-dev15]
- [Installeer alle de Service Fabric-runtime en -SDK (geen hulpprogramma's voor Visual Studio tools)][core-sdk]


## Uitvoering van PowerShell-script inschakelen

Service Fabric gebruikt Windows PowerShell-scripts om een lokaal ontwikkelcluster te maken en om toepassingen vanuit Visual Studio te implementeren. De uitvoering van deze scripts wordt standaard door Windows geblokkeerd. Als u ze wilt inschakelen, moet u het PowerShell-uitvoeringsbeleid wijzigen. Open PowerShell als een beheerder en voer de volgende opdracht in:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## Volgende stappen
Nu uw ontwikkelomgeving is ingesteld, kunt u apps ontwikkelen en uitvoeren van.

- [Uw eerste Service Fabric-toepassing in Visual Studio maken](service-fabric-create-your-first-application-in-visual-studio.md)
- [Meer informatie over het implementeren en beheren van toepassingen op uw lokale cluster](service-fabric-get-started-with-a-local-cluster.md)
- [Meer informatie over de programmeermodellen: Reliable Services en Reliable Actors](service-fabric-choose-framework.md)
- [Voorbeelden van Service Fabric-code op GitHub bekijken](https://aka.ms/servicefabricsamples)
- [Uw cluster visualiseren door gebruik te maken van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
- [Volg het leertraject voor Service Fabric voor een brede inleiding tot het platform](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Service Fabric-campagnepagina"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-koppeling"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-koppeling"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=ServiceFabricSDK "Core SDK WebPI-koppeling"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395



<!--HONumber=Jun16_HO2-->


