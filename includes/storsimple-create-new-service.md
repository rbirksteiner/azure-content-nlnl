<!--author=alkohli last changed:01/14/2016-->


#### Een nieuwe service maken

1. Meld u met uw Microsoft-accountreferenties aan bij de klassieke Azure-portal met deze URL: [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Klik in de klassieke Azure-portal op **Nieuw** > **Data Services** > **StorSimple Manager** > **Snelle invoer**.

3. Ga in het formulier dat wordt weergegeven, als volgt te werk:
  1. Geef een unieke **naam** voor uw service op. Dit is een beschrijvende naam die kan worden gebruikt om de service te identificeren. De naam kan tussen 2 en 50 tekens bevatten (letters, cijfers en afbreekstreepjes). De naam moet beginnen en eindigen met een letter of cijfer.
  2. Geef een **locatie** voor uw service op. Kies in het algemeen een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. U kunt ook rekening houden met het volgende: 
     
        - Als u bestaande workloads in Azure hebt die u ook wilt implementeren met uw StorSimple-apparaat, moet u dat datacenter gebruiken.
        - Uw StorSimple Manager-service en Azure Storage kunnen zich op twee verschillende locaties bevinden. In dat geval moet u het StorSimple Manager- en Azure Storage-account afzonderlijk maken. U maakt een Azure Storage-account door naar de Azure Storage-service in de klassieke Azure-portal te gaan en de stappen in [Een Azure Storage-account maken](storage-create-storage-account.md#create-a-storage-account) uit te voeren. Nadat u dit account hebt gemaakt, voegt u het toe aan de StorSimple Manager-service met de stappen in [Een nieuw opslagaccount voor de service maken](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
         
  3. Kies een **abonnement** in de vervolgkeuzelijst. Het abonnement is gekoppeld aan uw factureringsrekening. Dit veld wordt niet weergegeven als u slechts één abonnement hebt.
  4. Selecteer **Een nieuw opslagaccount maken** om automatisch een opslagaccount te maken met de service. Dit opslagaccount heeft een speciale naam, zoals storsimplebwv8c6dcnf. Als u een andere locatie voor uw gegevens wilt kiezen, schakelt u dit vakje uit. 
  5. Klik op **StorSimple Manager maken** om de service te maken.

   ![StorSimple Manager maken](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

  U wordt omgeleid naar de landingspagina **Service**. Het maken van een service duurt enkele minuten. Nadat de service is gemaakt, krijgt u hierover een melding en wordt de status van de service gewijzigd in **Actief**.
 
   ![Service maken](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Video beschikbaar](./media/storsimple-create-new-service/Video_icon.png) **Video beschikbaar**

Als u een video wilt bekijken over het maken van een nieuwe StorSimple Manager-service, klikt u [hier](https://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/).


<!--HONumber=Jun16_HO2-->


