<properties
    pageTitle="Aan de slag met Opslagverkenner (Preview) | Microsoft Azure"
    description="Azure Storage-resources beheren met Opslagverkenner (Preview)"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/05/2016"
    ms.author="tarcher" />

# Aan de slag met Opslagverkenner (Preview)

## Overzicht 

Microsoft Azure Opslagverkenner (Preview) is een zelfstandige app waardoor u eenvoudig met Azure Storage-gegevens kunt werken via Windows, OSX en Linux. In dit artikel leert u op welke manieren u verbinding kunt maken met Azure Storage-accounts en hoe u deze kunt beheren.

## Vereisten

- [Opslagverkenner (preview) downloaden en installeren](http://go.microsoft.com/fwlink/?LinkId=708343)

## Verbinding maken met een opslagaccount of -service

Opslagverkenner (Preview) biedt allerlei manieren om verbinding te maken met opslagaccounts. Dit omvat het verbinden met de opslagaccounts die zijn gekoppeld aan uw Azure-abonnementen, het verbinden met de opslagaccounts en services die worden gedeeld via andere Azure-abonnementen en het verbinden met en beheren van lokale opslag met de Azure-opslagemulator:

- [Verbinding maken met een Azure-abonnement](#connect-to-an-azure-subscription): beheer opslagresources die deel uitmaken van uw Azure-abonnement.
- [Verbinding maken met lokale opslag](#connect-to-local-storage): beheer lokale opslag met de Azure-opslagemulator. 
- [Koppelen aan externe opslag](#attach-or-detach-an-external-storage-account): beheer de opslagresources die deel uitmaken van een ander Azure-abonnement via de naam en sleutel van het opslagaccount.
- [Een account koppelen via SAS](#attach-account-using-sas): beheer de opslagresources van een ander Azure-abonnement met een SAS.
- [Een service koppelen via SAS](#attach-service-using-sas): beheer een specifieke opslagservice (blobcontainer, wachtrij of tabel) die deel uitmaakt van een ander Azure-abonnement. Dit gebeurt middels een SAS.

## Verbinding maken met een Azure-abonnement

> [AZURE.NOTE] Als u geen Azure-account hebt, kunt u zich [aanmelden voor een gratis proefversie](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) of [gebruikmaken van uw voordelen als Visual Studio-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Open Opslagverkenner (Preview). 

1. Als u Opslagverkenner (Preview) voor de eerste keer uitvoert of als u Opslagverkenner (Preview) al eerder hebt uitgevoerd, maar u nog geen verbinding hebt gemaakt met een Azure-account, krijgt u een informatiebalk te zien die u kunt gebruiken om verbinding te maken met een Azure-account.

    ![][0]
    
1. Selecteer **Verbinding maken met Microsoft Azure** en volg de dialoogvensters om u aan te melden met een Microsoft-account dat is gekoppeld aan ten minste één actief Azure-abonnement.

Wanneer u zich hebt aangemeld met een Microsoft-account, worden in het linkerdeelvenster van Opslagverkenner (Preview) alle opslagaccounts weergegeven die zijn gekoppeld aan alle Azure-abonnementen die zijn gekoppeld aan het Microsoft-account.

### De Azure-abonnementen filteren

In Opslagverkenner (Preview) kunt u filteren op Azure-abonnementen die zijn gekoppeld aan uw Microsoft-accounts waarbij u zich hebt aangemeld. De bijbehorende opslagaccounts worden dan weergegeven in het linkerdeelvenster.

1. Selecteer het pictogram **Instellingen** (tandwiel).

    ![][1]   

1.  Boven aan het linkerdeelvenster ziet u een vervolgkeuzelijst met alle Microsoft-accounts waarbij u zich hebt aangemeld. 

    ![][3]
     
1.  Selecteer het pijltje omlaag naast de vervolgkeuzelijst om alle Microsoft-accounts te zien waarbij u bent aangemeld en voor een koppeling waarmee u aanvullende Microsoft-accounts kunt toevoegen (door u aan te melden).

    ![][4]

1.  Selecteer het gewenste Microsoft-account in de vervolgkeuzelijst.

1.  In het linkerdeelvenster worden alle Azure-abonnementen weergegeven die zijn gekoppeld aan het geselecteerde Microsoft-account.
Middels het selectievakje links van elk Azure-abonnement kunt u aangeven of u Opslagverkenner (Preview) wel of niet alle opslagaccounts wilt laten weergeven die zijn gekoppeld aan die Azure-abonnementen. Met het inschakelen/uitschakelen van het selectievakje **Alle abonnementen** schakelt u in of uit dat alle vermelde Azure-abonnementen moeten worden geselecteerd.

    ![][2]  

1.  Wanneer u klaar bent met het selecteren van de Azure-abonnementen die u wilt beheren, selecteert u **Toepassen**. Het linkerdeelvenster wordt bijgewerkt met alle opslagaccounts voor elk geselecteerde Azure-abonnement voor het huidige Microsoft-account. 

### Aanvullende Microsoft-accounts toevoegen

Tijdens de volgende stappen maakt u verbinding met aanvullende Microsoft-accounts om de Azure-abonnementen en opslagaccounts van elk account te bekijken.

1.  Selecteer het pictogram **Instellingen** (tandwiel).

    ![][1]   

1.  Boven aan het linkerdeelvenster ziet u een vervolgkeuzelijst met alle momenteel gekoppelde Microsoft-accounts.

    ![][3]
     
1.  Selecteer het pijltje omlaag naast de vervolgkeuzelijst om alle Microsoft-accounts te zien waarbij u bent aangemeld en voor een koppeling waarmee u aanvullende Microsoft-accounts kunt toevoegen (door u aan te melden).

    ![][4]

1.  Selecteer **Een account toevoegen** en volg de aanwijzingen in de dialoogvensters om u aan te melden bij een account dat is gekoppeld aan ten minste één actief Azure-abonnement.

1.  Schakel de selectievakjes in bij de Azure-abonnementen die u wilt bekijken. 

    ![][2]  

1.  Selecteer **Toepassen**.

### Schakelen tussen Microsoft-accounts

Hoewel u verbinding kunt maken met meerdere Microsoft-accounts, worden in het linkerdeelvenster alleen de opslagaccounts weergegeven die zijn gekoppeld aan abonnementen voor één (het huidige) Microsoft-account. Als u verbinding maakt met meerdere Microsoft-accounts, kunt u tussen de accounts wisselen door de volgende stappen uit te voeren:

1.  Selecteer het pictogram **Instellingen** (tandwiel).

    ![][1]   

1.  Boven aan het linkerdeelvenster ziet u een vervolgkeuzelijst met alle momenteel gekoppelde Microsoft-accounts.

    ![][3]
     
1.  Selecteer het pijltje omlaag naast de vervolgkeuzelijst om alle Microsoft-accounts te zien waarbij u bent aangemeld en voor een koppeling waarmee u aanvullende Microsoft-accounts kunt toevoegen (door u aan te melden).

    ![][4]

1.  Selecteer het gewenste Microsoft-account.

1.  Schakel de selectievakjes in bij de Azure-abonnementen die u wilt bekijken. 

    ![][2]  

1.  Selecteer **Toepassen**.
  
## Verbinding maken met lokale opslag

Met Opslagverkenner (Preview) kunt u met lokale opslag werken via de Azure-opslagemulator. Hiermee kunt u code schrijven voor opslag en deze testen zonder dat u een opslagaccount hoeft te hebben geïmplementeerd in Azure (omdat het opslagaccount wordt gesimuleerd door de Azure-opslagemulator).

>[AZURE.NOTE] De Azure-opslagemulator wordt momenteel alleen door Windows ondersteund. 

1. Open Opslagverkenner (Preview). 

1. In het linkerdeelvenster vouwt u het knooppunt **(Ontwikkeling)** uit.

    ![][21]

1. Als u de Azure-opslagemulator nog niet hebt geïnstalleerd, wordt u via de informatiebalk gevraagd dit te doen. Wanneer de informatiebalk wordt weergegeven, selecteert u **De nieuwste versie downloaden** en installeert u de emulator. 

    ![][22]

1. Wanneer de emulator is geïnstalleerd, hebt u de mogelijkheid om lokale blobs, wachtrijen en tabellen te maken en ermee te werken. Als u wilt leren hoe u met elk type opslagaccount werkt, selecteert u de bijbehorende koppelingen hieronder:

    - [Resources voor Azure Blob Storage beheren](./vs-azure-tools-storage-explorer-blobs.md)
    - Resources voor Azure Queue Storage beheren - *Binnenkort beschikbaar*
    - Resources voor Azure Table Storage beheren - *Binnenkort beschikbaar*

## Koppelen aan een extern opslagaccount of de koppeling opheffen

Opslagverkenner (Preview) biedt de mogelijkheid om externe opslagaccounts te koppelen zodat u opslagaccounts eenvoudig kunt delen. In dit gedeelte wordt uitgelegd hoe u externe opslagaccounts koppelt (en hoe u de koppeling opheft).

### De opslagaccountreferenties ophalen

Als u een extern opslagaccount wilt delen, moet de eigenaar van dat account eerst de referenties ontvangen (accountnaam en -sleutel) voor het account en daarna die informatie delen met de persoon die wil verbinden met het (externe) account. U kunt de opslagaccountreferenties verkrijgen via de Azure Portal door deze stappen te volgen: 

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com).
1.  Selecteer **Bladeren**.
1.  Selecteer **Opslagaccounts**.
1.  Op de blade **Opslagaccounts** selecteert u het gewenste opslagaccount.
1.  Op de blade **Instellingen** van het geselecteerde opslagaccount selecteert u **Toegangssleutels**.

    ![][5]
    
1.  In de blade **Toegangssleutels** kopieert u de waarden **STORAGE ACCOUNT NAME** en **KEY 1** voor gebruik bij het koppelen aan het opslagaccount. 

    ![][6]

### Koppelen aan een extern opslagaccount

1.  Klik in Opslagverkenner (Preview) met de rechtermuisknop op **Opslagaccounts** en selecteer in het contextmenu **Externe opslag koppelen**.

    ![][7]
    
1.  In het gedeelte *De opslagaccountreferenties ophalen* wordt uitgelegd hoe u de naam en de waarden van sleutel 1 van het opslagaccount ophaalt. Die waarden worden gebruikt voor deze stap. In het dialoogvenster **Externe opslag koppelen** voert u de naam van het opslagaccount in in het vak **Accountnaam** en voert u de waarde van sleutel 1 in in het vak **Accountsleutel**. Selecteer **OK** wanneer u gereed bent. 

    ![][8]

    Na het koppelen wordt het externe opslagaccount weergegeven met de tekst **(Extern)** toegevoegd aan de opslagaccountnaam. 

    ![][9]

### De koppeling met een extern opslagaccount opheffen

1.  Klik met de rechtermuisknop op het externe opslagaccount dat u wilt loskoppelen en selecteer in het contextmenu **Loskoppelen**.

    ![][10]

1.  Wanneer het bevestigingsbericht wordt weergegeven, selecteert u **Ja** om het loskoppelen van het externe opslagaccount te bevestigen.

    ![][12]

## Een account koppelen via SAS

Een SAS (Shared Access Signature) biedt de beheerder van een Azure-abonnement de mogelijkheid om tijdelijk toegang te verlenen tot een opslagaccount zonder dat daarvoor de Azure-abonnementsreferenties hoeven worden opgegeven. 

Een voorbeeld: stel gebruiker A is beheerder van een Azure-abonnement en gebruiker A wil gebruiker B een beperkte tijd toegang bieden tot een opslagaccount, met bepaalde machtigingen:

1. Gebruiker A genereert een SAS (bestaande uit de verbindingsreeks voor het opslagaccount) die een bepaalde tijd geldig is en de gewenste machtigingen heeft.
1. Gebruiker A deelt de SAS met de persoon die toegang wil tot het opslagaccount (gebruiker B, in dit geval).  
1. Gebruiker B gebruikt Opslagverkenner (Preview) om verbinding te maken met het account van gebruiker A middels de opgegeven SAS. 

### Een SAS ophalen voor het account dat u wilt delen

1.  Open Opslagverkenner (Preview).
1.  In het linkerdeelvenster klikt u met de rechtermuisknop op het opslagaccount dat u wilt delen. Selecteer in het contextmenu de optie **Shared Access Signature ophalen**.

    ![][13]

1. In het dialoogvenster **Shared Access Signature** geeft u het tijdsbestek op en de machtigingen die u wilt gebruiken voor het account. Selecteer vervolgens **Maken**.

    ![][14]
 
1. Er wordt een tweede dialoogvenster **Shared Access Signature** weergegeven. Hierin staat de SAS. Selecteer **Kopiëren** naast de **verbindingsreeks** om de handtekening naar het klembord te kopiëren. Selecteer **Sluiten** om het dialoogvenster te sluiten.

### Koppelen aan het gedeelde account met behulp van de SAS

1.  Open Opslagverkenner (Preview).
1.  Klik in het linkerdeelvenster met de rechtermuisknop op **Opslagaccounts** en selecteer in het contextmenu **Account koppelen via SAS**.
    ![][15]

1. In het dialoogvenster **Een account koppelen via SAS**:

    - **Accountnaam**: geef de naam op die u wilt gebruiken voor dit account. **OPMERKING:** de accountnaam hoeft niet overeen te komen met de oorspronkelijke naam van het opslagaccount waarvoor de SAS is gegenereerd. 
    - **Verbindingsreeks**: plak de verbindingsreeks die u eerder hebt gekopieerd.
    - Selecteer **OK** wanneer u gereed bent.
    
    ![][16]

Na het koppelen wordt het opslagaccount weergegeven met de tekst (SAS) toegevoegd aan de opgegeven accountnaam.

![][17]

## Een service koppelen via SAS

In het gedeelte [Een account koppelen via SAS](#attach-account-using-sas) leest u hoe de beheerder van een Azure-abonnement tijdelijke toegang kan verlenen tot een opslagaccount door een SAS te genereren (en delen) voor het opslagaccount. Op deze manier kunt u ook een SAS genereren voor een bepaalde service (blobcontainer, wachtrij of tabel) binnen een opslagaccount.  

### Een SAS genereren voor de service die u wilt delen

In deze context is een service een blobcontainer, een wachtrij of een tabel. In de volgende gedeelten wordt uitgelegd hoe u een SAS maakt voor de vermelde service:

- [De SAS ophalen voor een blobcontainer](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- De SAS ophalen voor een wachtrij - *binnenkort beschikbaar*
- De SAS ophalen voor een tabel - *binnenkort beschikbaar*

### Koppelen aan de gedeelde-accountservice met behulp van de SAS

1.  Open Opslagverkenner (Preview).
1.  Klik in het linkerdeelvenster met de rechtermuisknop op **Opslagaccounts** en selecteer in het contextmenu **Service koppelen via SAS**.
    ![][18]

1. In het dialoogvenster **Account koppelen via SAS** plakt u de SAS-URI die u eerder hebt gekopieerd. Klik op **OK**.

    ![][19]

Na het koppelen wordt de service weergegeven onder het knooppunt **(service-SAS)**. 

![][20]

## Zoeken naar opslagaccounts

Als u een lange lijst met opslagaccounts hebt, kunt u via het zoekvak boven aan het linkerdeelvenster snel bepaalde opslagaccounts zoeken. 

Wanneer u in het zoekvak typt, worden in het linkerdeelvenster alleen de opslagaccounts weergegeven die overeenkomen met de zoekwaarde die u tot dan toe hebt ingevoerd. In de volgende schermafbeelding ziet u een voorbeeld van een zoekopdracht. De naam van het opslagaccount moet de tekst "tarcher" bevatten.

![][11]
    
Als u de zoekopdracht wilt wissen, klikt u op **x** in het zoekvak.

## Volgende stappen
- [Azure Blob Storage-resources beheren met Opslagverkenner (Preview)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png



<!--HONumber=Jun16_HO2-->


