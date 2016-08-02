<properties
   pageTitle="Het internetdomein van een bedrijf naar een Traffic Manager-domein laten wijzen | Microsoft Azure"
   description="Aan de hand van dit artikel kunt u een domeinnaam van uw bedrijf laten wijzen naar een Traffic Manager-domeinnaam."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Het internetdomein van een bedrijf naar een Traffic Manager-domein laten wijzen

Als u de domeinnaam van uw bedrijf wilt laten wijzen naar een Traffic Manager-domeinnaam, wijzigt u de DNS-resourcerecord op uw internet DNS-server ,zodat u gebruikmaakt van het CNAME-recordtype. Dit recordtype koppelt de domeinnaam van uw bedrijf aan de domeinnaam van uw Traffic Manager-profiel. U vindt de Traffic Manager-domeinnaam in het gedeelte **Algemeen** van de configuratiepagina voor het Traffic Manager-profiel.

Als u in de Traffic Manager-domeinnaam naar de domeinnaam van uw bedrijf (bijvoorbeeld www.contoso.com) wilt wijzen, moet u de DNS-resourcerecord als volgt bijwerken:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Alle aanvragen voor *www.contoso.com* worden nu doorgestuurd naar *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] U kunt niet naar domeinen op het tweede niveau wijzen, zoals *contoso.com* op het Traffic Manager-domein. Dit is een beperking van het DNS-protocol, dat het gebruik van CNAME-records voor domeinnamen op het tweede niveau niet toestaat.

## Volgende stappen

[Methoden voor het doorsturen van Traffic Manager](traffic-manager-routing-methods.md)

[Traffic Manager - Een profiel uitschakelen, inschakelen of verwijderen](disable-enable-or-delete-a-profile.md)

[Traffic Manager - Een eindpunt in- of uitschakelen](disable-or-enable-an-endpoint.md)



<!--HONumber=Jun16_HO2-->


