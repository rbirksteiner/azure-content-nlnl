## Over records

Elke DNS-record heeft een naam en een type. Records zijn ingedeeld in verschillende typen overeenkomstig de gegevens die ze bevatten. Het meest voorkomende type is een 'A'-record, waarmee een naam aan een IPv4-adres wordt toegewezen. Een ander type is een 'MX'-record, waarmee een naam aan een e-mailserver wordt toegewezen.

Azure DNS ondersteunt alle algemene DNS-recordtypen, waaronder A, AAAA, CNAME MX, NS, SOA, SRV en TXT. SOA-recordsets worden automatisch met elke zone gemaakt. Ze kunnen niet afzonderlijk worden gemaakt. Houd er rekening mee dat SPF-records moeten worden gemaakt met het recordtype TXT. Ga voor meer informatie naar [deze pagina](http://tools.ietf.org/html/rfc7208#section-3.1).

In Azure DNS worden records opgegeven met behulp van relatieve namen. Een FQDN-domeinnaam (Fully Qualified Domain Name) bevat anders dan een 'relatieve naam' de zonenaam. De relatieve record naam 'www' in de zone contoso.com geeft bijvoorbeeld de volledig gekwalificeerde naam van de record www.contoso.com.

## Over recordsets

Soms moet u meer dan één DNS-record maken met een bepaalde naam en een bepaald type. Stel bijvoorbeeld dat de website 'www.contoso.com' wordt gehost op twee verschillende IP-adressen. De website vereist twee verschillende A-records, één voor elk IP-adres. Dit is een voorbeeld van een recordset:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS beheert DNS-records door gebruik te maken van recordsets. Een recordset is een verzameling DNS-records in een zone die dezelfde naam hebben en van hetzelfde type zijn. De meeste recordsets bevatten één record, maar voorbeelden zoals deze, waarin een verzameling meer dan een record bevat, zijn niet ongewoon.

SOA- en CNAME-recordsets zijn uitzonderingen. De DNS-standaarden staan het gebruik van meerdere records met dezelfde naam voor deze typen niet toe.

De TTL (of Time to Live) geeft aan hoe lang records door clients in de cache worden geplaatst voordat ze opnieuw worden opgevraagd. In dit voorbeeld is de TTL 3600 seconden oftewel 1 uur. De TTL is opgegeven voor de recordset en niet voor elke record, zodat dezelfde waarde wordt gebruikt voor alle records in deze recordset.

#### Recordsets met jokertekens

Azure DNS ondersteunt [recordsets met jokertekens](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Deze worden geretourneerd voor elke query met een overeenkomende naam (tenzij er een exemplaar is dat meer overeenkomt en afkomstig is uit een andere recordset zonder jokertekens). Recordsets met jokertekens worden ondersteund voor alle recordtypen behalve NS en SOA.  

Als u een recordset met jokertekens wilt maken, moet u de recordnaam '\*' gebruiken. Of gebruik een naam met het label '\*', bijvoorbeeld '\*.foo'.

#### CNAME-recordsets

CNAME-recordsets kunnen niet worden gecombineerd met andere recordsets met dezelfde naam. U kunt bijvoorbeeld geen CNAME-recordset met de relatieve naam 'www' en tegelijkertijd een A-record met de relatieve naam 'www' maken. Omdat het toppunt van de zone (naam = '@') altijd NS- en SOA-recordsets bevat die zijn gemaakt toen de zone werd gemaakt, kunt u in het toppunt van de zone geen CNAME-recordset maken. Deze beperkingen worden veroorzaakt door de DNS-standaarden en zijn geen beperkingen van Azure DNS.



<!--HONumber=Jun16_HO2-->


