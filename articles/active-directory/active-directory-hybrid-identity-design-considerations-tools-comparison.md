<properties
    pageTitle="Hybride identiteit: vergelijking van hulpprogramma’s voor directory-integratie | Microsoft Azure"
    description="Deze pagina bevat een uitgebreide tabel waarin de verschillende hulpprogramma's voor directory-integratie worden vergeleken."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/12/2016"
    ms.author="billmath"/>

# Vergelijking van hulpprogramma’s voor directory-integratie voor hybride identiteit

In de afgelopen jaren hebben de hulpprogramma's voor directory-integratie zich enorm ontwikkeld.  Dit document geeft een overzicht van deze hulpprogramma's en hierin worden de beschikbare functies in elk hulpprogramma vergeleken.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect bevat de onderdelen en functionaliteit die eerder zijn uitgebracht als Dirsync en AAD Sync. Deze hulpprogramma's worden niet meer afzonderlijk uitgebracht en alle toekomstige verbeteringen worden opgenomen in updates voor Azure AD Connect, zodat u altijd weet waar u de meest recente functionaliteit kunt ophalen.
>
>DirSync en Azure AD Sync zijn afgeschaft. U vindt meer informatie in [Windows Azure Active Directory Sync (DirSync) en Azure Active Directory Sync (Azure AD Sync) upgraden](https://azure.microsoft.com/documentation/articles/active-directory-aadconnect-dirsync-deprecated/?WT.mc_id=DirSyncDepACOM).


Gebruik de volgende sleutel voor elk van de tabellen.

● = Nu beschikbaar  
FR = Toekomstige release  
PP = Openbare preview  

## Synchronisatie van on-premises naar cloud

| Functie  | Azure Active Directory Connect  | Azure Active Directory-synchronisatieservices (AAD Sync) | Hulpprogramma voor Azure Active Directory-synchronisatie (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Verbinding maken met één on-premises AD-forest | ● | ● | ● | ● |● |
| Verbinding maken met meerdere on-premises AD-forests |●  | ● |  | ● |● |
| Verbinding maken met meerdere on-premises Exchange-organisaties | ● |  |  |  | |
| Verbinding maken met één on-premises LDAP-adreslijst | FR |  |  | ● |● |
| Verbinding maken met meerdere on-premises LDAP-adreslijsten |FR  |  |  | ● |● |
| Verbinding maken met on-premises AD- en on-premises LDAP-adreslijsten |FR  |  |  | ● |● |
| Verbinding maken met aangepaste systemen (SQL, Oracle, MySQL enzovoort) | FR |  |  | ● |● |
| Door de klant gedefinieerde kenmerken synchroniseren (directory-extensies) | ● |  |  |  |  |
|Verbinding maken met on-premises HR (SAP, Oracle eBusiness, PeopleSoft)| FR |  |  | ● |● |
|Biedt ondersteuning voor FIM-synchronisatieregels en -connectors voor de inrichting van on-premises systemen.|  |  |  | ● |● |

## Synchronisatie van cloud naar on-premises

| Functie  | Azure Active Directory Connect  | Azure Active Directory-synchronisatieservices | Hulpprogramma voor Azure Active Directory-synchronisatie (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Write-back van apparaten | ● |  | ● |  ||
| Write-back van kenmerken (voor de hybride implementatie voor Exchange) | ● | ● | ● | ● |● |
| Write-back van gebruikers en groepsobjecten |  ●|  | |  ||
| Write-back van wachtwoorden (van de selfservice voor wachtwoordherstel (SSPR) en het wijzigen van wachtwoorden) |  ● | ● |  |  ||



## Ondersteuning van verificatiefuncties

| Functie  | Azure Active Directory Connect | Azure Active Directory-synchronisatieservices | Hulpprogramma voor Azure Active Directory-synchronisatie (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Microsoft Identity Manager 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Wachtwoordsynchronisatie voor één on-premises AD-forest | ● | ● | ● |  ||
| Wachtwoordsynchronisatie voor meerdere on-premises AD-forests |  ●| ● |  |  ||
| Eenmalige aanmelding met federatie | ● | ● | ● | ● |● |
| Write-back van wachtwoorden (van SSPR en het wijzigen van wachtwoorden) |●  | ● |  |  ||



## Instellingen en installatie

| Functie  | Azure Active Directory Connect  | Azure Active Directory-synchronisatieservices | Hulpprogramma voor Azure Active Directory-synchronisatie (DirSync) | Microsoft Identity Manager 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Biedt ondersteuning voor installatie op een domeincontroller | ● | ● | ● |  |
| Biedt ondersteuning voor installatie met SQL Express | ● | ● | ● |  |
| Eenvoudig upgraden van DirSync |● |  |  |  |
| Lokalisatie van beheerder-UX naar Windows Server-talen | ● | ● | ● |  |
|Lokalisatie van eindgebruiker-UX naar Windows Server-talen| |  |  |● |
| Ondersteuning voor Windows Server 2008 en Windows Server 2008 R2 | ● voor synchronisatie, niet voor federatie| ● | ●  | ● |
| Ondersteuning voor Windows Server 2012 en Windows Server 2012 R2 | ● | ● | ● | ● |

## Filteren en configuratie

Functie  | Azure Active Directory Connect | Azure Active Directory-synchronisatieservices | Hulpprogramma voor Azure Active Directory-synchronisatie (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Microsoft Identity Manager 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filteren op domeinen en organisatie-eenheden | ● | ● | ● | ●  | ●
Filteren op kenmerkwaarden van objecten | ● | ● | ● | ●| ●
Toestaan dat minimale set kenmerken worden gesynchroniseerd (MinSync) | ● | ● |  ||
Toestaan dat verschillende servicesjablonen worden toegepast voor kenmerkstromen |●  | ● |  ||
Toestaan dat het verwijderen van kenmerken van AD naar Azure AD stroomt | ● | ● |  |  |
Geavanceerd aanpassen voor kenmerkstromen toestaan | ● | ● |  | ●  | ●

## Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](active-directory-aadconnect.md).



<!--HONumber=Jun16_HO2-->


