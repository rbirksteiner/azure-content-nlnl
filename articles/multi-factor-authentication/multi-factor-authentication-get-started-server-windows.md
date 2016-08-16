<properties 
    pageTitle="Windows-verificatie en Azure Multi-Factor Authentication-server" 
    description="Dit is de Azure Multi-Factor Authentication-pagina die u helpt bij het implementeren van Windows-verificatie en de Azure Multi-Factor Authentication-server." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Windows-verificatie en Azure Multi-Factor Authentication-server

In de sectie Windows-verificatie kan de beheerder Windows-verificatie inschakelen en configureren voor een of meer toepassingen.  Hier volgt een lijst met zaken waarmee u rekening moet houden voordat u Windows-verificatie instelt.

-  Het systeem moet opnieuw worden opgestart om Azure Multi-Factor Authentication voor Terminal Services te activeren.
-  Als 'Overeenkomende Azure Multi-Factor Authentication-gebruiker vereisen' is ingeschakeld en u niet voorkomt in de lijst met gebruikers, kunt u zich niet aanmelden bij de computer wanneer deze opnieuw is opgestart.
-  Goedgekeurde IP-adressen is afhankelijk van de vraag of de toepassing de client-IP kan aanbieden bij de verificatie. Momenteel wordt alleen Terminal Services ondersteund.  







>[AZURE.NOTE]Deze functie wordt niet ondersteund om Terminal Services op Windows Server 2012 R2 te beveiligen.
 



## Gebruik de volgende procedure als u een toepassing met Windows-verificatie wilt beveiligen.

1. Klik in de Azure Multi-Factor Authentication-server op het pictogram Windows-authenticatie.
![Windows-verificatie](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Schakel het selectievakje Windows-verificatie in. Dit selectievakje is standaard uitgeschakeld.
3. Op het tabblad Toepassingen kan de beheerder een of meer toepassingen configureren voor Windows-verificatie.
4. Selecteer een server of toepassing. Geef op of de server/toepassing is ingeschakeld. Klik op OK.
5. Klik op de knop Toevoegen… .
6. Op het tabblad Goedgekeurde IP-adressen kunt u Azure Multi-Factor Authentication voor Windows-sessies die afkomstig zijn van specifieke IP-adressen overslaan. Als werknemers bijvoorbeeld de toepassing zowel op kantoor als thuis gebruiken, kunt u instellen dat hun telefoons voor Azure Multi-Factor Authentication niet overgaan wanneer ze op kantoor zijn. Hiervoor geeft u het subnet van de werkplek op als een van de goedgekeurde IP-adressen.
7. Klik op de knop Toevoegen… .
8. Selecteer Eén IP-adres als u één IP-adres wilt overslaan.
9. Selecteer IP-bereik als u een heel IP-adresbereik wilt overslaan. Voorbeeld 10.63.193.1-10.63.193.100.
10. Selecteer Subnet als u een bereik van IP-adressen wilt opgeven met subnetnotatie. Voer het IP-adres in waarop het subnet begint en kies het juiste IP-masker uit de vervolgkeuzelijst. 
11. Klik op de knop OK.


<!--HONumber=Jun16_HO2-->


