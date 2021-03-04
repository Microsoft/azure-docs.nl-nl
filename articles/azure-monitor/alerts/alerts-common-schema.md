---
title: Algemeen waarschuwings schema voor waarschuwingen voor Azure Monitor
description: Meer informatie over het algemene waarschuwings schema, waarom u het moet gebruiken en hoe u het kunt inschakelen
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 39ea469b4109f4715db0d0a73ad069121058a46f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038254"
---
# <a name="common-alert-schema"></a>Algemeen waarschuwingsschema

In dit artikel wordt beschreven wat het algemene waarschuwings schema is, wat de voor delen zijn van het gebruik en hoe dit kan worden ingeschakeld.

## <a name="what-is-the-common-alert-schema"></a>Wat is het algemene waarschuwings schema?

Het algemene waarschuwings schema standaardisert de verbruiks ervaring voor waarschuwings meldingen in azure vandaag. In het verleden hebben de drie waarschuwings typen in azure vandaag (metrische gegevens, logboeken en activiteiten Logboeken) hun eigen e-mail sjablonen, webhook-schema's, enzovoort. Met het algemene waarschuwings schema kunt u nu waarschuwings meldingen met een consistent schema ontvangen.

Elk waarschuwings exemplaar beschrijft **de bron die is beïnvloed** en **de oorzaak van de waarschuwing**, en deze instanties worden beschreven in het algemene schema in de volgende secties:
* **Essentials**: een set **gestandaardiseerde velden**, gemeen schappelijk voor alle waarschuwings typen, waarmee wordt beschreven in **welke resource** de waarschuwing zich bevindt, evenals aanvullende algemene meta gegevens voor waarschuwingen (bijvoorbeeld Ernst of beschrijving). 
* **Waarschuwings context**: een set velden waarin de **oorzaak van de waarschuwing** wordt beschreven, met velden die variëren op **basis van het waarschuwings type**. Een metrische waarschuwing heeft bijvoorbeeld velden als de metrische naam en metrische waarde in de context van de waarschuwing, terwijl een waarschuwing in het activiteiten logboek informatie bevat over de gebeurtenis die de waarschuwing heeft gegenereerd. 

De typische integratie scenario's die we horen van klanten, omvatten de route ring van het waarschuwings exemplaar naar het betreffende team op basis van een draai tabel (bijvoorbeeld resource groep), waarna het verantwoordelijke team aan het werk gaat. Met het gemeen schappelijke waarschuwings schema kunt u gestandaardiseerde routerings logica hebben in waarschuwings typen door gebruik te maken van de essentiële velden, waardoor de context velden niet worden gebruikt voor de betrokken teams om verder te onderzoeken.

Dit betekent dat u mogelijk minder integraties kunt hebben, waardoor het proces van het beheren en onderhouden van deze een _veel_ eenvoudiger taak kan worden uitgevoerd. Daarnaast zal toekomstige waarschuwingen voor nettoladingen in de toekomst (bijvoorbeeld aanpassing, diagnose van diagnostiek, enz.) alleen worden verhoogd in het algemene schema.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Welke verbeteringen bieden het algemene schema voor waarschuwingen?

Het algemene waarschuwings schema wordt in eerste instantie in uw waarschuwings meldingen gemanifesteerd. De uitbrei dingen die u ziet, worden hieronder weer gegeven:

| Bewerking | Verbeteringen |
|:---|:---|
| Sms | Een consistente SMS-sjabloon voor alle waarschuwings typen. |
| E-mail | Een consistente en gedetailleerde e-mail sjabloon waarmee u problemen eenvoudig kunt onderzoeken in één oogopslag. Inge sloten diep gaande koppelingen naar het waarschuwings exemplaar op de portal en de betreffende resource zorgen ervoor dat u snel naar het herstel proces kunt gaan. |
| Webhook/Logic-app/Azure function/Automation-Runbook | Een consistente JSON-structuur voor alle waarschuwings typen, waarmee u eenvoudig integraties kunt bouwen voor de verschillende typen waarschuwingen. |

Het nieuwe schema biedt ook een rijkere ervaring op het niveau van waarschuwingen voor zowel de Azure Portal als de Azure mobile app in de onmiddellijke toekomst. 

[Meer informatie over de schema definities voor webhooks/Logic Apps/Azure Functions/Automation-Runbooks.](./alerts-common-schema-definitions.md)

> [!NOTE]
> De volgende acties bieden geen ondersteuning voor het algemene waarschuwings schema: ITSM-connector.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Het algemene waarschuwings schema Hoe kan ik inschakelen?

U kunt in-of afmelden bij het algemene waarschuwings schema via actie groepen, op zowel de portal als via de REST API. De wissel knop om naar het nieuwe schema te scha kelen, bestaat op actie niveau. U moet zich bijvoorbeeld afzonderlijk aanmelden voor een e-mail actie en een webhook-actie.

> [!NOTE]
> 1. De volgende waarschuwings typen ondersteunen standaard het algemene schema (geen opt-in vereist):
>     * Slimme detectie waarschuwingen
> 1. De volgende waarschuwings typen bieden momenteel geen ondersteuning voor het algemene schema:
>     * Waarschuwingen die zijn gegenereerd door [VM Insights](../vm/vminsights-overview.md)
>     * Waarschuwingen die zijn gegenereerd door [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md)

### <a name="through-the-azure-portal"></a>Via de Azure Portal

![Opt-in voor algemeen waarschuwings schema](media/alerts-common-schema/portal-opt-in.png)

1. Open een bestaande of een nieuwe actie in een actie groep. 
1. Selecteer Ja voor de wissel knop om het algemene waarschuwings schema in te scha kelen, zoals wordt weer gegeven.

### <a name="through-the-action-groups-rest-api"></a>Via de actie groepen REST API

U kunt ook de [Action groups-API](/rest/api/monitor/actiongroups) gebruiken om u aan te melden bij het algemene waarschuwings schema. Tijdens het maken van de aanroep [Create of update](/rest/api/monitor/actiongroups/createorupdate) rest API, kunt u de vlag "useCommonAlertSchema" instellen op True (om aan te melden) of ' false ' (om uit te kiezen) voor een van de volgende acties: e-mail/webhook/Logic app/Azure function/Automation runbook.

De volgende hoofd tekst van de aanvraag in het REST API [maken of bijwerken](/rest/api/monitor/actiongroups/createorupdate) gaat bijvoorbeeld als volgt te werk:

* Schakel het algemene waarschuwings schema in voor de e-mail actie ' e-mail adres van John Splinter '
* Het algemene waarschuwings schema uitschakelen voor de e-mail actie "Rob Smith e-mail"
* Het algemene waarschuwings schema inschakelen voor de webhook-actie "voor beeld van webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Volgende stappen

- [Algemene schema definities voor waarschuwingen voor webhooks/Logic Apps/Azure Functions/Automation-Runbooks.](./alerts-common-schema-definitions.md)
- [Meer informatie over het maken van een logische app die gebruikmaakt van het algemene waarschuwings schema voor het afhandelen van al uw waarschuwingen.](./alerts-common-schema-integrations.md)