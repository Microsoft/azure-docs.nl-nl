---
title: Het Azure Active Directory controle logboek schema in Azure Monitor interpreteren | Microsoft Docs
description: Beschrijf het schema voor het audit logboek van Azure AD voor gebruik in Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4d32e0b939731d64f3ef2495ab606b5dc0780e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592288"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>Het Azure AD audit logs-schema interpreteren in Azure Monitor (preview)

In dit artikel wordt het schema voor het audit logboek voor Azure Active Directory (Azure AD) beschreven in Azure Monitor. Elke afzonderlijke logboek vermelding wordt als tekst opgeslagen en opgemaakt als een JSON-blob, zoals wordt weer gegeven in de volgende twee voor beelden: 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>Beschrijvingen van velden en eigenschappen

| Veldnaam | Beschrijving |
|------------|-------------|
| tijd       | De datum en tijd (UTC). |
| operationName | De naam van de bewerking. |
| operationVersion | De REST API versie die door de client is aangevraagd. |
| category | De *controle* is momenteel de enige ondersteunde waarde. |
| tenantId | De Tenant-GUID die is gekoppeld aan de logboeken. |
| resultType | Het resultaat van de bewerking. Het resultaat kan *slagen* of *mislukken*. |
| resultSignature |  Dit veld is niet-toegewezen en u kunt het veilig negeren. | 
| resultDescription | Een aanvullende beschrijving van het resultaat, indien beschikbaar. | 
| durationMs |  Dit veld is niet-toegewezen en u kunt het veilig negeren. |
| callerIpAddress | Het IP-adres van de client die de aanvraag heeft ingediend. | 
| correlationId | Een optionele GUID die door de client wordt door gegeven. Dit kan helpen bij het correleren aan de client zijde met bewerkingen aan de server zijde en is handig wanneer u Logboeken traceert die services omvatten. |
| identity | De identiteit van het token dat is gepresenteerd tijdens het maken van de aanvraag. De identiteit kan een gebruikers account, systeem account of Service-Principal zijn. |
| niveau | Het bericht type. Voor audit Logboeken is het niveau altijd *informatief*. |
| location | De locatie van het Data Center. |
| properties | Een lijst met ondersteunde eigenschappen die betrekking hebben op een audit logboek. Zie de volgende tabel voor meer informatie. | 

<br>

| Naam van eigenschap | Description |
|---------------|-------------|
| AuditEventCategory | Het type controle gebeurtenis. Dit kan *gebruikers beheer*, *toepassings beheer* of een ander type zijn.|
| Identiteits type | Het type kan *toepassing* of *gebruiker* zijn. |
| Type bewerking | Het type kan worden *toegevoegd*, *bijgewerkt*, *verwijderd*. of *andere*. |
| Doel resource type | Hiermee geeft u het doel resource type op waarop de bewerking is uitgevoerd. Het type kan *toepassing*, *gebruiker*, *rol*, *beleid* zijn | 
| Doel resource naam | De naam van de doel resource. Dit kan een toepassings naam, een rolnaam, een user principal name of een Service Principal Name zijn. |
| additionalTargets | Een lijst met aanvullende eigenschappen voor specifieke bewerkingen. Voor een update bewerking worden bijvoorbeeld de oude waarden en de nieuwe waarden weer gegeven onder *targetUpdatedProperties*. | 

## <a name="next-steps"></a>Volgende stappen

* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Aanmeldingslogboekenschema interpreteren in Azure Monitor)
* [Diagnostische logboeken van Azure](../../azure-monitor/essentials/platform-logs-overview.md)
* [Veelgestelde vragen en bekende problemen](concept-activity-logs-azure-monitor.md#frequently-asked-questions)