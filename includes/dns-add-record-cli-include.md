#### Een A-recordset met één record maken

Gebruik `azure network dns record-set create` om een recordset te maken. Geef de resourcegroep, zonenaam, de relatieve naam van de recordset, het recordtype en de TTL (Time to Live) op.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Nadat u de A-recordset hebt gemaakt, voegt u het IPv4-adres aan de recordset toe met `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### Een AAAA-recordset met één record maken

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### Een CNAME-recordset met één record maken

CNAME-records staan slechts één tekenreekswaarde toe.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### Een MX-recordset met één record maken

In dit voorbeeld gebruiken we de naam van de recordset '@' om de MX-record te maken in het toppunt van de zone (in dit geval 'contoso.com'). Dit is gebruikelijk voor MX-records.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### Een NS-recordset met één record maken

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### Een SRV-recordset met één record maken

Als u een SRV-record in de zonebasis maakt, kunt u '_service' en '_protocol' in de recordnaam opgeven. Het is niet nodig om '@' op te nemen in de recordnaam.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### Een TXT-recordset met één record maken

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"



<!--HONumber=Jun16_HO2-->


