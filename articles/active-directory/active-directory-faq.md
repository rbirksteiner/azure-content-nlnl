<properties
    pageTitle="Veelgestelde vragen over Azure Active Directory | Microsoft Azure"
    description="Veelgestelde vragen over Azure Active Directory bevat antwoorden op vragen, samen met informatie over het openen van Azure en Azure Active Directory, wachtwoordbeheer en het openen van toepassingen."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/20/2016"
    ms.author="markusvi"/>

# Veelgestelde vragen over Azure Active Directory

Azure Active Directory is een uitgebreide IDaaS-oplossing (Identity as a Service) waarin alle aspecten van identiteit, toegangsbeheer en beveiliging zijn opgenomen.


Zie [What is Azure Active Directory?](active-directory-whatis.md) (Wat is Azure Active Directory?) voor meer informatie.



## Azure en Azure Active Directory openen


**V: waarom wordt 'Geen abonnementen gevonden' weergegeven wanneer ik Azure AD wil openen in de klassieke Azure-portal (https://manage.windowsazure.com)?**

**A:** Voor toegang tot de klassieke Azure-portal moet elke gebruiker machtigingen hebben voor een Azure-abonnement. Als u een betaald Office 365- of Azure AD-abonnement hebt, gaat u naar  [http://aka.ms/accessAAD](http://aka.ms/accessAAD) voor een eenmalige activeringsstap, anders moet u een volledige [proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) of een betaald abonnement activeren. 

Zie voor meer informatie:

- [How Azure subscriptions are associated with Azure Active Directory (Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory)](active-directory-how-subscriptions-associated-directory.md)

- [Manage the directory for your Office 365 subscription in Azure (De map voor uw Office 365-abonnement in Azure beheren)](active-directory-manage-o365-subscription.md)

---

**V: wat is de relatie tussen Azure AD, Office 365 en Azure?**

**A:** Azure Active Directory biedt u een algemene identiteit en toegang tot alle Microsoft Online Services. Ongeacht of u Office 365, Microsoft Azure, Intune of andere toepassingen gebruikt, gebruikt u al Azure AD bij het aanmelden en het toegangsbeheer voor al deze services. 

Alle gebruikers die u hebt ingesteld voor Microsoft Online Services, zijn gedefinieerd als gebruikersaccounts in een of meer exemplaren van Azure AD. U kunt deze accounts instellen voor gratis Azure AD-mogelijkheden, zoals toegang tot cloudtoepassingen.
 
Daarnaast vormen betaalde Azure AD-services (bijvoorbeeld: Azure AD Basic, Premium, EMS enzovoort) een aanvulling op andere Online Services, zoals Office 365 en Microsoft Azure, met uitgebreide oplossingen voor zakelijk schaalbeheer en beveiliging.


---



## Aan de slag met hybride Azure AD


**V: hoe kan ik mijn on-premises directory verbinden met Azure AD?**

**A:** u kunt uw on-premises directory verbinden met Azure AD via **Azure AD Connect**. 

Zie [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md) (Uw on-premises identiteiten integreren met Azure Active Directory) voor meer informatie.


---

**V: hoe stel ik eenmalige aanmelding in tussen mijn on-premises directory en mijn cloudtoepassingen?**

**A:** U hoeft eenmalige aanmelding alleen in te stellen tussen uw on-premises directory en Azure AD. Zo lang u uw cloudtoepassingen opent via Azure AD, wordt er met de service automatisch voor gezorgd dat uw gebruikers juist worden geverifieerd met hun on-premises referenties.

U kunt eenmalige aanmelding eenvoudig on-premises implementeren met federatieoplossingen, zoals ADFS, of door wachtwoordhashsynchronisatie te configureren. U kunt beide opties eenvoudig implementeren met de wizard Azure AD Connect-configuratie.
  

Zie [Integrating your on-premises identities with Azure Active Directory](active-directory-aadconnect.md) (Uw on-premises identiteiten integreren met Azure Active Directory) voor meer informatie.
  

---

**V: bevat Azure Active Directory een selfserviceportal voor gebruikers in mijn organisatie?**

**A:** Ja, Azure Active Directory bevat het deelvenster [Azure AD Access](http://myapps.microsoft.com) voor selfservice voor gebruikers en toegang tot toepassingen. Als u een Office 365-klant bent, vindt u veel van dezelfde mogelijkheden in de Office 365-portal. 

Zie [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie. 



---

**V: helpt Azure AD mij bij het beheren van mijn on-premises infrastructuur?**

**A:** ja. **Connect Health** is opgenomen in de Azure AD Premium-versie. Azure AD Connect Health helpt u om inzicht te verkrijgen in uw on-premises infrastructuur voor identiteiten en de synchronisatieservices.  

Zie [Monitor your on-premises identity infrastructure and synchronization services in the cloud](active-directory-aadconnect-health.md) (Uw on-premises infrastructuur voor identiteiten en synchronisatieservices in de cloud controleren) voor meer informatie.  

---

## Wachtwoordbeheer

**V: Kan ik Terugschrijven van wachtwoord van Azure AD gebruiken zonder wachtwoordsynchronisatie? (Ik wil Azure AD SSPR gebruiken met Terugschrijven van wachtwoord, maar ik wil niet dat mijn wachtwoorden worden opgeslagen in de cloud.)**

**A:** U hoeft uw AD-wachtwoorden niet te synchroniseren naar Azure AD om terugschrijven in te schakelen. In een federatieve omgeving is eenmalige aanmelding van Azure AD afhankelijk van de on-premises directory voor het verifiëren van de gebruiker. In dit geval is er geen on-premises wachtwoord vereist dat moet worden bijgehouden in Azure AD.

---

**V: hoe lang duurt het voordat een wachtwoord on-premises is teruggeschreven naar AD?**

**A:** het terugschrijven van het wachtwoord wordt in realtime uitgevoerd. 

Zie [Getting started with Password Management](active-directory-passwords-getting-started.md) (Aan de slag met wachtwoordbeheer) voor meer informatie. 


---

**V: kan ik Terugschrijven van wachtwoord gebruiken met wachtwoorden die worden beheerd door een beheerder?**

**A:** Ja, als u Terugschrijven van wachtwoord hebt ingeschakeld, worden de wachtwoordbewerkingen van een beheerder teruggeschreven naar uw on-premises omgeving.  

Zie [Password Management Frequently Asked Questions](active-directory-passwords-faq.md) (Veelgestelde vragen over wachtwoordbeheer) voor meer antwoorden op vragen over wachtwoorden.

---

## Toegang tot toepassingen


**V: waar vind ik een lijst met toepassingen die vooraf zijn geïntegreerd met Azure AD en de bijbehorende mogelijkheden?**

**A:** Azure AD heeft meer dan 2600 vooraf geïntegreerde toepassingen van Microsoft, toepassingsserviceproviders of partners. Alle vooraf geïntegreerde toepassingen bieden ondersteuning voor eenmalige aanmelding. Via eenmalige aanmelding kunt u uw referenties voor uw organisatie gebruiken om toegang te krijgen tot uw apps. Een aantal toepassingen ondersteunt ook geautomatiseerde inrichting en het  ongedaan maken van de inrichting.

Zie de [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/) voor een volledige lijst met vooraf geïntegreerde toepassingen.


---

**V: wat moet ik doen als de benodigde toepassing niet beschikbaar in de Azure AD Marketplace?**

**A:** met Azure AD Premium kunt u elke gewenste toepassing toevoegen en configureren. U kunt eenmalige aanmelding en geautomatiseerde inrichting configureren, afhankelijk van de mogelijkheden van uw toepassing en uw voorkeuren.  

Zie voor meer informatie:

- [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)](active-directory-saas-custom-apps.md)
- [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)](active-directory-scim-provisioning.md) 


---

**V: hoe melden gebruikers zich aan bij toepassingen met Azure Active Directory?**
 
**A:** In Azure Active Directory kunnen gebruikers op verschillende manieren toepassingen weergeven en openen, zoals:

- Het deelvenster Azure AD Access

- Het startprogramma voor toepassingen van Office 365

- Directe aanmelding bij federatieve apps

- Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Zie [Deploying Azure AD integrated applications to users)](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) (Geïntegreerde Azure AD-toepassingen implementeren voor gebruikers) voor meer informatie.


---

**V: wat zijn de verschillende manieren waarop verificatie en eenmalige aanmelding kunnen worden ingesteld voor toepassingen met Azure Active Directory?**
 
**A:** Azure Active Directory biedt ondersteuning voor een groot aantal gestandaardiseerde protocollen voor verificatie en autorisatie, zoals SAML 2.0, OpenID Connect, OAuth 2.0 en WS-Federation. Daarnaast ondersteunt Azure AD het gebruik van wachtwoordkluizen en mogelijkheden voor automatische aanmelding voor apps die alleen ondersteuning bieden voor verificatie op basis van formulieren.  

Zie voor meer informatie:

- [Authentication Scenarios for Azure AD (Verificatiescenario's voor Azure AD)](active-directory-authentication-scenarios.md)

- [Active Directory Authentication Protocols (Active Directory-verificatieprotocollen)](https://msdn.microsoft.com/library/azure/dn151124.aspx)

- [How does single sign-on with Azure Active Directory work? (Hoe werkt eenmalige aanmelding met Azure Active Directory?)](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)


---

**V: kan ik toepassingen toevoegen die ik on-premises uitvoer?**

**A:** Via de Azure AD-toepassingsproxy hebt u eenvoudig en veilig toegang tot on-premises webtoepassingen die u kiest. U kunt deze toepassingen op dezelfde manier openen als uw SaaS-apps in Azure Active Directory. U hebt geen VPN nodig en u hoeft uw netwerkinfrastructuur niet te wijzigen.  

Zie [How to provide secure remote access to on-premises applications](active-directory-application-proxy-get-started.md) (Beveiligde externe toegang bieden voor on-premises toepassingen) voor meer informatie.


--- 

**V: op welke manier heb ik MFA nodig voor gebruikers die toegang willen hebben tot een bepaalde toepassing?**

**A:** Met voorwaardelijke toegang van Azure AD kunt u een uniek toegangsbeleid toewijzen aan elke toepassing. In uw beleid kunt u aangeven dat MFA altijd is vereist of alleen wanneer gebruikers niet zijn verbonden met het lokale netwerk.  

Zie [Securing access to Office 365 and other apps connected to Azure Active Directory](active-directory-conditional-access.md) (De toegang beveiligen tot Office 365 en andere apps die zijn verbonden met Azure Active Directory) voor meer informatie.


---

**V: wat is geautomatiseerde gebruikersinrichting voor SaaS-apps?**

**A:** Met Azure Active Directory kunt u het maken, onderhouden en verwijderen van gebruikers-id's in veel populaire cloudtoepassingen (SaaS) automatiseren. 

Zie [Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](active-directory-saas-app-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory) voor meer informatie.

---






<!--HONumber=Jun16_HO2-->


