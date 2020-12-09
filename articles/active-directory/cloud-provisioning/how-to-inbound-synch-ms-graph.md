---
title: Inkomende synchronisatie voor Cloud inrichting met behulp van MS Graph API
description: In dit onderwerp wordt beschreven hoe u binnenkomende synchronisatie inschakelt met alleen de Graph API
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f308f46fc021a1d08f4065d48558a6dd71786c7c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860352"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Inkomende synchronisatie voor Cloud inrichting met behulp van MS Graph API

In het volgende document wordt beschreven hoe u een volledig nieuw synchronisatie profiel repliceert met alleen MSGraph-Api's.  
De structuur van dit proces bestaat uit de volgende stappen.  Dit zijn:

- [Basisconfiguratie](#basic-setup)
- [Service-principals maken](#create-service-principals)
- [Synchronisatie taak maken](#create-sync-job)
- [Beoogd domein bijwerken](#update-targeted-domain)
- [Synchronisatie taak starten](#start-sync-job)
- [Beoordelings status](#review-status)

Gebruik deze [Microsoft Azure Active Directory-module voor Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) opdrachten om synchronisatie in te scha kelen voor een productie Tenant, een vereiste om de beheer webservice aan te roepen voor die Tenant.

## <a name="basic-setup"></a>Basisconfiguratie

### <a name="enable-tenant-flags"></a>Tenant vlaggen inschakelen

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
De eerste van deze twee opdrachten vereisen Azure Active Directory referenties. Deze Commandlets identificeren impliciet de Tenant en scha kelen deze in voor synchronisatie.

## <a name="create-service-principals"></a>Service-principals maken
Vervolgens moet u de [AD2AAD-toepassing/Service-Principal](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http) maken

U moet deze toepassings-ID 1a4721b3-e57f-4451-ae87-ef078703ec94 gebruiken. DisplayName is de URL van het AD-domein als deze wordt gebruikt in de portal (bijvoorbeeld contoso.com), maar het kan een andere naam hebben.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Synchronisatie taak maken
De uitvoer van de bovenstaande opdracht retourneert het objectId van de service-principal die is gemaakt. Voor dit voor beeld is de objectId 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Gebruik Microsoft Graph om een synchronizationJob toe te voegen aan die service-principal.  

Documentatie over het maken van een synchronisatie taak vindt u [hier](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http).

Als u de bovenstaande ID niet hebt geregistreerd, kunt u de Service-Principal vinden door de volgende MS Graph-aanroep uit te voeren. U hebt Directory. Read. alle machtigingen nodig om de volgende aanroep uit te voeren:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Zoek vervolgens naar de naam van uw app in de uitvoer.

Voer de volgende twee opdrachten uit om twee taken te maken: één voor het inrichten van de gebruiker/groep en een voor het synchroniseren van wacht woord-hashes. De aanvraag is twee maal hetzelfde, maar met andere sjabloon-Id's.


Roep de volgende twee aanvragen aan:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

U hebt twee aanroepen nodig als u beide wilt maken.

Voor beeld van retour waarde (voor inrichting):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Beoogd domein bijwerken
Voor deze Tenant zijn de object-id en toepassings-id van de Service-Principal als volgt:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 te gebruiken DisplayName: testApp

We moeten het domein bijwerken waarop deze configuratie is gericht, zodat de geheimen voor dit domein worden bijgewerkt.

Zorg ervoor dat de domein naam die u gebruikt dezelfde URL is die u hebt ingesteld voor uw on-premises domein controller

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

De verwachte reactie is... HTTP 204/geen inhoud

Hier is de gemarkeerde ' domein ' waarde de naam van het on-premises Active Directory domein waarvan de vermeldingen moeten worden ingericht voor Azure Active Directory.

## <a name="start-sync-job"></a>Synchronisatie taak starten
De taak kan opnieuw worden opgehaald via de volgende opdracht:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Documentatie voor het ophalen van taken vindt u [hier](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http). 
 
Als u de taak wilt starten, geeft u deze aanvraag uit met het objectId van de service-principal die u in de eerste stap hebt gemaakt en de taak-id die is geretourneerd door de aanvraag die de taak heeft gemaakt.

Documentatie over het starten van een taak vindt u [hier](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

De verwachte reactie is... HTTP 204/geen-inhoud.

Andere opdrachten voor het beheren van de taak worden [hier](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)beschreven.
 
Als u een taak opnieuw wilt starten, gebruikt u...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Beoordelings status
De status van uw taak ophalen via...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Zoek in de sectie ' status ' van het object Return naar relevante Details

## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
- [Transformaties](how-to-transformation.md)
- [Azure AD-synchronisatie-API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
