

## Nieuwe databasegebruiker mdb_owner-machtigingen toekennen

Voer de volgende stappen uit als u een bestaande databasegebruiker db_owner-machtigingen wilt verlenen.

Bij deze stappen wordt ervan uitgegaan dat u in Object Explorer in SSMS bent verbonden met SQL Database en dat u met uw logische server met SQL Database bent verbonden als principal-beheerder op serverniveau of met een gebruikersaccount dat is gemachtigd om gebruikersmachtigingen toe te kennen. 

1. Vouw in Object Explorer het knooppunt Databases uit en selecteer de database met de gebruiker waaraan u dbo-machtigingen wilt verlenen.

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Klik met de rechtermuisknop op de geselecteerde database en klik op **Query**.

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. In het queryvenster kunt u de volgende Transact-SQL-instructie bewerken en gebruiken om dbo-machtigingen aan een opgegeven gebruiker toe te kennen. 

    '''ALTER ROLE db_owner ADD MEMBER user1;

     ![SQL Server Management Studio: Verbinding maken met SQL Database-server](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)





<!--HONumber=Jun16_HO2-->


