<properties 
    pageTitle="Aan de slag met Azure Multi-Factor Authentication en Active Directory Federation Services" 
    description="Dit is de Azure Multi-Factor Authentication-pagina waarop wordt beschreven hoe u met Azure MFA en AD FS aan de slag kunt gaan." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Aan de slag met Azure Multi-Factor Authentication en Active Directory Federation Services



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Als uw organisatie met behulp van AD FS uw on-premises Active Directory heeft gefedereerd met Azure Active Directory, zijn de volgende twee opties beschikbaar voor het gebruik van Azure Multi-Factor Authentication.

- Cloudresources beveiligen met Azure Multi-Factor Authentication of Active Directory Federation Services 
- Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server 

De volgende tabel bevat een overzicht van de verschillen tussen de verificatie-ervaring bij het beveiligen van resources met Azure Multi-Factor Authentication en AD FS

|Verificatie-ervaring - op browser gebaseerde apps|Verificatie-ervaring - niet op browser gebaseerde apps
:------------- | :------------- | :------------- |
Beveiligen van Azure AD-resources met Azure Multi-Factor Authentication |<li>De eerste factor van de verificatie wordt on-premises uitgevoerd met AD FS.</li> <li>De tweede factor is een telefonische methode die met behulp van cloudverificatie wordt uitgevoerd.</li>|Eindgebruikers kunnen app-wachtwoorden gebruiken om zich aan te melden.
Azure AD-resources beveiligen met behulp van Active Directory Federation Services |<li>De eerste factor van de verificatie wordt on-premises uitgevoerd met AD FS.</li><li>De tweede factor wordt on-premises uitgevoerd door de claim toe te kennen.</li>|Eindgebruikers kunnen app-wachtwoorden gebruiken om zich aan te melden.

Valkuilen met app-wachtwoorden voor federatieve gebruikers: 

- App-wachtwoord wordt gecontroleerd met cloudverificatie en daarom worden federaties omzeild. Federatie wordt alleen actief gebruikt bij het instellen van het app-wachtwoord.
- On-premises instellingen voor toegangsbeheer van client worden niet herkend door het app-wachtwoord.
- U raakt on-premises mogelijkheden voor logboekregistratie bij verificatie voor het app-wachtwoord kwijt.
- Account uitschakelen/verwijderen kan met DirSync tot 3 uur duren, waardoor uitschakelen/verwijderen van app-wachtwoord in de cloudidentiteit wordt vertraagd.

Voor meer informatie over het instellen van Azure Multi-Factor Authentication of van de Azure Multi-Factor Authentication-server met AD FS, zie:

- [Cloudresources beveiligen met behulp van Azure Multi-Factor Authentication en AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 


<!--HONumber=Jun16_HO2-->


