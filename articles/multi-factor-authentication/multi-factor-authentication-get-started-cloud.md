<properties 
    pageTitle="Aan de slag met Microsoft Azure Multi-Factor Authentication in de cloud" 
    description="Dit is de pagina Azure Multi-Factor Authentication waarop wordt beschreven hoe u aan de slag kunt met Azure MFA in de cloud." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Aan de slag met Azure Multi-Factor Authentication in de cloud
In het volgende artikel leert u hoe u aan de slag gaat met Azure Multi-Factor Authentication in de cloud.

> [AZURE.NOTE]  De volgende documentatie bevat informatie over hoe u het voor gebruikers mogelijk maakt de **klassieke Azure-Portal** te gebruiken. Als u zoekt naar informatie over het instellen van Azure Multi-Factor Authentication voor O365-gebruikers, raadpleegt u [Setup multi-factor authentication voor Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US) (Multi-Factor Authentication instellen voor Office 365).

![MFA in de Cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Vereisten
Aan de volgende vereisten moet worden voldaan voordat u Azure Multi-Factor Authentication voor uw gebruikers kunt inschakelen. 




- [U moet u aanmelden voor een Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/): als u nog geen Azure-abonnement hebt, moet u er een nemen. Als u net begint en Azure MFA gebruikt, kunt u een proefabonnement nemen
2. [U moet een Multi-Factor Authentication-provider maken](multi-factor-authentication-get-started-auth-provider.md) en deze toewijzen aan uw adreslijst of [licenties toewijzen aan gebruikers](multi-factor-authentication-get-started-assign-licenses.md) 

> [AZURE.NOTE]  Licenties zijn beschikbaar voor gebruikers die Azure MFA, Azure AD Premium of Enterprise Mobility Suite (EMS) hebben.  MFA maakt deel uit van Azure AD Premium en EMS. Als u voldoende licenties hebt, hoeft u geen Multi-Factor Authentication-provider te maken. 
        

## Multi-Factor Authentication voor gebruikers inschakelen
Als u Multi-Factor Authentication wilt inschakelen voor een gebruiker, wijzigt u eenvoudigweg de status van de gebruiker van uitgeschakeld in ingeschakeld.  Zie voor meer informatie over de status van de gebruiker [Gebruikersstatussen in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Gebruik de volgende procedure om MFA in te schakelen voor uw gebruikers.

### Multi-Factor Authentication inschakelen
--------------------------------------------------------------------------------
1.  Meld u als beheerder aan bij de **klassieke Azure-portal**.
2.  Klik aan de linkerkant op **Active Directory**.
3.  Klik onder **Adreslijst** op de adreslijst voor de gebruiker die u wilt inschakelen.
![Klik op Adreslijst](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Klik aan de bovenkant op **Gebruikers**.
5.  Klik onder aan de pagina op **Multi-Factor Authentication beheren**.
![Klik op Adreslijst](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Hiermee wordt een nieuw browsertabblad geopend.  Zoek naar de gebruiker die u wilt inschakelen voor Multi-Factor Authentication. Mogelijk moet u de weergave bovenaan wijzigen. Zorg ervoor dat de status **Uitgeschakeld** is.
![Gebruiker inschakelen](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Plaats een **vinkje** in het vak naast hun naam.
7.  Klik op aan de rechterkant op **Inschakelen**. 
![Gebruiker inschakelen](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Klik op **Multi-Factor Auth inschakelen**.
![Gebruiker inschakelen](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  U zou nu moeten zien dat de status van de gebruiker is gewijzigd van **Uitgeschakeld** in **Ingeschakeld**.
![Gebruikers inschakelen](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Nadat u uw gebruikers hebt ingeschakeld, wordt aanbevolen ze hierover te informeren via e-mail.  U zou hen ook moeten informeren over hoe ze hun niet-browser-apps moeten gebruiken om te voorkomen dat ze worden geblokkeerd.


## Inschakelen van Multi-Factor Authentication automatiseren met PowerShell 

Als u de [status](multi-factor-authentication-whats-next.md) wilt wijzigen met [Azure AD PowerShell](powershell-install-configuremd), kunt u het volgende doen.  U kunt `$st.State` wijzigen zodat deze overeenkomt met een van de volgende statussen:


- Ingeschakeld
- Afgedwongen
- Uitgeschakeld  

> [AZURE.IMPORTANT]  Houd er rekening mee dat wanneer u rechtstreeks vanuit de status Uitgeschakeld naar de status Afgedwongen gaat, oudere verificatieclients niet meer werken omdat de gebruiker de registratieprocedure voor MFA niet heeft doorlopen en dus geen [app-wachtwoord](multi-factor-authentication-whats-next.md#app-passwords) heeft gekregen.  Als u oudere verificatieclients hebt en app-wachtwoorden vereist zijn, dan is het raadzaam om van een status Uitgeschakeld naar een status Ingeschakeld te gaan.  Gebruikers kunnen zich dan registeren en krijgen hun app-wachtwoorden.   
        
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Het gebruik van PowerShell is dan een optie om bulksgewijs gebruikers in te schakelen.  Momenteel bevat de Azure-portal geen functie voor het bulksgewijs inschakelen van gebruikers, en dus moet u elke gebruiker afzonderlijk selecteren.  Dit kan veel tijd kosten als u een groot aantal gebruikers hebt.  Als u een PowerShell-script maakt met behulp van het bovenstaande, kunt u door een lijst met gebruikers gaan en ze inschakelen.  Hier volgt een voorbeeld:
    
    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Zie voor meer informatie over de status van de gebruiker [Gebruikersstatussen in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## Volgende stappen
Nu dat u Multi-Factor Authentication in de cloud hebt ingesteld, kunt u uw implementatie gaan configureren en instellen.  Zie [Azure Multi-Factor Authentication configureren.]



<!--HONumber=Jun16_HO2-->


