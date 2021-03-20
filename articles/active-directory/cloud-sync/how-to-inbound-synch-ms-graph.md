---
title: Cloud synchronisatie programmatisch configureren met behulp van MS Graph API
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
ms.openlocfilehash: 6c84636ea86b3b640aef365c1c5d8e634b9a1f48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593157"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>Cloud synchronisatie programmatisch configureren met behulp van MS Graph API

In het volgende document wordt beschreven hoe u een volledig nieuw synchronisatie profiel repliceert met alleen MSGraph-Api's.  
De structuur van dit proces bestaat uit de volgende stappen.  Dit zijn:

- [Basisconfiguratie](#basic-setup)
- [Service-principals maken](#create-service-principals)
- [Synchronisatie taak maken](#create-sync-job)
- [Beoogd domein bijwerken](#update-targeted-domain)
- [Synchronisatie van wacht woord-hashes inschakelen](#enable-sync-password-hashes-on-configuration-blade)
- [Onbedoeld verwijderen](#accidental-deletes)
- [Synchronisatie taak starten](#start-sync-job)
- [Beoordelings status](#review-status)

Gebruik deze [Microsoft Azure Active Directory-module voor Windows PowerShell](/powershell/module/msonline/) opdrachten om synchronisatie in te scha kelen voor een productie Tenant, een vereiste om de beheer webservice aan te roepen voor die Tenant.

## <a name="basic-setup"></a>Basisconfiguratie

### <a name="enable-tenant-flags"></a>Tenant vlaggen inschakelen

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
De eerste van deze twee opdrachten vereisen Azure Active Directory referenties. Deze Commandlets identificeren impliciet de Tenant en scha kelen deze in voor synchronisatie.

## <a name="create-service-principals"></a>Service-principals maken
Vervolgens moet u de [AD2AAD-toepassing/Service-Principal](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) maken

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

Documentatie over het maken van een synchronisatie taak vindt u [hier](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta).

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
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Voeg het volgende sleutel/waarde-paar toe in de onderstaande matrix waarde, op basis van wat u probeert te doen:
 - Tenant vlaggen voor zowel PHS als synchronisatie inschakelen {Key: "AppKey", waarde: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Alleen Tenant vlag voor synchronisatie inschakelen (PHS niet inschakelen) {Key: "AppKey", waarde: "{" appKeyScenario ":" AD2AADProvisioning "}"}
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

De verwachte reactie is... HTTP 204/geen inhoud

Hier is de gemarkeerde ' domein ' waarde de naam van het on-premises Active Directory domein waarvan de vermeldingen moeten worden ingericht voor Azure Active Directory.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Synchronisatie van wacht woord-hashes op de Blade van de configuratie inschakelen

 Deze sectie heeft betrekking op het inschakelen van synchronisatie van wacht woord-hashes voor een bepaalde configuratie. Dit wijkt af van het AppKey-geheim waarmee de functie vlag op Tenant niveau wordt ingeschakeld. Dit geldt alleen voor één domein/configuratie. U moet de toepassings sleutel op de PHS instellen om deze te laten eindigen op het einde.

1. Het schema opruimen (waarschuwing dit is tamelijk groot) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Deze CredentialData-kenmerk toewijzing volgen:
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
3. Zoek de volgende object toewijzingen met de volgende namen in het schema
 - Active Directory gebruikers inrichten
 - Active Directory inetOrgPersons inrichten

 Object toewijzingen bevinden zich in het schema. synchronizationRules [0]. objectMappings (voor nu kunt u aannemen dat er slechts één synchronisatie regel is)

4. Voer de CredentialData-toewijzing uit stap (2) uit en voeg deze in de object toewijzingen in stap (3)

 De object toewijzing ziet er ongeveer als volgt uit:
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
 Kopieer/Plak de toewijzing van de bovenstaande stap voor het **maken van AD2AADProvisioning-en AD2AADPasswordHash-taken** in de attributeMappings-matrix. 

 De volg orde van de elementen in deze matrix is niet van belang (de back-end wordt voor u gesorteerd). Wees voorzichtig met het toevoegen van deze kenmerk toewijzing als de naam al bestaat in de matrix (bijvoorbeeld als er al een item in attributeMappings is met de targetAttributeName CredentialData). er kunnen zich dan conflicterende fouten voordoen, of de bestaande en nieuwe toewijzingen kunnen samen worden gecombineerd (doorgaans geen gewenst resultaat). Back-end ontdubbelt niet voor u. 

 Vergeet niet voor zowel gebruikers als inetOrgpersons

5. Sla het schema op dat u hebt gemaakt 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Voeg het schema toe aan de hoofd tekst van de aanvraag. 

## <a name="accidental-deletes"></a>Onbedoeld verwijderen
In deze sectie wordt beschreven hoe u [per ongeluk onopzettelijke verwijderingen](how-to-accidental-deletes.md) kunt in-of uitschakelen en de software kunt gebruiken.


### <a name="enabling-and-setting-the-threshold"></a>De drempel waarde inschakelen en instellen
Er zijn twee instellingen per taak die u kunt gebruiken:

 - DeleteThresholdEnabled: Hiermee wordt onopzettelijke Verwijder preventie voor de taak ingeschakeld wanneer deze is ingesteld op ' True '. Standaard ingesteld op waar.
 - DeleteThresholdValue: Hiermee definieert u het maximum aantal verwijderingen dat is toegestaan in elke uitvoering van de taak wanneer het onbedoeld verwijderen van een ongeluk is ingeschakeld. De waarde wordt standaard ingesteld op 500.  Als de waarde is ingesteld op 500, is het Maxi maal toegestane aantal verwijderingen voor elke uitvoering 499.

De instellingen voor drempel waarde verwijderen maken deel uit van de `SyncNotificationSettings` en kunnen worden gewijzigd via Graph. 

We moeten de SyncNotificationSettings bijwerken deze configuratie is gericht, dus werk de geheimen bij.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Voeg het volgende sleutel/waarde-paar toe in de onderstaande matrix waarde, op basis van wat u probeert te doen:

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

De instelling ' ingeschakeld ' in bovenstaand voor beeld is voor het in-en uitschakelen van e-mail meldingen wanneer de taak in quarantaine is geplaatst.


Op dit moment bieden we geen ondersteuning voor PATCH aanvragen voor geheimen. Daarom moet u alle waarden toevoegen in de hoofd tekst van de PUT-aanvraag (zoals in het bovenstaande voor beeld) om de andere waarden te behouden.

De bestaande waarden voor alle geheimen kunnen worden opgehaald met behulp van 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Verwijderingen toestaan
Als u wilt dat de verwijderingen door lopen nadat de taak in quarantaine is geplaatst, moet u een herstart met alleen ' ForceDeletes ' als het bereik. 

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






## <a name="start-sync-job"></a>Synchronisatie taak starten
De taak kan opnieuw worden opgehaald via de volgende opdracht:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

Documentatie voor het ophalen van taken vindt u [hier](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta). 
 
Als u de taak wilt starten, geeft u deze aanvraag uit met het objectId van de service-principal die u in de eerste stap hebt gemaakt en de taak-id die is geretourneerd door de aanvraag die de taak heeft gemaakt.

Documentatie over het starten van een taak vindt u [hier](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta). 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

De verwachte reactie is... HTTP 204/geen-inhoud.

Andere opdrachten voor het beheren van de taak worden [hier](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)beschreven.
 
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

- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)
- [Transformaties](how-to-transformation.md)
- [Azure AD-synchronisatie-API](/graph/api/resources/synchronization-overview?view=graph-rest-beta)