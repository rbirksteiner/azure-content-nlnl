<properties
    pageTitle="Overzicht van Azure Active Directory-apparaatregistratie | Microsoft Azure"
    description="Azure Active Directory-apparaatregistratie vormt de basis voor apparaatgebaseerde scenario's voor voorwaardelijke toegang. Als een apparaat is geregistreerd, richt Azure Active Directory-apparaatregistratie het apparaat in met een identiteit die wordt gebruikt om het apparaat te verifiëren wanneer de gebruiker zich aanmeldt."
    services="active-directory"
    keywords="device registration, enable device registration, device registration and MDM"
    documentationCenter=""
    authors="femila"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="03/29/2016"
    ms.author="femila"/>

# Aan de slag met Azure Active Directory-apparaatregistratie

Azure Active Directory-apparaatregistratie vormt de basis voor apparaatgebaseerde scenario's voor voorwaardelijke toegang. Als een apparaat is geregistreerd, geeft Azure Active Directory-apparaatregistratie het apparaat een identiteit die wordt gebruikt om het apparaat te verifiëren wanneer de gebruiker zich aanmeldt. Het geverifieerde apparaat en de kenmerken van het apparaat kunnen vervolgens worden gebruikt voor het afdwingen van voorwaardelijk toegangsbeleid voor toepassingen die in de cloud en on-premises worden gehost.

In combinatie met een MDM-oplossing (Mobile Device Management) zoals Intune, worden de apparaatkenmerken in Azure Active Directory bijgewerkt met extra informatie over het apparaat. Hiermee kunt u extra regels voor voorwaardelijke toegang maken die toegang afdwingen van apparaten, zodat ze voldoen aan uw standaarden voor beveiliging en compliance

Azure Active Directory-apparaatregistratie is beschikbaar in uw Azure Active-directory. De service biedt ondersteuning voor iOS-, Android- en Windows-apparaten. Voor de afzonderlijke scenario's die gebruikmaken van Azure Active Directory-apparaatregistratie gelden mogelijk meer specifieke vereisten en platformondersteuning.

## Mogelijke scenario's met Azure Active Directory-apparaatregistratie

Azure Active Directory-apparaatregistratie biedt ondersteuning voor iOS-, Android- en Windows-apparaten. Voor de afzonderlijke scenario's die gebruikmaken van Azure AD-apparaatregistratie gelden mogelijk meer specifieke vereisten en platformondersteuning. Het gaat om de volgende scenario's:

- **Voorwaardelijke toegang tot toepassingen die on-premises worden gehost**: u kunt geregistreerde apparaten gebruiken in combinatie met toegangsbeleid voor toepassingen die zijn geconfigureerd voor gebruik met AD FS met Windows Server 2012 R2. Zie [Setting up On-premises Conditional Access using Azure Active Directory Device Registration](active-directory-conditional-access-on-premises-setup.md) (Engelstalig) voor meer informatie over het instellen van on-premises voorwaardelijke toegang.

- **Voorwaardelijke toegang voor Office 365-toepassingen met Microsoft Intune**: IT-beheerders kunnen apparaatbeleid voor voorwaardelijke toegang inrichten ter beveiliging van bedrijfsresources, terwijl ze IT-medewerkers tegelijkertijd toegang geven tot de services via compatibele apparaten. Zie Conditional Access Device Policies for Office 365 services (Engelstalig) voor meer informatie.

##Azure Active Directory-apparaatregistratie configureren

U moet Azure AD-apparaatregistratie inschakelen in de Azure-portal, zodat mobiele apparaten de service kunnen herkennen door te zoeken naar bekende DNS-records. Configureer verder uw bedrijfs-DNS, zodat Windows 10-, Windows 8.1-, Windows 7-, Android- en iOS-apparaten de service kunnen herkennen en gebruiken.
U kunt geregistreerde apparaten bekijken en in- of uitschakelen in de beheerdersportal in Azure Active Directory.

### De service Azure Active Directory-apparaatregistratie inschakelen

1. Meld u bij de Microsoft Azure-portal aan als beheerder.
2. Selecteer in het linkerdeelvenster **Active Directory**.
3. Selecteer uw directory op het tabblad **Directory**.
4. Selecteer de tab **Configureren**.
5. Blader naar de sectie **Apparaten**.
6. Selecteer **ALLE** voor **GEBRUIKERS KUNNEN APPARATEN AAN WERKPLEK TOEVOEGEN**.
7. Selecteer the maximumaantal apparaten dat u per gebruiker wilt autoriseren.

>[AZURE.NOTE]
>Voor de inschrijving bij Microsoft Intune of Mobile Device Management voor Office 365 is Workplace Join vereist. Als u een van deze services hebt geconfigureerd, wordt ALLE geselecteerd en wordt de knop GEEN uitgeschakeld.

Verificatie met twee factoren is standaard niet ingeschakeld voor de service. Deze vorm van verificatie wordt echter wel aanbevolen bij het registreren van een apparaat.

- Voordat u verificatie met twee factoren vereist voor deze service, moet u een provider voor verificatie met twee factoren configureren in Azure Active Directory en uw gebruikersaccounts configureren voor meervoudige verificatie. Zie [Multi-Factor Authentication toevoegen aan Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Als u gebruikmaakt van AD FS met Windows Server 2012 R2, moet u een module voor verificatie met twee factoren configureren in AD FS. Zie [Using Multi-Factor Authentication with Active Directory Federation Services](../multi-factor-authentication/multi-factor-authentication-get-started-server.md) (Engelstalig).

## Herkenning van Azure Active Directory-apparaatregistratie configureren
Apparaten met Windows 7 en Windows 8.1 herkennen de apparaatregistratieservice door de naam van het gebruikersaccount te combineren met een bekende servernaam voor de apparaatregistratie.

U moet een DNS CNAME-record maken die verwijst naar de record A die is gekoppeld aan uw service voor Azure Active Directory-apparaatregistratie. De CNAME-record moet het bekende voorvoegsel 'enterpriseregistration' gebruiken, gevolgd door het UPN-achtervoegsel dat wordt gebruikt voor de gebruikersaccounts in uw organisatie. Gebruikt uw organisatie meerdere UPN-achtervoegsels, dan moeten er meerdere CNAME-records worden gemaakt in DNS.

Als u bijvoorbeeld twee UPN-achtervoegsels gebruikt binnen uw organisatie met de namen @contoso.com en @region.contoso.com, maakt u de volgende DNS-records.

| Vermelding                                     | Type  | Adres                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Apparaatobjecten bekijken en beheren in Azure Active Directory
1. Vanuit de Azure-beheerdersportal kunt u apparaten weergeven en blokkeren, en blokkeringen opheffen. Een geblokkeerd apparaat heeft niet langer toegang tot toepassingen die zijn geconfigureerd om alleen geregistreerde apparaten toe te staan.
2. Meld u bij de Microsoft Azure-portal aan als beheerder.
3. Selecteer in het linkerdeelvenster **Active Directory**.
4. Selecteer uw directory.
5. Selecteer de tab **Gebruikers**. Selecteer vervolgens een gebruiker om zijn of haar apparaten weer te geven.
6. Selecteer het tabblad **Apparaten**.
7. Selecteer **Geregistreerde apparaten** in de vervolgkeuzelijst.
8. Hier kunt u de geregistreerde apparaten van gebruikers bekijken of blokkeren, of blokkeringen ingedaan maken.

## Extra onderwerpen

U kunt uw Windows 7- en Windows 8.1-apparaten die deelnemen aan een domein registreren met Azure AD-apparaatregistratie. Het volgende onderwerp bevat meer informatie over de voorwaarden en de stappen die vereist zijn om apparaatregistratie te configureren op Windows 7- en Windows 8.1-apparaten.

- [Automatic Device Registration with Azure Active Directory for Windows Domain-Joined Devices (Engelstalig)](active-directory-conditional-access-automatic-device-registration.md)
- [Configure automatic device registration for Windows 7 domain joined devices (Engelstalig)](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configure automatic device registration for Windows 8.1 domain joined devices (Engelstalig)](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Automatische apparaatregistratie met Azure Active Directory voor Windows 10-apparaten die aan een domein deelnemen (Engelstalig artikel)](active-directory-azureadjoin-devices-group-policy.md)



<!--HONumber=Jun16_HO2-->


