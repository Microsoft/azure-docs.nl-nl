---
title: Azure Event Grid systeem onderwerpen verplaatsen naar een andere regio
description: In dit artikel wordt beschreven hoe u Azure Event Grid systeem onderwerpen van de ene regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: eb6029b206e7d47789371ee81e75c4e05c69ee65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89084529"
---
# <a name="move-azure-event-grid-system-topics-to-another-region"></a>Azure Event Grid systeem onderwerpen verplaatsen naar een andere regio
Mogelijk wilt u uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning. 

Dit zijn de stappen op hoog niveau die in dit artikel worden behandeld: 

- **Exporteer de resource groep** die het Azure Storage-account en het bijbehorende systeem onderwerp bevat naar een resource manager-sjabloon. U kunt ook een sjabloon exporteren voor het onderwerp System. Als u deze route gaat, moet u de Azure-gebeurtenis bron (in dit voor beeld een Azure Storage-account) verplaatsen naar de andere regio voordat u het systeem onderwerp verplaatst. Werk vervolgens in de geëxporteerde sjabloon voor het onderwerp System de externe ID voor het opslag account in de doel regio bij. 
- **Wijzig de sjabloon** om de `endpointUrl` eigenschap toe te wijzen aan een webhook die zich abonneert op het onderwerp van het systeem. Wanneer het onderwerp System wordt geëxporteerd, wordt het abonnement (in dit geval een webhook) ook geëxporteerd naar de sjabloon, maar de `endpointUrl` eigenschap is niet opgenomen. Daarom moet u deze bijwerken zodat deze verwijst naar het eind punt dat zich abonneert op het onderwerp. Werk ook de waarde van de `location` eigenschap bij naar de nieuwe locatie of regio. Voor andere typen gebeurtenis-handlers hoeft u alleen de locatie bij te werken. 
- **Gebruik de sjabloon om resources te implementeren** in de doel regio. U geeft de namen op voor het opslag account en het systeem onderwerp dat moet worden gemaakt in de doel regio. 
- **Controleer de implementatie**. Controleer of de webhook wordt aangeroepen wanneer u een bestand uploadt naar de Blob-opslag in de doel regio. 
- Om **het verplaatsen**, verwijderen van resources (gebeurtenis bron en systeem onderwerp) te volt ooien vanuit de bron regio. 

## <a name="prerequisites"></a>Vereisten
- Voltooi de [Snelstartgids: Stuur gebeurtenissen van Blob-opslag naar een webeindpunt met de Azure Portal](blob-event-quickstart-portal.md) in de bron regio. Deze stap is **optioneel**. Doe dit om de stappen in dit artikel te testen. Bewaar het opslag account in een afzonderlijke resource groep van het App Service en App Service plan. 
- Zorg ervoor dat de Event Grid-Service beschikbaar is in de doel regio. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Voorbereiden
Als u aan de slag wilt gaan, exporteert u een resource manager-sjabloon voor de resource groep die de bron van de systeem gebeurtenis (Azure Storage-account) en het bijbehorende systeem onderwerp bevat. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **resource groepen** in het menu links. Selecteer vervolgens de resource groep die de gebeurtenis bron bevat waarvoor het systeem onderwerp is gemaakt. In het volgende voor beeld is dit het **Azure Storage** -account. De resource groep bevat het opslag account en het bijbehorende systeem onderwerp. 

    :::image type="content" source="./media/move-system-topics-across-regions/resource-group-page.png" alt-text="Pagina resource groep":::        
3. Selecteer in het menu links **sjabloon exporteren** onder **instellingen** en selecteer vervolgens **downloaden** op de werk balk. 

    :::image type="content" source="./media/move-system-topics-across-regions/export-template-menu.png" alt-text="Storage-account-pagina sjabloon exporteren":::        
5. Zoek het **zip** -bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze. Dit zip-bestand bevat JSON-bestanden met de sjabloon en de para meters. 
1. Open de **template.jsop** in een editor van uw keuze. 
1. De URL voor de webhook wordt niet geëxporteerd naar de sjabloon. Voer daarom de volgende stappen uit:
    1. Zoek in het sjabloon bestand naar **webhook**. 
    1. Voeg in de sectie **Eigenschappen** een komma ( `,` ) toe aan het einde van de laatste regel. In dit voorbeeld is het `"preferredBatchSizeInKilobytes": 64`. 
    1. Voeg de `endpointUrl` eigenschap toe met de waarde die is ingesteld op uw webhook-URL, zoals wordt weer gegeven in het volgende voor beeld. 

        ```json
        "destination": {
            "properties": {
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64,
                "endpointUrl": "https://mysite.azurewebsites.net/api/updates"
            },
            "endpointType": "WebHook"
        }
        ```

        > [!NOTE]
        > Voor andere typen gebeurtenis-handlers worden alle eigenschappen geëxporteerd naar de sjabloon. U hoeft de eigenschap alleen bij `location` te werken naar de doel regio, zoals wordt weer gegeven in de volgende stap. 
7. Update `location` voor de bron van het **opslag account** naar de doel regio of-locatie. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .

    ```json
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccounts_spegridstorage080420_name')]",
    "location": "westus",
    ```
8. Herhaal de stap voor `location` het bijwerken van de bron van het **systeem onderwerp** in de sjabloon. 

    ```json
    "type": "Microsoft.EventGrid/systemTopics",
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('systemTopics_spegridsystopic080420_name')]",
    "location": "westus",
    ```
1. **Sla** de sjabloon op. 

## <a name="recreate"></a>Opnieuw maken 
Implementeer de sjabloon voor het maken van een opslag account en een systeem onderwerp voor het opslag account in de doel regio. 

1. Selecteer in de Azure Portal **een resource maken**.
2. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.
3. Selecteer **Sjabloonimlementatie**.
4. Selecteer **Maken**.
5. Selecteer **Bouw uw eigen sjabloon in de editor**.
6. Selecteer **bestand laden** en volg de instructies voor het laden van de **template.jsin** het bestand dat u in de laatste sectie hebt gedownload.
7. Selecteer **Opslaan** om de sjabloon op te slaan. 
8. Voer op de pagina **aangepaste implementatie** de volgende stappen uit. 
    1. Selecteer een Azure-**abonnement**. 
    1. Selecteer een bestaande **resource groep** in de doel regio of maak er een. 
    1. Selecteer bij **regio** de doel regio. Als u een bestaande resource groep hebt geselecteerd, is deze instelling alleen-lezen.
    1. Voer voor de naam van het **systeem onderwerp** een naam in voor het onderwerp System dat wordt gekoppeld aan het opslag account.  
    1. Voer voor de naam van het **opslag account** een naam in voor het opslag account dat moet worden gemaakt in de doel regio. 

        :::image type="content" source="./media/move-system-topics-across-regions/deploy-template.png" alt-text="Resource Manager-sjabloon implementeren":::
    5. Selecteer **Controleren en maken** onderaan de pagina. 
    1. Controleer op de pagina **controleren en maken** de instellingen en selecteer **maken**. 

## <a name="verify"></a>Verifiëren
1. Nadat de implementatie is voltooid, selecteert u de **resource groep goto**. 
1. Controleer op de pagina **resource groep** of de gebeurtenis bron (in dit voor beeld Azure Storage account) en het systeem onderwerp worden gemaakt. 
1. Upload een bestand naar een container in de Azure Blob-opslag en controleer of de webhook de gebeurtenis heeft ontvangen. Zie [een gebeurtenis naar uw eind punt verzenden](blob-event-quickstart-portal.md#send-an-event-to-your-endpoint)voor meer informatie.

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u de verplaatsing wilt volt ooien, verwijdert u de resource groep die het opslag account en het bijbehorende systeem onderwerp bevat in de bron regio.  

Als u opnieuw wilt beginnen, verwijdert u de resource groep in de doel regio en herhaalt u de stappen in de secties voor het [voorbereiden](#prepare) en [opnieuw maken](#recreate) van dit artikel.

Een resource groep (bron of doel) verwijderen met behulp van de Azure Portal:

1. In het zoek venster aan de bovenkant van Azure Portal, typt u **resource groepen** en selecteert u **resource groepen** uit Zoek resultaten. 
2. Selecteer de resource groep die u wilt verwijderen en selecteer **verwijderen** op de werk balk. 

    Resourcegroep verwijderen
3. Voer op de pagina Bevestiging de naam van de resource groep in en selecteer **verwijderen**.  

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u een Azure-gebeurtenis bron en het bijbehorende systeem onderwerp van de ene regio naar een andere regio kunt verplaatsen. Raadpleeg de volgende artikelen voor het verplaatsen van aangepaste onderwerpen, domeinen en partner naam ruimten in verschillende regio's.

- [Aangepaste onderwerpen verplaatsen tussen regio's](move-custom-topics-across-regions.md). 
- [Domeinen verplaatsen tussen regio's](move-domains-across-regions.md). 
- [Partner-naam ruimten verplaatsen tussen regio's](move-partner-namespaces-across-regions.md). 

Zie het volgende artikel voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure: [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).
