<properties
    pageTitle="Uw Azure AD-directory beheren | Microsoft Azure"
    description="Hierin wordt uitgelegd wat een Azure AD-tenant is en hoe u Azure beheert via Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    writer="markvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/07/2016"
    ms.author="markvi"/>

# Uw Azure AD-directory beheren

## Wat is een Azure AD-tenant?

Met betrekking tot fysieke werklocaties kan het woord 'tenant' worden gedefinieerd als een groep die of een bedrijf dat een gebouw gebruikt. Uw organisatie is bijvoorbeeld eigenaar van een kantoorgebouw. Samen met de gebouwen van andere organisaties staat uw gebouw in een straat. Uw organisatie kan worden gezien als de tenant van het gebouw. Het gebouw maakt deel uit van de activa van uw organisatie, biedt uw organisatie onderdak en zorgt ervoor dat u veilig zaken kunt doen. Het gebouw is bovendien gescheiden van de andere bedrijfsgebouwen in uw straat. Er is een duidelijke scheiding tussen uw bedrijfsmiddelen en die van de andere organisaties in de straat.

Met betrekking tot werklocaties in de cloud kan een tenant worden gedefinieerd als een client of organisatie die een bepaald exemplaar van de gebruikte cloudservice in eigendom heeft en beheert. Met betrekking tot het identiteitsplatform dat door Microsoft Azure wordt geleverd, is een tenant het eigen exemplaar van Azure Active Directory (Azure AD) dat uw organisatie na aanmelding voor een Microsoft-cloudservice (zoals Azure of Office 365) krijgt toegewezen en in eigendom neemt.

Elke Azure AD-directory is uniek en werkt afzonderlijk van andere Azure AD-directory’s. Net zoals een kantoorgebouw een beveiligd bedrijfsmiddel is van uw organisatie, is ook een Azure AD-directory ontworpen als een beveiligd bedrijfsmiddel dat alleen door uw organisatie kan worden gebruikt. De Azure AD-architectuur isoleert klant- en identiteitsgegevens, zodat deze niet door elkaar worden gehaald. Dit betekent dat gebruikers en beheerders van een Azure AD-directory niet per ongeluk of opzettelijk gegevens in een andere directory kunnen openen.

![Azure Active Directory beheren][1]

## Hoe krijg ik een Azure AD-directory?

Azure AD levert de kerndirectory en identiteitsbeheermogelijkheden voor de meeste cloudservices van Microsoft, waaronder:

- Azure
- Microsoft Office 365
- Microsoft Dynamics CRM Online
- Microsoft Intune

U krijgt een Azure AD-directory wanneer u zich aanmeldt voor een van deze Microsoft Cloud Services. U kunt naar behoefte aanvullende directory’s maken. Zo kunt u bijvoorbeeld uw eerste directory gebruiken als productiedirectory en nog een tweede directory maken voor testen en fasering.

> [AZURE.NOTE] Nadat u zich voor uw eerste service hebt aangemeld, doet u er verstandig aan het organisatiespecifieke beheerdersaccount dat u hebt gemaakt, ook te gebruiken wanneer u zich aanmeldt voor andere Microsoft-cloudservices.

De eerste keer dat u zich voor een Microsoft-cloudservice aanmeldt, moet u gegevens opgeven over uw organisatie en over de internetdomeinnaam die voor uw organisatie is geregistreerd. Deze gegevens worden vervolgens gebruikt voor het maken van een nieuw exemplaar van de Azure AD-directory voor uw organisatie. Wanneer u zich abonneert op meerdere Microsoft-cloudservices, wordt dezelfde directory gebruikt om aanmeldpogingen te verifiëren.

De extra services maken volledig gebruik van alle bestaande gebruikersaccounts, beleidsregels, instellingen en on-premises directoryintegratie die u configureert. Dit helpt de efficiëntie van de processen tussen de on-premises identiteitsinfrastructuur van uw organisatie en Azure AD te verbeteren.

Als u zich bijvoorbeeld oorspronkelijk had aangemeld voor een Microsoft Intune-abonnement en u de stappen hebt voltooid die nodig zijn om uw on-premises Active Directory verder te integreren in uw Azure AD-directory door directorysynchronisatie en/of servers voor eenmalige aanmelding te implementeren, kunt u zich ook aanmelden voor een andere Microsoft-cloudservice, zoals Office 365. U profiteert dan van dezelfde directoryintegratievoordelen waarvan u al profiteert met Microsoft Intune.

Voor meer informatie over de integratie van uw on-premises directory in Azure AD raadpleegt u [Directoryintegratie](active-directory-aadconnect.md).

### Een Azure AD-directory koppelen aan een nieuw Azure-abonnement

U kunt een nieuw Azure-abonnement koppelen aan dezelfde directory waarmee aanmeldingsbewerkingen voor een bestaand Office 365- of Microsoft Intune-abonnement worden geverifieerd. Meld u met uw werk- of schoolaccount aan bij de Azure-beheerportal. De beheerportal retourneert het bericht dat er geen abonnementen kunnen worden gevonden voor dat account. Selecteer **Aanmelden bij Azure**. Uw directory komt dan voor beheer beschikbaar in de portal. Voor meer informatie raadpleegt u [De directory voor uw Office 365-abonnement in Azure beheren](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure).

Voor een video over veelgestelde vragen over het gebruik van Azure AD raadpleegt u [Azure Active Directory - Veelgestelde vragen over registreren, aanmelden en gebruik](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions).

### Een Azure AD-directory maken door u als organisatie te registreren voor een Microsoft-cloudservice

Als u nog geen abonnement hebt op een Microsoft-cloudservice, gebruikt u een van onderstaande koppelingen om u te registreren. Wanneer u zich registreert voor uw eerste service, wordt er automatisch een Azure AD-directory gemaakt.

- [Microsoft Azure](https://account.windowsazure.com/organization)
- [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
- [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### Een door Azure ingerichte standaarddirectory beheren

Er wordt tegenwoordig automatisch een directory gemaakt wanneer u zich registreert bij Azure. Uw abonnement wordt ook direct aan die directory gekoppeld. Als u zich vóór oktober 2013 bij Azure hebt geregistreerd, is er niet automatisch een directory gemaakt. In dat geval heeft Azure later mogelijk alsnog een standaarddirectory voor uw account ingericht. Uw abonnement is dan gekoppeld aan die standaarddirectory.

Deze standaarddirectory’s zijn in oktober 2013 toegevoegd in het kader van een algemene verbetering van het beveiligingsmodel van Azure. Dankzij deze maatregel kunnen er aan alle Azure-klanten zakelijke identiteitsfuncties worden aangeboden. Bovendien kunnen alle Azure-resources zo alleen worden geopend door gebruikers van een directory. U kunt Azure niet gebruiken zonder directory. Daarom moest er voor elke gebruiker die zich vóór 7 juli 2013 had geregistreerd, maar geen directory had, een directory worden gemaakt. Als u destijds al een directory hebt gemaakt, is uw abonnement aan die directory gekoppeld.

Aan het gebruik van Azure AD zijn geen kosten verbonden. De directory is een gratis resource. U kunt echter ook kiezen voor Azure Active Directory Premium. Deze wordt afzonderlijk gelicentieerd en biedt aanvullende functies, bijvoorbeeld voor het gebruik van een huisstijl en voor het zelfstandig opnieuw instellen van wachtwoorden.

Als u de weergavenaam van uw directory wilt wijzigen, klikt u op de directory in de portal en klikt u op **Configureren**. Zoals verderop in dit onderwerp wordt uitgelegd, kunt u een nieuwe directory toevoegen of een directory die u niet meer nodig hebt, verwijderen. Als u uw abonnement wilt koppelen aan een andere directory, klikt u in de navigatiebalk links op **Instellingen**. Klik vervolgens onder aan de pagina **Abonnementen** op **Directory bewerken**. U kunt ook een aangepast domein maken met een DNS-naam die u hebt geregistreerd, in plaats van het standaarddomein *.onmicrosoft.com te gebruiken. Dit is mogelijk handiger wanneer u een service als SharePoint Online gebruikt.

## Directorygegevens beheren

Als beheerder van één of meer Microsoft-cloudserviceabonnementen kunt de Azure-beheerportal, de Microsoft Intune-accountportal of het Office 365-beheercentrum gebruiken voor het beheren van de directorygegevens van uw organisatie. U kunt ook de [Microsoft Azure Active Directory-module voor Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)-cmdlets downloaden en uitvoeren om de gegevens in Azure AD te beheren.

Via deze portals (en cmdlets) kunt u:

- Accounts voor gebruikers en groepen maken en beheren
- Gerelateerde cloudservices beheren waar uw organisatie op is geabonneerd
- On-premises integratie met uw directoryservice instellen

De Azure-beheerportal, het Office 365-beheercentrum, de Microsoft Intune-accountportal en de Azure AD-cmdlets lezen uit en schrijven naar één gedeeld exemplaar van Azure AD dat is gekoppeld aan de directory van uw organisatie, zoals wordt weergegeven in de volgende afbeelding. Op deze manier fungeren portals (of cmdlets) als front-endinterfaces die uw directorygegevens verzamelen en/of bewerken.

![][2]

Deze accountportals en de bijbehorende Azure AD PowerShell-cmdlets beheerden voorheen de gebruikers. Groepen worden op het Azure AD-platform gebouwd.

Wanneer u via een van de portals (of cmdlets) een wijziging aanbrengt aan de gegevens van uw organisatie terwijl u bent aangemeld voor een van deze services, wordt de wijziging ook weergegeven in de andere portals wanneer u zich de volgende keer aanmeldt voor die service. Dit wordt mogelijk gemaakt doordat de gegevens worden gedeeld tussen de diverse Microsoft-cloudservices waarop u bent geabonneerd.
Als u bijvoorbeeld het Office 365-beheercentrum hebt gebruikt om een gebruiker te blokkeren, kan de gebruiker zich ook niet meer aanmelden bij de andere services waarop uw organisatie is geabonneerd. Opent u het account van de betreffende gebruiker via de Microsoft Intune-accountportal, dan zult u ook hier zien dat de gebruiker is geblokkeerd.

## Hoe kan ik meerdere directory’s toevoegen en beheren?

U kunt een Azure AD-directory toevoegen via de Azure-beheerportal. Selecteer aan de linkerkant de **Active Directory**-extensie en klik op **Toevoegen**.

U kunt elke directory als volledig onafhankelijke resource beheren: elke directory is een peer, volledig uitgerust en logisch onafhankelijk van andere directory’s die u beheert. Er is geen sprake van een structuur met boven- en onderliggende directory’s. Onder deze onafhankelijkheid van uw directory’s vallen ook resourceonafhankelijkheid, beheeronafhankelijkheid en synchronisatieonafhankelijkheid.

- **Resourceonafhankelijkheid**. Wanneer u een resource maakt in of verwijdert uit de ene directory, heeft dat geen gevolgen voor de resources in andere directory’s. Externe gebruikers vormen hier een gedeeltelijke uitzondering op, zoals verderop wordt beschreven. Als u voor een bepaalde directory het aangepaste domein contoso.com gebruikt, kan dit domein niet meer voor andere directory’s worden gebruikt.
- **Beheeronafhankelijkheid**.  Als een gebruiker (geen beheerder) van de directory Contoso de testdirectory Test maakt:
    - ◦ Het hulpprogramma voor directorysynchronisatie synchroniseert de gegevens met één AD-forest.
    - ◦ De beheerders van de directory Contoso hebben geen directe beheerdersrechten voor de directory Test, tenzij de beheerder van Test hun deze rechten verleent. Beheerders van Contoso kunnen de toegang tot de directory Test beheren doordat ze het gebruikersaccount beheren waarmee Test is gemaakt.

    Wanneer u in één directory de beheerdersrol van een gebruiker wijzigt (toevoegt of verwijdert), heeft deze wijziging geen gevolgen voor de beheerdersrollen die de betreffende gebruiker mogelijk heeft in andere directory’s.


- **Synchronisatieonafhankelijkheid**. U kunt elke Azure AD onafhankelijk zodanig configureren dat de gegevens worden gesynchroniseerd vanuit één exemplaar van een van de volgende opties:
    - Het hulpprogramma voor directorysynchronisatie, om de gegevens met één AD-forest te synchroniseren
    - De Azure Active Directory-connector voor Forefront Identity Manager, om gegevens met één of meer on-premises forests en/of niet-AD-gegevensbronnen te synchroniseren.

In tegenstelling tot hoe dit bij andere Azure-resources werkt, zijn uw directory’s geen onderliggende resources van een Azure-abonnement. Wanneer u uw Azure-abonnement annuleert of als het abonnement verloopt, hebt u nog gewoon toegang tot uw directorygegevens via Azure AD PowerShell, de Azure Graph API of andere interfaces, zoals het Office 365-beheercentrum. U kunt ook een ander abonnement koppelen aan de directory.

## Hoe verwijder ik een Azure AD-directory?
Een hoofdbeheerder kan een Azure AD-directory verwijderen via de portal. Wanneer een directory wordt verwijderd, worden ook alle resources in de directory verwijderd. Daarom moet u er vóór het verwijderen zeker van zijn dat u de directory niet nodig hebt.

> [AZURE.NOTE]
> Een gebruiker die is aangemeld met een werk- of schoolaccount, kan zijn of haar basisdirectory niet verwijderen. Als een gebruiker bijvoorbeeld is aangemeld als joe@contoso.onmicrosoft.com, kan hij of zij niet de directory verwijderen die contoso.onmicrosoft.com gebruikt als standaarddomein.

### Voorwaarden voor het verwijderen van een Azure AD-directory

Als u een Azure AD-directory wilt verwijderen, moet aan bepaalde voorwaarden worden voldaan. Dit vermindert het risico dat het verwijderen van een directory een negatieve invloed heeft op gebruikers of toepassingen, zoals op de mogelijkheid van gebruikers om zich aan te melden bij Office 365 of om resources te openen in Azure. Het per ongeluk verwijderen van een directory voor een abonnement leidt er bijvoorbeeld toe dat gebruikers geen toegang meer hebben tot de Azure-resources voor dat abonnement.

Er wordt gecontroleerd of aan de volgende voorwaarden is voldaan:

- De enige gebruiker in de directory is de hoofdbeheerder die de directory gaat verwijderen. Andere gebruikers moeten worden verwijderd voordat de directory kan worden verwijderd. Als gebruikers on-premises worden gesynchroniseerd, moet de synchronisatie worden uitgeschakeld. Gebruikers moeten vervolgens worden verwijderd uit de clouddirectory via de Beheerportal of de Azure-module voor Windows PowerShell. Verwijdering van groepen of contactpersonen, zoals contactpersonen die vanuit het Office 365-beheercentrum zijn toegevoegd, is geen vereiste.
- Er mogen in de directory geen toepassingen aanwezig zijn. Alle toepassingen moeten worden verwijderd om de directory te kunnen verwijderen.
- Aan de directory mogen geen abonnementen op Microsoft Online Services zijn gekoppeld, zoals een abonnement op Microsoft Azure, Office 365 of Azure AD Premium. Als er bijvoorbeeld een standaarddirectory voor u is gemaakt in Azure, kunt u deze directory niet verwijderen als uw Azure-abonnement deze directory gebruikt voor verificatie. Het is evenmin mogelijk om een directory te verwijderen als een andere gebruiker er een abonnement aan heeft gekoppeld. Als u uw abonnement wilt koppelen aan een andere directory, meldt u zich aan bij de Azure-beheerportal en klikt u in de navigatiebalk aan de linkerkant op **Instellingen**. Klik vervolgens onder aan de pagina **Abonnementen** op **Directory bewerken**. Voor meer informatie over Azure-abonnementen raadpleegt u [Hoe Azure-abonnementen worden gekoppeld aan Azure AD](active-directory-how-subscriptions-associated-directory.md).

    > [AZURE.NOTE]
    > Een gebruiker die is aangemeld met een werk- of schoolaccount, kan zijn of haar basisdirectory niet verwijderen. Als een gebruiker bijvoorbeeld is aangemeld als joe@contoso.onmicrosoft.com, kan hij of zij niet de directory verwijderen die contoso.onmicrosoft.com gebruikt als standaarddomein.

- Aan de directory kunnen geen Multi-Factor Authentication-providers worden gekoppeld.


## Aanvullende bronnen

- [Azure AD-forum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Azure Multi-Factor Authentication-forum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
- [StackOverflow](http://stackoverflow.com/questions/tagged/azure)
- [Als organisatie registreren voor Azure](sign-up-organization.md)
- [Azure AD beheren met Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
- [Beheerdersrollen toewijzen in Azure AD](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png



<!--HONumber=Jun16_HO2-->


