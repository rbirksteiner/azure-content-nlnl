

## Nieuwe databasegebruiker maken met behulp van SSMS

Voer de volgende stappen uit als u in een bestaande database een nieuwe databasegebruiker wilt maken met behulp van SSMS. 

Bij deze stappen wordt ervan uitgegaan dat u in Object Explorer met SSMS bent verbonden met SQL Database en dat u met uw logische server met SQL Database bent verbonden als principal-beheerder op serverniveau of met een gebruikersaccount dat is gemachtigd om een nieuwe gebruiker te maken. 

1. Vouw in Object Explorer het knooppunt Databases uit en selecteer de database waarin u een nieuw gebruikersaccount wilt maken.

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Klik met de rechtermuisknop op de geselecteerde database en klik op **Query**.

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. In het queryvenster kunt u de volgende Transact-SQL-instructie bewerken en gebruiken om een contained-gebruiker in de gebruikersdatabase te maken. 

    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)






<!--HONumber=Jun16_HO2-->


