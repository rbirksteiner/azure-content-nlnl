<properties
    pageTitle="Verbinding maken met een virtuele Windows Server-machine | Microsoft Azure"
    description="Leer hoe u verbinding maakt en u aanmeldt bij een virtuele Windows-machine via de Azure Portal en het Resource Manager-implementatiemodel."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/05/2016"
    ms.author="cynthn"/>

# Verbinding maken met en aanmelden bij een virtuele machine in Azure waarop Windows wordt uitgevoerd 


U gebruikt de knop **Verbinden** in de Azure Portal om een Extern bureaublad-sessie te starten. Eerst maakt u verbinding met de virtuele machine en vervolgens meldt u zich aan.

## Verbinding maken met de virtuele machine

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als u dat nog niet hebt gedaan.

2.  Klik in het menu Hub op **Virtuele machines**.

3.  Selecteer de virtuele machine in de lijst.

4. Klik op de blade voor de virtuele machine op **Verbinden**.

    ![Schermafbeelding van de Azure Portal waarin wordt getoond hoe u verbinding maakt met uw virtuele machine.](./media/virtual-machines-windows-connect-logon/connect.png)
    
 > [AZURE.TIP] Als de knop Verbinden in de portal grijs is en u niet met Azure bent verbonden via een [Snelle Route](../expressroute/expressroute-introduction.md)-verbinding of een [VPN tussen sites](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)-verbinding, moet u een openbaar IP-adres maken en toewijzen aan uw virtuele machine voordat u Extern bureaublad kunt gebruiken. Er is meer informatie over [openbare IP-adressen in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md) beschikbaar.

## Aanmelden bij de virtuele machine

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## Volgende stappen

Als u problemen ondervindt wanneer u verbinding probeert te maken, raadpleegt u [Problemen oplossen met Extern bureaublad-verbindingen met een virtuele Windows-machine in Azure](virtual-machines-windows-troubleshoot-rdp-connection.md). Dit artikel leidt u door het opsporen en oplossen van veelvoorkomende problemen.



<!--HONumber=Jun16_HO2-->


