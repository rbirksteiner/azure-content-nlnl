<properties
    pageTitle="Azure AD Domain Services: wachtwoordsynchronisatie inschakelen | Microsoft Azure"
    description="Aan de slag met Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD Domain Services *(Preview)* - wachtwoordsynchronisatie met Azure AD Domain Services inschakelen

## Taak 5: wachtwoordsynchronisatie met AAD Domain Services inschakelen voor een gesynchroniseerde Azure AD-directory
Wanneer u Azure AD Domain Services hebt ingeschakeld voor uw Azure AD-directory, bestaat de volgende taak uit het inschakelen van de synchronisatie van wachtwoorden met Azure AD Domain Services. Hierdoor kunnen gebruikers zich aanmelden bij het domein met hun bedrijfsreferenties.

De vereiste stappen verschillen, afhankelijk van of uw organisatie een Azure AD-directory in de cloud heeft of kan synchroniseren met uw on-premises directory via Azure AD Connect.

<br>

> [AZURE.SELECTOR]
- [Azure AD-directory in de cloud](active-directory-ds-getting-started-password-sync.md)
- [Gesynchroniseerde Azure AD-directory](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>

### Gesynchroniseerde tenants - synchronisatie van referentie-hashes van NTLM en Kerberos naar Azure AD inschakelen
Als de Azure AD-tenant voor uw organisatie is ingesteld op synchronisatie met uw directory via Azure AD Connect, moet u Azure AD Connect configureren om de referentie-hashes te synchroniseren die zijn vereist voor NTLM en Kerberos-verificatie. Deze hashes worden standaard niet naar Azure AD gesynchroniseerd. Met de volgende stappen kunt u de synchronisatie van de hashes naar uw Azure AD-tenant inschakelen.

#### Azure AD Connect installeren of bijwerken

U moet de meest recente, aanbevolen versie van Azure AD Connect installeren op een computer die lid is van het domein. Als u een bestaand exemplaar van de Azure AD Connect-installatie hebt, moet u deze bijwerken om de Azure AD Connect GA-build te gebruiken. Zorg ervoor dat u over de nieuwste versie van Azure AD Connect beschikt om te voorkomen dat bekende problemen/fouten optreden die mogelijk al zijn opgelost.

**[Azure AD Connect downloaden](http://www.microsoft.com/download/details.aspx?id=47594)**

Aanbevolen versie: **1.1.130.0** - gepubliceerd op 12 April 2016.

  > [AZURE.WARNING] U MOET de meest recente, aanbevolen versie van Azure AD Connect installeren om ervoor te zorgen dat oudere wachtwoordreferenties (die zijn vereist voor NTLM- en Kerberos-verificatie) kunnen worden gesynchroniseerd met uw Azure AD-tenant. Deze functionaliteit is niet beschikbaar in eerdere versies van Azure AD Connect of in het oudere DirSync-hulpprogramma.

Installatie-instructies voor Azure AD Connect zijn beschikbaar in het artikel [Getting started with Azure AD Connect](../active-directory/active-directory-aadconnect.md) (Aan de slag met Azure AD Connect).


#### Volledige wachtwoordsynchronisatie met Azure AD afdwingen

Voer het volgende PowerShell-script uit in elk AD-forest als u volledige wachtwoordsynchronisatie wilt afdwingen en ervoor wilt zorgen dat de wachtwoord-hashes van alle on-premises gebruikers (inclusief de referentie-hashes voor NTLM- of Kerberos-verificatie) kunnen worden gesynchroniseerd met uw Azure AD-tenant.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Afhankelijk van de grootte van de directory (aantal gebruikers, groepen enzovoort), kan de synchronisatie van referenties naar Azure AD enige tijd duren. De wachtwoorden kunnen in het beheerde domein van Azure AD Domain Services worden gebruikt kort nadat de referentie-hashes zijn gesynchroniseerd naar Azure AD.


<br>

## Gerelateerde inhoud

- [Wachtwoordsynchronisatie met AAD Domain Services inschakelen voor een Azure AD-directory die zich alleen in de cloud bevindt](active-directory-ds-getting-started-password-sync.md)

- [Een beheerd domein van Azure AD Domain Services beheren](active-directory-ds-admin-guide-administer-domain.md)

- [Een virtuele Windows-computer toevoegen aan een beheerd domein van Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)

- [Een virtuele Red Hat Enterprise Linux-computer toevoegen aan een beheerd domein van Azure AD Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)



<!--HONumber=Jun16_HO2-->


