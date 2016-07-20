1.  Meld u online aan bij [Microsoft Azure Portal](https://portal.azure.com/).
2.  Klik in de snelbalk op **Nieuw**, klik vervolgens op **Gegevens en opslag**, en klik vervolgens op **Azure DocumentDB**.

    ![Schermopname van de Azure Portal voor het maken van een database, waarbij de knop Nieuw, Gegevens en opslag in de blade Maken en Azure DocumentDB in de blade Gegevens zijn gemarkeerd](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. In de blade **Nieuw DocumentDB-account** geeft u de gewenste configuratie op voor het DocumentDB-account.

    ![Schermopname van de nieuwe DocumentDB-blade](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


    - In het vak **ID** typt u de naam die het DocumentDB-account aanduidt.  Wanneer de **id** is gevalideerd, verschijnt een groen vinkje in het vak **ID**. De **ID**-waarde wordt de hostnaam binnen de URI. De **id** mag alleen kleine letters, cijfers en het minteken ('-') bevatten, en moet tussen 3 en 50 tekens zijn. Houd er rekening mee dat *documents.azure.com* wordt toegevoegd aan de eindpuntnaam die u kiest. Het resultaat hiervan wordt het eindpunt van uw DocumentDB-account.

    - Selecteer als **Abonnement** het Azure-abonnement dat u voor het DocumentDB-account wilt gebruiken. Als uw account slechts één abonnement heeft, wordt dit account standaard geselecteerd.

    - In **Resourcegroep** selecteert of maakt u een resourcegroep voor uw DocumentDB-account.  Standaard wordt een bestaande resourcegroep onder het Azure-abonnement gekozen.  U kunt er echter voor kiezen om een nieuwe resourcegroep te maken waaraan u uw DocumentDB-account wilt toevoegen. Zie [De Azure Portal gebruiken om uw Azure-resources te beheren](resource-group-portal.md) voor meer informatie.

    - Gebruik **Locatie** om de geografische locatie op te geven waar uw DocumentDB-account moet worden gehost.   

4.  Zodra de opties voor het nieuwe DocumentDB-account zijn geconfigureerd, klikt u op **Maken**.  Het kan een paar minuten duren voordat het DocumentDB-account is gemaakt.  Als u de status wilt zien, kunt u de voortgang via het startboard bekijken.  
    ![Schermopname van de tegel Maken op het startboard - Onlinedatabase maken](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)  

    Of u kunt de voortgang via Notification Hubs bekijken.  

    ![Snel databases maken: schermopname van Notification Hubs, waarop wordt weergegeven dat het DocumentDB-account wordt gemaakt](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Schermopname van Notification Hubs, waarop wordt weergegeven dat het DocumentDB-account is gemaakt en naar een resourcegroep is geïmplementeerd - melding van Onlinedatabase maken](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  Nadat het DocumentDB-account is gemaakt, kan het meteen met de standaardinstellingen in de onlineportal worden gebruikt. Merk op dat de standaardconsistentie van de DocumentDB-account is ingesteld op **Sessie**.  U kunt de standaardinstelling voor consistentie aanpassen door op het pictogram **Instellingen** op de bovenste opdrachtbalk te klikken, klik vervolgens op het item **Standaardconsistentie** onder **Functie** in de blade **Alle instellingen**.

    ![Schermopname van de blade Resourcegroep - begin met het ontwikkelen van de toepassing](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Schermopname van de blade Consistentieniveau - sessie Consistentie](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[Procedure: een DocumentDB-account maken]: #Howto
[Volgende stappen]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md



<!--HONumber=Jun16_HO2-->


