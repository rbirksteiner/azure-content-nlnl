
Volg deze stappen als u een nieuwe mobiele service wilt maken.

1.  Meld u aan bij de [klassieke Azure-portal](https://manage.windowsazure.com/). Klik onder aan het navigatievenster op **+Nieuw**. Vouw **Berekenen** uit en **Mobile Service** en klik vervolgens op **Maken**.
    
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png)

    Nu wordt het dialoogvenster **Een mobiele service maken** weergegeven.

2.  Op de pagina **Een mobiele service maken** selecteert u **Een nieuwe gratis SQL Database van 20 MB maken**, selecteert u **.NET**-runtime en typt u vervolgens een subdomeinnaam voor de nieuwe mobiele service in het tekstvak **URL**. Klik op de knop Pijl-rechts om naar de volgende pagina te gaan.
    
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png)

    De pagina **Database-instellingen opgeven** wordt nu weergegeven.

    > [AZURE.NOTE] Als onderdeel van deze zelfstudie maakt u een nieuw exemplaar van SQL Database en van de server. U kunt deze nieuwe database opnieuw gebruiken en deze beheren zoals u dat bij een andere instantie van SQL Database zou doen. Als u al een database in dezelfde regio hebt als de nieuwe mobiele service, kunt u in plaats daarvan **Bestaande database gebruiken** kiezen en vervolgens die database kiezen. Het gebruik van een database in een andere regio wordt niet aanbevolen vanwege de extra bandbreedtekosten en hogere latenties.

3.  Typ bij **Naam** de naam van de nieuwe database en typ vervolgens de **aanmeldingsnaam** (dit is de aanmeldingsnaam van de beheerder voor de nieuwe SQL Database-server), typ en bevestig het wachtwoord en klik op de knop met het vinkje om het proces te voltooien.
    ![](./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png)

U hebt nu een nieuwe mobiele service gemaakt die door uw mobiele apps kan worden gebruikt.



<!--HONumber=Jun16_HO2-->


