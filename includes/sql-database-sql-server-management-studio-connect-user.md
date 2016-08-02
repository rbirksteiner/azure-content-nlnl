## Verbinding maken met Azure SQL Database als een gebruiker

Voer de volgende stappen uit als u verbinding wilt maken met Azure SQL Database met SSMS als een gebruiker.

1. Typ 'Microsoft SQL Server Management Studio' in het zoekvak van Windows en klik vervolgens op de bureaublad-app om SSMS te starten.

2. Voer in het venster Verbinding maken met server de volgende informatie in:

- **Servertype**: de standaardwaarde is database-engine; verander deze waarde niet.
 - **Servernaam**: voer in de volgende notatie de naam in van de server die als host fungeert voor de SQL-database: *&lt;servernaam>*.**database.Windows.NET**
 - **Verificatietype**: Als u nog maar net begint, selecteer dan SQL-verificatie. Als u Active Directory voor de logische SQL Database-server hebt ingeschakeld, kunt u Active Directory-wachtwoordverificatie of geïntegreerde Active Directory-verificatie selecteren.
 - **Gebruikersnaam**: als u SQL-verificatie of Active Directory-wachtwoordverificatie hebt geselecteerd, voert u de naam in van een gebruiker die toegang heeft tot een database op de server.
 - **Wachtwoord**: als u SQL-verificatie of Active Directory-wachtwoordverificatie hebt geselecteerd, voert u het wachtwoord in van de opgegeven gebruiker.
   
       ![SQL Server Management Studio: Connect to SQL Database server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

3. Klik op **Opties** om de database op te geven waarmee u verbinding wilt maken.

      ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
 
4. Selecteer in **Verbinding maken met database** de database die u wilt verbinden.

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)

5. Klik op **Verbinden**.
 
6. Als het IP-adres van de client geen toegang nodig heeft tot de logische SQL Database-server, wordt u gevraagd om u aan te melden bij een Azure-account en een firewallregel op serverniveau te maken. Als u de beheerder van een Azure-abonnement bent, klikt u op **Aanmelden** om een firewallregel op serverniveau te maken. Als dat niet het geval is, vraagt u een beheerder om een firewallregel op serverniveau te maken of een firewallregel op databaseniveau in de database waarmee u verbinding probeert te maken.
 
      ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
 
7. Als uw referenties u toegang verlenen tot de betreffende database, wordt Object Explorer geopend en kunt u nu administratieve taken uitvoeren of gegevens opvragen, afhankelijk van de gebruikersmachtigingen.
  
      ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)
      
 
 ## Verbindingsfouten oplossen

De meest voorkomende oorzaak van verbindingsfouten zijn fouten in de servernaam (onthoud, <*servernaam*> is de naam van de logische server, niet van de database), de gebruikersnaam of het wachtwoord, plus het feit dat de server om veiligheidsoverwegingen geen verbinding toestaat. 





<!--HONumber=Jun16_HO2-->


