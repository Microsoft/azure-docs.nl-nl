---
title: Programmatisch cloudsynchronisatie configureren met MS Graph API
description: In dit onderwerp wordt beschreven hoe u binnenkomende synchronisatie kunt inschakelen met alleen de Graph API
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
ms.openlocfilehash: 8fe220cf7b5cb8b67e5ab7ded221494e89a28aa5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530267"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Programmatisch cloudsynchronisatie configureren met MS Graph API

In het volgende document wordt beschreven hoe u een nieuw synchronisatieprofiel repliceert met alleen MSGraph-API's.  
De structuur van hoe u dit doet, bestaat uit de volgende stappen.  Dit zijn:

- [Programmatisch cloudsynchronisatie configureren met MS Graph API](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [Basisconfiguratie](#basic-setup)
    - [Tenantvlaggen inschakelen](#enable-tenant-flags)
  - [Service-principals maken](#create-service-principals)
  - [Synchronisatie job maken](#create-sync-job)
  - [Doeldomein bijwerken](#update-targeted-domain)
  - [Wachtwoordhashes synchroniseren inschakelen op de configuratieblade](#enable-sync-password-hashes-on-configuration-blade)
  - [Onbedoeld verwijderen](#accidental-deletes)
    - [De drempelwaarde inschakelen en instellen](#enabling-and-setting-the-threshold)
    - [Verwijderen toestaan](#allowing-deletes)
  - [Synchronisatie-taak starten](#start-sync-job)
  - [Status controleren](#review-status)
  - [Volgende stappen](#next-steps)

Gebruik deze [Microsoft Azure Active Directory-module voor Windows PowerShell](/powershell/module/msonline/) om synchronisatie in te stellen voor een productieten tenant, een vereiste voor het aanroepen van de beheerwebservice voor die tenant.

## <a name="basic-setup"></a>Basisconfiguratie

### <a name="enable-tenant-flags"></a>Tenantvlaggen inschakelen

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Voor de eerste van deze twee opdrachten zijn Azure Active Directory referenties vereist. Deze commandlets identificeren impliciet de tenant en maken deze mogelijk voor synchronisatie.

## <a name="create-service-principals"></a>Service-principals maken
Vervolgens moeten we de [AD2AAD-toepassing/service-principal maken](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true)

U moet deze toepassings-id gebruiken 1a4721b3-e57f-4451-ae87-ef078703ec94. De displayName is de URL van het AD-domein, als deze wordt gebruikt in de portal (bijvoorbeeld contoso.com), maar deze kan een andere naam hebben.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Synchronisatie job maken
De uitvoer van de bovenstaande opdracht retourneren de objectId van de service-principal die is gemaakt. In dit voorbeeld is de objectId 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Gebruik Microsoft Graph om een synchronisatiejob toe te voegen aan die service-principal.  

Documentatie voor het maken van een synchronisatie job vindt u [hier.](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)

Als u de bovenstaande id niet hebt noteerd, kunt u de service-principal vinden door de volgende MS Graph-aanroep uit te uitvoeren. U hebt directory.Read.All-machtigingen nodig om die aanroep te doen:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Zoek vervolgens de naam van uw app in de uitvoer.

Voer de volgende twee opdrachten uit om twee taken te maken: één voor het inrichten van gebruikers/groepen en één voor wachtwoord-hashsynchronisatie. Het is twee keer dezelfde aanvraag, maar met verschillende sjabloon-ID's.


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

Voorbeeld van retourwaarde (voor inrichting):

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

## <a name="update-targeted-domain"></a>Doeldomein bijwerken
Voor deze tenant zijn de object-id en toepassings-id van de service-principal als volgt:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-00000000000 DisplayName: testApp

We moeten het domein bijwerken dat op deze configuratie is gericht, dus werk de geheimen voor dit domein bij.

Zorg ervoor dat de domeinnaam die u gebruikt dezelfde URL is die u hebt ingesteld voor uw on-prem-domeincontroller

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Voeg het volgende sleutel-waardepaar toe aan de onderstaande waarde matrix op basis van wat u probeert te doen:
 - Zowel PHS inschakelen als tenantvlaggen synchroniseren { sleutel: "AppKey", waarde: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - Alleen tenantvlag synchroniseren inschakelen (PHS niet inschakelen) { sleutel: "AppKey", waarde: "{"appKeyScenario":"AD2AADProvisioning"}" }
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

Het verwachte antwoord is ... HTTP 204/Geen inhoud

Hier is de gemarkeerde waarde 'Domein' de naam van het on-premises Active Directory-domein van waaruit vermeldingen moeten worden ingericht voor Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Wachtwoordhashes synchroniseren inschakelen op de configuratieblade

 Deze sectie gaat over het inschakelen van het synchroniseren van wachtwoordhashes voor een bepaalde configuratie. Dit is anders dan het AppKey-geheim waarmee de functievlag op tenantniveau wordt gebruikt. Dit is slechts voor één domein/configuratie. U moet de toepassingssleutel instellen op de PHS-sleutel om dit end-to-end te laten werken.

1. Haal het schema op (waarschuwing dat dit vrij groot is) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Neem deze kenmerktoewijzing CredentialData:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Zoek de volgende objecttoewijzingen met de volgende namen in het schema
 - Active Directory-gebruikers inrichten
 - Active Directory inrichtenOrgPersons

 Objecttoewijzingen zijn binnen het schema.synchronizationRules[0].objectMappings (U kunt er nu van uitgaan dat er slechts 1 synchronisatieregel is)

4. Neem de CredentialData Mapping uit stap (2) en voeg deze in bij de objecttoewijzingen in Stap (3)

 De objecttoewijzing ziet er als volgende uit:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Kopieer/plak de toewijzing uit de bovenstaande stap **AD2AADProvisioning en AD2AADPasswordHash** in de matrix attributeMappings. 

 De volgorde van elementen in deze matrix is niet van belang (de back-end wordt voor u gesorteerd). Wees voorzichtig met het toevoegen van deze kenmerktoewijzing als de naam al in de matrix bestaat (bijvoorbeeld als er al een item in attributeMappings staat met de targetAttributeName CredentialData). U kunt conflictfouten krijgen of de bestaande en nieuwe toewijzingen kunnen worden gecombineerd (meestal niet de gewenste uitkomst). Back-end ontdubbelt niet voor u. 

 Vergeet niet om dit te doen voor gebruikers en inetOrgpersons

5. Het schema opslaan dat u hebt gemaakt 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Voeg het schema toe aan de aanvraag body. 

## <a name="accidental-deletes"></a>Onbedoeld verwijderen
In deze sectie wordt besloten hoe [](how-to-accidental-deletes.md) u programmatisch onbedoeld verwijderen programmatisch in-/uit kunt schakelen en gebruiken.


### <a name="enabling-and-setting-the-threshold"></a>De drempelwaarde inschakelen en instellen
Er zijn twee instellingen per taak die u kunt gebruiken:

 - DeleteThresholdEnabled: hiermee schakelt u onbedoelde preventie van verwijderen voor de taak in wanneer deze is ingesteld op 'true'. Standaard ingesteld op 'true'.
 - DeleteThresholdValue: hiermee definieert u het maximum aantal verwijderingen dat is toegestaan bij elke uitvoering van de taak wanneer onbedoeld verwijderen is ingeschakeld. De waarde is standaard ingesteld op 500.  Dus als de waarde is ingesteld op 500, is het maximale aantal toegestane verwijderen 499 in elke uitvoering.

De instellingen voor drempelwaarde verwijderen maken deel uit van de `SyncNotificationSettings` en kunnen worden gewijzigd via de grafiek. 

We moeten de SyncNotificationSettings bijwerken die op deze configuratie is gericht, dus werk de geheimen bij.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Voeg het volgende sleutel-waardepaar toe aan de onderstaande waarde matrix op basis van wat u probeert te doen:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

De instelling Ingeschakeld in het bovenstaande voorbeeld is voor het in-/uitschakelen van e-mailmeldingen wanneer de taak in quarantaine is geplaatst.


Op dit moment bieden we geen ondersteuning voor PATCH-aanvragen voor geheimen, dus u moet alle waarden toevoegen in de body van de PUT-aanvraag (zoals in het bovenstaande voorbeeld) om de andere waarden te behouden.

De bestaande waarden voor alle geheimen kunnen worden opgehaald met behulp van 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Verwijderen toestaan
Als u wilt toestaan dat de verwijderingen worden doorgestroomd nadat de taak in quarantaine is geplaatst, moet u opnieuw opstarten met alleen ForceDeletes als bereik. 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Synchronisatie-taak starten
De taak kan opnieuw worden opgehaald met de volgende opdracht:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Documentatie voor het ophalen van taken vindt u [hier.](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true) 
 
Als u de taak wilt starten, moet u deze aanvraag indienen met behulp van de object-id van de service-principal die u in de eerste stap hebt gemaakt en de taak-id die is geretourneerd op basis van de aanvraag die de taak heeft gemaakt.

Documentatie voor het starten van een taak vindt u [hier.](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true) 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Het verwachte antwoord is ... HTTP 204/Geen inhoud.

Andere opdrachten voor het beheren van de taak worden hier [beschreven.](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true)
 
Als u een taak opnieuw wilt starten, gebruikt u ...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Status controleren
Haal de taakstatussen op via ...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Kijk in de sectie 'status' van het retourobject voor relevante details

## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)
- [Transformaties](how-to-transformation.md)
- [Azure AD-synchronisatie-API](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
