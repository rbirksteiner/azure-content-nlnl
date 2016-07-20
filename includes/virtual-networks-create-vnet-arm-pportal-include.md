## Een VNet aanmaken in de Azure-portal

Volg de onderstaande stappen om een VNet aan te maken met behulp van de Azure Preview Portal op basis van het bovenstaande scenario .

1. Navigeer via een browser naar http://portal.azure.com en log, indien nodig, in met uw Azure-account.
2. Klik op **Nieuw** > **Netwerken** > **Virtueel netwerk** en vervolgens op **Resource Manager** vanuit de lijst **Selecteer een implementatiemodel**. Klik daarna op **Aanmaken**, zoals in de afbeelding hieronder wordt weergegeven.

    ![VNet aanmaken in Azure-portal](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. Configureer de VNet-instellingen op de blade **Virtueel netwerk aanmaken**, zoals aangegeven in de onderstaande afbeelding.

    ![Een virtuele netwerkblade aanmaken](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Klik op **Resourcegroep** en selecteer een resourcegroep om toe te voegen aan het VNet of klik op **Nieuwe aanmaken** om het VNet toe te voegen aan een nieuwe resourcegroep. In de onderstaande afbeelding worden de instellingen voor de resourcegroep genaamd **TestRG** weergegeven. Zie voor meer informatie over resourcegroepen [Overzicht van Azure Resource Manager](../articles/resource-group-overview.md/#resource-groups).

    ![Resourcegroep](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Wijzig zo nodig de instellingen van het **Abonnement** en de **Locatie** voor uw VNet. 

6. Als u VNet niet als tegel wilt laten weergeven in het **Startboard** schakelt u **Vastmaken aan Startboard** uit. 

7. Klik op **Aanmaken** en let op de tegel met de naam **Virtueel netwerk aanmaken** die in de onderstaande afbeelding wordt weergegeven.

    ![Tegel Virtueel netwerk aanmaken](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Wacht tot het VNet is aangemaakt en klik vervolgens, in de blade **Virtueel netwerk**, op **Alle instellingen** > **Subnetten** > **Toevoegen** zoals hieronder wordt weergegeven.

    ![Subnet toevoegen in de Azure-portal](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Geef de subnetinstellingen voor het *Back-end*-subnet op, zoals hieronder wordt weergegeven, en klik vervolgens op **OK**. 

    ![Subnetinstellingen](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Let op de lijst met subnetten, zoals wordt weergegeven in de afbeelding hieronder.

    ![Lijst met subnetten in VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)


<!--HONumber=Jun16_HO2-->


