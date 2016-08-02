<properties
   pageTitle="Verificatie met Amazon Web Services configureren | Microsoft Azure"
   description="In dit artikel wordt beschreven hoe u een AWS-referentie voor runbooks in Azure Automation maakt en valideert bij het beheren van AWS-resources."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="aws authentication, configure aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/10/2016"
   ms.author="magoedte"/>

# Runbooks met Amazon Web Services verifiëren
Het automatiseren van algemene taken met resources in Amazon Web Services (AWS) kan worden bewerkstelligd met Automation-runbooks in Azure.  U kunt vele taken in AWS automatiseren met Automation-runbooks, net zoals u dit kunt met resources in Azure.  U hebt slechts twee dingen nodig:

* Een AWS-abonnement en een set referenties.  Het gaat dan met name om uw AWS-toegangssleutel en de geheime sleutel.  Lees voor meer informatie het artikel [AWS-referenties gebruiken](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Een Azure-abonnement en een Automation-account.  Raadpleeg het artikel [Uitvoeren als-account voor Azure configureren](../automation/automation-sec-configure-azure-runas-account.md) voor meer informatie over het instellen van een Azure Automation-account.  

Als u wilt verifiëren met AWS, moet u een set AWS-referenties opgeven om uw runbooks te verifiëren die vanuit Azure Automation worden uitgevoerd. Als u al een Automation-account hebt gemaakt en u wilt dit account gebruiken om te verifiëren met AWS, kunt u de stappen in de volgende sectie uitvoeren.  Als u een toegewezen account voor runbooks wilt dat is gericht op AWS-resources, moet u eerst een nieuw [Uitvoeren als-account voor Automation](../automation/automation-sec-configure-azure-runas-account) maken (sla de optie voor het maken van een service-principal over). Volg daarna de onderstaande stappen.

## Automation-account configureren
Voor communicatie tussen Azure Automation en AWS moet u eerst uw AWS-referenties ophalen en deze opslaan als assets in Azure Automation.  Voer de volgende stappen uit die in het AWS-document [Toegangssleutels voor uw AWS-account beheren](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) zijn gedocumenteerd om een toegangssleutel te maken en kopieer de **toegangssleutel-id** en **geheime toegangssleutel** (download desgewenst uw sleutelbestand om het ergens veilig op te slaan).

Nadat u uw AWS-beveiligingssleutels hebt gemaakt en gekopieerd, moet u een referentieasset met een Azure Automation-account maken om deze sleutels veilig op te slaan en ernaar te verwijzen met uw runbooks.  Volg de stappen in de sectie **Een nieuwe referentie maken** in het artikel [Referentieassets in Azure Automation](../automation/automation-certificates.md/###To create a new credential with the Azure portal) en voer de volgende gegevens in:

1. Voer in het vak **Naam** **AWScred** in of een geschikte waarde die voldoet aan uw naamgevingsstandaarden.  
2. Typ in het vak **Gebruikersnaam** uw **toegangs-id** en uw **geheime toegangssleutel** in het vak **Wachtwoord** en **Wachtwoord bevestigen**.   

## Volgende stappen

- Lees het oplossingsartikel [Implementatie van een virtuele machine in Amazon Web Services automatiseren](../automation/automation-scenario-aws-deployment.md) voor informatie over het maken van runbooks om taken in AWS te automatiseren.



<!--HONumber=Jun16_HO2-->


