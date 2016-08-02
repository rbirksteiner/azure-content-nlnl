<properties 
    pageTitle="Functionaliteit toevoegen aan uw eerste web-app" 
    description="Voeg in een paar minuten handige functies toe aan uw eerste web-app." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin" 
    manager="wpickett" 
    editor="" 
/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="hero-article"
    ms.date="05/12/2016" 
    ms.author="cephalin"
/>

# Functionaliteit toevoegen aan uw eerste web-app

In [In vijf minuten een web-app implementeren in Azure](app-service-web-get-started.md) hebt u een voorbeeld-web-app geïmplementeerd in [Azure App Service](../app-service/app-service-value-prop-what-is.md). In dit artikel gaat u snel een aantal handige functies aan uw geïmplementeerde web-app toevoegen. U gaat zo dadelijk het volgende doen:

- verificatie voor uw gebruikers afdwingen
- uw app automatisch schalen
- meldingen ontvangen over de prestaties van uw app

Het maakt niet uit welke voorbeeld-app u in het vorige artikel hebt geïmplementeerd. U kunt deze zelfstudie altijd volgen.

De drie activiteiten in deze zelfstudie zijn slechts enkele voorbeelden van de vele handige voorzieningen waarover u kunt beschikken wanneer u uw web-app in App Service plaatst. Veel functies zijn beschikbaar in de categorie **Gratis** (dit is de laag waarop uw eerste web-app wordt uitgevoerd) en u kunt het tegoed van uw proefabonnement gebruiken om functies uit te proberen die een hogere prijscategorie vereisen. Uw web-app blijft echter in de categorie **Gratis** staan, tenzij u deze expliciet wijzigt in een andere prijscategorie.

>[AZURE.NOTE] De web-app die u met Azure CLI hebt gemaakt, wordt uitgevoerd in de laag **Gratis**. Deze staat slechts één gedeelde virtuele machine met resourcequota toe. Zie [App Service-beperkingen](../azure-subscription-service-limits.md#app-service-limits) voor meer informatie over wat u in de categorie **Gratis** krijgt.

## Uw gebruikers verifiëren

Nu gaan we kijken hoe gemakkelijk het is om verificatie toe te voegen aan uw app (lees hier meer over in [App Service-verificatie/autorisatie](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)).

1. Op de portalblade voor uw app, die u zojuist hebt geopend, klikt u op **Instellingen** > **Verificatie/autorisatie**.  
    ![Verificatie - blade Instellingen](./media/app-service-web-get-started/aad-login-settings.png)
    
2. Klik op **Aan** om verificatie in te schakelen.  
    
4. Klik in **Verificatieproviders** op **Azure Active Directory**.  
    ![Verificatie - Azure AD selecteren](./media/app-service-web-get-started/aad-login-config.png)

5. Klik op de blade **Azure Active Directory-instellingen** op **Express** en vervolgens op **OK**. Met de standaardinstellingen maakt u een nieuwe Azure AD-toepassing in de standaardmap.  
 ![Verificatie - express-configuratie](./media/app-service-web-get-started/aad-login-express.png)

6. Klik op **Opslaan**.  
    ![Verificatie - configuratie opslaan](./media/app-service-web-get-started/aad-login-save.png)

    Wanneer de wijziging is voltooid, ziet u dat de meldingenbel groen is en een bericht bevat.

7. Klik op de portalblade van de app op de **URL**-koppeling (of op **Bladeren** in de menubalk). De koppeling is een HTTP-adres.  
    ![Verificatie - bladeren naar de URL](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Zodra de app op een nieuw tabblad is geopend, verandert de inhoud van het URL-vak echter een aantal keer tot uiteindelijk het HTTPS-adres van uw app wordt weergegeven. Wat u ziet, is dat u al bent aangemeld bij uw Azure-abonnement en dat u automatisch bent geverifieerd in de app.  
    ![Verificatie - aangemeld](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Dus als u nu in een andere browser een niet-geverifieerde sessie opent, ziet u een aanmeldingsscherm als u naar dezelfde URL navigeert.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
    Als u nooit eerder iets met Azure Active Directory hebt gedaan, bevat de standaardmap wellicht geen Azure AD-gebruikers. In dat geval is het enige account dat u daar ziet, waarschijnlijk het Microsoft-account van uw Azure-abonnement. Daarom bent u eerder in dezelfde browser automatisch aangemeld bij de app. U kunt hetzelfde Microsoft-account gebruiken om u op deze aanmeldingspagina aan te melden.

Gefeliciteerd, u verifieert nu al het verkeer naar uw web-app.

Misschien hebt u op de blade **Verificatie/autorisatie** al opgemerkt dat u veel meer kunt doen, zoals:

- Aanmelden bij sociale media inschakelen
- Opties voor meerdere aanmeldingen inschakelen
- Het standaardgedrag wijzigen wanneer gebruikers de eerste keer naar uw app navigeren

App Service biedt een kant-en-klare oplossing voor een aantal algemene verificatiebehoeften. U hoeft de verificatielogica dus niet zelf te verstrekken. Zie [App Service-verificatie/autorisatie](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/) voor meer informatie.

## Uw app automatisch schalen op basis van de vraag

Nu gaan we de app automatisch schalen, zodat de capaciteit automatisch wordt aangepast aan de behoeften van de gebruiker (lees hier meer over in [Prijscategorie schalen in Azure App Service](app-service-scale) en [Aantal exemplaren handmatig of automatisch schalen](../azure-portal/insights-how-to-scale.md)). 

Kort samengevat kunt u een web-app op twee manieren schalen:

- [Omhoog schalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): het verkrijgen van meer CPU, geheugen, schijfruimte en extra functies, zoals toegewezen virtuele machines, aangepaste domeinen en certificaten, faseringssites, automatisch schalen en meer. U kunt uitschalen door de prijscategorie te wijzigen van het App Service-plan waartoe uw app behoort.
- [Uitschalen](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): het verhogen van het aantal virtuele machines waarop uw app wordt uitgevoerd.
Afhankelijk van uw prijscategorie kunt u uitschalen naar maximaal 50 exemplaren.

We laten de details verder achterwege en gaan automatisch schalen instellen.

1. Eerst schalen we omhoog om automatisch schalen in te schakelen. Klik op de portalblade van uw app op **Instellingen** > **Omhoog schalen (App Service-plan)**.  
    ![Omhoog schalen - blade Instellingen](./media/app-service-web-get-started/scale-up-settings.png)

2. Blader naar en selecteer de categorie **S1-standaard**, de laagste categorie die automatisch schalen ondersteunt (omcirkeld in de schermafbeelding) en klik op **Selecteren**.  
    ![Omhoog schalen - categorie kiezen](./media/app-service-web-get-started/scale-up-select.png)

    U bent klaar met omhoog schalen.
    
    >[AZURE.IMPORTANT] Deze categorie verbruikt tegoeden van uw gratis proefversie. Als u een betalen-naar-verbruik-account hebt, worden er accountkosten in rekening gebracht.
    
3. Nu gaan we automatisch schalen configureren. Klik op de portalblade van uw app op **Instellingen** > **Uitschalen (App Service-plan)**.  
    ![Uitschalen - blade Instellingen](./media/app-service-web-get-started/scale-out-settings.png)

4. Wijzig **Schalen op** in **CPU-percentage**. De schuifregelaars onder de vervolgkeuzelijst worden dienovereenkomstig bijgewerkt. Vervolgens definieert u een bereik van **Exemplaren** tussen **1** en **2** en een **Doelbereik** tussen **40** en **80**. Daartoe typt u een waarde in de vakken of verschuift u de schuifregelaars.  
 ![Uitschalen - automatisch schalen configureren](./media/app-service-web-get-started/scale-out-configure.png)
    
    Op basis van deze configuratie wordt uw app automatisch uitgeschaald wanneer het CPU-gebruik hoger is dan 80% en ingeschaald wanneer het CPU-gebruik lager is dan 40%. 
    
5. Klik in de menubalk op **Opslaan**.

Gefeliciteerd, uw app wordt nu automatisch geschaald.

Misschien hebt u op de blade **Schaalinstellingen** al opgemerkt dat u veel meer kunt doen, zoals:

- Handmatig schalen naar een specifiek aantal exemplaren
- Schalen op andere prestatiegegevens, zoals geheugenpercentage of wachtrij voor schijf
- Het schaalgedrag aanpassen wanneer er een prestatieregel wordt geactiveerd
- Automatisch schalen volgens een schema
- Gedrag van automatisch schalen instellen voor een toekomstige gebeurtenis

Zie [Prijscategorie schalen in Azure App Service](../app-service/app-service-scale.md) voor meer informatie over het omhoog schalen van uw app. Zie [Aantal exemplaren handmatig of automatisch schalen](../azure-portal/insights-how-to-scale.md) voor meer informatie over uitschalen.

## Waarschuwingen voor uw app ontvangen

Wat gebeurt er nu uw app automatisch wordt geschaald wanneer het maximumaantal exemplaren (2) wordt bereikt en het CPU-gebruik hoger is dan het gewenste verbruik (80%)? U kunt een waarschuwing instellen (lees hier meer over in [Meldingen van waarschuwingen ontvangen](../azure-portal/insights-receive-alert-notifications.md) als u een melding van deze situatie wilt ontvangen, zodat u de app verder kunt omhoog schalen of uitschalen. We gaan snel een waarschuwing instellen voor dit scenario.

1. Klik op de portalblade van uw app op **Hulpmiddelen** > **Waarschuwingen**.  
    ![Waarschuwingen - blade Instellingen](./media/app-service-web-get-started/alert-settings.png)

2. Klik op **Waarschuwing toevoegen**. Selecteer vervolgens in het vak **Resource** de resource die eindigt op **(serverfarms)**. Dit is uw App Service-plan.  
    ![Waarschuwingen - waarschuwing toevoegen voor App Service-plan](./media/app-service-web-get-started/alert-add.png)

3. In **Naam** geeft u `CPU Maxed` op, in **Metriek** kiest u **CPU-percentage** en in **Drempelwaarde** de optie `90`. Selecteer vervolgens **E-mailadressen van eigenaren, bijdragers en lezers** en klik op **OK**.   
 ![Waarschuwingen - waarschuwing configureren](./media/app-service-web-get-started/alert-configure.png)
    
    Wanneer de waarschuwing in Azure is gemaakt, wordt deze weergegeven op de blade **Waarschuwingen**.  
    ![Waarschuwingen - voltooid](./media/app-service-web-get-started/alert-done.png)

Gefeliciteerd, u krijgt nu waarschuwingen. 

Met deze waarschuwingsinstelling wordt het CPU-verbruik elke vijf minuten gecontroleerd. Als deze waarde hoger is dan 90%, ontvangen u en iedereen die is geautoriseerd, per e-mail een waarschuwing. Als u wilt weten wie er is geautoriseerd om waarschuwingen te ontvangen, gaat u terug naar de portalblade van uw app en klikt u op de knop **Toegang**.  
![Zien wie er waarschuwingen ontvangen](./media/app-service-web-get-started/alert-rbac.png)

Waarschijnlijk ziet u dat **Abonnementsbeheerders** al **Eigenaar** zijn van de app. Als u de accountbeheerder bent van uw Azure-abonnement (bijvoorbeeld uw proefabonnement), behoort u ook tot deze groep. Zie [Op rollen gebaseerd Azure-toegangsbeheer](../active-directory/role-based-access-control-configure.md) voor meer informatie over op rollen gebaseerd toegangsbeheer.

> [AZURE.NOTE] Waarschuwingsregels is een functie van Azure. Zie [Meldingen van waarschuwingen ontvangen](../azure-portal/insights-receive-alert-notifications.md) voor meer informatie. 

## Volgende stappen

Tijdens het configureren van de waarschuwing hebt u wellicht de uitgebreide set hulpprogramma's op de blade **Hulpmiddelen** gezien. Hier kunt u problemen oplossen, prestaties bewaken, testen op beveiligingsproblemen, resources beheren, communiceren met de VM-console en nuttige extensies toevoegen. Klik eens op de verschillende opties en bekijk de eenvoudige maar krachtige hulpprogramma's die u binnen handbereik hebt. 

Ontdek hoe u meer kunt doen met uw geïmplementeerde app. Dit zijn slechts enkele voorbeelden:

- [Een aangepaste domeinnaam kopen en configureren](custom-dns-web-site-buydomains-web-app.md): koop een aantrekkelijke domeinnaam voor uw web-app in plaats van het domein *.azurewebsites.net. Of gebruik een domein dat u al hebt.
- [Faseringsomgevingen instellen](web-sites-staged-publishing.md): implementeer uw app op een tijdelijke URL voordat u de app in productie neemt. Werk uw live web-app probleemloos bij. Stel een uitgebreide DevOps-oplossing in met meerdere implementatiesites. 
- [Continue implementatie instellen](web-sites-publish-source-control.md): integreer app-implementatie in uw bronbeheersysteem. Implementeer naar Azure met elke doorvoer.
- [Toegang tot on-premises resources](web-sites-hybrid-connection-get-started.md): maak gebruik van een bestaande on-premises database of een bestaand CRM-systeem.
- [Een back-up maken van uw app](web-sites-backup.md): stel voor uw web-app back-up- en herstelfunctionaliteit in. Bereid u voor op onverwachte problemen en het herstel daarvan.
- [Schakel diagnostische logboeken in](web-sites-enable-diagnostic-log.md): lees de IIS-logboeken vanuit Azure of toepassingstraceringen. Lees ze in een stream, download ze of plaats ze in [Application Insights](../application-insights/app-insights-overview.md) voor directe analyse.
- [Uw app scannen op beveiligingsproblemen](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
Scan uw web-app op actieve bedreigingen met behulp van de service die wordt geleverd door [Tinfoil Security](https://www.tinfoilsecurity.com/).
- [Achtergrondtaken uitvoeren](../azure-functions/functions-overview.md): voer taken uit voor gegevensverwerking, rapportage, enzovoort.
- [Ontdek hoe App Service werkt](../app-service/app-service-how-works-readme.md) 


<!--HONumber=Jun16_HO2-->


