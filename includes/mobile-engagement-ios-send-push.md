###Uw pushcertificaat toegang verlenen tot Mobile Engagement

Om Mobile Engagement namens u pushmeldingen te laten verzenden, moet u het toegang geven tot uw certificaat. Dit kunt u doen door uw certificaat te configureren en in te voeren in de Mobile Engagement-portal. Zorg dat u uw .p12-certificaat verkrijgt zoals uitgelegd in de [Apple-documentatie](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

1. Navigeer naar uw Mobile Engagement-portal. Zorg ervoor dat u zich op de juiste locatie bevindt en klik vervolgens op de knop **Engage** onderaan:

    ![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. Klik op de pagina **Instellingen** in de Engagement-portal. Klik daarna op de sectie **Native pushbericht** om uw p12-certificaat te uploaden:

    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. Selecteer uw p12, upload het en typ uw wachtwoord:

    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>Een melding verzenden naar uw app

We gaan nu een eenvoudige pushmeldingcampagne maken waarbij een pushmelding wordt verzonden naar de app:

1. Navigeer naar het tabblad **Reach** in uw Mobile Engagement-portal.

2. Klik op **Nieuwe aankondiging** om uw pushcampagne te maken.

    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. Stel de eerste velden van uw campagne in:

    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

    -   Geef uw campagne een **naam**. 
    -   Selecteer als **Leveringstijd** **Alleen buiten app**: dit is het eenvoudig Apple-pushmeldingentype met een beetje tekst.
    -   Typ in de meldingentekst eerst de **Titel** die als eerste regel in de pushmelding wordt weergegeven.
    -   Typ vervolgens uw **bericht**. Dit wordt op de tweede regel weergegeven.

4. Blader naar beneden en selecteer in de sectie Inhoud **Alleen melding**.

    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. U hebt nu de meest elementaire campagne gemaakt. Schuif nu naar beneden en klik op de knop **Maken** om uw pushmeldingcampagne op te slaan. 

6. Klik ten slotte op **Activeren** om de pushmelding te verzenden. 

    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

7. U kunt de melding in het meldingencentrum op uw iOS-apparaat als volgt ontvangen:

    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)

8. Als u een Apple Watch hebt die is gekoppeld aan dit iOS-apparaat, ziet u de melding op uw Apple Watch:

    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)


 

 


<!--HONumber=Jun16_HO2-->


