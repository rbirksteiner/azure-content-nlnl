<properties
   pageTitle="Inzichten verkrijgen van Azure Security Center-gegevens met Power BI| Microsoft Azure"
   description="U kunt met het Power BI-inhoudspakket van Azure Security Center gemakkelijk beveiligingswaarschuwingen, aanbevelingen, aangevallen resources en trends vinden op basis van een gegevensset die voor uw rapportage is gemaakt."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Inzichten verkrijgen van Azure Security Center-gegevens met Power BI
U kunt met het [Power BI-dashboard](http://aka.ms/azure-security-center-power-bi) voor Azure Security Center aanbevelingen en beveiligingswaarschuwingen vanaf elke locatie, inclusief uw mobiele apparaat, visualiseren, analyseren en filteren. Gebruik het Power BI-dashboard om trends en aanvalspatronen zichtbaar te maken: bekijk beveiligingswaarschuwingen op resource of bron-IP-adres en niet-opgeloste beveiligingsrisico's op resource of ouderdom. U kunt Security Center-aanbevelingen en -beveiligingswaarschuwingen ook op interessante manieren verfijnen met andere gegevens, bijvoorbeeld met [auditlogboeken van Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) en [Azure SQL Database Auditing](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/) die beide Power BI-dashboards bieden, of u kunt deze gegevens exporteren naar Excel voor eenvoudige rapportage over de beveiligingsstatus van uw cloudresources.

> [AZURE.NOTE] De informatie in dit document is van toepassing op de preview-versie van Azure Security Center.


##Het Azure Security Center-dashboard gebruiken voor toegang tot Power BI
U kunt het Azure Security Center-dashboard ook gebruiken voor toegang tot Power BI-rapporten. Volg de onderstaande stappen om deze taak uitvoeren: 

1. Klik in het **Azure Security Center**-dashboard op de knop **Verkennen in Power BI**.

    ![Verbinding maken met Azure Security Center via Power BI](./media/security-center-powerbi/security-center-powerbi-fig9-new.png) 

2. De blade **Verkennen in Power BI** wordt geopend aan de rechterkant zoals hieronder wordt weergegeven:

    ![Verbinding maken met Azure Security Center via Power BI](./media/security-center-powerbi/security-center-powerbi-fig2-new.png)

3. Als u het Power BI-dashboard voor het eerst maakt, kunt u een van de onderstaande opties in de blade Verkennen in Power BI kiezen: 

    - **Security insights dashboard** (Dashboard voor beveiligingsinzichten): kies deze optie als u een dashboard wilt maken dat de beveiligingsstatus, threads en detecties bevat. Dit is een meer algemene optie voor personen met de DevOps-rol die verantwoordelijk zijn voor het analyseren van de beveiligingsstatus en gedetecteerde waarschuwingen voor alle abonnementen.
    - **Policy management dashboard** (Dashboard voor beleidsbeheer): kies deze optie als u het beleid voor beheer en afdwinging wilt verkennen.  Dit is een meer algemene optie voor de centrale IT-afdeling die meer gericht is op governance. Dit dashboard kan worden gebruikt om inzicht te krijgen in de naleving van het beveiligingsbeleid binnen de organisatie.
    - Als u al een Power BI-dashboard hebt, klikt u op **Go to your current Power BI dashboard** (Ga naar uw huidige Power BI-dashboard).

4. Klik bij dit voorbeeld op **Dashboard voor beveiligingsinzichten** zodat het onderstaande venster wordt weergegeven:

    ![Dashboard voor beveiligingsinzichten van Azure Security Center](./media/security-center-powerbi/security-center-powerbi-fig3-new.png)

5. Zorg ervoor dat de **verificatiemethode** is ingesteld op **oAuth2** en klik op **Aanmelden**.
6. Het venster **Power BI** wordt geopend en u ziet een rapport met een structuur die vergelijkbaar is met de structuur hieronder:
    
    ![Dashboard voor beveiligingsinzichten](./media/security-center-powerbi/security-center-powerbi-fig5.png)

> [AZURE.NOTE] Het rapport wordt volgens planning dagelijks vernieuwd. Als er een fout bij het vernieuwen optreedt, leest u [Potential Refresh Issues with the Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) (Mogelijke problemen bij het vernieuwen met Azure Security Center Power BI) voor meer informatie over het oplossen van problemen.

Hier ziet u het aantal beveiligingswaarschuwingen en aanbevelingen, evenals het aantal VM's, Azure SQL-databases en netwerkresources die worden bewaakt door Azure Security Center.

U wordt via een koppeling naar Azure Security Center doorgestuurd naar Azure Portal. De grafieken maken het gemakkelijk informatie over beveiligingsaanbevelingen en -waarschuwingen te visualiseren, waaronder:

- Beveiligingsstatus van de resource
- Pending Recommendations Overall (Alle in behandeling zijnde aanbevelingen)
- VM Recommendations (Aanbevelingen voor VM's)
- Waarschuwingen gedurende een periode
- Aangevallen resources
- Aangevallen IP-adressen

Achter elke grafiek wordt meer inzicht geboden. Selecteer een tegel voor meer informatie. Zo kunt u met de tegel Beveiligingsstatus van de resource aanvullende details over in behandeling zijnde aanbevelingen op resources bekijken (zie hieronder):

![Aanbevelingen](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Als u op een lijn van deze grafiek klikt, worden de andere lijnen lichter gekleurd weergegeven en kunt u uw aandacht op de geselecteerde lijn richten. Als u wilt terugkeren naar het dashboard, klikt u op **Azure Security Center** onder de optie **Dashboards** in het linkerdeelvenster van deze pagina.

> [AZURE.NOTE] Als u de rapporten wilt aanpassen door extra velden toe te voegen of bestaande visuele elementen te wijzigen, kunt u het rapport bewerken. Zie [Interact with a report in Editing View in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) (Met een rapport in de bewerkingsweergave in Power BI werken) voor meer informatie.

De tegels **Waarschuwingen gedurende een periode, Aangevallen resources** en **Attacker IPs** (IP-adressen van aanvallers) hebben vergelijkbare uitvoer wanneer u op de tegels klikt. Dit komt doordat het rapport de informatie met betrekking tot deze drie variabelen samenvoegt en deze **Resources die worden aangevallen** noemt (zie hieronder):

![Resources die worden aangevallen](./media/security-center-powerbi/security-center-powerbi-fig7.png)

Op dit punt kunt u ook een kopie van het rapport opslaan of het rapport afdrukken of op het web publiceren met de beschikbare opties in het menu **Bestand**.

![Menu Bestand](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## Uw Azure Security Center-gegevens verkennen met Power BI-services

Maak verbinding met de [Power BI-inhoudspakketservices](https://msit.powerbi.com/groups/me/getdata/services) in Power BI en voer de volgende stappen uit:

1. In het venster **Inhoudspakket voor Power BI** ziet u twee opties zoals hieronder wordt weergegeven.

    ![Inhoudspakket voor Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

2. In dit voorbeeld klikt u op **Ophalen** in de tegel **Azure Security Center-beleidsbeheer**.

3. Selecteer in het venster **Verbinding maken met Azure Security Center-beleidsbeheer** de optie **oAuth2** in de vervolgkeuzelijst **Verificatiemethode** (zie hieronder) en klik op de knop **Aanmelden**.

    ![Venster Beleidsbeheer](./media/security-center-powerbi/security-center-powerbi-fig4-new.png)

4. U wordt omgeleid naar een verificatiepagina waar u de referenties moet typen die u gebruikt om verbinding te maken met Azure Security Center. Nadat het verificatieproces is voltooid, importeert Power BI gegevens om uw rapporten samen te stellen. Gedurende deze tijd wordt mogelijk het volgende bericht weergegeven in de rechterhoek van uw browser:

    ![Verbinding maken met Azure Security Center via Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Wanneer het dashboard voor het eerst wordt gemaakt, duurt het langer dan normaal, met name wanneer u meerdere abonnementen hebt. 

5. Als het proces is voltooid, wordt het rapport **Beleidsbeheer** in uw Power BI-dashboard van Azure Security Center geladen.


## Volgende stappen
In dit document hebt u kunnen lezen hoe u Power BI in Azure Security Center gebruikt. Zie de volgende onderwerpen voor meer informatie over Azure Security Center:

- [Plannings- en bedieningsgids voor het Azure Beveiligingscentrum](security-center-planning-and-operations-guide.md): leer hoe u de overstap naar Azure Security Center kunt plannen.
- [Beveiligingsbeleid instellen in Azure Security Center](security-center-policies.md): leer hoe u beveiligingsinstellingen configureert in Azure Security Center
- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren
- [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service
- [Azure Security Blog](http://blogs.msdn.com/b/azuresecurity/) (Azure-beveiligingsblog): lees blogberichten over de beveiliging en naleving van Azure



<!--HONumber=Jun16_HO2-->


