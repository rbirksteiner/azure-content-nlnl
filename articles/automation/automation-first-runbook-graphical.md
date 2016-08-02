<properties
    pageTitle="Mijn eerste grafische runbook in Azure Automation | Microsoft Azure"
    description="Zelfstudie die u helpt bij het maken, testen en publiceren van een eenvoudig grafisch runbook."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="runbook, runbook template, runbook automation, azure runbook"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="magoedte;bwren"/>

# Mijn eerste grafische runbook

> [AZURE.SELECTOR] - [Grafisch](automation-first-runbook-graphical.md) - [PowerShell](automation-first-runbook-textual-PowerShell.md) - [PowerShell Workflow](automation-first-runbook-textual.md)

In deze zelfstudie wordt stap voor stap het maken van een [grafisch runbook](automation-runbook-types.md#graphical-runbooks) in Azure Automation beschreven.  We beginnen met een eenvoudig runbook dat we testen en publiceren terwijl we uitleggen hoe u de status van de runbooktaak kunt bijhouden.  Vervolgens wijzigen we het runbook zodat Azure-resources daadwerkelijk worden beheerd, in dit geval door een virtuele machine van Azure te starten.  Daarna maken we het runbook krachtiger door runbookparameters en voorwaardelijke koppelingen toe te voegen.

## Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

-   Azure-abonnement.  Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of <a href="/pricing/free-account/" target="_blank">[u aanmelden voor een gratis account](https://azure.microsoft.com/free/).
-   [Uitvoeren als-account voor Azure](automation-sec-configure-azure-runas-account.md) om het runbook te bevatten en Azure-resources te verifiëren.  Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
-   Een virtuele machine van Azure.  We stoppen en starten deze machine, dus het mag geen productiemachine zijn.


## Stap 1: nieuw runbook maken

We beginnen met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld* als uitvoer wordt gegeven.

1.  Open uw Automation-account in Azure Portal.  
    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account.  U zou al enkele assets moeten hebben.  De meeste daarvan zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account.  Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).
2.  Klik op de tegel **Runbooks** om de lijst met runbooks te openen.<br> ![Besturingselement Runbooks](media/automation-first-runbook-graphical/runbooks-control.png)
3.  Maak een nieuw runbook door te klikken op de knop **Een runbook toevoegen** en daarna op **Een nieuw runbook maken**.
4.  Geef het runbook de naam *MyFirstRunbook-Graphical*.
5.  In dit geval gaan we een [grafisch runbook](automation-graphical-authoring-intro.md) maken, dus selecteer **Grafisch** voor **Runbooktype**.<br> ![Nieuw runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6.  Klik op **Maken** om het runbook te maken en de grafische editor te openen.

## Stap 2: activiteiten toevoegen aan het runbook

Met het besturingselement Bibliotheek aan de linkerkant van de editor kunt u activiteiten selecteren die u aan uw runbook wilt toevoegen.  We gaan een cmdlet **Write-Output** toevoegen om tekst als uitvoer van het runbook te krijgen.

1.  Klik in het besturingselement Bibliotheek in het zoektekstvak en typ **Write-Output**.  De zoekresultaten worden eronder weergegeven. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
2.  Schuif omlaag naar de onderkant van de lijst.  U kunt met de rechtermuisknop klikken op **Write-Output** en **Toevoegen aan papier** selecteren of klikken op de knop met de drie puntjes naast de cmdlet en **Toevoegen aan papier** selecteren.
3.  Klik op de activiteit **Write-Output** op het papier.  Hiermee opent u de besturingselementblade Configuratie waarmee u de activiteit kunt configureren.
4.  Het **label** krijgt standaard de naam van de cmdlet, maar we kunnen dit wijzigen in een duidelijkere beschrijving. Wijzig dit in *Schrijf Hallo Wereld naar uitvoer*.
5.  Klik op **Parameters** om waarden op te geven voor de parameters van de cmdlet.  
    Sommige cmdlets hebben meerdere parametersets. U moet dan selecteren welke u wilt gebruiken. In dit geval heeft **Write-Output** slechts één parameterset, dus u hoeft er geen te selecteren. <br> ![Eigenschappen Write-Output](media/automation-first-runbook-graphical/write-output-properties-b.png)
6.  Selecteer de parameter **InputObject**.  Dit is de parameter waarin we de tekst opgeven die naar de uitvoerstroom moet worden verzonden.
7.  Selecteer in de vervolgkeuzelijst **Gegevensbron** de optie **PowerShell-expressie**.  In de vervolgkeuzelijst **Gegevensbron** staan verschillende bronnen die u kunt gebruiken om een parameterwaarde te vullen.  
    U kunt uitvoer van deze bronnen gebruiken, zoals een andere activiteit, een Automation-asset of een PowerShell-expressie.  In dit geval willen we uitvoer van de tekst *Hallo wereld*. We kunnen een PowerShell-expressie gebruiken en een tekenreeks opgeven.
8.  Typ in het vak **Expressie** de tekst *"Hallo wereld"* en klik vervolgens tweemaal op **OK** om terug te gaan naar het papier.<br> ![PowerShell-expressie](media/automation-first-runbook-graphical/expression-hello-world.png)
9.  Klik op **Opslaan** om het runbook op te slaan.<br> ![Runbook opslaan](media/automation-first-runbook-graphical/runbook-toolbar-save-revised20165.png)

## Stap 3: het runbook testen

Voordat we het runbook publiceren om het beschikbaar te maken in productie, willen we het testen om er zeker van te zijn dat het goed werkt.  Wanneer u een runbook test, voert u de **concept**versie uit en geeft u de uitvoer interactief weer.

1.  Klik op **Testvenster** om de blade Test te openen.<br> ![Testvenster](media/automation-first-runbook-graphical/runbook-toolbar-test-revised20165.png)
2.  Klik op **Start** om de test te starten.  Dit moet de enige ingeschakelde optie zijn.
3.  Een [runbooktaak](automation-runbook-execution.md) wordt gemaakt en de status ervan wordt in het venster weergegeven.  
    In eerste instantie is de taakstatus *In de wachtrij geplaatst*. Hiermee wordt aangegeven dat er wordt gewacht tot in de cloud een runbook-werkrol beschikbaar is.  De taakstatus verandert daarna in *Starten* wanneer een werkrol de taak claimt en daarna in *Wordt uitgevoerd* wanneer het runbook daadwerkelijk wordt uitgevoerd.  
4.  Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In ons geval zouden we *Hallo wereld* moeten zien.<br> ![Hallo wereld](media/automation-first-runbook-graphical/runbook-test-results.png)
5.  Sluit de blade Test om terug te gaan naar het papier.

## Stap 4: het runbook publiceren en starten

Het runbook dat we zojuist hebben gemaakt, bevindt zich nog steeds in de modus Concept. We moeten het publiceren voordat we het in productie kunnen uitvoeren.  Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie.  In ons geval hebben we nog geen gepubliceerde versie omdat we het runbook zojuist hebben gemaakt.

1.  Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.<br> ![Publiceren](media/automation-first-runbook-graphical/runbook-toolbar-publish-revised20166.png)
2.  Als u naar links schuift om het runbook weer te geven op de blade **Runbooks**, wordt de **Ontwerpstatus** **Gepubliceerd** getoond.
3.  Schuif terug naar rechts om de blade voor **MyFirstRunbook** weer te geven.  
    Met de opties bovenaan kunnen we het runbook starten, plannen dat het op een bepaald moment in de toekomst start of een [webhook](automation-webhooks.md) maken zodat het kan worden gestart via een HTTP-aanroep.
4.  We willen het runbook starten, dus klikt u op **Starten** en vervolgens op **Ja** wanneer hierom wordt gevraagd.<br> ![Runbook starten](media/automation-first-runbook-graphical/runbook-controls-start-revised20165.png)
5.  Een taakblade wordt geopend voor de runbooktaak die we zojuist hebben gemaakt.  We kunnen deze blade sluiten, maar in dit geval laten we de blade open zodat we de voortgang van de taak kunnen bekijken.
6.  De taakstatus wordt weergegeven in **Taakoverzicht** en komt overeen met de statuswaarden die we zagen toen we het runbook testten.<br> ![Taakoverzicht](media/automation-first-runbook-graphical/runbook-job-summary.png)
7.  Zodra voor het runbook de status *Voltooid* wordt weergegeven, klikt u op **Uitvoer**. De blade **Uitvoer** wordt geopend en we zien onze tekst *Hallo wereld* in het deelvenster.<br> ![Taakoverzicht](media/automation-first-runbook-graphical/runbook-job-output.png)  
8.  Sluit de blade Uitvoer.
9.  Klik op **Alle logboeken** om de blade Streams voor de runbooktaak te openen.  We zouden alleen *Hallo wereld* moeten zien in de uitvoerstroom, maar er kunnen ook andere stromen voor een runbooktaak worden weergegeven, zoals Uitgebreid en Fout als hiernaar wordt geschreven met het runbook.<br> ![Taakoverzicht](media/automation-first-runbook-graphical/runbook-job-AllLogs.png)
10. Sluit de blade Alle logboeken en de blade Taak om terug te gaan naar de blade MyFirstRunbook.
11. Klik op **Taken** om de blade Taken voor dit runbook te openen.  Hiermee worden alle taken weergegeven die met dit runbook zijn gemaakt. We zouden slechts één weergegeven taak moeten zien, aangezien we de taak slechts eenmaal hebben uitgevoerd.<br> ![Taken](media/automation-first-runbook-graphical/runbook-control-jobs.png)
12. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat we hebben bekeken toen we het runbook startten.  Hiermee kunt u teruggaan in de tijd en de details bekijken van elke taak die voor een bepaald runbook is gemaakt.

## Stap 5: variabele assets maken

We hebben ons runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. We willen dat er Azure-resources mee worden beheerd.  Voordat we het runbook configureren voor verificatie, maken we een variabele die het abonnements-id moet bevatten en verwijzen we hiernaar nadat we de activiteit hebben ingesteld om te verifiëren in stap 6 hieronder.  Door een verwijzing naar de abonnementscontext op te nemen, kunt u eenvoudig werken met verschillende abonnementen.  Voordat u verder gaat, kopieert u uw abonnements-id uit de optie Abonnementen in het deelvenster Navigatie.  

1. Klik op de blade Automation-accounts op de tegel **Assets**. De blade **Assets** wordt geopend.
2. Klik op de blade Assets op de tegel **Variabelen**.
3. Klik op de blade Variabelen op **Een variabele toevoegen**.<br>![Automation-variabele](media/automation-first-runbook-graphical/create-new-subscriptionid-variable.png)
4. Voer op de blade Nieuwe variabele, in het vak **Naam**, **AzureSubscriptionId** in en voer in het vak **Waarde** uw abonnements-id in.  Behoud *tekenreeks* voor het **type** en de standaardwaarde voor **Versleuteling**.  
5. Klik op **Maken** om de variabele te maken.  


## Stap 6: verificatie toevoegen voor het beheren van Azure-resources

Nu we een variabele hebben die onze abonnements-id kan bevatten, kunnen we ons runbook configureren voor verificatie met de Uitvoeren als-referenties waarnaar wordt verwezen in de [vereisten](#prerequisites).  Dit doen we door de Uitvoeren als-verbinding voor Azure **Asset** en de cmdlet **Add-AzureRMAccount** toe te voegen aan het papier.  

1.  Open de grafische editor door te klikken op **Bewerken** op de blade MyFirstRunbook.<br> ![Runbook bewerken](media/automation-first-runbook-graphical/runbook-controls-edit-revised20165.png)
2.  We hebben **Schrijf Hallo Wereld naar uitvoer** niet meer nodig, dus klik er met de rechtermuisknop op en selecteer **Verwijderen**.
3.  Vouw in het besturingselement Bibliotheek **Verbindingen** uit en voeg **AzureRunAsConnection** toe aan het papier door **Toevoegen aan papier** te selecteren.
4.  Selecteer op het papier **AzureRunAsConnection** en typ in het besturingselementvenster Configuratie **Uitvoeren als-verbinding ophalen** in het tekstvak **Label**.  Dit is de verbinding. 
5.  Typ in het besturingselement Bibliotheek **Add-AzureRmAccount** in het zoektekstvak.
6.  Voeg **Add-AzureRmAccount** toe aan het papier.<br> ![Add-AzureRMAccount](media/automation-first-runbook-graphical/search-powershell-cmdlet-addazurermaccount.png)
7.  Beweeg de muisaanwijzer over **Uitvoeren als-verbinding ophalen** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar **Add-AzureRmAccount**.  De pijl die u zojuist hebt gemaakt, is een *koppeling*.  Het runbook wordt gestart met **Uitvoeren als-verbinding ophalen** en vervolgens wordt **Add-AzureRmAccount** uitgevoerd.<br> ![Koppeling tussen activiteiten maken](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
8.  Selecteer op het papier **Add-AzureRmAccount** en typ in het besturingselementvenster Configuratie **Aanmelden bij Azure** in het tekstvak **Label**.
9.  Klik op **Parameters**, waarna de blade Parameterconfiguratie van activiteit wordt weergegeven. 
10.  **Add-AzureRmAccount** bevat meerdere parametersets, dus moeten we er een selecteren voordat we parameterwaarden kunnen opgeven.  Klik op **Parameterset** en selecteer vervolgens de parameterset **ServicePrincipalCertificate**. 
11.  Zodra u de parameterset hebt geselecteerd, worden de parameters weergegeven op de blade Parameterconfiguratie van activiteit.  Klik op **APPLICATIONID**.<br> ![Parameters voor Azure RM-account toevoegen](media/automation-first-runbook-graphical/add-azurermaccount-parameterset.png)
12.  Selecteer op de blade Parameterwaarde de optie **Uitvoer van activiteit** voor de **gegevensbron** en selecteer **Uitvoeren als-verbinding ophalen** in de lijst. In het tekstvak **Pad naar veld** typt u **ApplicationId** en vervolgens klikt u op **OK**.  We geven de naam van de eigenschap voor het pad naar het veld op omdat de uitvoer van de activiteit een object met meerdere eigenschappen bevat.
13.  Klik op **CERTIFICATETHUMBPRINT** en selecteer op de blade Parameterwaarde de optie **Uitvoer van activiteit** voor de **gegevensbron**.  Selecteer **Uitvoeren als-verbinding ophalen** in de lijst, typ in het tekstvak **Pad naar veld** **CertificateThumbrprint** en klik vervolgens op **OK**. 
14.  Klik op **SERVICEPRINCIPAL** en selecteer op de blade Parameterwaarde **ConstantValue** voor de **gegevensbron**, klik op de optie **Waar** en klik vervolgens op **OK**.
15.  Klik op **TENANTID** en selecteer op de blade Parameterwaarde de optie **Uitvoer van activiteit** voor de **gegevensbron**.  Selecteer **Uitvoeren als-verbinding ophalen** in de lijst, typ in het tekstvak **Pad naar veld** **TenantId** en klik vervolgens tweemaal op **OK**.  
16.  Typ in het besturingselement Bibliotheek **Set-AzureRmContext** in het zoektekstvak.
17.  Voeg **Set-AzureRmContext** toe aan het papier.
18.  Selecteer op het papier **Set-AzureRmContext** en typ in het besturingselementvenster Configuratie **Abonnements-id opgeven** in het tekstvak **Label**.
19.  Klik op **Parameters**, waarna de blade Parameterconfiguratie van activiteit wordt weergegeven. 
20. **Set-AzureRmContext** bevat meerdere parametersets, dus moeten we er een selecteren voordat we parameterwaarden kunnen opgeven.  Klik op **Parameterset** en selecteer vervolgens de parameterset **SubscriptionId**.  
21.  Zodra u de parameterset hebt geselecteerd, worden de parameters weergegeven op de blade Parameterconfiguratie van activiteit.  Klik op **SubscriptionID**.
22.  Selecteer op de blade Parameterwaarde de optie **Variabeleasset** voor de **gegevensbron** en selecteer **AzureSubscriptionId** in de lijst en klik vervolgens tweemaal op **OK**.   
23.  Beweeg de muisaanwijzer over **Aanmelden bij Azure** totdat een cirkel wordt weergegeven aan de onderkant van de vorm. Klik op de cirkel en sleep de pijl naar **Abonnements-id opgeven**.


Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-auth-config.png)

## Stap 7: activiteit toevoegen om een virtuele machine te starten

We voegen nu een activiteit **Start-AzureRmVM** toe om een virtuele machine te starten.  U kunt elke virtuele machine in uw Azure-abonnement selecteren en voorlopig hardcoderen we deze naam in de cmdlet.

1. Typ in het besturingselement Bibliotheek **Start-AzureRm** in het zoektekstvak.
2. Voeg **Start-AzureRmVM** toe aan het papier, klik erop en sleep het onder **Abonnements-id opgeven**.
3. Beweeg de muisaanwijzer over **Abonnements-id opgeven** totdat een cirkel wordt weergegeven aan de onderkant van de vorm.  Klik op de cirkel en sleep de pijl naar **Start-AzureRmVM**. 
4.  Selecteer **Start-AzureRmVM**.  Klik op **Parameters** en vervolgens op **Parameterset** om de sets voor **Start-AzureRmVM** weer te geven.  Selecteer de parameterset **ResourceGroupNameParameterSetName**. Naast **ResourceGroupName** en **Name** staan uitroeptekens.  Hiermee wordt aangegeven dat dit vereiste parameters zijn.  Voor beide parameters worden tekenreekswaarden verwacht.
5.  Selecteer **Name**.  Selecteer **PowerShell-expressie** voor de **gegevensbron** en typ de naam van de virtuele machine tussen dubbele aanhalingstekens die we met dit runbook gaan starten.  Klik op **OK**.<br>![Parameterwaarde naam Start-AzureRmVM](media/automation-first-runbook-graphical/runbook-startvm-nameparameter.png)
6.  Selecteer **ResourceGroupName**. Gebruik **PowerShell-expressie** voor de **gegevensbron** en typ de naam van de resourcegroep tussen dubbele aanhalingstekens.  Klik op **OK**.<br> ![Parameters Start-AzureRmVM](media/automation-first-runbook-graphical/startazurermvm-params.png)
8.  Klik op Testvenster zodat we het runbook kunnen testen.
9.  Klik op **Start** om de test te starten.  Nadat deze is voltooid, controleert u of de virtuele machine is gestart.

Uw runbook zou er op dit punt als volgt moeten uitzien: <br>![Configuratie runbookverificatie](media/automation-first-runbook-graphical/runbook-startvm.png)

## Stap 8: aanvullende invoerparameters toevoegen aan het runbook

Met ons runbook wordt momenteel de virtuele machine gestart in de resourcegroep die we hebben opgegeven in de cmdlet **Start-AzureRmVM**, maar ons runbook zou nuttiger zijn als we beide konden opgeven wanneer het runbook wordt gestart.  We voegen nu invoerparameters aan het runbook toe om deze functionaliteit te bieden.

1. Open de grafische editor door te klikken op **Bewerken** in het deelvenster **MyFirstRunbook**.
2. Klik op **Invoer en uitvoer** en vervolgens op **Invoer toevoegen** om het deelvenster Invoerparameter van runbook te openen.<br> ![Invoer en uitvoer van runbook](media/automation-first-runbook-graphical/runbook-toolbar-InputandOutput-revised20165.png)
3. Geef *VMName* op voor de **Naam**.  Behoud *tekenreeks* voor het **type**, maar wijzig **Verplicht** in *Ja*.  Klik op **OK**.
4. Maak een tweede verplichte invoerparameter met de naam *ResourceGroupName* en klik vervolgens op **OK** om het venster **Invoer en uitvoer** te sluiten.<br> ![Invoerparameters van runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
5. Selecteer de activiteit **Start-AzureRmVM** en klik vervolgens op **Parameters**.
6. Wijzig de **gegevensbron** voor **Name** in **Runbookinvoer** en selecteer vervolgens **VMName**.<br>
7. Wijzig de **gegevensbron** voor **ResourceGroupName** in **Runbookinvoer** en selecteer vervolgens **ResourceGroupName**.<br> ![Parameters Start-AzureVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
8. Sla het runbook op en open het testvenster.  U kunt nu waarden opgeven voor de twee invoervariabelen die in de test worden gebruikt.
9. Sluit het testvenster.
10. Klik op **Publiceren** om de nieuwe versie van het runbook te publiceren.
11. Stop de virtuele machine die u in de vorige stap hebt gestart.
12. Klik op **Starten** om het runbook te starten.  Typ waarden voor **VMName** en **ResourceGroupName** voor de virtuele machine die u wilt starten.<br> ![Runbook starten](media/automation-first-runbook-graphical/runbook-start-inputparams.png)
13. Wanneer het runbook is voltooid, controleert u of de virtuele machine is gestart.

## Stap 9: een voorwaardelijke koppeling maken

We zullen het runbook nu wijzigen zodat alleen wordt geprobeerd de virtuele machine te starten als deze nog niet is gestart.  We doen dit door een cmdlet **Get-AzureRmVM** toe te voegen aan het runbook waarmee de exemplaarniveaustatus van de virtuele machine wordt opgehaald. We voegen vervolgens een PowerShell Workflow-codemodule genaamd **Status ophalen** toe met een PowerShell-codefragment om te bepalen of de status van de virtuele machine 'actief' of 'gestopt' is.  Met een voorwaardelijke koppeling van de module **Status ophalen** wordt **Start-AzureRmVM** alleen uitgevoerd als de huidige uitvoeringsstatus 'gestopt' is.  Ten slotte zorgen we dat een bericht wordt weergegeven waarin staat of de virtuele machine is gestart of niet, met de PowerShell-cmdlet Write-Output.

1. Open **MyFirstRunbook** in de grafische editor.
2. Verwijder de koppeling tussen **Abonnements-id opgeven** en **Start-AzureRmVM** door erop te klikken en vervolgens te drukken op de toets *Delete*.
3. Typ in het besturingselement Bibliotheek **Get-AzureRm** in het zoektekstvak.
4. Voeg **Get-AzureRmVM** toe aan het papier.
5. Selecteer **Get-AzureRmVM** en vervolgens **Parameterset** om de sets voor **Get-AzureRmVM** weer te geven.  Selecteer de parameterset **GetVirtualMachineInResourceGroupNameParamSet**.  Naast **ResourceGroupName** en **Name** staan uitroeptekens.  Hiermee wordt aangegeven dat dit vereiste parameters zijn.  Voor beide parameters worden tekenreekswaarden verwacht.
6. Selecteer onder **Gegevensbron** voor **Name** de optie **Runbookinvoer** en selecteer vervolgens **VMName**.  Klik op **OK**.
7. Selecteer onder **Gegevensbron** voor **ResourceGroupName** de optie **Runbookinvoer** en selecteer vervolgens **ResourceGroupName**.  Klik op **OK**.
8. Selecteer onder **Gegevensbron** voor **Status** de optie **Constante waarde** en klik vervolgens op **Waar**.  Klik op **OK**.  
9. Maak een koppeling van **Abonnements-id opgeven** naar **Get-AzureRmVM**.
10. Vouw in het besturingselement Bibliotheek **Runbookbesturing** uit en voeg **Code** toe aan het papier.  
11. Maak een koppeling van **Get-AzureRmVM** naar **Code**.  
12. Klik op **Code** en wijzig in het deelvenster Configuratie het label in **Status ophalen**.
13. Selecteer de parameter **Code**, waarna de blade **Code-editor** wordt weergegeven.  
14. Plak het volgende codefragment in de code-editor:

     ```
     $StatusesJson = $ActivityOutput['Get-AzureRmVM'].StatusesText 
     $Statuses = ConvertFrom-Json $StatusesJson 
     $StatusOut ="" 
     foreach ($Status in $Statuses){ 
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"} 
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"} 
     } 
     $StatusOut 
     ```

15. Maak een koppeling van **Status ophalen** naar **Start-AzureRmVM**.<br> ![Runbook met codemodule](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
16. Selecteer de koppeling en wijzig in het deelvenster Configuratie **Voorwaarde toepassen** in **Ja**.   De koppeling wordt een gestreepte lijn waarmee wordt aangegeven dat de doelactiviteit alleen wordt uitgevoerd als de voorwaarde 'waar' is.  
17. Voor **Expressie van voorwaarde** typt u *$ActivityOutput['Get Status'] -eq "Stopped"*.  **Start-AzureRmVM** wordt nu alleen uitgevoerd als de virtuele machine is gestopt.
18. Vouw in het besturingselement Bibliotheek **Cmdlets** uit en vervolgens **Microsoft.PowerShell.Utility**.
19. Voeg **Write-Output** tweemaal aan het papier toe.<br> ![Runbook met Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
20. In het eerste besturingselement **Write-Output** wijzigt u de waarde voor **Label** in *VM gestart melden*.
21. Voor **InputObject** wijzigt u **Gegevensbron** in **PowerShell-expressie** en typt u de expressie *"$VMName successfully started."*.
22. In het tweede besturingselement **Write-Output** wijzigt u de waarde voor **Label** in *Starten VM mislukt melden*.
23. Voor **InputObject** wijzigt u **Gegevensbron** in **PowerShell-expressie** en typt u de expressie *"$VMName could not start."*.
24. Maak een koppeling van **Start-AzureRmVM** naar **VM gestart melden** en **Starten VM mislukt melden**.
25. Selecteer de koppeling naar **VM gestart melden** en wijzig **Voorwaarde toepassen** in **Waar**.
26. Voor **Expressie van voorwaarde** typt u *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true*.  Dit besturingselement Write-Output wordt nu alleen uitgevoerd als de virtuele machine is gestart.
27. Selecteer de koppeling naar **Starten VM mislukt melden** en wijzig **Voorwaarde toepassen** in **Waar**.
28. Voor **Expressie van voorwaarde** typt u *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true*.  Dit besturingselement Write-Output wordt nu alleen uitgevoerd als de virtuele machine niet is gestart. 
29. Sla het runbook op en open het testvenster.
30. Start het runbook als de virtuele machine is gestopt; deze zou moeten starten.

## Volgende stappen

-   Zie voor meer informatie over grafisch ontwerpen [Grafisch ontwerpen in Azure Automation](automation-graphical-authoring-intro.md)
-   Zie [Mijn eerste PowerShell-runbook](automation-first-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks
-   Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks



<!--HONumber=Jun16_HO2-->


