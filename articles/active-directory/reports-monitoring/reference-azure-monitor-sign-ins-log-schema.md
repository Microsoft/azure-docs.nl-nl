---
title: Schema voor aanmeldings logboek in Azure Monitor | Microsoft Docs
description: Beschrijf het schema voor logboek registratie van Azure AD voor gebruik in Azure Monitor
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
ms.date: 03/12/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: bad8ae86827144269e816a6c2e01d6af3f4d88ac
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225415"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Het schema voor logboek registraties van Azure AD interpreteren in Azure Monitor

In dit artikel wordt het Azure Active Directory (Azure AD)-aanmeldings logboek schema in Azure Monitor beschreven. De meeste informatie die betrekking heeft op aanmeldingen, vindt u in het kenmerk *Eigenschappen* van het `records` object.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Veldbeschrijvingen

| Veldnaam | Sleutel | Beschrijving |
| --- | --- | --- | 
| Tijd |  - | De datum en tijd, in UTC. |
| ResourceId | - | Deze waarde is niet-toegewezen en u kunt dit veld veilig negeren.  |
| OperationName | - | Voor aanmeldingen is deze waarde altijd *aanmeldings activiteit*. |
| OperationVersion | - | De REST API versie die door de client is aangevraagd. |
| Categorie | - | Voor aanmeldingen is deze waarde altijd *Aanmelden*. | 
| TenantId | - | De Tenant-GUID die is gekoppeld aan de logboeken. |
| ResultType | - | Het resultaat van de aanmeldings bewerking kan *slagen* of *mislukken*. | 
| ResultSignature | - | Bevat de fout code, indien aanwezig, voor de aanmeldings bewerking. |
| ResultDescription | N.v.t. of leeg | Bevat de fout beschrijving voor de aanmeldings bewerking. |
| riskDetail | riskDetail | Biedt de ' reason ' achter een specifieke status van een Risk ante gebruiker, aanmelding of een risico detectie. De mogelijke waarden zijn: `none` , `adminGeneratedTemporaryPassword` , `userPerformedSecuredPasswordChange` , `userPerformedSecuredPasswordReset` , `adminConfirmedSigninSafe` , `aiConfirmedSigninSafe` , `userPassedMFADrivenByRiskBasedPolicy` , `adminDismissedAllRiskForUser` , `adminConfirmedSigninCompromised` , `unknownFutureValue` . De waarde `none` betekent dat er geen actie is uitgevoerd voor de gebruiker of zich tot nu toe heeft aangemeld. <br>**Opmerking:** Voor de details van deze eigenschap is een Azure AD Premium P2-licentie vereist. Andere licenties retour neren de waarde `hidden` . |
| riskEventTypes | riskEventTypes | Typen risico detectie die zijn gekoppeld aan de aanmelding. De mogelijke waarden zijn: `unlikelyTravel` , `anonymizedIPAddress` , `maliciousIPAddress` , `unfamiliarFeatures` , `malwareInfectedIPAddress` , `suspiciousIPAddress` , `leakedCredentials` , `investigationsThreatIntelligence` ,  `generic` en `unknownFutureValue` . |
| authProcessingDetails | Azure AD-App-verificatie bibliotheek | Bevat informatie over familie, bibliotheek en platform in indeling: "familie: ADAL-bibliotheek: ADAL.JS 1.0.0-platform: JS" |
| authProcessingDetails | IsCAEToken | Waarden zijn waar of onwaar |
| riskLevelAggregated | riskLevel | Samengevoegd risico niveau. De mogelijke waarden zijn: `none` , `low` , `medium` , `high` , `hidden` en `unknownFutureValue` . De waarde `hidden` betekent dat de gebruiker of aanmelding niet is ingeschakeld voor Azure AD Identity Protection. **Opmerking:** De Details voor deze eigenschap zijn alleen beschikbaar voor klanten met een Azure AD Premium P2. Alle andere klanten worden geretourneerd `hidden` . |
| riskLevelDuringSignIn | riskLevel | Risico niveau tijdens het aanmelden. De mogelijke waarden zijn: `none` , `low` , `medium` , `high` , `hidden` en `unknownFutureValue` . De waarde `hidden` betekent dat de gebruiker of aanmelding niet is ingeschakeld voor Azure AD Identity Protection. **Opmerking:** De Details voor deze eigenschap zijn alleen beschikbaar voor klanten met een Azure AD Premium P2. Alle andere klanten worden geretourneerd `hidden` . |
| riskState | riskState | Hiermee wordt de status van de Risk ante gebruiker, het aanmelden of een risico detectie gerapporteerd. De mogelijke waarden zijn: `none` , `confirmedSafe` , `remediated` , `dismissed` , `atRisk` , `confirmedCompromised` , `unknownFutureValue` . |
| DurationMs | - | Deze waarde is niet-toegewezen en u kunt dit veld veilig negeren. |
| CallerIpAddress | - | Het IP-adres van de client die de aanvraag heeft ingediend. | 
| CorrelationId | - | De optionele GUID die door de client wordt door gegeven. Deze waarde kan bijdragen aan de activiteiten aan de client zijde met bewerkingen aan de server zijde en is handig wanneer u Logboeken traceert die services omvatten. |
| Identiteit | - | De identiteit van het token dat is gepresenteerd tijdens het maken van de aanvraag. Dit kan een gebruikers account, systeem account of Service-Principal zijn. |
| Niveau | - | Geeft het type bericht. Voor audit is het altijd *informatief*. |
| Locatie | - | Hiermee wordt de locatie van de aanmeldings activiteit verstrekt. |
| Eigenschappen | - | Een lijst met alle eigenschappen die aan aanmeldingen zijn gekoppeld.|

## <a name="next-steps"></a>Volgende stappen

* [Interpret audit logs schema in Azure Monitor](reference-azure-monitor-audit-log-schema.md) (Auditlogboekenschema interpreteren in Azure Monitor)
* [Meer informatie over Azure platform-logboeken](../../azure-monitor/essentials/platform-logs-overview.md)