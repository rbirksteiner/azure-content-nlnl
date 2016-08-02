<properties
    pageTitle="VM's in Azure beveiligen met Azure Backup | Microsoft Azure"
    description="U kunt Azure VM's beveiligen met de Azure Backup-service. In deze zelfstudie wordt uitgelegd hoe u een kluis maakt, VM's registreert, beleid maakt en VM's in Azure beveiligt."
    services="backup"
    documentationCenter=""
    authors="markgalioto"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/22/2016"
    ms.author="markgal; jimpark"/>


# Eerste blik: een back-up maken van virtuele machines van Azure

> [AZURE.SELECTOR]
- [Back-ups maken van ARM VM's](backup-azure-vms-first-look-arm.md)
- [Back-ups maken van VM's in de klassieke modus](backup-azure-vms-first-look.md)

In deze zelfstudie leert u hoe u back-ups naar Azure maakt van een Azure virtuele machine (VM). Voor deze zelfstudie gelden de volgende vereisten:

- U hebt een VM gemaakt in uw Azure-abonnement.
- De VM heeft verbinding met openbare IP-adressen in Azure. Zie [Netwerkverbinding](./backup-azure-vms-prepare.md#network-connectivity) voor meer informatie.

Het maken van een back-up van een VM bestaat uit vijf belangrijke stappen:  

![stap-een](./media/backup-azure-vms-first-look/step-one.png) Een back-upkluis maken of een bestaande back-upkluis gebruiken. <br/>
![stap-twee](./media/backup-azure-vms-first-look/step-two.png) De virtuele machines detecteren en registreren met de klassieke Azure-portal. <br/>
![stap-drie](./media/backup-azure-vms-first-look/step-three.png) De VM-agent installeren. <br/>
![stap-vier](./media/backup-azure-vms-first-look/step-four.png) Het beleid voor het beveiligen van de virtuele machines maken. <br/>
![stap-vijf](./media/backup-azure-vms-first-look/step-five.png) De back-up uitvoeren.

![Globaal overzicht van het back-upproces van VM's](./media/backup-azure-vms-first-look/backupazurevm-classic.png)

>[AZURE.NOTE] Azure heeft twee implementatiemodellen voor het maken van en werken met resources: [Resource Manager en het klassieke model](../resource-manager-deployment-model.md). Deze zelfstudie behandelt VM's die in de klassieke Azure-portal kunnen worden gemaakt. De Azure Backup-service ondersteunt VM's op basis van Azure Resource Manager (ARM), ook wel IaaS V2 VM's genoemd. Zie [Eerste blik: Back-ups maken van ARM VM's naar een Recovery Services-kluis](backup-azure-vms-first-look-arm.md) voor meer informatie over het maken van back-ups van ARM VM's.



## Stap 1 - Een back-upkluis maken voor een VM

Een back-upkluis is een entiteit waarmee alle back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De back-upkluis bevat ook het back-upbeleid dat wordt toegepast op de virtuele machines waarvan een back-up wordt gemaakt.

1. Meld u aan bij de [klassieke Azure-portal](http://manage.windowsazure.com/).

2. Klik in de linkerbenedenhoek van Azure Portal op **Nieuw**

    ![Klik op Nieuw menu](./media/backup-azure-vms-first-look/new-button.png)

3. Klik in de wizard Snel maken op **Data Services** > **Recovery Services** > **Back-upkluis** > **Snel maken**.

    ![Back-upkluis maken](./media/backup-azure-vms-first-look/new-vault-wizard-one-subscription.png)

    U moet de **naam** en **regio** in de wizard opgeven. Als u meer dan één abonnement beheert, verschijnt er een dialoogvenster waarin u het abonnement moet kiezen.

4. Voer voor **Naam** een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement.

5. Selecteer in **Regio** de geografische regio voor de kluis. De kluis **moet** zich in dezelfde regio bevinden als de virtuele machines die door de kluis worden beveiligd.

    Als u niet weet in welke regio uw VM zich bevindt, sluit u deze wizard en klikt u op **Virtual Machines** in de lijst met Azure-services. U vindt de regio in de kolom Locatie. Als u virtuele machines in meerdere regio's hebt, maakt u een back-upkluis in elke regio.

6. Als het dialoogvenster **Abonnement** niet in de wizard wordt weergegeven, gaat u naar de volgende stap. Als u met meerdere abonnementen werkt, selecteert u het abonnement dat u aan de nieuwe back-upkluis wilt koppelen.

    ![Een pop-upmelding voor de kluis maken](./media/backup-azure-vms-first-look/backup-vaultcreate.png)

7. Klik op **Kluis maken**. Het kan even duren voordat de back-upkluis is gemaakt. Houd de statusmeldingen onder in de portal in de gaten.

    ![Een pop-upmelding voor de kluis maken](./media/backup-azure-vms-first-look/create-vault-demo.png)

    Er verschijnt een bericht met de melding dat de kluis is gemaakt. De kluis wordt op de pagina **Recovery Services** weergegeven als **Actief**.

    ![Een pop-upmelding voor de kluis maken](./media/backup-azure-vms-first-look/create-vault-demo-success.png)

8. Selecteer in de lijst met kluizen op de pagina **Recovery Services** de kluis die u hebt gemaakt om de pagina **Quick Start** te openen.

    ![Lijst met back-upkluizen](./media/backup-azure-vms-first-look/active-vault-demo.png)

9. Klik op de pagina **Quick Start** op **Configureren** om de optie voor opslagreplicatie te openen.
    ![Lijst met back-upkluizen](./media/backup-azure-vms-first-look/configure-storage.png)

10. Kies bij de optie voor **opslagreplicatie** de juiste replicatieoptie voor uw kluis.

    ![Lijst met back-upkluizen](./media/backup-azure-vms-first-look/backup-vault-storage-options-border.png)

    Uw kluis heeft standaard geografisch redundante opslag. Kies geografisch redundante opslag als dit uw primaire back-up is. Kies lokaal redundante opslag als u een goedkopere optie wilt die niet zo duurzaam is. U vindt meer informatie over de opties voor geografisch redundante en lokaal redundante opslag in het [overzicht van Azure Storage-replicatie](../storage/storage-redundancy.md).

Nadat u de opslagoptie voor uw kluis hebt gekozen, bent u klaar om de VM aan de kluis te koppelen. Voordat u de VM aan de kluis koppelt, detecteert en registreert u de virtuele machines van Azure.

## Stap 2 - Virtuele machines van Azure detecteren en registreren
Voordat u de VM met een kluis registreert, moet u het detectieproces uitvoeren om nieuwe VM's weer te geven. Er wordt een lijst met virtuele machines in het abonnement weergegeven, samen met aanvullende informatie zoals de naam van de cloudservice en de regio.

1. Meld u aan bij de [klassieke Azure-portal](http://manage.windowsazure.com/)

2. Klik in de klassieke Azure-portal op **Recovery Services** om een lijst met Recovery Services-kluizen weer te geven.
    ![Workload selecteren](./media/backup-azure-vms-first-look/recovery-services-icon.png)

3. Selecteer in de lijst met kluizen de kluis voor de back-up van een VM.

    Wanneer u uw kluis selecteert, wordt de pagina **Quick Start** geopend

4. Klik in het kluismenu op **Geregistreerde items**.

    ![Workload selecteren](./media/backup-azure-vms-first-look/configure-registered-items.png)

5. Selecteer in het menu **Type** **Azure virtuele machine**.

    ![Workload selecteren](./media/backup-azure-vms/discovery-select-workload.png)

6. Klik op **DISCOVER** onder aan de pagina.
    ![Detectieknop ](./media/backup-azure-vms/discover-button-only.png)

    Het detectieproces kan enkele minuten in beslag nemen, terwijl er een tabelindeling van de virtuele machines wordt gemaakt. Onder in het scherm verschijnt een melding dat het proces wordt uitgevoerd.

    ![VM's detecteren](./media/backup-azure-vms/discovering-vms.png)

    De melding wijzigt zodra het proces is voltooid.

    ![Detectie uitgevoerd](./media/backup-azure-vms-first-look/discovery-complete.png)

7. Klik op **REGISTER** onder aan de pagina.
    ![Registratieknop](./media/backup-azure-vms-first-look/register-icon.png)

8. Selecteer in het snelmenu **Items registreren** de virtuele machines die u wilt registreren.

    >[AZURE.TIP] U kunt in meerdere virtuele machines tegelijkertijd registreren.

    Voor elke virtuele machine die u hebt geselecteerd wordt een taak gemaakt.

9. Klik op **Taak weergeven** in de melding om naar de pagina **Taken** te gaan.

    ![Taak registreren](./media/backup-azure-vms/register-create-job.png)

    De virtuele machine wordt ook in de lijst met geregistreerde items weergegeven, samen met de status van de bewerking voor de registratie.

    ![Status 1 registreren](./media/backup-azure-vms/register-status01.png)

    Wanneer de bewerking is voltooid, wordt de status aangepast in *Geregistreerd*.

    ![Registratie van status 2](./media/backup-azure-vms/register-status02.png)

## Stap 3 - De VM-agent op de virtuele machine installeren

De Azure VM-agent moet worden geïnstalleerd op de Azure virtuele machine om de Backup-extensie te kunnen gebruiken. Als uw VM is gemaakt vanuit de Azure-galerie, is de VM-agent al aanwezig op de VM. U kunt dan doorgaan met [Uw VM's beveiligen](backup-azure-vms-first-look.md#step-4---protect-azure-virtual-machines).

Als uw VM is gemigreerd van een on-premises datacenter, is de VM-agent waarschijnlijk niet op de VM geïnstalleerd. U moet de VM-agent op de virtuele machine installeren voordat u doorgaat met het beveiligen van de VM. Zie de [sectie VM-agent van het artikel Back-ups van VM's maken](backup-azure-vms-prepare.md#vm-agent) voor gedetailleerde stappen voor het installeren van de VM-agent.


## Stap 4 - Het back-upbeleid maken
Voordat u de eerste back-uptaak activeert, stelt u een schema in voor het maken van momentopnamen van de back-up. Het schema voor het maken van momentopnamen en de duur dat deze momentopnamen worden bewaard, vormen samen het back-upbeleid. De informatie over deze bewaarperiode is gebaseerd op het zogenaamde grootvader-vader-zoon-rotatieschema.

1. Navigeer naar de back-upkluis in **Recovery Services** in de klassieke Azure-portal en klik op **Geregistreerde items**.
2. Selecteer **Virtuele machine van Azure** in de vervolgkeuzelijst.

    ![Workload selecteren in de portal](./media/backup-azure-vms/select-workload.png)

3. Klik op **PROTECT** onder aan de pagina.
    ![Klik op Beveiligen](./media/backup-azure-vms-first-look/protect-icon.png)

    De **wizard Items beveiligen** verschijnt. Hierin worden *alleen* virtuele machines weergegeven die zijn geregistreerd en niet zijn beveiligd.

    ![Beveiliging op schaal configureren](./media/backup-azure-vms/protect-at-scale.png)

4. Selecteer de virtuele machines die u wilt beveiligen.

    Als er twee of meer virtuele machines met dezelfde naam zijn, gebruikt u de cloudservice om onderscheid te maken tussen de virtuele machines.

5. Selecteer in het menu **Beveiliging configureren** een bestaand beleid of maak een nieuw beleid om de virtuele machines die u hebt geïdentificeerd, te beveiligen.

    Nieuwe back-upkluizen hebben een standaardbeleid dat is gekoppeld aan de kluis. Dit beleid maakt dagelijks elke avond een momentopname en de momentopnamen worden dertig dagen bewaard. Aan elk back-upbeleid kunnen meerdere virtuele machines zijn gekoppeld. Er kan echter maar één beleid tegelijk aan een virtuele machine zijn gekoppeld.

    ![Beveiligen met een nieuw beleid](./media/backup-azure-vms/policy-schedule.png)

    >[AZURE.NOTE] Een back-upbeleid bevat een bewaarschema voor de geplande back-ups. Als u een bestaand back-upbeleid selecteert, kunt u de opties in de volgende stap niet wijzigen.

6. Geef bij **Bewaartermijn** het dagelijkse, wekelijkse, maandelijkse en jaarlijkse bereik voor de specifieke back-uppunten op.

    ![Er wordt een back-up van de virtuele machine gemaakt met een herstelpunt](./media/backup-azure-vms/long-term-retention.png)

    Het bewaarbeleid geeft aan hoe lang een back-up wordt bewaard. U kunt verschillende bewaarbeleidsregels opgeven op basis van het moment waarop de back-up wordt gemaakt.

7. Klik op **Taken** om de lijst met taken voor **Beveiliging configureren** weer te geven.

    ![Beveiligingstaak configureren ](./media/backup-azure-vms/protect-configureprotection.png)

    Nu dat u het beleid hebt ingesteld, gaat u naar de volgende stap en voert u de eerste back-up uit.

## Stap 5 - Eerste back-up

Als een virtuele machine is beveiligd met een beleid, kunt u deze relatie bekijken op het tabblad **Beveiligde items**. Totdat de eerste back-up wordt uitgevoerd, staat de **beveiligingsstatus** op **Beveiligd - (eerste back-up in behandeling)**. Standaard is de eerste geplande back-up de *eerste back-up*.

![Back-up in behandeling](./media/backup-azure-vms-first-look/protection-pending-border.png)

Als u de eerste back-up nu wilt starten, doet u het volgende:

1. Klik op de pagina **Beveiligde items** op pagina **Backup Now (Nu back-up maken)** onder aan de pagina.
    ![Pictogram van Backup Now (Nu back-up maken)](./media/backup-azure-vms-first-look/backup-now-icon.png)

    De Azure Backup-service maakt een back-uptaak voor de eerste back-upbewerking.

2. Klik op het tabblad **Taken** om de lijst met taken weer te geven.

    ![Back-up wordt uitgevoerd](./media/backup-azure-vms-first-look/protect-inprogress.png)

    Wanneer de eerste back-up is voltooid, wordt de status van de virtuele machine in het tabblad **Beveiligde items** gewijzigd in *Beveiligd*.

    ![Er wordt een back-up van de virtuele machine gemaakt met een herstelpunt](./media/backup-azure-vms/protect-backedupvm.png)

    >[AZURE.NOTE] Het maken van back-ups van virtuele machines is een lokaal proces. U kunt geen back-ups maken van virtuele machines als de back-upkluis zich niet in dezelfde regio bevindt. Voor elke Azure-regio met VM's waarvan een back-up moet worden gemaakt, moet ten minste één back-upkluis in deze regio worden gemaakt.

## Volgende stappen
Nu u een back-up hebt gemaakt van een VM, zijn er verschillende volgende stappen die van belang kunnen zijn. De meeste logische stap is om uzelf vertrouwd te maken met het herstellen van gegevens op een VM. Er zijn ook beheertaken waarmee u leert hoe u uw gegevens veilig kunt houden en de kosten kunt minimaliseren.

- [Uw virtuele machines beheren en controleren](backup-azure-manage-vms.md)
- [Virtuele machines herstellen](backup-azure-restore-vms.md)
- [Hulp bij het oplossen van problemen](backup-azure-vms-troubleshoot.md)


## Vragen?
Als u vragen hebt of als er een functie is die u graag opgenomen zag worden, [stuurt u ons feedback](http://aka.ms/azurebackup_feedback).



<!--HONumber=Jun16_HO2-->


