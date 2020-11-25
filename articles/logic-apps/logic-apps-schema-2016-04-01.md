---
title: Schema-updates juni-1-2016
description: De schema versie 2016-06-01 is bijgewerkt voor definities van logische apps in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: ccc7df5bfac327fabf05f210764dbe10658b5015
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000314"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Schema-updates voor Azure Logic Apps van 1 juni 2016

De [bijgewerkte schema](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) -en API-versie voor Azure Logic apps bevatten belang rijke verbeteringen die logische apps betrouwbaarder en gemakkelijker te gebruiken zijn:

* Met [bereiken](#scopes) kunt u acties groeperen of nesten als een verzameling acties.
* [Voor waarden en lussen](#conditions-loops) zijn nu acties voor de eerste klasse.
* Nauw keurige volg orde voor het uitvoeren van acties met de `runAfter` eigenschap, vervangen `dependsOn`

Als u uw Logic apps van het voorbeeld schema van 1 augustus 2015 wilt bijwerken naar het schema van 1 juni 2016, [raadpleegt u de sectie upgrades](#upgrade-your-schema).

<a name="scopes"></a>

## <a name="scopes"></a>Bereiken

Dit schema bevat scopes waarmee u acties kunt groeperen of acties in elkaar moet nesten. Een voor waarde kan bijvoorbeeld een andere voor waarde bevatten. Lees meer over de syntaxis van het [bereik](./logic-apps-control-flow-loops.md)of Bekijk dit voor beeld van een basis bereik:

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>Voor waarden en lussen

In vorige schema versies waren voor waarden en lussen para meters die zijn gekoppeld aan één actie. In dit schema wordt deze beperking opgetrokken, dus voor waarden en lussen zijn nu beschikbaar als actie typen. Meer informatie over [lussen en bereiken](./logic-apps-control-flow-loops.md), [voor waarden](../logic-apps/logic-apps-control-flow-conditional-statement.md)of een overzicht van dit basis voorbeeld waarin een voorwaarde actie wordt weer gegeven:

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>eigenschap runAfter

De `runAfter` eigenschap vervangt `dependsOn` meer nauw keurigheid wanneer u de uitvoerings volgorde opgeeft voor acties op basis van de status van vorige acties. De `dependsOn` eigenschap geeft aan of de actie is uitgevoerd en is geslaagd, op basis van het feit of de vorige actie is geslaagd, mislukt of is overgeslagen, niet het aantal keren dat u de actie wilt uitvoeren. De `runAfter` eigenschap biedt flexibiliteit als een object dat alle actie namen opgeeft waarna het object wordt uitgevoerd. Deze eigenschap definieert ook een matrix met statussen die acceptabel zijn als triggers. Als u bijvoorbeeld wilt dat een actie wordt uitgevoerd nadat de actie is voltooid en nadat actie B is geslaagd of mislukt, stelt u deze eigenschap in `runAfter` :

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>Uw schema bijwerken

Als u een upgrade wilt uitvoeren naar het [meest recente schema](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json), hoeft u slechts een paar stappen uit te voeren. Het upgrade proces omvat het uitvoeren van het upgrade script, het opslaan als een nieuwe logische app, en als u dat wilt, is het mogelijk de vorige logische app te overschrijven.

1. Open uw logische app in de Azure Portal.

2. Ga naar **overzicht**. Kies op de werk balk van de logische app de optie **schema bijwerken**.
   
   ![Een update schema kiezen][1]
   
   De bijgewerkte definitie wordt geretourneerd, die u zo nodig kunt kopiëren en plakken in een resource definitie. 

   > [!IMPORTANT]
   > *Zorg ervoor dat* u **opslaan kiest als** u wilt dat alle verbindings verwijzingen geldig blijven in de bijgewerkte logische app.

3. Kies **Opslaan als** in de werk balk Blade bijwerken.

4. Voer de naam en de status van de logica in. Kies **maken** om uw bijgewerkte logische app te implementeren.

5. Controleer of uw bijgewerkte logische app werkt zoals verwacht.
   
   > [!NOTE]
   > Als u een hand matige of aanvraag trigger gebruikt, wordt de call back-URL in uw nieuwe logische app gewijzigd. Test de nieuwe URL om te controleren of de end-to-end-ervaring werkt. Als u eerdere Url's wilt behouden, kunt u de bestaande logische app klonen.

6. *Optioneel* Als u uw vorige logische app wilt overschrijven met de nieuwe schema versie, kiest u in de werk balk **klonen** naast **schema bijwerken**. Deze stap is alleen nodig als u dezelfde resource-ID of de aanvraag-URL van uw logische app wilt blijven gebruiken.

## <a name="upgrade-tool-notes"></a>Opmerkingen bij upgrade hulpprogramma

### <a name="mapping-conditions"></a>Toewijzings voorwaarden

In de definitie van de upgrade maakt het hulp programma het beste resultaat bij het groeperen van waar en ONWAAR Branch-acties samen als een bereik. Met name het ontwerp patroon van `@equals(actions('a').status, 'Skipped')` wordt weer gegeven als een `else` actie. Als het hulp programma echter niet-herken bare patronen detecteert, kan het hulp programma afzonderlijke voor waarden maken voor zowel de vertakking waar als de onwaar. U kunt, indien nodig, acties opnieuw toewijzen na de upgrade.

#### <a name="foreach-loop-with-condition"></a>foreach-lus met voor waarde

In het nieuwe schema kunt u de filter actie gebruiken om het patroon te repliceren dat gebruikmaakt **van een voor elke** lus met één voor waarde per item. De wijziging wordt echter automatisch uitgevoerd wanneer u een upgrade uitvoert. De voor waarde wordt een filter actie die **voor elke** lus wordt weer gegeven, waarbij alleen een matrix van items wordt geretourneerd die overeenkomen met de voor waarde, en die matrix wordt door gegeven aan **elke** actie. Zie [lussen en bereiken](./logic-apps-control-flow-loops.md)voor een voor beeld.

### <a name="resource-tags"></a>Resourcetags

Nadat u de upgrade hebt uitgevoerd, worden de resource Tags verwijderd, dus u moet ze opnieuw instellen voor de bijgewerkte werk stroom.

## <a name="other-changes"></a>Andere wijzigingen

### <a name="renamed-manual-trigger-to-request-trigger"></a>De naam van de trigger Manual is gewijzigd in de trigger ' aanvraag '

Het `manual` trigger type is afgeschaft en is hernoemd `request` met type `http` . Deze wijziging maakt meer consistentie voor het soort patroon dat wordt gebruikt voor het bouwen van de trigger.

### <a name="new-filter-action"></a>Nieuwe actie filter

Als u een grote matrix omlaag wilt filteren op een kleinere set items, accepteert het nieuwe `filter` type een matrix en een voor waarde, evalueert de voor waarde voor elk item en retourneert een matrix met items die aan de voor waarde voldoen.

### <a name="restrictions-for-foreach-and-until-actions"></a>Beperkingen voor de acties foreach en until

De `foreach` and- `until` lus is beperkt tot één actie.

### <a name="new-trackedproperties-for-actions"></a>Nieuwe ' trackedProperties ' voor acties

Acties kunnen nu een extra eigenschap hebben met `trackedProperties` de naam, die gelijk is aan de `runAfter` `type` Eigenschappen en. Dit object geeft bepaalde actie-invoer of uitvoer op die u wilt opnemen in de telemetrie van Azure diagnostische gegevens die worden verzonden als onderdeel van een werk stroom. Bijvoorbeeld:

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Werk stroom definities voor logische apps maken](../logic-apps/logic-apps-author-definitions.md)
* [Implementatie van logische apps automatiseren](logic-apps-azure-resource-manager-templates-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
