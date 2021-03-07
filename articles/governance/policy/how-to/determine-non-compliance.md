---
title: Oorzaken van niet-naleving bepalen
description: Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Meer informatie over de oorzaak van de niet-naleving.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: a8168bf22aceaf5cbdec4b1346801aa62b7aa4ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439830"
---
# <a name="determine-causes-of-non-compliance"></a>Oorzaken van niet-naleving bepalen

Wanneer een Azure-resource wordt vastgesteld dat deze niet compatibel is met een beleids regel, is het handig om te begrijpen met welk gedeelte van de regel de resource niet compatibel is. Het is ook handig om te begrijpen welke wijziging een eerder compatibele resource heeft gewijzigd om deze niet-compatibel te maken. Er zijn twee manieren om deze informatie te vinden:

- [Compatibiliteits Details](#compliance-details)
- [Wijzigings overzicht (preview-versie)](#change-history)

## <a name="compliance-details"></a>Compatibiliteits Details

Wanneer een bron niet compatibel is, zijn de compatibiliteits Details voor die bron beschikbaar op de pagina **naleving van beleid** . Het deel venster nalevings Details bevat de volgende informatie:

- Resource Details, zoals naam, type, locatie en Resource-ID
- Nalevings status en tijds tempel van de laatste evaluatie van de huidige beleids toewijzing
- Een lijst met _redenen_ voor niet-naleving van de resource

> [!IMPORTANT]
> Als de compatibiliteits Details voor een _niet-compatibele_ resource de huidige waarde van eigenschappen voor die resource weer geven, moet de gebruiker een **Lees** bewerking hebben voor het **type** resource. Als de _niet-compatibele_ resource bijvoorbeeld **micro soft. Compute/informatie** is, moet de gebruiker de bewerking **micro soft. Compute/informatie/Read** hebben. Als de gebruiker niet de benodigde bewerking heeft, wordt er een toegangs fout weer gegeven.

Voer de volgende stappen uit om de compatibiliteits gegevens weer te geven:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer op de pagina **overzicht** of **naleving** een beleids regel in een **nalevings status** die _niet compatibel_ is.

1. Klik op het tabblad **resource naleving** van de pagina **naleving van beleid** met de rechter muisknop of selecteer het weglatings teken van een resource in een **compatibiliteits status** die _niet compatibel_ is. Selecteer vervolgens **nalevings details weer geven**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Scherm afbeelding van de koppeling ' nalevings details weer geven ' op het tabblad Resource naleving." border="false":::

1. In het deel venster **nalevings Details** worden de gegevens van de laatste evaluatie van de resource weer gegeven aan de huidige beleids toewijzing. In dit voor beeld wordt het veld **micro soft. SQL/servers/versie** _12,0_ aangetroffen terwijl de beleids definitie _14,0_ werd verwacht. Als de resource meerdere redenen niet voldoet, wordt deze in dit deel venster weer gegeven.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Scherm afbeelding van het deel venster nalevings Details en de redenen voor niet-naleving van de huidige waarde is twaalf en de doel waarde is 14." border="false":::

   Voor een **auditIfNotExists** -of **deployIfNotExists** -beleids definitie bevatten de details de eigenschap **Details. type** en eventuele optionele eigenschappen. Zie Eigenschappen van [auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) en [deployIfNotExists](../concepts/effects.md#deployifnotexists-properties)voor een lijst. De **laatste geëvalueerde resource** is een gerelateerde resource uit de sectie **Details** van de definitie.

   Voor beeld van een gedeeltelijke **deployIfNotExists** definitie:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Scherm opname van het deel venster met nalevings Details voor ifNotExists, inclusief het aantal geëvalueerde resources." border="false":::

> [!NOTE]
> Voor het beveiligen van gegevens, wanneer een eigenschaps waarde een _geheim_ is, wordt de huidige waarde sterretjes weer gegeven.

In deze details wordt uitgelegd waarom een resource momenteel niet compatibel is, maar niet wanneer de wijziging is aangebracht in de resource waardoor deze niet-compatibel is. Zie voor deze informatie de [wijzigings geschiedenis (preview-versie)](#change-history) hieronder.

### <a name="compliance-reasons"></a>Nalevings redenen

De volgende matrix wijst elke mogelijke _reden_ toe aan de verantwoordelijke [voor waarde](../concepts/definition-structure.md#conditions) in de beleids definitie:

|Reden | Voorwaarde |
|-|-|
|De huidige waarde moet de doel waarde als sleutel bevatten. |containsKey of **niet** notContainsKey |
|De huidige waarde moet de doel waarde bevatten. |bevat of **niet** notContains |
|De huidige waarde moet gelijk zijn aan de doel waarde. |is gelijk aan of **niet** notEquals |
|De huidige waarde moet kleiner zijn dan de doel waarde. |minder of **niet** greaterOrEquals |
|De huidige waarde moet groter zijn dan of gelijk zijn aan de doel waarde. |greaterOrEquals of **niet** minder |
|De huidige waarde moet groter zijn dan de doel waarde. |groter of **niet** lessOrEquals |
|De huidige waarde moet kleiner zijn dan of gelijk zijn aan de doel waarde. |lessOrEquals of **niet** groter |
|De huidige waarde moet bestaan. |reeds |
|De huidige waarde moet de doel waarde hebben. |in of **niet** notIn |
|De huidige waarde moet vergelijkbaar zijn met de doel waarde. |zoals of **niet** notLike |
|De huidige waarde moet hoofdletter gevoelig overeenkomen met de doel waarde. |komt overeen met of **niet** notMatch |
|De huidige waarde moet hoofdletter gevoelig overeenkomen met de doel waarde. |matchInsensitively of **niet** notMatchInsensitively |
|De huidige waarde mag niet de doel waarde als sleutel bevatten. |notContainsKey of **niet** containsKey|
|De huidige waarde mag niet de doel waarde bevatten. |notContains of bevat **niet** |
|De huidige waarde mag niet gelijk zijn aan de doel waarde. |notEquals of is **niet** gelijk aan |
|De huidige waarde mag niet bestaan. |bestaat **niet**  |
|De huidige waarde mag niet de doel waarde hebben. |notIn of **niet** in |
|De huidige waarde mag niet gelijk zijn aan de doel waarde. |notLike of **niet** als |
|De huidige waarde mag niet hoofdletter gevoelig overeenkomen met de doel waarde. |notMatch of komt **niet** overeen |
|De huidige waarde mag niet hoofdletter gevoelig overeenkomen met de doel waarde. |notMatchInsensitively of **niet** matchInsensitively |
|Er zijn geen gerelateerde resources die overeenkomen met de effect Details in de beleids definitie. |Een resource van het type dat is gedefinieerd in **then. Details. type** en gerelateerd aan de resource die is gedefinieerd in het **als** gedeelte van de beleids regel bestaat niet. |

## <a name="component-details-for-resource-provider-modes"></a>Onderdeel Details voor resource provider modi

Voor toewijzingen met een [resource provider modus](../concepts/definition-structure.md#resource-manager-modes)selecteert u de _niet-compatibele_ resource om een diep gaande weer gave te openen. Op het **tabblad Compatibiliteit van onderdelen** is aanvullende informatie specifiek voor de resource provider modus in het toegewezen beleid met het _niet-compatibele_ **onderdeel** en de **onderdeel-id**.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Scherm afbeelding van het tabblad Compatibiliteit van onderdelen en Details van naleving voor een toewijzing van de resource provider modus." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Compliance-details voor gastconfiguratie

Voor _auditIfNotExists_ -beleid in de categorie _gast configuratie_ kunnen er meerdere instellingen worden geëvalueerd in de virtuele machine en moet u Details per instelling bekijken. Als u bijvoorbeeld wilt controleren op een lijst met wachtwoord beleid en slechts een van de statussen _niet-compatibel_ is, moet u weten welke specifieke wachtwoord beleidsregels niet voldoen aan de vereisten en waarom.

Het is ook mogelijk dat u niet rechtstreeks toegang hebt tot de virtuele machine, maar u moet rapporteren waarom de virtuele machine _niet compatibel_ is.

### <a name="azure-portal"></a>Azure Portal

Volg dezelfde stappen in de bovenstaande sectie voor het weer geven van nalevings Details van het beleid.

Selecteer in het deel venster compatibiliteits Details de koppeling **laatste geëvalueerde resource**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Scherm opname van het weer geven van de compatibiliteits Details van de auditIfNotExists-definitie." border="false":::

Op de pagina **gast toewijzing** worden alle beschik bare nalevings details weer gegeven. Elke rij in de weer gave vertegenwoordigt een evaluatie die in de machine is uitgevoerd. In de kolom **reden** wordt een woord groep weer gegeven waarin wordt beschreven waarom de gast toewijzing _niet compatibel_ is. Als u bijvoorbeeld wachtwoord beleid controleert, wordt in de kolom **reden** tekst weer gegeven, inclusief de huidige waarde voor elke instelling.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Scherm afbeelding van de nalevings Details van de gast toewijzing." border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Wijzigings overzicht (preview-versie)

Als onderdeel van een nieuwe **open bare preview** zijn de laatste 14 dagen aan wijzigings geschiedenis beschikbaar voor alle Azure-resources die de verwijdering van de [volledige modus](../../../azure-resource-manager/templates/complete-mode-deletion.md)ondersteunen. Wijzigings overzicht bevat details over wanneer een wijziging is gedetecteerd en een _visueel verschil_ voor elke wijziging. Een wijzigings detectie wordt geactiveerd wanneer de Azure Resource Manager eigenschappen worden toegevoegd, verwijderd of gewijzigd.

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer op de pagina **overzicht** of **naleving** een beleids regel in elke **compatibiliteits status**.

1. Selecteer een resource onder het tabblad **resource naleving** van de pagina **naleving van beleid** .

1. Selecteer het tabblad **wijzigings overzicht (preview)** op de pagina **resource naleving** . Er wordt een lijst weer gegeven met gedetecteerde wijzigingen, indien aanwezig.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Scherm afbeelding van het tabblad wijzigings overzicht en gedetecteerde wijzigings tijden op de pagina Resource naleving." border="false":::

1. Selecteer een van de gedetecteerde wijzigingen. Het _visuele verschil_ voor de resource wordt weer gegeven op de pagina **wijzigings overzicht** .

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Scherm afbeelding van het visuele verschil tussen de status van de voor-en after-eigenschap van de wijzigings geschiedenis op de pagina wijzigings overzicht." border="false":::

De _visuele diff_ -aideds bij het identificeren van wijzigingen aan een resource. De gedetecteerde wijzigingen zijn mogelijk niet gerelateerd aan de huidige nalevings status van de resource.

De wijzigings geschiedenis gegevens worden door [Azure resource Graph](../../resource-graph/overview.md)verschaft. Als u wilt zoeken naar deze informatie buiten de Azure Portal, raadpleegt u [resource wijzigingen ophalen](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](../concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../concepts/effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
