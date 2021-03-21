---
title: Diagnostische logboeken van Azure Service Bus | Microsoft Docs
description: Dit artikel bevat een overzicht van alle operationele en Diagnostische logboeken die beschikbaar zijn voor Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: f2672d61285025eadb8f742ca2518297107595ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585267"
---
# <a name="enable-diagnostics-logs-for-service-bus"></a>Diagnostische logboeken voor Service Bus inschakelen

Wanneer u begint met het gebruik van uw Azure Service Bus-naam ruimte, wilt u wellicht controleren hoe en wanneer uw naam ruimte wordt gemaakt, verwijderd of geopend. Dit artikel bevat een overzicht van alle operationele en Diagnostische logboeken die beschikbaar zijn.

Azure Service Bus ondersteunt momenteel activiteiten en operationele logboeken, die *beheer bewerkingen* vastleggen die worden uitgevoerd op de Azure service bus naam ruimte. In deze logboeken wordt het bewerkings type vastgelegd, inclusief het maken van de wachtrij, de gebruikte resources en de status van de bewerking.

## <a name="operational-logs-schema"></a>Schema van operationele logboeken

Alle logboeken worden opgeslagen in de indeling van de JavaScript Object Notation (JSON) op de volgende twee locaties:

- **AzureActivity**: geeft logboeken weer van bewerkingen en acties die worden uitgevoerd op uw naam ruimte in de Azure portal of via Azure Resource Manager sjabloon implementaties.
- **AzureDiagnostics**: geeft logboeken weer van bewerkingen en acties die worden uitgevoerd op uw naam ruimte met behulp van de API of via beheer-clients in de taal-SDK.

De JSON-teken reeksen van het operationele logboek bevatten de elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ------- | ------- |
| ActivityId: | Interne ID, gebruikt om de opgegeven activiteit te identificeren |
| Gebeurtenisnaam | Naam van bewerking |
| ResourceId | Resource-ID Azure Resource Manager |
| SubscriptionId | Abonnements-id |
| EventTimeString | Bewerkings tijd |
| EventProperties | Bewerkings eigenschappen |
| Status | Bewerkingsstatus |
| Caller | Aanroepen van de bewerking (de Azure Portal-of beheer-client) |
| Categorie | OperationalLogs |

Hier volgt een voor beeld van een JSON-teken reeks voor een operationeel logboek:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Gebeurtenissen en bewerkingen vastgelegd in operationele logboeken

In operationele logboeken worden alle beheer bewerkingen vastgelegd die worden uitgevoerd op de naam ruimte van de Azure Service Bus. Gegevens bewerkingen worden niet vastgelegd vanwege de grote hoeveelheid gegevens bewerkingen die worden uitgevoerd op Azure Service Bus.

> [!NOTE]
> We raden u aan client-side tracering te gebruiken om gegevens bewerkingen beter te kunnen volgen.

De volgende beheer bewerkingen worden vastgelegd in operationele logboeken: 

| Bereik | Bewerking|
|-------| -------- |
| Naamruimte | <ul> <li> Naam ruimte maken</li> <li> Naam ruimte bijwerken </li> <li> Naam ruimte verwijderen </li> <li> Het SharedAccess-beleid van de naam ruimte bijwerken </li> </ul> | 
| Wachtrij | <ul> <li> Wachtrij maken</li> <li> Wachtrij bijwerken</li> <li> Wachtrij verwijderen </li> <li> Verwijder wachtrij voor autoverwijderen </li> </ul> | 
| Onderwerp | <ul> <li> Onderwerp maken </li> <li> Onderwerp bijwerken </li> <li> Onderwerp verwijderen </li> <li> Onderwerp verwijderen autoverwijderen </li> </ul> |
| Abonnement | <ul> <li> Abonnement maken </li> <li> Abonnement bijwerken </li> <li> Abonnement verwijderen </li> <li> Abonnement voor verwijderen autoverwijderen </li> </ul> |

> [!NOTE]
> Op dit moment worden *Lees* bewerkingen niet bijgehouden in de operationele Logboeken.

## <a name="enable-operational-logs"></a>Operationele logboeken inschakelen

Operationele logboeken zijn standaard uitgeschakeld. Ga als volgt te werk om Diagnostische logboeken in te scha kelen:

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Azure service bus-naam ruimte en selecteer vervolgens **Diagnostische instellingen** onder **bewaking**.

   ![De koppeling Diagnostische instellingen](./media/service-bus-diagnostic-logs/image1.png)

1. Selecteer in het deel venster **Diagnostische instellingen** de optie **Diagnostische instelling toevoegen**.  

   ![De koppeling diagnostische instelling toevoegen](./media/service-bus-diagnostic-logs/image2.png)

1. Configureer de diagnostische instellingen door het volgende te doen:

   a. Voer in het vak **naam** een naam in voor de diagnostische instellingen.  

   b. Selecteer een van de volgende drie bestemmingen voor uw Diagnostische logboeken:  
   - Als u **archiveren naar een opslag account** selecteert, moet u het opslag account configureren waarin de diagnostische logboeken worden opgeslagen.  
   - Als u **Stream naar een event hub** selecteert, moet u de Event hub configureren waarnaar u de diagnostische logboeken wilt streamen.
   - Als u **verzenden naar log Analytics** selecteert, moet u opgeven naar welk exemplaar van log Analytics de diagnostische gegevens worden verzonden.  

   c. Schakel het selectie vakje **OperationalLogs** in.

    ![Het deel venster Diagnostische instellingen](./media/service-bus-diagnostic-logs/image3.png)

1. Selecteer **Opslaan**.

De nieuwe instellingen worden in ongeveer 10 minuten van kracht. De logboeken worden weer gegeven in het geconfigureerde archiverings doel in het deel venster **Diagnostische logboeken** .

Zie het [overzicht van Azure Diagnostics-logboeken](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie over het configureren van diagnostische instellingen.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Service Bus raadpleegt u:

* [Inleiding tot Service Bus](service-bus-messaging-overview.md)
* [Aan de slag met Service Bus](service-bus-dotnet-get-started-with-queues.md)