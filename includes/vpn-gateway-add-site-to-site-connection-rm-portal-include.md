1. Zoek uw virtuele netwerkgateway en klik op **Alle instellingen** om de blade **Instellingen** te openen.

2. Op de blade **Instellingen** klikt u op **Verbindingen** en vervolgens op **Toevoegen** bovenaan de blade om de blade **Verbinding toevoegen** te openen.

    ![Site-naar-Site-verbinding maken](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/addconnection250.png)

3. Op de blade **Verbinding toevoegen** geeft u uw verbinding een **Naam**. 

4. Voor **Verbindingstype** selecteert u **Site-naar-site (IPSec)**.

5. Voor **Virtuele netwerkgateway** geldt een vaste waarde, omdat u verbinding maakt vanaf deze gateway.

6. Voor **Lokale netwerkgateway** klikt u op **Een lokale netwerkgateway kiezen** en selecteert u de lokale netwerkgateway die u wilt gebruiken. 

7. Voor **Gedeelde sleutel** moet de hier gebruikte waarde overeenkomen met de waarde die u voor uw lokale VPN-apparaat gebruikt. Als het VPN-apparaat op uw lokale netwerk geen gedeelde sleutel biedt, kunt u er een bedenken en deze hier en in uw lokale apparaat invoeren. Het belangrijkste is dat ze overeenkomen.

8. De resterende waarden voor **Abonnement**, **Resourcegroep**, en **Locatie** zijn vast.

9. Klik op **OK** om uw verbinding te maken. U ziet *Verbinding maken* op het scherm knipperen.

10. Wanneer de verbinding voltooid is, wordt het weergegeven in de blade **Verbindingen** voor uw Gateway.

    ![Site-naar-Site-verbinding maken](./media/vpn-gateway-add-site-to-site-connection-rm-portal-include/connectionstatus450.png)




<!--HONumber=Jun16_HO2-->


