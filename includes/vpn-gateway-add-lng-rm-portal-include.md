1. Navigeer in de Azure-beheerportal naar **Nieuw** **>** **Netwerken** **>** **lokale netwerkgateway**.

    ![maak een lokale netwerkgateway](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. Op de **blade Lokale netwerkgateway maken**, voert u een **Naam** in voor uw lokale netwerkgateway-object.
 
3. Voer een **IP-adres** voor uw gateway in. Dit is het IP-adres van het externe VPN-apparaat waarmee u verbinding wilt maken. Het mag zich niet achter NAT bevinden en moet bereikbaar zijn voor Azure.

4. **Adresruimte** verwijst naar de adresbereiken op uw lokale (meestal on-premises) netwerk. U kunt meerdere adresruimtebereiken toevoegen. De bereiken die u hier opgeeft, mogen de adresruimtebereiken die u gebruikt voor een van de virtuele netwerken die via de gateway communiceren niet overlappen.  U moet coördineren met uw on-premises configuratie evenals met de netwerkruimten van uw virtuele Azure-netwerk.
 
5. Voor **Abonnement** controleert u of het juiste abonnement wordt weergegeven.

6. Voor **Resourcegroep** selecteert u de resourcegroep die u wilt gebruiken. U kunt een nieuwe resourcegroep maken of een resourcegroep selecteren die u al hebt gemaakt. Typ de naam in het vak om een nieuwe resourcegroep te maken. Om een resourcegroep te selecteren die u al hebt gemaakt, klikt u op **Resourcegroep** om de blade **Resourcegroep** te openen en selecteert u vervolgens de resourcegroep die u wilt gebruiken.

7. Als u een nieuwe lokale netwerkgateway maakt, kunt u voor **Locatie** dezelfde locatie gebruiken als de virtuele netwerkgateway. Maar dit is niet vereist. De lokale netwerkgateway kan zich op een andere locatie bevinden. 

8. Laat 'Vastmaken aan dashboard' geselecteerd als u deze lokale netwerkgateway eenvoudig vanuit het dashboard wilt vinden.

9. Klik op **Maken** om de lokale netwerkgateway te maken. U ziet 'Lokale netwerkgateway implementeren' op uw dashboard.

10. Wanneer de  lokale netwerkgateway is gemaakt, wordt deze in de portal geopend, zodat u deze kunt bekijken.

    



<!--HONumber=Jun16_HO2-->


