## Een back-upbeleid definiëren

Een back-upbeleid definieert een matrix met wanneer de momentopnamen van de gegevens zijn gemaakt en hoe lang deze momentopnamen worden bewaard. Bij het definiëren van een beleid voor het maken van een back-up van een virtuele machine, kunt u ervoor zorgen dat er *eenmaal per dag* een back-uptaak wordt uitgevoerd. Wanneer u een nieuw beleid maakt, wordt dit toegepast op de kluis. De interface voor het back-upbeleid ziet er als volgt uit:

![Back-upbeleid](./media/backup-create-policy-for-vms/backup-policy.png)

Ga als volgt te werk om een beleid te maken:

1. Voer een naam in voor de **beleidsnaam**.

2. Momentopnamen van uw gegevens kunnen met tussenpozen van een dag of een week worden gemaakt. Gebruik de vervolgkeuzelijst **Back-upfrequentie** om te kiezen of u wilt dat er dagelijks of wekelijks momentopnamen worden gemaakt.

    - Als u voor een dagelijks interval kiest, moet u met het gemarkeerde besturingselement het tijdstip op de dag kiezen waarop de momentopname moet worden gemaakt. Als u het tijdstip wilt wijzigen, maakt u de selectie voor het huidige tijdstip ongedaan en selecteert u het nieuwe tijdstip.

    ![Dagelijks back-upbeleid](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Als u voor een wekelijkse interval kiest, moet u met het gemarkeerde besturingselement de dag(en) van de week en het tijdstip op de dag kiezen waarop de momentopname moet worden gemaakt. Selecteer een of meer dagen in het menu met de dagen. Selecteer een tijdstip in het menu met de uren. Als u het tijdstip wilt wijzigen, maakt u de selectie voor het huidige tijdstip ongedaan en selecteert u het nieuwe tijdstip.

    ![Wekelijks back-upbeleid](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. Standaard zijn alle opties voor een **bewaartermijn** geselecteerd. Schakel de limieten voor de bewaartermijnen uit die u niet wilt gebruiken. Geef vervolgens een of meer intervallen op die moeten worden gebruikt.

    Door bewaartermijnen van een maand en een jaar te gebruiken, kunt u opgeven dat momentopnamen met tussenstappen van een week of een dag worden gemaakt.

    >[AZURE.NOTE] Als u een virtuele machine beveiligt, wordt één keer per dag een back-uptaak uitgevoerd. Het tijdstip waarop de back-up wordt uitgevoerd, is voor elke bewaartermijn hetzelfde.

4. Nadat u alle opties voor het beleid hebt ingesteld, klikt u boven in de blade op **Opslaan**.

    Het nieuwe beleid wordt onmiddellijk op de kluis toegepast.



<!--HONumber=Jun16_HO2-->


