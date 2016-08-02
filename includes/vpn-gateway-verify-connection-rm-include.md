U kunt een VPN-verbinding in de Azure Portal controleren door te navigeren naar **Gateways voor virtueel netwerk** **>** ***klik op uw gatewaynaam*** **>** **Instellingen** **>** **Verbindingen**. Als u de naam van de verbinding selecteert, kunt u meer informatie bekijken over de verbinding. In het onderstaande voorbeeld is geen verbinding gemaakt en is geen sprake van een gegevensstroom.


![Verbinding controleren](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Uw verbinding controleren met behulp van PowerShell

Het is ook mogelijk om te verifiëren of de verbinding is voltooid met behulp van `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. U kunt het volgende cmdlet-voorbeeld gebruiken om de waarden aan te passen aan uw eigen waarden. Als dit wordt gevraagd, selecteert u "A" om alles uit te voeren.

    Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Nadat de cmdlet is voltooid, scrolt u om de waarden weer te geven. In het volgende voorbeeld wordt de verbindingsstatus getoond als *verbonden* en ziet u inkomende en uitgaande bytes.

    Body:
    {
      "name": "localtovon",
      "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
    ltovon",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
        "virtualNetworkGateway1": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
    teways/vnetgw1"
        },
        "localNetworkGateway2": {
          "id":
    "/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
    ways/LocalSite"
        },
        "connectionType": "IPsec",
        "routingWeight": 10,
        "sharedKey": "abc123",
        "connectionStatus": "Connected",
        "ingressBytesTransferred": 33509044,
        "egressBytesTransferred": 4142431
      }


<!--HONumber=Jun16_HO2-->


