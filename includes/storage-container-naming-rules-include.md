Elke blob in Azure-opslag moet zich in een container bevinden. De container maakt deel uit van de blob-naam. Bijvoorbeeld, `mycontainer` is de naam van de container in deze voorbeelden van blob-URI's:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Een containernaam moet een geldige DNS-naam zijn die voldoet aan de volgende naamgevingsregels:

1. Containernamen moeten beginnen met een letter of cijfer en mogen alleen letters, cijfers en het streepje (-) bevatten.
1. Elk streepje (-) moet direct worden voorafgegaan en worden gevolgd door een letter of cijfer; opeenvolgende streepjes zijn in containernamen niet toegestaan.
1. Alle letters in een containernaam moeten kleine letters zijn.
1. Containernamen moeten 3 tot 63 tekens lang zijn.

> [AZURE.IMPORTANT] De naam van een container bestaat alleen uit kleine letters. Als u een hoofdletter in een containernaam opneemt of de naamgevingsregels op een andere manier schendt, wordt mogelijk een 400-fout (ongeldige aanvraag) weergegeven. 


<!--HONumber=Jun16_HO2-->


