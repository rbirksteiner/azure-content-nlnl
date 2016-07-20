> [AZURE.IMPORTANT] Als u pushmeldingen wilt ontvangen van Mobile Engagement moet u `Silent Remote Notifications` in uw toepassing inschakelen. U moet de waarde van de externe melding toevoegen aan de matrix UIBackgroundModes in uw Info.plist-bestand.

1. Open het bestand `info.plist` in het project.
2. Klik met de rechtermuisknop op het eerste item in de lijst (`Information Property List`) en voeg een nieuwe rij toe.

    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)

3. In de nieuwe rij doet u het volgende: `Required background modes`

    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)

4. Klik op de pijl-links om de rij uit te vouwen.
5. Voeg de volgende waarde toe aan het item 0. `App downloads content in response to push notifications`

    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)

6. Zodra u de wijziging hebt aangebracht, moet de XML van info.plist de volgende sleutel en waarde bevatten:

        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>

7. Als u **Xcode 7+** en **iOS 9+** gebruikt:
    - Schakel **Pushmeldingen** in Doelen > uw doelnaam > Mogelijkheden in.



<!--HONumber=Jun16_HO2-->


