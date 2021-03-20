---
title: Azure Event Grid aangepaste onderwerpen verplaatsen naar een andere regio
description: Dit artikel laat u zien hoe u Azure Event Grid aangepaste onderwerpen van de ene regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89145333"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Azure Event Grid aangepaste onderwerpen verplaatsen naar een andere regio
Mogelijk wilt u uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning. 

Dit zijn de stappen op hoog niveau die in dit artikel worden behandeld: 

- **De resource van het aangepaste onderwerp exporteren** naar een Azure Resource Manager sjabloon. 

    > [!IMPORTANT]
    > Alleen het aangepaste onderwerp wordt geëxporteerd naar de sjabloon. Abonnementen voor het onderwerp worden niet geëxporteerd.
- **Gebruik de sjabloon om het aangepaste onderwerp te implementeren** in de doel regio. 
- **Maak abonnementen hand matig** in de doel regio. Wanneer u het aangepaste onderwerp naar een sjabloon in de huidige regio hebt geëxporteerd, wordt alleen het onderwerp geëxporteerd. Abonnementen worden niet opgenomen in de sjabloon, dus maak ze hand matig nadat het aangepaste onderwerp in de doel regio is gemaakt. 
- **Controleer de implementatie**. Controleer of het aangepaste onderwerp is gemaakt in de doel regio. 
- Als u **het verplaatsen wilt volt ooien**, verwijdert u het aangepaste onderwerp uit de bron regio. 

## <a name="prerequisites"></a>Vereisten
- Voltooi de [Snelstartgids: aangepaste gebeurtenissen door sturen naar een webeindpunt](custom-event-quickstart-portal.md) in de bron regio. Voer deze stap uit zodat u de stappen in dit artikel kunt testen. 
- Zorg ervoor dat de Event Grid-Service beschikbaar is in de doel regio. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Voorbereiden
Als u aan de slag wilt gaan, exporteert u een resource manager-sjabloon voor het aangepaste onderwerp. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Typ **Event grid onderwerpen** in de zoek balk en selecteer **Event grid onderwerpen** uit de lijst met resultaten. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Event Grid onderwerpen zoeken en selecteren":::
3. Selecteer het **onderwerp** dat u wilt exporteren naar een resource manager-sjabloon. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Het aangepaste onderwerp selecteren":::   
4. Selecteer op de pagina **Event grid onderwerp** de optie **sjabloon exporteren** onder **instellingen** in het menu links en selecteer vervolgens **downloaden** op de werk balk. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Sjabloon exporteren-> downloaden":::   

    > [!IMPORTANT]
    > Alleen het onderwerp wordt naar de sjabloon geëxporteerd. Abonnementen voor het onderwerp worden niet geëxporteerd. U moet dus abonnementen maken voor het onderwerp nadat u het onderwerp naar de doel regio hebt verplaatst. 
5. Zoek het **zip** -bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze. Dit zip-bestand bevat JSON-bestanden met de sjabloon en de para meters. 
1. Open de **template.jsop** in een editor van uw keuze. 
8. Update `location` voor het **onderwerp** van de resource naar de doel regio of-locatie. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .

    ```json
    "type": "Microsoft.EventGrid/topics",
    "apiVersion": "2020-06-01",
    "name": "[parameters('topics_mytopic0130_name')]",
    "location": "westus"
    ```
1. **Sla** de sjabloon op. 

## <a name="recreate"></a>Opnieuw maken 
Implementeer de sjabloon voor het maken van een aangepast onderwerp in de doel regio. 

1. Selecteer in de Azure Portal **een resource maken**.
2. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.
3. Selecteer **Sjabloonimlementatie**.
4. Selecteer **Maken**.
5. Selecteer **Bouw uw eigen sjabloon in de editor**.
6. Selecteer **bestand laden** en volg de instructies voor het laden van de **template.jsin** het bestand dat u in de laatste sectie hebt gedownload.
7. Selecteer **Opslaan** om de sjabloon op te slaan. 
8. Voer op de pagina **aangepaste implementatie** de volgende stappen uit: 
    1. Selecteer een Azure-**abonnement**. 
    1. Selecteer een bestaande **resource groep** in de doel regio of maak er een. 
    1. Selecteer bij **regio** de doel regio. Als u een bestaande resource groep hebt geselecteerd, is deze instelling alleen-lezen. 
    1. Voer voor de **onderwerpnaam** een nieuwe naam in voor het onderwerp. 
    1. Selecteer **Controleren en maken** onderaan de pagina. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Aangepaste implementatie":::
    1. Controleer op de pagina **controleren en maken** de instellingen en selecteer **maken**. 

## <a name="verify"></a>Verifiëren

1. Nadat de implementatie is voltooid, selecteert **u naar resource**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Ga naar resource":::
1. Controleer of de pagina **Event grid onderwerp** wordt weer geven voor het aangepaste onderwerp.   
1. Volg de stappen in het gedeelte [aangepaste gebeurtenissen naar een webeindpunt door sturen](custom-event-quickstart-portal.md#send-an-event-to-your-topic) om gebeurtenissen naar het onderwerp te verzenden. Controleer of de gebeurtenis-handler van webhooks is aangeroepen. 

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u de verplaatsing wilt volt ooien, verwijdert u het aangepaste onderwerp in de bron regio.  

Als u opnieuw wilt beginnen, verwijdert u het onderwerp in de doel regio en herhaalt u de stappen in de secties voor het [voorbereiden](#prepare) en [opnieuw maken](#recreate) van dit artikel.

Een aangepast onderwerp verwijderen met behulp van de Azure Portal:

1. Typ **Event grid onderwerpen** in het venster Zoeken boven aan Azure Portal en selecteer **Event grid onderwerpen** uit Zoek resultaten. 
2. Selecteer het onderwerp dat u wilt verwijderen en selecteer **verwijderen** op de werk balk. 
3. Voer op de pagina Bevestiging de naam van de resource groep in en selecteer **verwijderen**.  

Als u de resource groep met het aangepaste onderwerp wilt verwijderen met behulp van de Azure Portal:

1. In het zoek venster aan de bovenkant van Azure Portal, typt u **resource groepen** en selecteert u **resource groepen** uit Zoek resultaten. 
2. Selecteer de resource groep die u wilt verwijderen en selecteer **verwijderen** op de werk balk. 
3. Voer op de pagina Bevestiging de naam van de resource groep in en selecteer **verwijderen**.  

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u een Event Grid aangepast onderwerp van de ene regio naar een andere regio kunt verplaatsen. Raadpleeg de volgende artikelen voor het verplaatsen van systeem onderwerpen, domeinen en partner naam ruimten in verschillende regio's.

- [Systeem onderwerpen verplaatsen tussen regio's](move-system-topics-across-regions.md). 
- [Domeinen verplaatsen tussen regio's](move-domains-across-regions.md). 
- [Partner-naam ruimten verplaatsen tussen regio's](move-partner-namespaces-across-regions.md).

Zie het volgende artikel voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure: [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).