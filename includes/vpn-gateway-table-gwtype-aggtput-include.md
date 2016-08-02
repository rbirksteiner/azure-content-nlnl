|    | **Doorvoer VPN-gateway (1)** | **Max. IPsec-tunnels VPN-gateway (2)** | **Doorvoer ExpressRoute-gateway** | **VPN-gateway en ExpressRoute bestaan tegelijk**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **Basis-SKU**              |  100 Mbps | 10                         |  500 Mbps                           | Nee   |
| **Standaard SKU**           |  100 Mbps | 10                         | 1000 Mbps                           | Ja  |
| **High Performance SKU**   | 200 Mbps  | 30                         | 2000 Mbps                           | Ja  |

- (1) De VPN-doorvoer is een ruwe schatting op basis van metingen tussen VNET's in dezelfde Azure-regio. Het is geen garantie van wat u kunt krijgen bij verbindingen tussen meerdere gebouwen via internet, maar moet worden gebruikt als een maximaal mogelijke meting.
- (2) Voor het aantal tunnels raadpleegt u de onderstaande VPN op basis van route. Een op beleid gebaseerde VPN biedt slechts ondersteuning voor één site-naar-site VPN-tunnel


<!--HONumber=Jun16_HO2-->


