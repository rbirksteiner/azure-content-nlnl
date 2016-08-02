
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## Een nieuwe Azure SQL-firewall op serverniveau maken

Voer de volgende stappen uit in Azure Portal om een firewallregel op serverniveau te maken die verbindingen van een afzonderlijk IP-adres (de clientcomputer) of een IP-adresbereik naar een logische SQL-server toestaat. 

1. Als u momenteel niet bent verbonden, maakt u eerst verbinding met [Azure Portal](http://portal.azure.com).
2. Klik in de standaardblade op **SQL Server**.

    ![nieuwe serverfirewall](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

2. Klik in de blade SQL Server op de SQL-server waarop u de firewallregel wilt maken. 

    ![nieuwe serverfirewall](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
           
3. Bekijk de eigenschappen van uw server.

    ![nieuwe serverfirewall](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
      
4. Klik in de blade Instellingen op **Firewall**.

    ![nieuwe serverfirewall](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)
    

    > [AZURE.IMPORTANT] Als u de optie **Firewall** niet ziet, gaat u terug en controleert u of u de blade hebt gekozen voor de logische SQL Database-server en niet de blade voor een SQL-database.

5. Klik op **Client-IP toevoegen** om in Azure een regel voor het IP-adres van de client te maken.

      ![nieuwe serverfirewall]    (./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

6. Klik desgewenst op het IP-adres dat is toegevoegd om het firewalladres zo te bewerken dat toegang is toegestaan tot een bereik van IP-adressen.

      ![nieuwe serverfirewall](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
    
7. Klik op **Opslaan** om de firewallregel op serverniveau te maken.

     ![nieuwe serverfirewall](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

    >[AZURE.IMPORTANT] Het client-IP-adres kan van tijd tot tijd veranderen. Het is dan ook mogelijk dat u pas weer toegang hebt tot de server wanneer u een nieuwe firewallregel hebt gemaakt. U kunt het IP-adres controleren met [Bing](http://www.bing.com/search?q=my%20ip%20address) en vervolgens één IP-adres of een bereik van IP-adressen toevoegen. Zie [Firewallinstellingen configureren](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal) voor meer informatie.



<!--HONumber=Jun16_HO2-->


