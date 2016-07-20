Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Voordat u uw domein kunt gaan hosten, moet u een DNS-zone maken. Alle DNS-records die zijn gemaakt voor een bepaald domein, bevinden zich binnen een DNS-zone voor het domein. 

Het domein 'contoso.com' kan bijvoorbeeld een aantal DNS-records bevatten, zoals 'mail.contoso.com' (voor een e-mailserver) en 'www.contoso.com' (voor een website). 


## <a name="names"></a>Over DNS-zonenamen
 
- De naam van de zone moet uniek zijn binnen de resourcegroep en de zone mag niet al bestaan. Anders mislukt de bewerking.

- Dezelfde zonenaam kan opnieuw worden gebruikt in een andere resourcegroep of een ander Azure-abonnement. 

- Als meerdere zones dezelfde naam delen, krijgt elk exemplaar andere serveradressen toegewezen en kan slechts één exemplaar van het bovenliggende domein worden overgedragen. Zie [Delegate a domain to Azure DNS](../articles/dns/dns-domain-delegation.md) (Een domein aan Azure DNS overdragen) voor meer informatie.


<!--HONumber=Jun16_HO2-->


