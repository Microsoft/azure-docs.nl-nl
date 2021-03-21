---
title: Automatisch schalen gebruiken om e-mail berichten en webhook-waarschuwings meldingen te verzenden
description: Meer informatie over het gebruik van acties voor automatisch schalen voor het aanroepen van web-Url's of het verzenden van e-mail meldingen in Azure Monitor.
ms.topic: conceptual
ms.date: 04/03/2017
ms.subservice: autoscale
ms.openlocfilehash: 3b1f13fd1ce8bedcbe58385d4cee321f1d1405df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100610842"
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Acties voor automatisch schalen gebruiken voor het verzenden van e-mail berichten en waarschuwingen voor webhooks in Azure Monitor
Dit artikel laat u zien hoe u triggers kunt instellen, zodat u specifieke Web-Url's aanroept of e-mail berichten verzendt op basis van acties voor automatisch schalen in Azure.  

## <a name="webhooks"></a>Webhooks
Met webhooks kunt u de meldingen van Azure-waarschuwingen naar andere systemen routeren voor nabewerkingen of aangepaste meldingen. U kunt de waarschuwing bijvoorbeeld routeren naar services die een inkomende webaanvraag voor het verzenden van SMS-berichten, het registreren van fouten, het melden van een team via chat-of bericht Services, enzovoort. De webhook-URI moet een geldig HTTP-of HTTPS-eind punt zijn.

## <a name="email"></a>E-mail
E-mail kan worden verzonden naar een geldig e-mail adres. Beheerders en mede beheerders van het abonnement waarbij de regel wordt uitgevoerd, worden ook op de hoogte gesteld.

## <a name="cloud-services-and-app-services"></a>Cloud Services en App Services
U kunt zich aanmelden vanaf de Azure Portal voor Cloud Services en server farms (App Services).

* Kies de **schaal op basis van** metrische gegevens.

![schalen op](./media/autoscale-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Schaalsets voor virtuele machines
Als u nieuwere Virtual Machines hebt gemaakt met Resource Manager (virtuele-machine schaal sets), kunt u dit configureren met behulp van REST API, Resource Manager-sjablonen, Power shell en CLI. Er is nog geen portal-interface beschikbaar.
Wanneer u de REST API-of Resource Manager-sjabloon gebruikt, neemt u het element meldingen op in uw [autoscalesettings](/azure/templates/microsoft.insights/2015-04-01/autoscalesettings) met de volgende opties.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```

| Veld | Ingevuld? | Beschrijving |
| --- | --- | --- |
| bewerking |ja |de waarde moet ' Scale ' zijn |
| sendToSubscriptionAdministrator |ja |de waarde moet ' waar ' of ' onwaar ' zijn |
| sendToSubscriptionCoAdministrators |ja |de waarde moet ' waar ' of ' onwaar ' zijn |
| customEmails |ja |de waarde kan null [] of een teken reeks matrix met e-mail berichten zijn |
| webhooks |ja |waarde kan null of een geldige URI zijn |
| serviceUri |ja |een geldige https-URI |
| properties |ja |waarde moet leeg zijn {} of sleutel-waardeparen kunnen bevatten |

## <a name="authentication-in-webhooks"></a>Verificatie in webhooks
De webhook kan worden geverifieerd met verificatie op basis van tokens, waarbij u de webhook-URI opslaat met een token-ID als een query parameter. Bijvoorbeeld https: \/ /mysamplealert/webcallback? tokenid = sometokenid&someparameter = eenwaarde

## <a name="autoscale-notification-webhook-payload-schema"></a>Automatische schaal melding webhook Payload schema
Wanneer de melding over automatisch schalen wordt gegenereerd, worden de volgende meta gegevens opgenomen in de nettolading van de webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Veld | Ingevuld? | Beschrijving |
| --- | --- | --- |
| status |ja |De status die aangeeft dat er een actie voor automatisch schalen is gegenereerd |
| bewerking |ja |Voor een verhoging van de instanties wordt het ' uitschalen ' en voor een afname van de instanties de waarde ' schaalt in ' |
| context |ja |De actie context voor automatisch schalen |
| tijdstempel |ja |Tijds tempel wanneer de actie voor automatisch schalen is geactiveerd |
| id |Ja |Resource Manager-ID van de instelling voor automatisch schalen |
| naam |Ja |De naam van de instelling voor automatisch schalen |
| nadere |Ja |Uitleg van de actie die de service voor automatisch schalen heeft ondernomen en de wijziging van het aantal instanties |
| subscriptionId |Ja |Abonnements-ID van de doel resource die wordt geschaald |
| resourceGroupName |Ja |De naam van de resource groep van de doel resource die wordt geschaald |
| resourceName |Ja |De naam van de doel resource die wordt geschaald |
| resourceType |Ja |De drie ondersteunde waarden: ' micro soft. classiccompute/domainnames/sleuven/roles '-Cloud service roles ' micro soft. Compute/virtualmachinescalesets '-Virtual Machine Scale Sets en ' micro soft. web/server farms '-web-app |
| resourceId |Ja |Resource Manager-ID van de doel resource die wordt geschaald |
| portalLink |Ja |Azure Portal koppeling naar de overzichts pagina van de doel resource |
| oldCapacity |Ja |Het huidige (oude) exemplaar aantal wanneer automatisch schalen een schaal actie heeft uitgevoerd |
| newCapacity |Ja |Het nieuwe aantal exemplaren dat automatisch wordt geschaald naar de resource |
| properties |Nee |Optioneel. Set <sleutel, waarde> paren (bijvoorbeeld woorden lijst <teken reeks, teken reeks>). Het veld eigenschappen is optioneel. In een aangepaste werk stroom op basis van een gebruikers interface of logische app kunt u sleutels en waarden invoeren die kunnen worden door gegeven met behulp van de payload. Een alternatieve manier om aangepaste eigenschappen door te geven aan de uitgaande webhook-aanroep is het gebruik van de webhook-URI zelf (als query parameters) |
