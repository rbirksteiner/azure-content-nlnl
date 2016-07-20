
Om uw VPN-apparaat te configureren, hebt u het openbare IP-adres van de virtuele netwerkgateway nodig voor het configureren van uw on-premises VPN-apparaat. Neem contact op met de fabrikant van uw apparaat voor specifieke informatie over configuraties en het configureren van uw apparaat. Raadpleeg de [VPN-apparaten](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) voor meer informatie over VPN-apparaten die goed werken in combinatie met Azure.

Gebruik het volgende voorbeeld om het openbare IP-adres van uw virtuele netwerkgateway te vinden met behulp van PowerShell:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

U kunt het openbare IP-adres voor uw virtuele netwerkgateway ook vinden in de Azure-portal. Navigeer naar **Virtuele netwerkgateways** en klik vervolgens op de naam van uw gateway.


<!--HONumber=Jun16_HO2-->


