### Voorvoegsels toevoegen of verwijderen als u nog geen VPN-gatewayverbinding hebt aangemaakt

- Gebruik het onderstaande voorbeeld om aanvullende adresvoorvoegsels **toe te voegen** aan een lokale netwerkgateway die u hebt aangemaakt, maar die nog geen VPN-gatewayverbinding heeft.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Gebruik het onderstaande voorbeeld om een adresvoorvoegsel **te verwijderen** uit een lokale netwerkgateway die geen VPN-verbinding heeft. Laat de voorvoegsels weg die u niet langer nodig hebt. In dit voorbeeld hebben we voorvoegsel 20.0.0.0/24 (van het vorige voorbeeld) niet langer nodig, dus werken we de lokale netwerkgateway bij zonder dit voorvoegsel.

        $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Voorvoegsels toevoegen of verwijderen als u al een VPN-gatewayverbinding hebt aangemaakt

Als u uw VPN-verbinding hebt aangemaakt en u wilt IP-adresvoorvoegsels toevoegen aan of verwijderen uit uw lokale netwerkgateway, moet u de volgende stappen uitvoeren in de volgorde waarin ze staan vermeld. Dit veroorzaakt enige downtime in uw VPN-verbinding.

>[AZURE.IMPORTANT] Verwijder de VPN-gateway niet. Als u dat doet, moet u de stappen herhalen om hem opnieuw tot stand te brengen. Bovendien moet u uw on-premises router opnieuw configureren met de nieuwe instellingen.
 
1. Verwijder de IPsec-verbinding. 
2. Wijzig de voorvoegsels voor uw lokale netwerkgateway. 
3. Maak een nieuwe IPsec-verbinding aan. 

U kunt het volgende voorbeeld als richtlijn gebruiken.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

    Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
    Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
    
    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Jun16_HO2-->


