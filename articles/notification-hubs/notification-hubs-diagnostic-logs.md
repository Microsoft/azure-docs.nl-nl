---
title: Diagnostische logboeken van Azure Notification Hubs | Microsoft Docs
description: Dit artikel bevat een overzicht van alle operationele en Diagnostische logboeken die beschikbaar zijn voor Azure Notification Hubs.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b98a04a70062461cec603bea83052c4f1224819e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736233"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Diagnostische logboeken voor Notification Hubs inschakelen

Wanneer u begint met het gebruik van de naam ruimte van Azure Notification Hubs, wilt u wellicht controleren hoe en wanneer uw naam ruimte wordt gemaakt, verwijderd of geopend. Dit artikel bevat een overzicht van alle operationele en Diagnostische logboeken die beschikbaar zijn.

Azure Notification Hubs ondersteunt momenteel activiteiten en operationele logboeken, die *beheer bewerkingen* vastleggen die worden uitgevoerd op de Azure notification hubs naam ruimte.

## <a name="diagnostic-logs-schema"></a>Schema voor Diagnostische logboeken

Alle logboeken worden opgeslagen in de indeling van de JavaScript Object Notation (JSON) op de volgende twee locaties:

- **AzureActivity**: geeft logboeken weer van bewerkingen en acties die worden uitgevoerd op uw naam ruimte in de Azure portal of via Azure Resource Manager sjabloon implementaties.
- **AzureDiagnostics**: geeft logboeken weer van bewerkingen en acties die worden uitgevoerd op uw naam ruimte met behulp van de API of via beheer-clients in de taal-SDK.

De JSON-teken reeksen van het diagnostische logboek bevatten de elementen die in de volgende tabel worden weer gegeven:

| Naam | Beschrijving |
| ------- | ------- |
| tijd | UTC-tijds tempel van het logboek |
| resourceId | Relatief pad naar de Azure-resource |
| operationName | De naam van de beheer bewerking |
| category | Logboek categorie. Geldige waarden: `OperationalLogs` |
| callerIdentity | Identiteit van de aanroeper die de beheer bewerking heeft gestart |
| resultType | Status van de beheer bewerking. Geldige waarden: `Succeeded` of `Failed` |
| resultDescription | Beschrijving van de beheer bewerking |
| correlationId | Correlatie-ID van de beheer bewerking (indien opgegeven) |
| callerIpAddress | Het IP-adres van de beller. Leeg voor aanroepen die afkomstig zijn van de Azure Portal |

Hier volgt een voor beeld van een JSON-teken reeks voor een operationeel logboek:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

Het `callerIdentity` veld kan leeg zijn of een JSON-teken reeks met een van de volgende notaties hebben.

Voor aanroepen die afkomstig zijn van de Azure Portal het `identity` veld leeg is. Het logboek kan worden gecorreleerd aan activiteiten Logboeken om de aangemelde gebruiker te bepalen.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Voor aanroepen via Azure Resource Manager het `identity` veld bevat de gebruikers naam van de gebruiker die is aangemeld.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Voor aanroepen naar de Notification Hubs REST API het `identity` veld de naam bevat van het toegangs beleid dat is gebruikt voor het genereren van het SharedAccessSignature-token.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Gebeurtenissen en bewerkingen vastgelegd in operationele logboeken

In operationele logboeken worden alle beheer bewerkingen vastgelegd die worden uitgevoerd op de Azure Notification Hubs-naam ruimte. Gegevens bewerkingen worden niet vastgelegd vanwege de grote hoeveelheid gegevens bewerkingen die worden uitgevoerd op Azure Notification Hubs.

De volgende beheer bewerkingen worden vastgelegd in operationele logboeken: 

| Bereik | Naam van bewerking | Beschrijving van bewerking |
| :-- | :-- | :-- |
| Naamruimte | Micro soft. notification hubs/naam ruimten/authorizationRules/actie | Lijst met autorisatie regels |
| Naamruimte | Micro soft. notification hubs/naam ruimten/authorizationRules/verwijderen | Autorisatie regel verwijderen |
| Naamruimte | Micro soft. notification hubs/naam ruimten/authorizationRules/listkeys ophalen/actie | Lijst met sleutels |
| Naamruimte | Micro soft. notification hubs/naam ruimten/authorizationRules/lezen | Autorisatie regel ophalen |
| Naamruimte | Micro soft. notification hubs/naam ruimten/authorizationRules/regenerateKeys/actie | Sleutels opnieuw genereren |
| Naamruimte | Micro soft. notification hubs/naam ruimten/authorizationRules/schrijven | Autorisatie regel maken of bijwerken |
| Naamruimte | Micro soft. notification hubs/naam ruimten/verwijderen | Naam ruimte verwijderen |
| Naamruimte | Micro soft. notification hubs/naam ruimten/lezen | Naam ruimte ophalen |
| Naamruimte | Micro soft. notification hubs/naam ruimten/schrijven | Naam ruimte maken of bijwerken |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/actie | Lijst met autorisatie regels |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/verwijderen | Autorisatie regel verwijderen |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/listkeys ophalen/actie | Lijst met sleutels |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/lezen | Autorisatie regel lezen |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/regenerateKeys/actie | Sleutels opnieuw genereren |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/schrijven | Autorisatie regel maken of bijwerken |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/verwijderen | Notification hub verwijderen |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/pnsCredentials/actie | PNS-referenties maken, bijwerken of ophalen |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/lezen | Notification hub ophalen |
| Notification Hub | Micro soft. notification hubs/naam ruimten/notification hubs/schrijven | Een notification hub maken of bijwerken |

## <a name="enable-operational-logs"></a>Operationele logboeken inschakelen

Operationele logboeken zijn standaard uitgeschakeld. Ga als volgt te werk om Logboeken in te scha kelen:

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Azure notification hubs-naam ruimte en selecteer vervolgens **Diagnostische instellingen** onder **bewaking**.

   ![De koppeling Diagnostische instellingen](./media/notification-hubs-diagnostic-logs/image-1.png)

1. Selecteer in het deel venster **Diagnostische instellingen** de optie **Diagnostische instelling toevoegen**.  

   ![De koppeling diagnostische instelling toevoegen](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Configureer de diagnostische instellingen door het volgende te doen:

   a. Voer in het vak **naam** een naam in voor de diagnostische instellingen.  

   b. Selecteer een van de volgende drie bestemmingen voor uw Diagnostische logboeken:  
   - Als u **verzenden naar log Analytics werk ruimte** selecteert, moet u opgeven naar welk exemplaar van log Analytics de diagnostische gegevens worden verzonden.  
   - Als u **archiveren naar een opslag account** selecteert, moet u het opslag account configureren waarin de diagnostische logboeken worden opgeslagen.  
   - Als u **Stream naar een event hub** selecteert, moet u de Event hub configureren waarnaar u de diagnostische logboeken wilt streamen.

   c. Schakel het selectie vakje **OperationalLogs** in.

    ![Het deel venster Diagnostische instellingen](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Selecteer **Opslaan**.

De nieuwe instellingen worden in ongeveer 10 minuten van kracht. De logboeken worden weer gegeven in het geconfigureerde archiverings doel in het deel venster **Diagnostische logboeken** .

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het configureren van diagnostische instellingen:
* [Overzicht van Azure Diagnostics-logboeken](../azure-monitor/essentials/platform-logs-overview.md).

Zie voor meer informatie over Azure Notification Hubs:
* [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md)