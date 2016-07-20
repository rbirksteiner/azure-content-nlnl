- **Op beleid gebaseerd VPN-type:** op beleid gebaseerde VPN-verbindingen werden voorheen statische routeringsgateways genoemd in het klassieke implementatiemodel. VPN-verbindingen op basis van beleid versleutelen pakketten en sturen deze via IPsec-tunnels op basis van het IPsec-beleid dat is geconfigureerd met de combinaties van adresvoorvoegsels tussen uw lokale netwerk en het Azure VNET. Het beleid (of de verkeersselector) wordt gewoonlijk gedefinieerd als een toegangslijst in de configuratie van het VPN-apparaat. De waarde voor een op beleid gebaseerd VPN-type is *PolicyBased*.

- **VPN-type op basis van een route**: VPN-verbindingen op basis van een route werden voorheen dynamische routeringsgateways genoemd in het klassieke implementatiemodel. VPN-verbindingen op basis van een route gebruiken "routes" in de IP-doorstuurtabel of routeringstabel om pakketten naar de bijbehorende tunnelinterfaces te sturen. De tunnelinterfaces versleutelen of ontsleutelen de pakketten vervolgens naar en vanuit de tunnels. Het beleid (of de verkeersselector) voor VPN-verbindingen op basis van een route is geconfigureerd als alles-naar-alles (of jokertekens). De waarde voor een VPN-type op basis van een route is *RouteBased*.


<!--HONumber=Jun16_HO2-->


