### Wordt BGP ondersteunt op alle Azure VPN-gateway SKU’s?

Nee, BGP wordt ondersteund op de VPN-gateways **Standard** en **HighPerformance** van Azure. SKU **Basic** wordt NIET ondersteund.

### Kan ik BGP gebruiken met beleid-gebaseerde VPN-gateways van Azure?

Nee, BGP wordt alleen ondersteund op route-gebaseerde VPN-gateways.

### Kan ik persoonlijke ASN's (autonome systeemnummers) gebruiken?

Ja, u kunt uw eigen openbare ASN's of persoonlijke ASN's voor zowel uw on-premises netwerken en virtuele netwerken van Azure gebruiken.

### Kan ik hetzelfde ASN gebruiken voor on-premises VPN-netwerken en Azure VNets?

Nee, u moet verschillende ASN’s toewijzen aan uw on-premises netwerken en uw Azure Vnets als u ze beide verbindt met BGP. Azure VPN-gateways krijgen standaard een ASN van 65515 toegewezen, onafhankelijk of BGP is ingeschakeld voor verbinding tussen gebouwen. U kunt deze standaardwaarde onderdrukken door een andere ASN toe te wijzen bij het aanmaken van de VPN-gateway of door het ASN te wijzigen nadat de gateway is aangemaakt. U moet uw lokale ASN's toewijzen aan de bijbehorende on-premises netwerkgateways van Azure.



### Welke adresvoorvoegsels maakt Azure VPN-gateways aan mij bekend?

Azure VPN-gateway maakt de volgende routes bekend aan uw on-premises BGP-apparaten:

- Uw VNet-adresvoorvoegsels
- Adresvoorvoegsels voor alle lokale netwerkgateways die zijn verbonden met de Azure VPN-gateway
- Routes die afkomstig zijn van andere BGP-peeringsessies die zijn verbonden met de Azure VPN-gateway, **behalve standaardroutes of routes die overlappen met een VNet-voorvoegsel**.

### Kan ik BGP in combinatie met mijn VNet-naar-VNet-verbindingen gebruiken?

Ja, u kunt BGP zowel gebruiken voor verbindingen tussen gebouwen als voor VNet-naar-VNet-verbindingen.

### Kan ik BGP combineren met niet-BGP-verbindingen voor mijn Azure VPN-gateways?

Ja, u kunt zowel BGP- als niet-BGP-verbindingen combineren voor dezelfde VPN-gateway.

### Biedt Azure VPN-gateway ondersteuning voor BGP-transitroutering?

Ja, BGP-transitroutering wordt ondersteund, met uitzondering dat Azure VPN-gateways **GEEN** standaardroutes bekendmaakt aan andere BGP-peers. Om transitroutering op meerdere VPN-gateways mogelijk te maken, moet u BGP op alle tussenliggende VNet-naar-VNet-verbindingen inschakelen.

### Kan ik meer dan één tunnel aanbrengen tussen Azure VPN-gateway en mijn on-premises netwerk?

Ja, u kunt meer dan één S2S-VPN-tunnel aanbrengen tussen een Azure VPN-gateway en uw on-premises netwerk. Houd er rekening mee dat deze tunnels deel uitmaken van het totaalaantal tunnels voor uw Azure VPN-gateways. Als u bijvoorbeeld twee redundante tunnels heeft tussen uw Azure VPN-gateway en een van uw on-premises netwerken, gebruiken ze 2 tunnels van het totaalaantal voor uw Azure VPN-gateway (10 voor Standard en 30 voor HighPerformance).

### Kan ik meerdere tunnels tussen twee Azure VNets met BGP hebben?

Nee, redundante tunnels tussen één paar virtuele netwerken wordt niet ondersteund.

### Kan ik BGP gebruiken voor S2S-VPN in een configuratie waarin zowel ExpressRoute als S2S-VPN wordt gebruikt?

Momenteel niet.

### Welk adres gebruikt Azure VPN-gateway voor BGP-peer-IP?

De Azure VPN-gateway wijst één IP-adres uit het GatewaySubnet-bereik toe dat wordt gedefinieerd voor het virtuele netwerk. Dit is standaard het een-na-laatste adres van het bereik. Als uw GatewaySubnet bijvoorbeeld 10.12.255.0.0/27 is, variërend van 10.42.255.0.0 tot 10.42.255.31, wordt het BGP-peer-IP-adres op de Azure VPN-gateway 10.12.255.30. U kunt deze informatie vinden als u de informatie van de Azure VPN-gateway laat weergeven.

### Wat zijn de vereisten voor de BGP-peer-IP-adressen op mijn VPN-apparaat?

Het adres van uw on-premises BGP-peer **MAG NIET** gelijk zijn aan het openbare IP-adres van uw VPN-apparaat. Gebruik een ander IP-adres op het VPN-apparaat voor uw BGP-peer-IP. Het kan een adres zijn dat is toegewezen aan de loopback-interface op het apparaat. Specificeer dit adres in de bijbehorende lokale netwerkgateway die de locatie vertegenwoordigt.

### Wat moet ik opgeven als adresvoorvoegsels voor mijn lokale netwerkgateway wanneer ik BGP gebruik?

Azure Local Network Gateway geeft u de eerste adresvoorvoegsels voor het on-premises netwerk. Bij BGP moet u het voorvoegsel van de host (/32-voorvoegsel) van uw BGP-peer-IP-adres toewijzen als de adresruimte voor dat on-premises netwerk. Als uw BGP-peer-IP-adres 10.52.255.254 is, moet u "10.52.255.254/32" opgeven als de lokale netwerkadresruimte van de lokale netwerkgateway voor dit on-premises netwerk. Hierdoor bent u ervan verzekerd dat de Azure VPN-gateway de BGP-sessie opricht via de S2S-VPN-tunnel.

### Wat moet ik toevoegen aan mijn on-premises VPN-apparaat voor de BGP-peeringsessie?

U moet een hostroute van het Azure BGP-peer-IP-adres toevoegen aan uw VPN-apparaat die verwijst naar de IPSec-S2S VPN-tunnel. Als het Azure VPN-peer-IP-adres bijvoorbeeld "10.12.255.30" is, dient u een hostroute toe te voegen voor "10.12.255.30" met een nexthop-interface van de overeenkomende IPsec-tunnelinterface op uw VPN-apparaat.


<!--HONumber=Jun16_HO2-->


