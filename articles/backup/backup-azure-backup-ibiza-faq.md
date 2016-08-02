<properties
   pageTitle="Veelgestelde vragen over de openbare evaluatieversie van Azure Backup | Microsoft Azure"
   description="In deze versie van de veelgestelde vragen wordt de openbare evaluatieversie van de Azure Backup-service behandeld. Antwoorden op veelgestelde vragen over Backup-agent, het maken, bewaren, terugzetten en beveiligen van back-ups en andere veelgestelde vragen over Azure Backup."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="backup solution; backup service"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="03/30/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# De openbare evaluatieversie van de Azure Backup-service - Veelgestelde vragen

> [AZURE.SELECTOR]
- [Veelgestelde vragen over Backup voor de klassieke modus](backup-azure-backup-faq.md)
- [Veelgestelde vragen over Backup voor de ARM-modus](backup-azure-backup-ibiza-faq.md)

Dit artikel bevat informatie over de openbare evaluatieversie van de Azure Backup-service. Dit artikel wordt bijgewerkt zodra er meer veelgestelde vragen zijn en vormt een aanvulling op de [Veelgestelde vragen over Azure Backup](backup-azure-backup-faq). Veelgestelde vragen over Azure Backup bevat de volledige serie vragen en antwoorden over de Azure Backup-service.  

U kunt vragen over Azure Backup stellen in de sectie Disqus van dit artikel of in een verwant artikel. U kunt ook in het [discussieforum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) vragen over de Azure Backup-service plaatsen.

## Wat zit er in de openbare evaluatieversie?
De openbare evaluatieversie bevat de Recovery Services-kluis en ondersteuning voor ARM bij het beveiligen van Azure-VM's. De Recovery Services-kluis is een kluis van de volgende generatie. Het is de kluis die wordt gebruikt door de Azure Backup- en de Azure Site Recovery (ASR)-services. U kunt het zien als de v.2-kluis.

## Recovery Services- en Backup-kluizen

**V1. Recovery Services-kluizen zijn v.2, worden Backup-kluizen (v.1) nog steeds ondersteund?** <br/>
A1. Ja, Backup-kluizen worden nog steeds ondersteund. U maakt Backup-kluizen in de klassieke portal. Recovery Services-kluizen worden in Azure Portal gemaakt.

**V2. Kan ik een Backup-kluis migreren naar een Recovery Services-kluis?** <br/>
A2. Op dit moment is het niet mogelijk om de inhoud van een Backup-kluis te migreren naar een Recovery Services-kluis. We werken ernaar toe om deze functionaliteit toe te voegen, maar dit is niet beschikbaar in de openbare evaluatieversie.

**V3. Ondersteunen Recovery Services-kluizen v.1 of v.2 VM's?** <br/>
 A3. Recovery Services-kluizen ondersteunen v.1 en v.2 VM's. U kunt een back-up naar een Recovery Services-kluis maken van een VM die in de klassieke portal is gemaakt (v.1) of van een VM die in Azure Portal is gemaakt (v.2).


## ARM-ondersteuning voor Azure-VM's

**V1. Zijn er beperkingen in de ARM-ondersteuning voor Azure-VM's?** <br/>
A1. De PowerShell-cmdlets voor ARM zijn momenteel niet beschikbaar. U moet de gebruikersinterface van Azure Portal gebruiken om resources aan een resourcegroep toe te voegen.



<!--HONumber=Jun16_HO2-->


