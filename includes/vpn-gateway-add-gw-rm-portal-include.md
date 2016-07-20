1. Ga, in de portal, naar **Nieuw** en vervolgens naar **Netwerken**. Selecteer **Virtuele netwerkgateway** uit de lijst.

    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/creategw250.png)

2. Op de blade **Virtuele netwerkgateway aanmaken**, in het veld **Naam**, voert u de naam van uw gateway in. Deze mag niet gelijk zijn aan de naam van het gateway-subnet. De naam is degene van het gateway-object.
 
3. Kies vervolgens het virtuele netwerk waar u deze gateway wilt implementeren. Klik op de pijl om de blade **Een virtueel netwerk kiezen** te openen en klik daarna op het VNet. Het VNet moet al een geldig gateway-subnet hebben om in de lijst te worden weergegeven.

4. Kies een openbaar IP-adres. Klik op de pijl om de blade **Kies openbaar IP-adres** te openen. Klik vervolgens op **Nieuw aanmaken** om de blade **Openbaar IP-adres** te openen. Geef uw openbare IP-adres een naam. Houd er rekening mee dat hier niet om een IP-adres wordt gevraagd. Het IP-adres wordt dynamisch toegewezen. Dit is in plaats daarvan de naam van het IP-adresobject waaraan het adres wordt toegewezen. Klik op **OK** om uw wijzigingen op te slaan.

5. Bij **Gatewaytype** selecteert u het gatewaytype dat wordt opgegeven voor uw configuratie.

6. Bij **VPN-type** selecteert u het VPN-type dat wordt opgegeven voor uw configuratie.

7. Bij **Abonnement** controleert u of het juiste abonnement is geselecteerd.

8. Bij **Resourcegroep** wordt de resourcegroep bepaald door het virtuele netwerk dat u selecteert.

9. Bij **locatie** dient u ervoor te zorgen dat de locatie wordt weergegeven waarin zowel uw resourcegroep als uw VNet aanwezig zijn.

10. U kunt **Vastmaken aan dashboard** selecteren, als u wilt dat uw gateway op het dashboard wordt weergegeven. Klik op **Aanmaken** om de gateway aan te maken. Op het dashboard wordt de tegel 'Virtuele netwerkgateway implementeren' weergegeven. Het aanmaken van een gateway kan tot 45 minuten duren. Op de achtergrond is er veel gaande. U moet mogelijk uw portal-pagina vernieuwen om de voltooide status te kunnen zien.

    
    ![Gateway](./media/vpn-gateway-add-gw-rm-portal-include/deployvnetgw150.png)

11. Nadat de gateway is aangemaakt, kunt u het IP-adres dat eraan is toegewezen bekijken door naar het virtuele netwerk in de portal te kijken. De gateway wordt weergegeven als aangesloten apparaat. U kunt op het aangesloten apparaat klikken (uw virtuele netwerkgateway) om meer informatie te laten weergeven.






<!--HONumber=Jun16_HO2-->


