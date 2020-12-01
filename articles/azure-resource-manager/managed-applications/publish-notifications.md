---
title: Beheerde apps met meldingen
description: Configureer beheerde toepassingen met webhook-eind punten voor het ontvangen van meldingen over het maken, bijwerken, verwijderen en fouten op de exemplaren van de beheerde toepassing.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: cec17b98daa8eca31cda076921288e2838960511
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434529"
---
# <a name="azure-managed-applications-with-notifications"></a>Door Azure beheerde toepassingen met meldingen

Met Azure Managed Application Notifications kunnen uitgevers acties automatiseren op basis van levenscyclus gebeurtenissen van de beheerde toepassings exemplaren. Uitgevers kunnen aangepaste e-mailhook-eind punten opgeven om gebeurtenis meldingen over nieuwe en bestaande exemplaren van beheerde toepassingen te ontvangen. Uitgevers kunnen aangepaste werk stromen instellen op het moment dat de toepassing wordt ingericht, bijgewerkt en verwijderd.

## <a name="getting-started"></a>Aan de slag
Als u beheerde toepassingen wilt ontvangen, breidt u een openbaar HTTPS-eind punt in en geeft u het op wanneer u de service catalog Application Definition of de Azure Marketplace-aanbieding publiceert.

Hier volgen de aanbevolen stappen om snel aan de slag te gaan:
1. Stel een openbaar HTTPS-eind punt in waarmee de binnenkomende POST-aanvragen worden geregistreerd en geretourneerd `200 OK` .
2. Voeg het eind punt toe aan de service catalog Application Definition of de Azure Marketplace-aanbieding, zoals verderop in dit artikel wordt uitgelegd.
3. Maak een exemplaar van een beheerde toepassing dat verwijst naar de toepassings definitie of Azure Marketplace-aanbieding.
4. Controleer of de meldingen worden ontvangen.
5. Schakel autorisatie in zoals beschreven in de sectie **eindpunt verificatie** van dit artikel.
6. Volg de instructies in de sectie **meldings schema** van dit artikel om de meldings aanvragen te parseren en uw bedrijfs logica te implementeren op basis van de melding.

## <a name="add-service-catalog-application-definition-notifications"></a>Service catalogus-toepassings definitie meldingen toevoegen
#### <a name="azure-portal"></a>Azure Portal
Zie [een service catalogus toepassing publiceren via Azure Portal](./publish-portal.md)om aan de slag te gaan.

![Meldingen voor de toepassings definitie van de Service catalogus in de Azure Portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST-API

> [!NOTE]
> Op dit moment kunt u slechts één eind punt in de `notificationEndpoints` in de eigenschappen van de toepassings definitie opgeven.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Meldingen over beheerde toepassingen voor Azure Marketplace toevoegen
Zie [een Azure-toepassings aanbieding maken](../../marketplace/create-new-azure-apps-offer.md)voor meer informatie.

![Meldingen over beheerde toepassingen van Azure Marketplace in de Azure Portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Gebeurtenistriggers
In de volgende tabel worden alle mogelijke combi Naties van Event type en ProvisioningState en de triggers beschreven:

EventType | ProvisioningState | Trigger voor melding
---|---|---
PUT | Geaccepteerd | De beheerde resource groep is gemaakt en geprojecteerd na het plaatsen van de toepassing (voordat de implementatie binnen de beheerde resource groep is gestart).
PUT | Geslaagd | Volledige inrichting van de beheerde toepassing is voltooid na een PUT.
PUT | Mislukt | Fout bij het inrichten van een toepassings exemplaar op enig moment.
VERZENDEN | Geslaagd | Na een geslaagde PATCH voor het exemplaar van de beheerde toepassing voor het bijwerken van tags, JIT-toegangs beleid of beheerde identiteit.
DELETE | Verwijderen | Zodra de gebruiker een exemplaar van een beheerde app verwijdert.
DELETE | Verwijderd | Na de volledige en geslaagde verwijdering van de beheerde toepassing.
DELETE | Mislukt | Na een fout tijdens het ongedaan maken van de inrichting die de verwijdering blokkeert.
## <a name="notification-schema"></a>Meldings schema
Wanneer u het webhook-eind punt voor het afhandelen van meldingen hebt ingesteld, moet u de payload parseren om belang rijke eigenschappen op te halen om vervolgens te reageren op de melding. Meldingen over beheerde toepassingen voor de Service catalogus en Azure Marketplace bieden veel van dezelfde eigenschappen. Er worden twee kleine verschillen beschreven in de tabel die volgt op de voor beelden.

#### <a name="service-catalog-application-notification-schema"></a>Schema voor toepassings meldingen van Service catalogus
Hier volgt een voor beeld van een service catalogus melding nadat het inrichten van een beheerde toepassings instantie is geslaagd:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Als het inrichten mislukt, wordt een melding met de fout gegevens verzonden naar het opgegeven eind punt.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Schema voor toepassings meldingen van Azure Marketplace

Hier volgt een voor beeld van een service catalogus melding nadat het inrichten van een beheerde toepassings instantie is geslaagd:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Als het inrichten mislukt, wordt een melding met de fout gegevens verzonden naar het opgegeven eind punt.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parameter | Beschrijving
---|---
eventType | Het type gebeurtenis dat de melding heeft geactiveerd. (Bijvoorbeeld PUT, PATCH, DELETE)
applicationId | De volledig gekwalificeerde resource-id van de beheerde toepassing waarvoor de melding is geactiveerd.
eventTime | De tijds tempel van de gebeurtenis die de melding heeft geactiveerd. (Datum en tijd in UTC ISO 8601-indeling.)
provisioningState | De inrichtings status van het exemplaar van de beheerde toepassing. (Bijvoorbeeld geslaagd, mislukt, verwijderen, verwijderd.)
fout | *Wordt alleen opgegeven als de provisioningState is mislukt*. Bevat de fout code, het bericht en de details van het probleem dat de fout heeft veroorzaakt.
applicationDefinitionId | *Alleen opgegeven voor door service catalog beheerde toepassingen*. Hiermee wordt de volledig gekwalificeerde resource-id van de toepassings definitie aangegeven waarvoor het exemplaar van de beheerde toepassing is ingericht.
plannen | *Alleen opgegeven voor door Azure Marketplace beheerde toepassingen*. Vertegenwoordigt de uitgever, aanbieding, SKU en versie van het exemplaar van de beheerde toepassing.
billingDetails | *Alleen opgegeven voor door Azure Marketplace beheerde toepassingen.* De facturerings gegevens van het exemplaar van de beheerde toepassing. Bevat de resourceUsageId die u kunt gebruiken om een query uit te zoeken op Azure Marketplace voor gebruiks gegevens.

## <a name="endpoint-authentication"></a>Eindpunt verificatie
Het webhook-eind punt beveiligen en de authenticiteit van de melding controleren:
1. Geef een query parameter op boven op de webhook-URI, zoals: https \: //your-endpoint.com? sig = GUID. Controleer bij elke melding of de query parameter `sig` de verwachte waarde heeft `Guid` .
2. Geef een GET-aanvraag voor het beheerde toepassings exemplaar op met behulp van applicationId. Controleer of de provisioningState overeenkomt met de provisioningState van de melding om consistentie te garanderen.

## <a name="notification-retries"></a>Meldingen opnieuw proberen

De service Managed Application notification verwacht een `200 OK` reactie van het webhook-eind punt naar de melding. De meldings service probeert opnieuw als het webhook-eind punt een HTTP-fout code retourneert die groter is dan of gelijk is aan 500, als er een fout code van 429 wordt geretourneerd of als het eind punt tijdelijk niet bereikbaar is. Als het webhook-eind punt binnen 10 uur niet beschikbaar is, wordt het meldings bericht verwijderd en wordt de nieuwe poging gestopt.
