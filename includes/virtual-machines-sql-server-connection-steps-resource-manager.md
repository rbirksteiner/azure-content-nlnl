### Een DNS-label configureren voor het openbare IP-adres

Voor verbinding met de SQL Server Database Engine van het internet, moet u eerst een DNS-label voor uw openbare IP-adres configureren.

> [AZURE.NOTE] DNS-labels zijn niet vereist als u alleen verbinding wilt maken met het SQL Server-exemplaar binnen hetzelfde virtuele netwerk of alleen lokaal verbinding wilt maken.

Als u een DNS-label wilt maken, selecteert u eerst **Virtuele machines** in de portal. Selecteer uw SQL Server VM om de eigenschappen op te halen.

1. Selecteer in de virtuele machineblade uw **openbare IP-adres.**

    ![openbaar ip adres](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

2. Vouw in de eigenschappen voor uw openbare IP-adres **Configuratie** open.

3. Voer een naam voor het DNS-label in. Deze naam is een A-Record die kan worden gebruikt om verbinding maken met uw SQL Server VM met uw naam in plaats van rechtstreeks met het IP-adres.

    ![dns label](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### Verbinding maken met de Database-engine vanaf een andere computer

1. Open SQL Server Management Studio (SSMS) op een computer die is verbonden met internet.

2. Bewerk in het dialoogvenster **Verbinding maken met server** of **Verbinding maken met Database-engine** de waarde voor **Servernaam**. Voer de volledige DNS-naam van de virtuele machine in (zoals bepaald in de vorige taak).

3. Kies in het vak **Verificatie** **SQL Server-verificatie**.

5. Typ in het vak **Aanmelden** een geldige SQL-aanmeldingsnaam.

6. Typ in het vak **Wachtwoord** het wachtwoord van de aanmelding.

7. Klik op **Verbinden**.

    ![ssms verbinden](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)



<!--HONumber=Jun16_HO2-->


