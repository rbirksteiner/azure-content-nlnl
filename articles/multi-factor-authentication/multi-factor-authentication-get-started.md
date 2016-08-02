<properties 
    pageTitle="Azure Multi-Factor Authentication - Aan de slag" 
    description="Kies de Multi-Factor Authentication-beveiligingsoplossing die bij u past door uzelf de vraag te stellen: 'Wat wil ik beveiligen en waar bevinden zich mijn gebruikers?'.  Kies vervolgens de cloud, MFA-Server of AD FS." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

#De Multi-Factor-beveiligingsoplossing kiezen die bij u past

Omdat er verschillende versies van Azure Multi-Factor Authentication zijn, moet er een aantal dingen worden bepaald om te kunnen beoordelen welke versie voor u de beste is.  Die dingen zijn:

-   [Wat wil ik beveiligen?](#what-am-i-trying-to-secure)
-   [Waar bevinden de gebruikers zich?](#where-are-the-users-located)

In de volgende secties vindt u richtlijnen die u helpen om elk van deze twee vragen te beantwoorden.

## Wat wil ik beveiligen?

Om te bepalen welke Multi-Factor Authentication-oplossing de juiste is, moet eerst de vraag worden beantwoord wat u wilt beveiligen met een tweede verificatiemethode.  Is het een toepassing die deel uitmaakt van Azure?  Of is het bijvoorbeeld een systeem voor externe toegang?  Door te bepalen wat we willen beveiligen, kan vraag worden beantwoord waar Multi-Factor Authentication moet worden ingeschakeld.  


Wat wilt u beveiligen| Multi-factor Authentication in de cloud|Multi-Factor Authentication-server 
------------- | :-------------: | :-------------: |
Eigen Microsoft-apps|* |* |
SaaS-apps in de app-galerie|* |* |
IIS-toepassingen die zijn gepubliceerd via toepassingsproxy van Azure AD|* |* |
IIS-toepassingen die niet zijn gepubliceerd via toepassingsproxy van Azure AD | |* |
Externe toegang zoals VPN, RDG| |* |



## Waar bevinden de gebruikers zich?

Afhankelijk van waar onze gebruikers zich bevinden, kunnen we bepalen wat de juiste oplossing is, of dat Multi-Factor Authentication in de cloud is of on-premises via de MFA Server.



Locatie van gebruikers| Oplossing
------------- | :------------- | 
Azure Active Directory| Multi-factor Authentication in de cloud|
Azure AD en on-premises AD dat gebruikmaakt van federatie met AD FS| Zowel MFA in de cloud als MFA Server zijn beschikbare opties 
Azure AD en on-premises AD dat gebruikmaakt van DirSync, Azure AD Sync, Azure AD Connect - geen wachtwoordsynchronisatie|Zowel MFA in de cloud als MFA Server zijn beschikbare opties 
Azure AD en on-premises AD dat gebruikmaakt van DirSync, Azure AD Sync, Azure AD Connect - met wachtwoordsynchronisatie|Multi-factor Authentication in de cloud
On-premises Active Directory|Multi-Factor Authentication-server

De volgende tabel bevat een vergelijking van de functies van Multi-Factor Authentication in de cloud met de functies van de Multi-Factor Authentication-server.

 | Multi-factor Authentication in de cloud | Multi-Factor Authentication-server
------------- | :-------------: | :-------------: |
Meldingen op mobiele app als een tweede factor | ● | ● |
Een verificatiecode op mobiele app als een tweede factor | ● | ●
Telefoonoproep als tweede factor | ● | ● 
Sms in één richting als tweede factor | ● | ●
Sms in twee richtingen als tweede factor |  | ● 
Hardwaretokens als tweede factor |  | ● 
App-wachtwoorden voor clients die geen ondersteuning voor MFA bieden | ● |  
Beheerdercontrole over verificatiemethoden | ● | ● 
Pincodemodus |  | ●
Fraudewaarschuwing | ● | ●
MFA-rapporten | ● | ● 
Eenmalige toegang |  | ● 
Aangepaste begroeting voor telefoongesprekken | ● | ● 
Aanpasbare nummerweergave voor telefoongesprekken | ● | ● 
Goedgekeurde IP-adressen | ● | ● 
MFA herinneren voor vertrouwde apparaten  | ● |  
Voorwaardelijke toegang | ● | ● 
Cache |  | ● 

Nu we hebben vastgesteld of Multi-Factor Authentication in de cloud of de on-premises MFA-server de beste oplossing is, kunnen we beginnen met het instellen en gebruiken van Azure Multi-Factor Authentication.   **Selecteer het pictogram dat bij uw scenario past.**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>





<!--HONumber=Jun16_HO2-->


