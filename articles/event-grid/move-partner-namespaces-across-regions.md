---
title: Azure Event Grid partner-naam ruimten naar een andere regio verplaatsen
description: In dit artikel wordt beschreven hoe u Azure Event Grid partner-naam ruimten van de ene regio naar een andere regio kunt verplaatsen.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89084546"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Azure Event Grid partner-naam ruimten naar een andere regio verplaatsen
Mogelijk wilt u uw resources om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning. 

Dit zijn de stappen op hoog niveau die in dit artikel worden behandeld: 

- **De partner naam ruimte resource exporteren** naar een Azure Resource Manager sjabloon. Definities voor gebeurtenis kanaal resources in de sjabloon verwijderen. Een gebeurtenis kanaal bevat mogelijk een verwijzing naar de Azure Resource Manager-ID van het partner onderwerp, dat eigendom is van een klant. Ze kunnen dus niet worden gemaakt met behulp van de sjabloon in de doel regio.  
- **Gebruik de sjabloon voor het implementeren van de naam ruimte** van de partner naar de doel regio. Maak vervolgens gebeurtenis kanalen in de nieuwe partner naam ruimte in de doel regio. 
- Als u **de verplaatsing wilt volt ooien**, verwijdert u de naam ruimte van de partner uit de bron regio. 

    > [!NOTE]
    > - Het exporteren van **partner onderwerpen** naar een Azure Resource Manager-sjabloon wordt niet ondersteund, omdat klanten niet rechtstreeks een partner onderwerp kunnen maken. 
    > - **Partner registraties** zijn globale resources (niet gebonden aan een bepaalde regio), zodat ze van de ene regio naar een andere regio kunnen worden verplaatst, is niet van toepassing. 

## <a name="prerequisites"></a>Vereisten
- Zorg ervoor dat de Event Grid-Service beschikbaar is in de doel regio. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Voorbereiden
Als u aan de slag wilt gaan, exporteert u een resource manager-sjabloon voor de partner naam ruimte. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. In de zoek balk aan de bovenkant typt u **Event grid partner naam ruimten** en selecteert u **Event grid partner naam ruimten** in de lijst met resultaten. 
3. Selecteer de **partner-naam ruimte** die u wilt exporteren naar een resource manager-sjabloon. 
4. Selecteer op de pagina **Event grid partner naam ruimte** de optie **sjabloon exporteren** onder **instellingen** in het menu links en selecteer vervolgens **downloaden** op de werk balk. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Sjabloon exporteren-> downloaden" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Zoek het **zip** -bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze. Dit zip-bestand bevat JSON-bestanden met de sjabloon en de para meters. 
1. Open de **template.jsop** in een editor van uw keuze. 
8. Update `location` voor het **onderwerp** van de resource naar de doel regio of-locatie. Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van locatie codes. De code voor een regio is de naam van de regio zonder spaties, bijvoorbeeld `West US` is gelijk aan `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Sla** de sjabloon op. 

## <a name="recreate"></a>Opnieuw maken 
Implementeer de sjabloon voor het maken van een partner naam ruimte in de doel regio. 

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
    1. Selecteer bij **locatie** de doel regio. Als u een bestaande resource groep hebt geselecteerd, is deze instelling alleen-lezen. 
    1. Voer voor de naam van de **partner naam ruimte** een naam in voor de nieuwe partner naam ruimte. 
    1. Voor de externe ID van de partner registratie voert u de bron-ID van de partner registratie in met de volgende indeling: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Schakel het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.     
    1. Selecteer **controleren + maken** om het implementatie proces te starten. 
    1. Controleer op de pagina **controleren en maken** de instellingen en selecteer **maken**. 

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen
Als u de verplaatsing wilt volt ooien, verwijdert u de naam ruimte van de partner in de bron regio.  

Als u opnieuw wilt beginnen, verwijdert u de partner naam ruimte in de doel regio en herhaalt u de stappen in de secties voor het [voorbereiden](#prepare) en [opnieuw maken](#recreate) van dit artikel.

Een partner naam ruimte verwijderen met behulp van de Azure Portal:

1. Typ **Event grid partner-naam ruimten** in het venster Zoeken boven aan Azure Portal en selecteer **Event grid partner naam ruimten** in de zoek resultaten. 
2. Selecteer de naam ruimte van de partner die u wilt verwijderen en selecteer **verwijderen** op de werk balk. 
3. **Bevestig** het verwijderen om de partner naam ruimte te verwijderen. 

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u een Event Grid partner naam ruimte van de ene regio naar een andere regio kunt verplaatsen. Raadpleeg de volgende artikelen voor het verplaatsen van systeem onderwerpen, aangepaste onderwerpen en domeinen in verschillende regio's.

- [Systeem onderwerpen verplaatsen tussen regio's](move-system-topics-across-regions.md). 
- [Aangepaste onderwerpen verplaatsen tussen regio's](move-custom-topics-across-regions.md). 
- [Domeinen verplaatsen tussen regio's](move-domains-across-regions.md).

Zie het volgende artikel voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure: [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).