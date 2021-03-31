---
title: Azure Event Grid domeinen verplaatsen naar een andere regio
description: In dit artikel wordt beschreven hoe u Azure Event Grid domeinen van de ene regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89084547"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Azure Event Grid domeinen verplaatsen naar een andere regio
Mogelijk wilt u uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning. 

Dit zijn de stappen op hoog niveau die in dit artikel worden behandeld: 

- **De domein resource exporteren** naar een Azure Resource Manager sjabloon. 

    > [!IMPORTANT]
    > De domein resource en onderwerpen in het domein worden geëxporteerd naar de sjabloon. De onderwerpen abonnementen voor domeinen worden niet geëxporteerd. 
- **Gebruik de sjabloon om het domein te implementeren** in de doel regio. 
- **Maak abonnementen voor domein onderwerpen hand matig** in de doel regio. Wanneer u het domein naar een sjabloon in de huidige regio hebt geëxporteerd, worden abonnementen voor domein onderwerpen niet geëxporteerd. Daarom maakt u deze nadat de onderwerpen domein en domein zijn gemaakt in de doel regio. 
- **Controleer de implementatie**. Een gebeurtenis verzenden naar een domein onderwerp in het domein en controleren of de gebeurtenis-handler die aan het abonnement is gekoppeld, is aangeroepen. 
- Als u **het verplaatsen wilt volt ooien**, verwijdert u het domein uit de bron regio. 

## <a name="prerequisites"></a>Vereisten
- Zorg ervoor dat de Event Grid-Service beschikbaar is in de doel regio. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Voorbereiden
Exporteer een resource manager-sjabloon voor het domein om aan de slag te gaan. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Typ **Event grid domeinen** in de zoek balk en selecteer **Event grid domeinen** in de lijst met resultaten. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Event Grid domeinen zoeken en selecteren":::
3. Selecteer het **domein** dat u wilt exporteren naar een resource manager-sjabloon. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Het domein selecteren":::   
4. Selecteer op de pagina **Event grid domein** de optie **sjabloon exporteren** onder **instellingen** in het menu links en selecteer vervolgens **downloaden** op de werk balk. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Sjabloon exporteren-> downloaden" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Domein-en domein onderwerpen worden geëxporteerd. Abonnementen voor domein onderwerpen worden niet geëxporteerd. U moet dus abonnementen voor domein onderwerpen maken nadat u domein onderwerpen hebt verplaatst. 
5. Zoek het **zip** -bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze. Dit zip-bestand bevat JSON-bestanden met de sjabloon en de para meters. 
1. Open de **template.jsop** in een editor van uw keuze. 
8. Update `location` voor de **domein** resource naar de doel regio of-locatie. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Sla** de sjabloon op. 

## <a name="recreate"></a>Opnieuw maken 
Implementeer de sjabloon voor het maken van de onderwerpen domein en domein in de doel regio. 

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
    1. Voer voor de **domein naam** een nieuwe naam in voor het domein. 
    1. Selecteer **Controleren + maken**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Sjabloon implementeren":::        
    1. Nadat de validatie van de sjabloon is geslaagd, selecteert u onder aan de pagina **maken** om de resource te implementeren. 
    1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource groep** om naar de pagina resource groep te gaan. Bevestig dat er zich een domein in de resource groep bevindt. Selecteer het domein. Controleer of er domein onderwerpen aanwezig zijn in het domein. 

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u de verplaatsing wilt volt ooien, verwijdert u het domein in de bron regio.  

Als u opnieuw wilt beginnen, verwijdert u het domein in de doel regio en herhaalt u de stappen in de secties voor het [voorbereiden](#prepare) en [opnieuw maken](#recreate) van dit artikel.

Een domein verwijderen met behulp van de Azure Portal:

1. In het zoek venster aan de bovenkant van Azure Portal, typt u **Event grid domeinen** en selecteert u **Event grid domeinen** in de zoek resultaten. 
2. Selecteer het domein dat u wilt verwijderen en selecteer **verwijderen** op de werk balk. 
3. Voer op de pagina Bevestiging de naam van de resource groep in en selecteer **verwijderen**.  

Als u de resource groep die het domein bevat wilt verwijderen met behulp van de Azure Portal:

1. In het zoek venster aan de bovenkant van Azure Portal, typt u **resource groepen** en selecteert u **resource groepen** uit Zoek resultaten. 
2. Selecteer de resource groep die u wilt verwijderen en selecteer **verwijderen** op de werk balk. 
3. Voer op de pagina Bevestiging de naam van de resource groep in en selecteer **verwijderen**.  

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u een Event Grid domein van de ene regio naar een andere regio kunt verplaatsen. Raadpleeg de volgende artikelen voor het verplaatsen van systeem onderwerpen, aangepaste onderwerpen en partner naam ruimten in verschillende regio's.

- [Systeem onderwerpen verplaatsen tussen regio's](move-system-topics-across-regions.md). 
- [Aangepaste onderwerpen verplaatsen tussen regio's](move-custom-topics-across-regions.md). 
- [Partner-naam ruimten verplaatsen tussen regio's](move-partner-namespaces-across-regions.md).

Zie het volgende artikel voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure: [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).