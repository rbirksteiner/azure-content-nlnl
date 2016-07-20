De volgende tabel bevat de vereisten voor VPN-gateways op basis van beleid en route. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen. Voor het klassieke model zijn op beleid gebaseerde VPN-gateways hetzelfde als statische gateways. Op route gebaseerde gateways zijn hetzelfde als dynamische gateways.


|   | **Op beleid gebaseerde Basic VPN-gateway** | **Op route gebaseerde Basic VPN-gateway** | **Op route gebaseerde Standard VPN-gateway**   | **Op route gebaseerde High Performance VPN-gateway** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Site-naar-site-connectiviteit (S2S)**  | VPN-configuratie op basis van beleid        | VPN-configuratie op basis van route  | VPN-configuratie op basis van route     | VPN-configuratie op basis van route    |
| **Punt-naar-site-verbinding (P2S**)      | Niet ondersteund   | Ondersteund (kan tegelijk bestaan met S2S)  | Ondersteund (kan tegelijk bestaan met S2S)  | Ondersteund (kan tegelijk bestaan met S2S) |
| **Verificatiemethode**                 |    Vooraf gedeelde sleutel  | Vooraf gedeelde sleutel voor S2S-connectiviteit, certificaten voor P2S-connectiviteit | Vooraf gedeelde sleutel voor S2S-connectiviteit, certificaten voor P2S-connectiviteit | Vooraf gedeelde sleutel voor S2S-connectiviteit, certificaten voor P2S-connectiviteit |
| **Maximumaantal S2S-verbindingen**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Maximumaantal P2S-verbindingen**       | Niet ondersteund                  | 128                                                                   | 128                               | 128                              |
|**Ondersteuning voor actieve routering (BGP)**           | Niet ondersteund                  | Niet ondersteund                                                         | Niet ondersteund                     | Niet ondersteund                    |
 


<!--HONumber=Jun16_HO2-->


