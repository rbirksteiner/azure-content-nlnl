<properties
   pageTitle="Een Windows-app op elk apparaat uitvoeren met Azure RemoteApp | Microsoft Azure"
   description="Lees hoe u een Windows-app deelt met uw gebruikers met behulp van Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="05/02/2016"
   ms.author="elizapo"/>

# Een Windows-app op elk apparaat uitvoeren met Azure RemoteApp

U kunt een Windows-toepassing overal en op elk apparaat uitvoeren, gewoon met Azure RemoteApp. Of het nu gaat om een aangepaste toepassing die tien jaar geleden is geschreven of een Office-app, uw gebruikers zijn voor die paar toepassingen niet langer gebonden aan een specifiek besturingssysteem (zoals Windows XP).

Met Azure RemoteApp kunnen uw gebruikers ook hun eigen Android- of Apple-apparaten gebruiken en dezelfde ervaring krijgen als op Windows (of op een Windows Phone). Dit wordt bereikt door het hosten van uw Windows-toepassing in een verzameling van virtuele machines in Windows Azure: uw gebruikers hebben toegang vanaf elke locatie waar ze een internetverbinding hebben. 

Hieronder vindt u een voorbeeld van hoe u dit precies doet.

In dit artikel gaan we Access delen met al uw gebruikers. U kunt echter ELKE willekeurige app gebruiken. Als u de app op een computer met Windows Server 2012 R2 kunt installeren, kunt u de app delen door de volgende stappen uit te voeren. Controleer de [appvereisten](remoteapp-appreqs.md) om zeker te weten dat uw app werkt.

Aangezien Access een database is en we willen dat die database nuttig is, gaan we enkele extra stappen uitvoeren om gebruikers toegang te geven tot de Access-gegevensshare. Als uw app geen database is of uw gebruikers geen toegang hoeven te hebben tot een bestandsshare, kunt u die stappen in deze zelfstudie overslaan.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


## Een verzameling maken in RemoteApp

Eerst maakt u een verzameling. De verzameling fungeert als een container voor uw apps en gebruikers. Elke verzameling is gebaseerd op een installatiekopie: u kunt uw eigen installatiekopie maken of de installatiekopie gebruiken die deel uitmaakt van uw abonnement. In deze zelfstudie gebruiken we de installatiekopie van het Office 2013-proefabonnement, omdat deze de app bevat die we willen delen.

1. Schuif in de Azure Portal omlaag in de navigatiestructuur aan de linkerkant tot u RemoteApp ziet. Open die pagina.
2. Klik op **Een RemoteApp-verzameling maken**.
3. Klik op **Snelle invoer** en voer een naam in voor uw verzameling.
4. Selecteer de regio die u wilt gebruiken om de verzameling te maken. Selecteer de regio die het geografisch het dichtst bij de locatie ligt waar uw gebruikers de app gaan gebruiken. Zo krijgen ze de best mogelijke ervaring. In deze zelfstudie bevinden de gebruikers zich bijvoorbeeld in Redmond, Washington. De dichtstbijzijnde Azure-regio is **VS - west**.
5. Selecteer het abonnement dat u wilt gebruiken. Een basisabonnement omvat 16 gebruikers op een grote virtuele machine van Azure en een standaardabonnement is geschikt voor 10 gebruikers op een grote virtuele machine van Azure. In het algemeen is een basisabonnement zeer geschikt voor een werkstroom bedoeld voor gegevensinvoer. Voor een productiviteits-app, zoals Office, kiest u voor een standaardabonnement.
6. Tot slot selecteert u de installatiekopie van Office 2013 Professional. Deze installatiekopie bevat Office 2013-apps. Vergeet echter niet dat deze installatiekopie alleen geschikt is voor proefverzamelingen en POC’s. U kunt deze installatiekopie niet gebruiken in een productieverzameling.
7. Klik nu op **Een RemoteApp-verzameling maken**.

![Een cloudverzameling maken in RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Hiermee start u het maken van de verzameling, maar dit kan een uur duren.

U kunt nu uw gebruikers toevoegen.

## De app delen met gebruikers

Zodra uw verzameling is gemaakt, kunt u Access gaan publiceren aan uw gebruikers en de gebruikers toevoegen die toegang moeten hebben.

Als u bent weggegaan van het Azure RemoteApp-knooppunt terwijl de verzameling werd gemaakt, gaat u eerst weer terug naar het knooppunt vanaf de startpagina van Azure.

2. Klik op de verzameling die u eerder hebt gemaakt voor toegang tot extra opties en configureer de verzameling.
![Een nieuwe RemoteApp-cloudverzameling](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. Klik op het tabblad **Publiceren** op **Publiceren** onder aan het scherm en klik op **Programma’s in het menu Start publiceren**.
![Een RemoteApp-programma publiceren](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Selecteer de apps die u wilt publiceren in de lijst. In dit geval kiest u Access. Klik op **Voltooien**. Wacht tot het publiceren van de app is voltooid.
![Access publiceren in RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Wanneer de app is gepubliceerd, gaat u naar het tabblad **Gebruikerstoegang** om alle gebruikers toe te voegen die toegang nodig hebben tot uw apps. Voer gebruikersnamen (e-mailadressen) voor uw gebruikers in en klik vervolgens op **Opslaan**.

![Gebruikers toevoegen aan RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Nu is het tijd om uw gebruikers te vertellen over deze nieuwe apps en hoe ze toegang hiertoe hebben. U doet dit door uw gebruikers een e-mail te sturen met de URL voor het downloaden van de extern bureaublad-client.
![De clientdownload-URL voor RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## Toegang tot Access configureren

Sommige apps hebben nog extra configuratie nodig nadat u ze via RemoteApp hebt geïmplementeerd. Voor Access houdt dit in dat we een bestandsshare op Azure gaan maken waartoe elke gebruiker toegang heeft. Als u dit niet wilt doen, kunt u een [hybride verzameling](remoteapp-create-hybrid-deployment.md) maken (in plaats van onze cloudverzameling) die uw gebruikers toegang biedt tot bestanden en informatie op uw lokale netwerk. Vervolgens laten we onze gebruikers weten dat ze een lokaal station op hun computer moeten toewijzen aan het Azure-bestandssysteem.

Het eerste deel doet u als beheerder. Vervolgens hebben we een aantal stappen voor uw gebruikers.

1. Publiceer eerst de opdrachtregelinterface (cmd.exe). Op het tabblad **Publiceren** kiest u **cmd** en klikt u op **Publiceren > Programma publiceren met behulp van pad**.
2. Voer de naam in van de app en het pad. In deze zelfstudie gebruikt u 'Verkenner' als de naam en '%SYSTEMDRIVE%\windows\explorer.exe' als het pad.
![Publiceer het bestand cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Nu gaat u een Azure-[opslagaccount](../storage/storage-create-storage-account.md) maken. We hebben die van ons 'accessstorage' genoemd, dus kies een naam die zinvol voor u is. (Er kan slechts één 'accessstorage' zijn.) ![Ons Azure-opslagaccount.](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Ga terug naar uw dashboard zodat u het pad naar uw opslag kunt ophalen (eindpuntlocatie). U gaat dit straks gebruiken, dus kopieer het ergens.
![Het pad van het opslagaccount](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Nadat het opslagaccount is gemaakt, hebt u de primaire toegangssleutel nodig. Klik op **Toegangssleutels beheren** en kopieer de primaire toegangssleutel.
6. Nu stelt u de context van het opslagaccount in en maakt u een nieuwe bestandsshare voor Access. Voer de volgende cmdlets uit in een verhoogd Windows PowerShell-venster:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Voor onze share zijn deze cmdlets uitgevoerd:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Nu is de gebruiker aan zet. Laat uw gebruikers eerst een [RemoteApp-client](remoteapp-clients.md) installeren. Vervolgens moeten de gebruikers een station van hun account toewijzen aan de Azure-bestandsshare die u hebt gemaakt en hun Access-bestanden toevoegen. Dit doen ze als volgt:

1. Ga naar de gepubliceerde apps in de RemoteApp-client. Start het programma cmd.exe.
2. Voer de volgende opdracht uit om een station vanaf uw computer aan de bestandsshare toe te wijzen:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Als u de parameter **/persistent** op Ja zet, blijft het toegewezen station behouden voor volgende sessies.
1. Start nu de Verkenner-app vanuit RemoteApp. Kopieer alle Access-bestanden die u in de gedeelde app wilt gebruiken naar de bestandsshare.
![Access-bestanden in een Azure-share zetten](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Tot slot opent u Access en de database die u zojuist hebt gedeeld. Hier ziet u uw gegevens in Access uitgevoerd vanuit de cloud.
![Een echte Access-database vanuit de cloud](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Nu kunt u Access op al uw apparaten gebruiken, maar zorg er wel voor dat u een RemoteApp-client installeert.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Volgende stappen

Nu u hebt geleerd hoe een verzameling maakt, kunt u proberen een [verzameling te maken die gebruikmaakt van Office 365](remoteapp-tutorial-o365anywhere.md). U kunt ook een [hybride verzameling](remoteapp-create-hybrid-deployment.md) maken die toegang heeft tot uw lokale netwerk.

<!--Image references-->
 



<!--HONumber=Jun16_HO2-->


