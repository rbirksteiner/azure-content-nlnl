<properties
   pageTitle="Een virtuele machine van Azure versleutelen | Microsoft Azure"
   description="In dit document leest u hoe u een virtuele machine van Azure kunt versleutelen wanneer u een waarschuwingsbericht ontvangt van het Azure Beveiligingscentrum."
   services="security, security-center"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/27/2016"
   ms.author="tomsh"/>

# Een virtuele machine van Azure versleutelen
Het Azure Beveiligingscentrum stuurt u een waarschuwingsbericht wanneer u virtuele machines hebt die niet versleuteld zijn. Deze waarschuwingsberichten hebben een hoge prioriteit. Het wordt aangeraden om deze virtuele machines te versleutelen.

![Aanbevelingen voor schijfversleuteling](./media/security-center-disk-encryption\security-center-disk-encryption-fig1.png)

> [AZURE.NOTE] De informatie in dit document is van toepassing op de voorbeeldversie van het Azure Beveiligingscentrum.

Wanneer het Azure Beveiligingscentrum heeft vastgesteld welke Azure Virtual Machines versleuteld moeten worden, volgt u de volgende stappen om dit te doen:

- Installeer en configureer Azure PowerShell. Hierdoor kunt u de PowerShell-opdrachten uitvoeren die nodig zijn om de vereisten voor het versleutelen van Azure Virtual Machines te installeren.
- Download het Azure PowerShell-script met vereisten voor Azure Disk Encryption en voer dit uit.
- Versleutel uw virtuele machines

Dit document is bedoeld om u in staat te stellen zelf uw virtuele machines te versleutelen, zelfs als u weinig tot geen kennis hebt van Azure PowerShell.
In dit document gaan we ervan uit dat u gebruikmaakt van Windows 10 als clientmachine om Azure Disk Encryption te configureren.

Er zijn een aantal manieren om de vereisten te installeren en de versleuteling te configureren voor Azure Virtual Machines. Als u al goed bekend bent met Azure PowerShell of Azure CLI, maakt u mogelijk liever gebruik van een andere manier.

> [AZURE.NOTE] Zie [Azure Disk Encryption for Windows and Linux Azure Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) voor meer informatie over andere manieren om de versleuteling van virtuele machines van Azure te configureren.

## Azure PowerShell installeren en configureren
Azure PowerShell-versie 1.2.1 of hoger moet op uw computer geïnstalleerd zijn. Het artikel [How to install and configure Azure PowerShell](../powershell-install-configure.md) bevat alle stappen die u moet volgen om uw computer zo in te richten dat deze kan werken met Azure PowerShell. De eenvoudigste manier is de installatiemethode met behulp van de Web PI die beschreven staat in dat artikel. Als u Azure PowerShell al hebt geïnstalleerd, dient u dit opnieuw te installeren met behulp van de Web PI zodat u beschikt over de nieuwste versie van Azure PowerShell.


## Download het configuratiescript met vereisten voor Azure Disk Encryption en voer dit uit.
Het configuratiescript met vereisten voor Azure Disk Encryption installeert alle vereisten voor het versleutelen van uw Azure Virtual Machines.

1.  Ga naar de GitHub-pagina met het [installatiescript voor vereisten voor Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2.  Klik op de GitHub-pagina op de knop **Onbewerkt**.
3.  Gebruik **CTRL-A** om alle tekst op de pagina te selecteren en gebruik vervolgens **CTRL-C** om alle tekst op de pagina naar het klembord te kopiëren.
4.  Open **Kladblok** en plak de gekopieerde tekst in Kladblok.
5.  Maak een nieuwe map op uw C:-schijf met de naam **AzureADEScript**.
6.  Sla het Kladblok-bestand op: klik op **Bestand** en vervolgens op **Opslaan als**. Vul in het tekstvak Bestandsnaam de naam **“ADEPrereqScript.ps1”** in en klik op **Opslaan**. (Zet de naam tussen aanhalingstekens, anders wordt het bestand opgeslagen als .txt-bestand).

De inhoud van het script is nu opgeslagen. Open het script in de PowerShell ISE:

1.  Klik in het startmenu op **Cortana**. Vraag **Cortana** naar “PowerShell” door **PowerShell** te typen in het zoekvak.
2.  Klik met de rechtermuisknop op **Windows PowerShell ISE** en klik op **Als administrator uitvoeren**.
3.  In het venster **Administrator: Windows PowerShell ISE** klikt u op **Weergeven** en vervolgens op **Scriptvenster weergeven**.
4.  Als u het venster **Opdrachten** ziet aan de rechterkant van het venster, klikt u op de **“x”** in de rechterbovenhoek om dit venster te sluiten. Als u de tekst niet goed kunt lezen omdat deze te klein is, gebruikt u **CTRL+ het plus-teken (+)**. Als de tekst te groot is, gebruikt u **CTRL + het min-teken (-)**.
5.  Klik op **Bestand** en vervolgens op **Openen**. Blader naar de map **C:\AzureADEScript** en dubbelklik op het bestand **ADEPrereqScript**.
6.  De inhoud van het **ADEPrereqScript** wordt nu weergegeven in de PowerShell ISE. De verschillende onderdelen, zoals opdrachten, parameters en variabelen zijn voorzien van kleurcodering zodat u deze eenvoudig kunt onderscheiden.

Uw scherm ziet er nu als volgt uit.

![PowerShell ISE-venster](./media/security-center-disk-encryption\security-center-disk-encryption-fig2.png)

Het bovenste venster wordt het ‘scriptvenster' genoemd en het onderste venster de 'console'. Deze termen zullen we verderop in dit artikel gebruiken.

## Voer de PowerShell-opdracht met vereisten voor Azure Disk Encryption uit.

Het script met vereisten voor Azure Disk Encryption vraagt u na het openen om de volgende informatie:

- **Naam resourcegroep**: naam van de resourcegroep waar u de Sleutelkluis in wilt plaatsen.  Als er nog geen resourcegroep met de door u ingevulde naam bestaat, wordt deze aangemaakt. Als u al een resourcegroep hebt die u in dit abonnement wilt gebruiken, vul dan de naam van die resourcegroep in.
- **Naam Sleutelkluis**: naam van de sleutelkluis waar versleutelingssleutels in moeten worden geplaatst. Als u nog geen Sleutelkluis hebt met de door u ingevulde naam bestaat, wordt deze aangemaakt. Als u al een Sleutelkluis hebt die u wilt gebruiken, vul dan de naam van die Sleutelkluis in.
- **Locatie**: de locatie van de sleutelkluis. Zorg ervoor dat de Sleutelkluis en de VM’s die moeten worden versleuteld, zich op dezelfde locatie bevinden. Als u de locatie niet weet, volg dan de stappen verderop in dit artikel om deze te vinden.
- **Naam Azure Active Directory-toepassing **: naam van de Azure Active Directory-toepassing die wordt gebruikt om geheimen over te schrijven naar de Sleutelkluis. Als er nog geen toepassing met deze naam bestaat, wordt deze aangemaakt. Als u al een Azure Active Directory-toepassing hebt die u wilt gebruiken, vul dan de naam van die Azure Active Directory-toepassing in.

> [AZURE.NOTE] Als u wilt weten waarom u een Azure Active Directory-toepassing moet maken, zie dan het gedeelte *Register an application with Azure Active Directory* in het artikel [Getting Started with Azure Key Vault](../key-vault/key-vault-get-started.md).

Volg de volgende stappen om een virtuele machine van Azure te versleutelen:

1.  Als u de PowerShell ISE hebt gesloten, open PowerShell ISE dan met verhoogde bevoegdheid. Volg de bovenstaande instructies in dit artikel wanneer PowerShell ISE nog niet geopend is. Wanneer u het script hebt gesloten, open dan het bestand **ADEPrereqScript.ps1** door te klikken op **Bestand**, **Openen** en het script te selecteren in de map **c:\AzureADEScript**. Wanneer u dit artikel in zijn geheel hebt gevolgd, ga dan verder naar de volgende stap.
2.  In de console van de PowerShell ISE (het onderste venster van de  PowerShell ISE) verandert u de focus naar de locatie van het script door **cd c:\AzureADEScript** te typen en op **ENTER** te drukken.
3.  Stel het uitvoerbeleid op uw machine in zodat u het script kunt uitvoeren. Typ **Set-ExecutionPolicy Unrestricted** in de console en druk op ENTER. Wanneer u een dialoogvenster ziet over het effect van de wijzigingen in het uitvoerbeleid klikt u op **Ja op alles** of **Ja** Als u **Ja op alles** ziet, selecteert u die optie; als u **Ja op alles**niet ziet, klik dan op **Ja**).
4.  Meld u aan bij uw Azure-account. Typ **Login-AzureRmAccount** in de console en druk op **ENTER**. Er wordt een dialoogvenster weergegeven waarin u uw gegevens kunt invoeren (zorg ervoor dat u bevoegd bent om de virtuele machines te wijzigen; als u die bevoegdheid niet hebt, kunt u de virtuele machines niet versleutelen. Als u niet weet of u bevoegd bent, neem dan contact op met de eigenaar van uw abonnement of uw beheerder). U ziet nu informatie over uw **omgeving**, **account**, **tenant-ID**, **abonnements-ID** en **huidige opslagaccount**. Kopieer uw **abonnements-ID** naar het Kladblok. Dit hebt u nodig in stap 6.
5.  Bepaal bij welk abonnement uw virtuele machine hoort en de locatie ervan. Ga naar [https://portal.azure.com](ttps://portal.azure.com) en meld u aan.  Klik op **Virtuele machines** in de linkerkant van het scherm. U ziet een lijst met uw virtuele machines en de abonnementen waar deze bij horen.

    ![Virtuele machines](./media/security-center-disk-encryption\security-center-disk-encryption-fig3.png)

6.  Ga terug naar de PowerShell ISE. Stel de context van het abonnement in waarin het script wordt uitgevoerd. Typ **Select-AzureRmSubscription –SubscriptionId <your_subscription_Id>** in de console (vervang **< your_subscription_Id >** door uw abonnements-ID) en druk op **ENTER**. U ziet nu informatie over de omgeving, **account**, **tenant-ID**, **abonnements-ID** en **huidige opslagaccount**.
7.  U kunt nu het script uitvoeren. Klik op de knop **Script uitvoeren** of druk op **F5**.

    ![Het PowerShell-script uitvoeren](./media/security-center-disk-encryption\security-center-disk-encryption-fig4.png)

8.  Het script vraagt om de **resourceGroupName:**. Voer de naam van de *Resourcegroep* in die u wilt gebruiken en druk vervolgens op **ENTER**. Als u geen resourcegroep hebt, voer dan de naam in die u wilt gebruiken voor een nieuwe resourcegroep. Als u al een *Resourcegroep* hebt die u wilt gebruiken (zoals de groep waar uw virtuele machine zich bevindt), vul dan de naam van de bestaande resourcegroep in.
9.  Het script vraagt om de **keyVaultName:**. Voer de naam van de *Sleutelkluis* in die u wilt gebruiken en druk vervolgens op ENTER. Als u geen sleutelkluis hebt, voer dan de naam in die u wilt gebruiken voor een nieuwe sleutelkluis. Als u al een Sleutelkluis hebt die u wilt gebruiken, vul dan de naam van die *Sleutelkluis* in.
10. Het script vraagt om de **location:**. Voer de naam van de locatie in waar de VM die u wilt versleutelen zich bevindt en druk vervolgens op **ENTER**. Als u de locatie niet meer weet, gaat u terug naar stap 5.
11. Het script vraagt om de **aadAppName:**. Voer de naam van de *Azure Active Directory*-toepassing in die u wilt gebruiken en druk vervolgens op **ENTER**. Als u geen sleutelkluis hebt, voer dan de naam in die u wilt gebruiken voor een nieuwe sleutelkluis. Als u al een *Azure Active Directory-toepassing* hebt die u wilt gebruiken, vul dan de naam van de bestaande *Azure Active Directory-toepassing in*.
12. Een aanmeldvenster wordt weergegeven. Vul uw gegevens in (u dient zich inderdaad nog een keer aan te melden).
13. Het script wordt uitgevoerd. Wanneer het is voltooid, wordt u gevraagt om de waarden van de velden **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl**, en **keyVaultResourceId** te kopiëren. Kopieer deze waarden naar het klembord en plak ze in het Kladblok.
14. Ga terug naar de PowerShell ISE, plaats de cursor aan het einde van de laatste regel en druk op **ENTER**.

Het resultaat van het script ziet er als volgt uit:

![PowerShell-resultaat](./media/security-center-disk-encryption\security-center-disk-encryption-fig5.png)

## De virtuele machine van Azure versleutelen

U kunt nu uw virtuele machine versleutelen. Als uw virtuele machine zich in dezelfde resourcegroep bevindt als uw Sleutelkluis, kunt u verdergaan naar het gedeelte met de stappen voor het versleutelen. Als uw virtuele machine zich niet in dezelfde resourcegroep bevindt als uw Sleutelkluis, moet u het volgende invullen in de console in de PowerShell ISE:

**$resourceGroupName = <’Virtual_Machine_RG’>**

Vervang **< Virtual_Machine_RG >** door de naam van de resourcegroep waar uw virtuele machines zich bevinden, tussen enkele aanhalingstekens. Druk vervolgens op **ENTER**.
Om te bevestigen dat u de juiste naam van de resourcegroep hebt ingevoerd, typt u het volgende in de PowerShell ISE-console:

**$resourceGroupName**

Druk op **ENTER**. U ziet nu de naam van de resourcegroep waar uw virtuele machines zich bevinden. Bijvoorbeeld:

![PowerShell-resultaat](./media/security-center-disk-encryption\security-center-disk-encryption-fig6.png)

### Stappen voor het versleutelen

PowerShell heeft als eerste de naam van de virtuele machine nodig die u wilt versleutelen. Typ in de console:

**$vmName = <’your_vm_name’>**

Vervang **<’your_vm_name’>** door de naam van de VM (zet de naam tussen enkele aanhalingstekens) en druk vervolgens op **ENTER**.

Om te bevestigen dat u de juiste naam van de VM hebt ingevoerd, typt u:

**$vmName**

Druk op **ENTER**. U ziet nu de naam van de virtuele machine die u wilt versleutelen. Bijvoorbeeld:

![PowerShell-resultaat](./media/security-center-disk-encryption\security-center-disk-encryption-fig7.png)

U kunt de opdracht om de virtuele machine te versleutelen op twee manieren uitvoeren. Bij de eerste manier typt u de volgende opdracht in de PowerShell ISE-console:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId
~~~

Na deze opdracht drukt u op **ENTER**.

Bij de tweede  manier klikt u in het scriptvenster (het bovenste venster van de PowerShell ISE) en scrolt u naar de onderkant van het script. Selecteer de bovenstaande opdrachtlijst, klik met de rechtermuisknop en klik vervolgens op **Selectie uitvoeren** of druk op **F8**.

![PowerShell ISE](./media/security-center-disk-encryption\security-center-disk-encryption-fig8.png)

Bij beide methodes wordt er een dialoogvenster weergegeven waarin staat dat het ongeveer 10-15 minuten duurt voordat de bewerking is voltooid. Klik op **Ja**.

Tijdens het versleutelingsproces kunt u teruggaan naar de Azure Portal en de status van de virtuele machine bekijken. Klik op **Virtual Machines** aan de linkerkant van de pagina. Klik vervolgens in de blade **Virtual Machines** op de naam van de virtuele machine die u aan het versleutelen bent. Een blade wordt weergegeven waarin u kunt zien dat de **Status** vermeldt dat de machine aan het **Bijwerken** is. Dit toont aan dat versleuteling wordt uitgevoerd.

![Meer informatie over de VM](./media/security-center-disk-encryption\security-center-disk-encryption-fig9.png)

Ga terug naar de PowerShell ISE. Wanneer het script is voltooid, ziet het scherm er als volgt uit.

![PowerShell-resultaat](./media/security-center-disk-encryption\security-center-disk-encryption-fig10.png)

Om aan te tonen dat de virtuele machine nu versleuteld is, gaat u terug naar de Azure Portal en klikt u op **Virtual Machines** aan de linkerkant van de pagina. Klik op de naam van de virtuele machine die u hebt versleuteld. Klik in de blade **Instellingen** op **Schijven**.

![Opties voor instellingen](./media/security-center-disk-encryption\security-center-disk-encryption-fig11.png)

In de blade **Schijven** ziet u dat de **Versleuteling** is **Ingeschakeld**.

![Schijfeigenschappen](./media/security-center-disk-encryption\security-center-disk-encryption-fig12.png)


## Volgende stappen

In dit document hebt u kunnen lezen hoe u een virtuele machine van Azure kunt versleutelen. Zie de volgende onderwerpen voor meer informatie over het Azure Beveiligingscentrum:

- [Security health monitoring in Azure Security Center](security-center-monitoring.md): ontdek hoe u de status van uw Azure-bronnen kunt monitoren.
- [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
- [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/): lees blogposts over de beveiliging en compliance van Azure.



<!--HONumber=Jun16_HO2-->


