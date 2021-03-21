---
title: Resource parameters voor Azure Monitor werkmappen
description: Meer informatie over het gebruik van resource parameters om het verzamelen van resources in werkmappen toe te staan. Gebruik de resource parameters om het bereik in te stellen waaruit de gegevens moeten worden opgehaald.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d4a6d04f88544b3a4f9f9c0c0b290d17f5ef148d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725574"
---
# <a name="workbook-resource-parameters"></a>Resource parameters voor werkmap

Met resource parameters kan resources in werkmappen worden verzameld. Dit is handig bij het instellen van het bereik van waaruit de gegevens moeten worden opgehaald. Een voor beeld: Hiermee staat u gebruikers toe om de set Vm's te selecteren, die later door de grafieken wordt gebruikt wanneer de gegevens worden gepresenteerd.

Waarden van resource-kiezers kunnen afkomstig zijn uit de context van de werkmap, de statische lijst of van Azure resource Graph-query's.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Een resource parameter maken (werkmap resources)
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `Applications`
    2. Parameter type: `Resource picker`
    3. Vereist: `checked`
    4. Meerdere selecties toestaan: `checked`
5. Gegevens ophalen uit: `Workbook Resources`
6. Alleen resource typen toevoegen: `Application Insights`
7. Kies opslaan op de werk balk om de para meter te maken.

![Afbeelding van het maken van een resource parameter met werkmap resources](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Een resource parameter maken (Azure resource Graph)
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `Applications`
    2. Parameter type: `Resource picker`
    3. Vereist: `checked`
    4. Meerdere selecties toestaan: `checked`
5. Gegevens ophalen uit: `Query`
    1. Query type: `Azure Resource Graph`
    2. Geabonneerd `Use default subscriptions`
    3. Voeg dit fragment toe aan het besturings element query
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Kies opslaan op de werk balk om de para meter te maken.

![Afbeelding van het maken van een resource parameter met Azure resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure resource Graph is nog niet beschikbaar in alle Clouds. Zorg ervoor dat deze wordt ondersteund in uw doel-Cloud als u deze aanpak kiest.

[Documentatie voor Azure Resource Graph](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Een resource parameter maken (JSON-lijst)
1. Beginnen met een lege werkmap in de bewerkings modus.
2. Kies _para meters toevoegen_ uit de koppelingen in de werkmap.
3. Klik op de knop Blue _para meter toevoegen_ .
4. Typ in het deel venster nieuwe para meters dat verschijnt:
    1. Parameter naam: `Applications`
    2. Parameter type: `Resource picker`
    3. Vereist: `checked`
    4. Meerdere selecties toestaan: `checked`
5. Gegevens ophalen uit: `JSON`
    1. Voeg dit JSON-fragment toe in het besturings element voor inhoud
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Druk op de knop Blue _Update_ .
6. Stel de `Include only resource types` _Application Insights_ in op
7. Kies opslaan op de werk balk om de para meter te maken.

## <a name="referencing-a-resource-parameter"></a>Verwijzen naar een resource parameter
1. Voeg een besturings element query toe aan de werkmap en selecteer een Application Insights resource.
2. Gebruik de _Application Insights_ vervolg keuzelijst om de para meter aan het besturings element te koppelen. Hiermee stelt u het bereik van de query in op de resources die tijdens de uitvoering worden geretourneerd door de para meter.
4. Voeg dit fragment toe aan het besturings element KQL
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Voer de query uit om de resultaten te bekijken. 

![Afbeelding van een resource parameter waarnaar wordt verwezen in een besturings element query](./media/workbooks-resources/resource-reference.png)

> Deze benadering kan worden gebruikt om resources te binden aan andere besturings elementen, zoals metrische gegevens.

## <a name="resource-parameter-options"></a>Opties voor de resource parameter
| Parameter | Uitleg | Voorbeeld |
| ------------- |:-------------|:-------------|
| `{Applications}` | De geselecteerde resource-ID | _/Subscriptions/<sub-ID>/resourceGroups/<Resource-Group>/providers/<resource-type>/acmeauthentication_ |
| `{Applications:label}` | Het label van de geselecteerde resource | `acmefrontend` |
| `{Applications:value}` | De waarde van de geselecteerde resource | _'/Subscriptions/<sub-ID>/resourceGroups/<Resource-Group>/providers/<resource-type>/acmeauthentication '_ |
| `{Applications:name}` | De naam van de geselecteerde resource | `acmefrontend` |
| `{Applications:resourceGroup}` | De resource groep van de geselecteerde resource | `acmegroup` |
| `{Applications:resourceType}` | Het type van de geselecteerde resource | _micro soft. Insights/onderdelen_ |
| `{Applications:subscription}` | Het abonnement van de geselecteerde resource |  |
| `{Applications:grid}` | Een raster met de eigenschappen van de resource. Handig om weer te geven in een tekst blok tijdens fout opsporing  |  |

## <a name="next-steps"></a>Volgende stappen

* [Ga](./workbooks-overview.md#visualizations) voor meer informatie over werkmappen veel uitgebreide visualisaties opties.
* De toegang tot uw werkmap resources [beheren](./workbooks-access-control.md) en delen.