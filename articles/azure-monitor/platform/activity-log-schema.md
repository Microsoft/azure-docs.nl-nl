---
title: Azure-gebeurtenisschema in het activiteitenlogboek
description: Beschrijft het gebeurtenis schema voor elke categorie in het Azure-activiteiten logboek.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 09/30/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 9bda92667cfc3afb44a55adf3f3c12798a734ddc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522716"
---
# <a name="azure-activity-log-event-schema"></a>Azure-gebeurtenisschema in het activiteitenlogboek
Het [Azure-activiteiten logboek](platform-logs-overview.md) biedt inzicht in alle gebeurtenissen op abonnements niveau die zich in azure hebben voorgedaan. In dit artikel worden de categorieën en het schema voor activiteiten Logboeken beschreven. 

Het schema is afhankelijk van hoe u het logboek opent:
 
- De schema's die in dit artikel worden beschreven, zijn wanneer u het activiteiten logboek opent vanuit het [rest API](/rest/api/monitor/activitylogs). Dit is ook het schema dat wordt gebruikt wanneer u de **JSON** -optie selecteert bij het weer geven van een gebeurtenis in de Azure Portal.
- Zie het laatste sectie [schema van het opslag account en de Event hubs](#schema-from-storage-account-and-event-hubs) voor het schema wanneer u een [Diagnostische instelling](diagnostic-settings.md) gebruikt om het activiteiten logboek te verzenden naar Azure Storage of Azure Event hubs.
- Zie [Azure monitor gegevens referentie](/azure/azure-monitor/reference/) voor het schema wanneer u een [Diagnostische instelling](diagnostic-settings.md) gebruikt om het activiteiten logboek naar een log Analytics-werk ruimte te verzenden.

## <a name="severity-level"></a>Ernstniveau
Elk item in het activiteiten logboek heeft een Ernst niveau. Het Ernst niveau kan een van de volgende waarden hebben:  

| Ernst | Beschrijving |
|:---|:---|
| Kritiek | Gebeurtenissen die de onmiddellijke aandacht vereisen van een systeem beheerder. Kan erop wijzen dat een toepassing of systeem is mislukt of niet meer reageert.
| Fout | Gebeurtenissen die duiden op een probleem, maar geen onmiddellijke aandacht vereisen.
| Waarschuwing | Gebeurtenissen die forewarning aan mogelijke problemen bieden, hoewel er geen echte fout is opgetreden. Geef aan dat een resource niet de ideale status heeft en mogelijk later afnemen in het weer geven van fouten of kritieke gebeurtenissen.  
| Informatief | Gebeurtenissen waarmee niet-kritieke gegevens aan de beheerder worden door gegeven. Vergelijkbaar met een notitie met de volgende tekst: "voor uw gegevens". 

De devlopers van elke resource provider kiezen de ernst niveaus van hun resource vermeldingen. Als gevolg hiervan kan de daad werkelijke ernst variëren, afhankelijk van hoe uw toepassing is gebouwd. Zo zijn items die ' kritiek ' zijn voor een bepaalde bron die in isloation wordt opgenomen, mogelijk niet zo belang rijk als ' fouten ' in een resource type dat is gecentraliseerd voor uw Azure-toepassing. Houd rekening met dit feit bij het bepalen van de gebeurtenissen die moeten worden gewaarschuwd.  

## <a name="categories"></a>Categorieën
Elke gebeurtenis in het activiteiten logboek heeft een bepaalde categorie die wordt beschreven in de volgende tabel. Zie de secties hieronder voor meer informatie over elke categorie en het bijbehorende schema wanneer u het activiteiten logboek opent vanuit de portal, Power shell, CLI en REST API. Het schema is anders wanneer u [het activiteiten logboek streamt naar Storage of event hubs](./resource-logs.md#send-to-azure-event-hubs). In de laatste sectie van het artikel is een toewijzing van de eigenschappen aan het [schema voor bron logboeken](./resource-logs-schema.md) opgenomen.

| Categorie | Beschrijving |
|:---|:---|
| [Administratief](#administrative-category) | Bevat de record van alle bewerkingen voor maken, bijwerken, verwijderen en acties die zijn uitgevoerd via Resource Manager. Voor beelden van beheer gebeurtenissen zijn het maken van een _virtuele machine_ en het verwijderen van de _netwerk beveiligings groep_.<br><br>Elke actie die door een gebruiker of toepassing wordt uitgevoerd met behulp van Resource Manager, is gemodelleerd als een bewerking voor een bepaald bron type. Als het bewerkings type _schrijven_, _verwijderen_ of _actie_ is, worden de records van zowel het begin als het slagen of mislukken van die bewerking vastgelegd in de beheer categorie. Beheer gebeurtenissen omvatten ook eventuele wijzigingen in op rollen gebaseerd toegangs beheer van Azure in een abonnement. |
| [Status van service](#service-health-category) | Bevat de record van alle service status incidenten die zich in azure hebben voorgedaan. Een voor beeld van een Service Health gebeurtenis _SQL Azure in VS-Oost_ ondervindt downtime. <br><br>Service Health gebeurtenissen zijn beschikbaar in zes rassen: _actie vereist_, _assistentie herstel_, _incident_, _onderhoud_, _informatie_ of _beveiliging_. Deze gebeurtenissen worden alleen gemaakt als u een resource in het abonnement hebt die van invloed is op de gebeurtenis.
| [Status van resources](#resource-health-category) | Bevat de record van de resource status gebeurtenissen die zijn opgetreden in uw Azure-resources. Een voor beeld van een Resource Health gebeurtenis is de status van de _virtuele machine is gewijzigd in niet beschikbaar_.<br><br>Resource Health gebeurtenissen kunnen een van de vier statussen vertegenwoordigen: _beschikbaar, niet_ _beschikbaar_, _gedegradeerd_ en _onbekend_. Daarnaast kunnen Resource Health gebeurtenissen worden gecategoriseerd als _platform gestart_ of door de _gebruiker gestart_. |
| [Waarschuwing](#alert-category) | Bevat de registratie van activeringen voor Azure-waarschuwingen. Een voor beeld van een waarschuwings gebeurtenis is _CPU% op myVM heeft de afgelopen vijf minuten meer dan 80_.|
| [Automatisch schalen](#autoscale-category) | Bevat de record van gebeurtenissen die betrekking hebben op de werking van de engine voor automatisch schalen op basis van de instellingen voor automatisch schalen die u hebt gedefinieerd in uw abonnement. Een voor beeld van een automatisch schalen-gebeurtenis is het _Omhoog schalen van de actie voor schalen is mislukt_. |
| [Aanbeveling](#recommendation-category) | Bevat aanbevelings gebeurtenissen van Azure Advisor. |
| [Beveiliging](#security-category) | Bevat de record van waarschuwingen die zijn gegenereerd door Azure Security Center. Een voor beeld van een beveiligings gebeurtenis is een _verdacht dubbel extensie bestand_ dat wordt uitgevoerd. |
| [Beleid](#policy-category) | Bevat records van alle bewerkingen voor effect acties die zijn uitgevoerd door Azure Policy. Voor beelden van beleids gebeurtenissen zijn onder andere _controle_ en _weigeren_. Elke actie die wordt uitgevoerd door beleid, wordt gemodelleerd als een bewerking voor een resource. |

## <a name="administrative-category"></a>Beheer categorie
Deze categorie bevat de record van alle bewerkingen voor maken, bijwerken, verwijderen en acties die zijn uitgevoerd via Resource Manager. Voor beelden van de typen gebeurtenissen die in deze categorie worden weer gegeven zijn onder andere "virtuele machine maken" en "netwerk beveiligings groep verwijderen" elke actie die door een gebruiker of toepassing wordt uitgevoerd met behulp van Resource Manager is gemodelleerd als een bewerking voor een bepaald bron type. Als het bewerkings type schrijven, verwijderen of actie is, worden de records van zowel het begin als het slagen of mislukken van die bewerking vastgelegd in de beheer categorie. De beheer categorie bevat ook eventuele wijzigingen in op rollen gebaseerd toegangs beheer van Azure in een abonnement.

### <a name="sample-event"></a>Voorbeeld gebeurtenis
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschrijvingen van eigenschappen
| Naam van element | Beschrijving |
| --- | --- |
| autorisatie |BLOB van de Azure RBAC-eigenschappen van de gebeurtenis. Bevat meestal de eigenschappen ' Action ', ' Role ' en ' scope '. |
| aanroeper |Het e-mail adres van de gebruiker die de bewerking, UPN-claim of SPN-claim heeft uitgevoerd op basis van Beschik baarheid. |
| detailhandelkanalen |Een van de volgende waarden: ' admin ', ' Operation ' |
| claims |Het JWT-token dat wordt gebruikt door Active Directory om de gebruiker of toepassing te verifiëren om deze bewerking uit te voeren in Resource Manager. |
| correlationId |Meestal een GUID in de teken reeks indeling. Gebeurtenissen die een correlationId delen, horen bij dezelfde uber-actie. |
| beschrijving |Statische tekst beschrijving van een gebeurtenis. |
| eventDataId |De unieke id van een gebeurtenis. |
| eventName | Beschrijvende naam van de beheer gebeurtenis. |
| category | Altijd ' beheer ' |
| httpRequest |BLOB waarmee de HTTP-aanvraag wordt beschreven. Bevat meestal de ' clientRequestId ', ' clientIpAddress ' en ' methode ' (HTTP-methode. Bijvoorbeeld PUT). |
| niveau |Het niveau van de gebeurtenis. Een van de volgende waarden: "kritiek", "fout", "waarschuwing" en "informatief" |
| resourceGroupName |De naam van de resource groep voor de betrokken resource. |
| resourceProviderName |De naam van de resource provider voor de betrokken resource |
| resourceType | Het type resource dat is beïnvloed door een beheer gebeurtenis. |
| resourceId |De resource-ID van de betrokken resource. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |De naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woorden lijst) waarin de details van de gebeurtenis worden beschreven. |
| status |De teken reeks die de status van de bewerking beschrijft. Enkele algemene waarden zijn: gestart, in uitvoering, geslaagd, mislukt, actief, opgelost. |
| subStatus |Doorgaans de HTTP-status code van de bijbehorende REST-aanroep, maar kan ook andere teken reeksen bevatten die een substatus beschrijven, zoals deze algemene waarden: OK (HTTP-status code: 200), gemaakt (HTTP-status code: 201), geaccepteerd (HTTP-status code: 202), geen inhoud (HTTP-status code: 204), ongeldige aanvraag (HTTP-status code: 400), niet gevonden (HTTP-status code: 404), conflict (HTTP-status code) : 409), interne server fout (HTTP-status code: 500), service niet beschikbaar (HTTP-status code: 503), time-out voor gateway (HTTP-status code: 504). |
| eventTimestamp |Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| submissionTimestamp |Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId |Azure-abonnements-ID. |

## <a name="service-health-category"></a>Categorie service status
Deze categorie bevat de record van alle service status incidenten die zich in azure hebben voorgedaan. Een voor beeld van het gebeurtenis type dat in deze categorie wordt weer geven, is ' SQL Azure in VS-Oost ondervindt downtime. ' Service Health-gebeurtenissen zijn beschikbaar in vijf rassen: actie vereist, assistentie herstel, incident, onderhoud, informatie of beveiliging, en alleen als u een resource in het abonnement hebt die van invloed is op de gebeurtenis.

### <a name="sample-event"></a>Voorbeeld gebeurtenis
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Raadpleeg het artikel over [service status meldingen](../../service-health/service-notifications.md) voor documentatie over de waarden in de eigenschappen.

## <a name="resource-health-category"></a>Resource status categorie
Deze categorie bevat de record van de resource status gebeurtenissen die zijn opgetreden in uw Azure-resources. Een voor beeld van het type gebeurtenis dat in deze categorie wordt weer geven, is de status van de virtuele machine is gewijzigd in niet beschikbaar. Resource Health-gebeurtenissen kunnen een van de vier statussen vertegenwoordigen: beschikbaar, niet beschikbaar, gedegradeerd en onbekend. Daarnaast kunnen resource status gebeurtenissen worden gecategoriseerd als platform gestart of door de gebruiker gestart.

### <a name="sample-event"></a>Voorbeeld gebeurtenis

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Beschrijvingen van eigenschappen
| Naam van element | Beschrijving |
| --- | --- |
| detailhandelkanalen | Altijd ' admin, bewerking ' |
| correlationId | Een GUID in de teken reeks indeling. |
| beschrijving |Statische tekst beschrijving van de waarschuwings gebeurtenis. |
| eventDataId |De unieke id van de waarschuwings gebeurtenis. |
| category | Altijd "ResourceHealth" |
| eventTimestamp |Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| niveau |Het niveau van de gebeurtenis. Een van de volgende waarden: "kritiek", "fout", "waarschuwing", "informatief" en "uitgebreid" |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |De naam van de bewerking. |
| resourceGroupName |De naam van de resource groep die de resource bevat. |
| resourceProviderName |Altijd ' micro soft. Resourcehealth/healthevent/Action '. |
| resourceType | Het type resource dat is beïnvloed door een Resource Health gebeurtenis. |
| resourceId | De naam van de resource-ID voor de betrokken resource. |
| status |Een teken reeks die de status van de status gebeurtenis beschrijft. De waarden kunnen zijn: actief, opgelost, InProgress, bijgewerkt. |
| subStatus | Meestal Null voor waarschuwingen. |
| submissionTimestamp |Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId |Azure-abonnements-ID. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woorden lijst) waarin de details van de gebeurtenis worden beschreven.|
| Eigenschappen. title | Een gebruiks vriendelijke teken reeks die de status van de resource beschrijft. |
| Eigenschappen. Details | Een gebruiks vriendelijke teken reeks met een beschrijving van meer informatie over de gebeurtenis. |
| Eigenschappen. currentHealthStatus | De huidige integriteits status van de resource. Een van de volgende waarden: ' beschikbaar ', ' niet beschikbaar ', ' gedegradeerd ' en ' onbekend '. |
| Eigenschappen. previousHealthStatus | De vorige integriteits status van de resource. Een van de volgende waarden: ' beschikbaar ', ' niet beschikbaar ', ' gedegradeerd ' en ' onbekend '. |
| Properties. type | Een beschrijving van het type resource status gebeurtenis. |
| Eigenschappen. oorzaak | Een beschrijving van de oorzaak van de resource status gebeurtenis. ' UserInitiated ' en ' PlatformInitiated '. |


## <a name="alert-category"></a>Categorie van de waarschuwing
Deze categorie bevat de registratie van alle activeringen van klassieke Azure-waarschuwingen. Een voor beeld van het gebeurtenis type dat in deze categorie wordt weer geven, is ' CPU% op myVM is in de afgelopen vijf minuten meer dan 80. ' Een groot aantal Azure-systemen heeft een waarschuwings concept: u kunt een regel van een bepaalde sortering definiëren en een melding ontvangen wanneer de voor waarden overeenkomen met die regel. Telkens wanneer een ondersteund Azure-waarschuwings type ' activeren ' of aan de voor waarden wordt voldaan om een melding te genereren, wordt er ook een record van de activering naar deze categorie van het activiteiten logboek gepusht.

### <a name="sample-event"></a>Voorbeeld gebeurtenis

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Beschrijvingen van eigenschappen
| Naam van element | Beschrijving |
| --- | --- |
| aanroeper | Altijd micro soft. Insights/alertRules |
| detailhandelkanalen | Altijd ' admin, bewerking ' |
| claims | JSON-blob met de SPN (Service Principal Name) of het resource type van de waarschuwings-engine. |
| correlationId | Een GUID in de teken reeks indeling. |
| beschrijving |Statische tekst beschrijving van de waarschuwings gebeurtenis. |
| eventDataId |De unieke id van de waarschuwings gebeurtenis. |
| category | Altijd "waarschuwing" |
| niveau |Het niveau van de gebeurtenis. Een van de volgende waarden: "kritiek", "fout", "waarschuwing" en "informatief" |
| resourceGroupName |De naam van de resource groep voor de betrokken resource als het een metrische waarschuwing is. Voor andere waarschuwings typen is dit de naam van de resource groep die de waarschuwing zelf bevat. |
| resourceProviderName |De naam van de resource provider voor de betrokken resource als het een metrische waarschuwing is. Voor andere waarschuwings typen is dit de naam van de resource provider voor de waarschuwing zelf. |
| resourceId | De naam van de resource-ID voor de betrokken resource als het een metrische waarschuwing is. Voor andere waarschuwings typen is het de resource-ID van de waarschuwings resource zelf. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |De naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woorden lijst) waarin de details van de gebeurtenis worden beschreven. |
| status |De teken reeks die de status van de bewerking beschrijft. Enkele algemene waarden zijn: gestart, in uitvoering, geslaagd, mislukt, actief, opgelost. |
| subStatus | Meestal Null voor waarschuwingen. |
| eventTimestamp |Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| submissionTimestamp |Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId |Azure-abonnements-ID. |

### <a name="properties-field-per-alert-type"></a>Eigenschappen veld per waarschuwings type
Het veld eigenschappen bevat verschillende waarden, afhankelijk van de bron van de waarschuwings gebeurtenis. Twee veelvoorkomende waarschuwings gebeurtenis providers zijn activiteiten logboek waarschuwingen en metrische waarschuwingen.

#### <a name="properties-for-activity-log-alerts"></a>Eigenschappen voor waarschuwingen voor activiteiten logboeken
| Naam van element | Beschrijving |
| --- | --- |
| Eigenschappen. subscriptionId | De abonnements-ID van de gebeurtenis in het activiteiten logboek, waardoor de waarschuwings regel voor het activiteiten logboek wordt geactiveerd. |
| Eigenschappen. eventDataId | De gebeurtenis gegevens-ID van de gebeurtenis in het activiteiten logboek, waardoor de waarschuwings regel voor het activiteiten logboek is geactiveerd. |
| Eigenschappen. resourceGroup | De resource groep van de gebeurtenis in het activiteiten logboek, waardoor de waarschuwings regel voor het activiteiten logboek wordt geactiveerd. |
| Eigenschappen. resourceId | De resource-ID van de gebeurtenis in het activiteiten logboek, waardoor de waarschuwings regel voor het activiteiten logboek wordt geactiveerd. |
| Eigenschappen. eventTimestamp | De gebeurtenis-time stamp van de gebeurtenis in het activiteiten logboek, waardoor de waarschuwings regel voor het activiteiten logboek wordt geactiveerd. |
| Eigenschappen. operationname | De bewerkings naam van de gebeurtenis in het activiteiten logboek, waardoor de waarschuwings regel voor het activiteiten logboek wordt geactiveerd. |
| Eigenschappen. status | De status van de gebeurtenis in het activiteiten logboek die de waarschuwings regel voor het activiteiten logboek heeft veroorzaakt dat deze wordt geactiveerd.|

#### <a name="properties-for-metric-alerts"></a>Eigenschappen voor metrische waarschuwingen
| Naam van element | Beschrijving |
| --- | --- |
| eigenschappen. RuleUri | Resource-ID van de metrische waarschuwings regel zelf. |
| eigenschappen. RuleName | De naam van de waarschuwings regel voor metrische gegevens. |
| eigenschappen. RuleDescription | De beschrijving van de metrische waarschuwings regel (zoals gedefinieerd in de waarschuwings regel). |
| eigenschappen. Spreek | De drempel waarde die wordt gebruikt voor de evaluatie van de metrische waarschuwings regel. |
| eigenschappen. WindowSizeInMinutes | De grootte van het venster die wordt gebruikt in de evaluatie van de metrische waarschuwings regel. |
| eigenschappen. Aggregatie | Het aggregatie type dat is gedefinieerd in de metrische waarschuwings regel. |
| eigenschappen. And | De voorwaardelijke operator die wordt gebruikt voor de evaluatie van de metrische waarschuwings regel. |
| eigenschappen. MetricName | De metrische naam van de metrische gegevens die wordt gebruikt in de evaluatie van de metrische waarschuwings regel. |
| eigenschappen. MetricUnit | De metrische eenheid voor de metrische gegevens die wordt gebruikt in de evaluatie van de metrische waarschuwings regel. |

## <a name="autoscale-category"></a>Categorie voor automatisch schalen
Deze categorie bevat een overzicht van alle gebeurtenissen die betrekking hebben op de werking van de engine voor automatisch schalen op basis van de instellingen voor automatisch schalen die u hebt gedefinieerd in uw abonnement. Een voor beeld van het type gebeurtenis dat in deze categorie wordt weer geven, is de actie omhoog schalen naar boven schalen is mislukt. Met automatisch schalen kunt u het aantal exemplaren in een ondersteund bron type, op basis van een tijd van de dag en/of belasting (metrische gegevens), in een door de instelling voor automatisch schalen schalen. Wanneer aan de voor waarden wordt voldaan om omhoog of omlaag te schalen, worden de gebeurtenissen start en geslaagd of mislukt in deze categorie vastgelegd.

### <a name="sample-event"></a>Voorbeeld gebeurtenis
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Beschrijvingen van eigenschappen
| Naam van element | Beschrijving |
| --- | --- |
| aanroeper | Altijd micro soft. Insights/autoscaleSettings |
| detailhandelkanalen | Altijd ' admin, bewerking ' |
| claims | JSON-blob met de SPN (Service Principal Name) of het resource type van de engine voor automatisch schalen. |
| correlationId | Een GUID in de teken reeks indeling. |
| beschrijving |Statische tekst beschrijving van de gebeurtenis automatisch schalen. |
| eventDataId |De unieke id van de gebeurtenis voor automatisch schalen. |
| niveau |Het niveau van de gebeurtenis. Een van de volgende waarden: "kritiek", "fout", "waarschuwing" en "informatief" |
| resourceGroupName |De naam van de resource groep voor de instelling voor automatisch schalen. |
| resourceProviderName |De naam van de resource provider voor de instelling voor automatisch schalen. |
| resourceId |Resource-ID van de instelling voor automatisch schalen. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |De naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woorden lijst) waarin de details van de gebeurtenis worden beschreven. |
| eigenschappen. Beschrijvingen | Gedetailleerde beschrijving van de werking van de engine voor automatisch schalen. |
| eigenschappen. ResourceName | De resource-ID van de betrokken resource (de resource waarop de schaal actie werd uitgevoerd) |
| eigenschappen. OldInstancesCount | Het aantal exemplaren voordat de actie voor automatisch schalen van kracht werd. |
| eigenschappen. NewInstancesCount | Het aantal exemplaren nadat de actie voor automatisch schalen is doorgevoerd. |
| eigenschappen. LastScaleActionTime | De tijds tempel van het tijdstip waarop de actie voor automatisch schalen heeft plaatsgevonden. |
| status |De teken reeks die de status van de bewerking beschrijft. Enkele algemene waarden zijn: gestart, in uitvoering, geslaagd, mislukt, actief, opgelost. |
| subStatus | Normaal gesp roken is null voor automatisch schalen. |
| eventTimestamp |Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| submissionTimestamp |Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId |Azure-abonnements-ID. |

## <a name="security-category"></a>Beveiligings categorie
Deze categorie bevat alle waarschuwingen die door Azure Security Center worden gegenereerd. Een voor beeld van het gebeurtenis type dat in deze categorie wordt weer geven, is het ' verdachte dubbele extensie bestand uitgevoerd '.

### <a name="sample-event"></a>Voorbeeld gebeurtenis
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschrijvingen van eigenschappen
| Naam van element | Beschrijving |
| --- | --- |
| detailhandelkanalen | Altijd ' bewerking ' |
| correlationId | Een GUID in de teken reeks indeling. |
| beschrijving |Statische tekst beschrijving van de beveiligings gebeurtenis. |
| eventDataId |De unieke id van de beveiligings gebeurtenis. |
| eventName |Beschrijvende naam van de beveiligings gebeurtenis. |
| category | Altijd ' Beveiliging ' |
| Id |De unieke resource-id van de beveiligings gebeurtenis. |
| niveau |Het niveau van de gebeurtenis. Een van de volgende waarden: "kritiek", "fout", "waarschuwing" of "informatief" |
| resourceGroupName |De naam van de resource groep voor de resource. |
| resourceProviderName |De naam van de resource provider voor Azure Security Center. Altijd ' micro soft. Security '. |
| resourceType |Het type resource dat de beveiligings gebeurtenis heeft gegenereerd, zoals ' micro soft. Security/locations/Alerts ' |
| resourceId |De resource-ID van de beveiligings waarschuwing. |
| operationId |Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName |De naam van de bewerking. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woorden lijst) waarin de details van de gebeurtenis worden beschreven. Deze eigenschappen variëren afhankelijk van het type beveiligings waarschuwing. Zie [Deze pagina](../../security-center/security-center-alerts-overview.md) voor een beschrijving van de typen waarschuwingen die afkomstig zijn van Security Center. |
| eigenschappen. Ernst |Het Ernst niveau. Mogelijke waarden zijn ' hoog ', ' gemiddeld ' of ' laag '. |
| status |De teken reeks die de status van de bewerking beschrijft. Enkele algemene waarden zijn: gestart, in uitvoering, geslaagd, mislukt, actief, opgelost. |
| subStatus | Meestal Null voor beveiligings gebeurtenissen. |
| eventTimestamp |Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| submissionTimestamp |Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId |Azure-abonnements-ID. |

## <a name="recommendation-category"></a>Aanbevelings categorie
Deze categorie bevat de record met nieuwe aanbevelingen die voor uw services worden gegenereerd. Een voor beeld van een aanbeveling is ' beschikbaarheids sets gebruiken voor verbeterde fout tolerantie. ' Er zijn vier typen Aanbevelings gebeurtenissen die kunnen worden gegenereerd: hoge Beschik baarheid, prestaties, beveiliging en kosten optimalisatie. 

### <a name="sample-event"></a>Voorbeeld gebeurtenis
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Beschrijvingen van eigenschappen
| Naam van element | Beschrijving |
| --- | --- |
| detailhandelkanalen | Altijd ' bewerking ' |
| correlationId | Een GUID in de teken reeks indeling. |
| beschrijving |Statische tekst beschrijving van de gebeurtenis recommender |
| eventDataId | De unieke id van de aanbevelings gebeurtenis. |
| category | Altijd ' aanbeveling ' |
| Id |De unieke resource-id van de aanbevelings gebeurtenis. |
| niveau |Het niveau van de gebeurtenis. Een van de volgende waarden: "kritiek", "fout", "waarschuwing" of "informatief" |
| operationName |De naam van de bewerking.  Altijd ' micro soft. Advisor/generateRecommendations/Action '|
| resourceGroupName |De naam van de resource groep voor de resource. |
| resourceProviderName |De naam van de resource provider voor de resource waarop deze aanbeveling van toepassing is, zoals ' micro soft. COMPUTe ' |
| resourceType |De naam van het resource type voor de resource waarop deze aanbeveling van toepassing is, zoals ' micro soft. COMPUTe/informatie ' |
| resourceId |De resource-ID van de resource waarop de aanbeveling van toepassing is |
| status | Altijd "actief" |
| submissionTimestamp |Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId |Azure-abonnements-ID. |
| properties |Set `<Key, Value>` paren (dat wil zeggen een woorden lijst) waarin de details van de aanbeveling worden beschreven.|
| Eigenschappen. recommendationSchemaVersion| Schema versie van de aanbevolen eigenschappen die zijn gepubliceerd in de vermelding in het activiteiten logboek |
| Eigenschappen. recommendationCategory | Categorie van de aanbeveling. Mogelijke waarden zijn ' hoge Beschik baarheid ', ' prestaties ', ' Beveiliging ' en ' kosten ' |
| Eigenschappen. recommendationImpact| Impact van de aanbeveling. Mogelijke waarden zijn ' hoog ', ' medium ', ' laag ' |
| Eigenschappen. recommendationRisk| Risico van de aanbeveling. Mogelijke waarden zijn ' error ', ' warning ', ' none ' |

## <a name="policy-category"></a>Beleids categorie

Deze categorie bevat records van alle bewerkingen voor effect acties die worden uitgevoerd door [Azure Policy](../../governance/policy/overview.md). Voor beelden van de typen gebeurtenissen die in deze categorie worden weer gegeven, zijn onder andere _controleren_ en _weigeren_. Elke actie die wordt uitgevoerd door beleid, wordt gemodelleerd als een bewerking voor een resource.

### <a name="sample-policy-event"></a>Voor beeld van beleids gebeurtenis

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Beschrijvingen van eigenschap beleids gebeurtenis

| Naam van element | Beschrijving |
| --- | --- |
| autorisatie | Matrix van eigenschappen van Azure RBAC van de gebeurtenis. Voor nieuwe resources zijn dit de actie en het bereik van de aanvraag die de evaluatie heeft geactiveerd. Voor bestaande resources is de actie ' micro soft. resources/checkPolicyCompliance/lezen '. |
| aanroeper | Voor nieuwe resources, de identiteit die een implementatie heeft gestart. Voor bestaande resources, de GUID van de Microsoft Azure Policy Insights RP. |
| detailhandelkanalen | Beleids gebeurtenissen gebruiken alleen het kanaal ' bewerking '. |
| claims | Het JWT-token dat wordt gebruikt door Active Directory om de gebruiker of toepassing te verifiëren om deze bewerking uit te voeren in Resource Manager. |
| correlationId | Meestal een GUID in de teken reeks indeling. Gebeurtenissen die een correlationId delen, horen bij dezelfde uber-actie. |
| beschrijving | Dit veld is leeg voor beleids gebeurtenissen. |
| eventDataId | De unieke id van een gebeurtenis. |
| eventName | ' BeginRequest ' of ' EndRequest '. "BeginRequest" wordt gebruikt voor uitgestelde auditIfNotExists-en deployIfNotExists-evaluaties en wanneer een deployIfNotExists effect een sjabloon implementatie start. Alle andere bewerkingen retour neren ' EndRequest '. |
| category | Declareert de gebeurtenis in het activiteiten logboek als onderdeel van het beleid. |
| eventTimestamp | Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| Id | De unieke id van de gebeurtenis voor de specifieke resource. |
| niveau | Het niveau van de gebeurtenis. Bij controle wordt gebruikgemaakt van "waarschuwing" en weigeren wordt "fout". Een auditIfNotExists-of deployIfNotExists-fout kan "waarschuwing" of "fout" genereren, afhankelijk van de ernst. Alle andere beleids gebeurtenissen gebruiken ' informatief '. |
| operationId | Een GUID die wordt gedeeld tussen de gebeurtenissen die overeenkomen met één bewerking. |
| operationName | De naam van de bewerking en is rechtstreeks gerelateerd aan het beleids effect. |
| resourceGroupName | De naam van de resource groep voor de geëvalueerde resource. |
| resourceProviderName | De naam van de resource provider voor de geëvalueerde resource. |
| resourceType | Voor nieuwe resources is het type dat wordt geëvalueerd. Voor bestaande resources retourneert ' micro soft. resources/checkPolicyCompliance '. |
| resourceId | Resource-ID van de geëvalueerde resource. |
| status | Teken reeks die de status van het resultaat van het beleids evaluatie beschrijft. De meeste beleids evaluaties retour neren ' geslaagd ', maar een deny-effect retourneert ' failed '. Fouten in auditIfNotExists of deployIfNotExists retour neren ook ' failed '. |
| subStatus | Veld is leeg voor beleids gebeurtenissen. |
| submissionTimestamp | Tijds tempel wanneer de gebeurtenis beschikbaar werd voor het uitvoeren van query's. |
| subscriptionId | Azure-abonnements-ID. |
| Eigenschappen. isComplianceCheck | Retourneert ' false ' wanneer een nieuwe resource wordt geïmplementeerd of de eigenschappen van de Resource Manager van een bestaande resource worden bijgewerkt. Alle andere [evaluatie triggers](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) resulteren in ' waar '. |
| Eigenschappen. resourceLocation | De Azure-regio van de resource die wordt geëvalueerd. |
| Eigenschappen. bovenliggende elementen | Een door komma's gescheiden lijst met bovenliggende beheer groepen die zijn besteld vanuit direct Parent naar het meest verouderde groot ouders. |
| Eigenschappen. beleids regels | Bevat details over de beleids definitie, toewijzing, effect en para meters die door deze beleids evaluatie worden geretourneerd. |
| relatedEvents | Dit veld is leeg voor beleids gebeurtenissen. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schema van opslag account en Event hubs
Wanneer u het Azure-activiteiten logboek streamt naar een opslag account of Event Hub, volgen de gegevens het schema van het [resource logboek](./resource-logs-schema.md). De volgende tabel bevat een overzicht van de eigenschappen van de bovenstaande schema's aan het schema voor bron Logboeken.

> [!IMPORTANT]
> De indeling van activiteiten logboek gegevens die naar een opslag account zijn geschreven, is gewijzigd in JSON-regels op nov. 1, 2018. Zie [voor bereiding voor het wijzigen van de indeling in azure monitor bron logboeken die zijn gearchiveerd in een opslag account](./resource-logs-blob-format.md) voor meer informatie over deze indelings wijziging.


| Schema-eigenschap van bron logboeken | REST API schema-eigenschap van activiteiten logboek | Opmerkingen |
| --- | --- | --- |
| tijd | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resource type, resourceGroupName zijn alle uitgestelde van de resourceId. |
| operationName | operationname. waarde |  |
| category | Onderdeel van de naam van de bewerking | Groepen van het bewerkings type-"schrijven"/"verwijderen"/"actie" |
| resultType | status. waarde | |
| resultSignature | substatus. waarde | |
| resultDescription | beschrijving |  |
| durationMs | N.v.t. | Altijd 0 |
| callerIpAddress | httpRequest. clientIpAddress |  |
| correlationId | correlationId |  |
| identity | Eigenschappen voor claims en autorisatie |  |
| Niveau | Niveau |  |
| location | N.v.t. | Locatie van waar de gebeurtenis is verwerkt. *Dit is niet de locatie van de resource, maar in plaats van de gebeurtenis. Deze eigenschap wordt verwijderd in een toekomstige update.* |
| Eigenschappen | Eigenschappen. eventProperties |  |
| Eigenschappen. eventCategory | category | Als Properties. eventCategory niet aanwezig is, is de categorie ' beheer ' |
| Eigenschappen. eventname | eventName |  |
| Eigenschappen. operationId | operationId |  |
| Eigenschappen. eventProperties | properties |  |

Hieronder volgt een voor beeld van een gebeurtenis die gebruikmaakt van dit schema..

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het activiteiten logboek](platform-logs-overview.md)
* [Een diagnostische instelling maken om het activiteiten logboek te verzenden naar Log Analytics-werk ruimte, Azure-opslag of event hubs](diagnostic-settings.md)
