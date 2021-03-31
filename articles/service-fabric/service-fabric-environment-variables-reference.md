---
title: Azure Service Fabric-omgevings variabelen
description: Meer informatie over omgevings variabelen in azure Service Fabric. Bevat een verwijzing naar een volledige lijst met variabelen en hun gebruik.
ms.topic: reference
ms.date: 12/07/2017
ms.openlocfilehash: b70249daa439b5a631b5a84b10c47f082ce75985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574578"
---
# <a name="service-fabric-environment-variables"></a>Omgevings variabelen Service Fabric

Service Fabric heeft ingebouwde omgevings variabelen ingesteld voor elk service-exemplaar. De volledige lijst met omgevings variabelen vindt u hieronder:

| Omgevings variabele                         | Beschrijving                                                            | Voorbeeld                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | De URI-naam van de infra structuur van de toepassing                                 | Fabric:/mijn toepassing                                                |
| Fabric_CodePackageName                       | De naam van het code pakket waartoe het proces behoort              | Code                                                                 |
| Fabric_Endpoint \_ IPOrFQDN \_ *ServiceEndpointName*     | Het IP-adres of de FQDN van het eind punt                                 | 10.0.0.1                                                     |
| Fabric \_ endpoint \_ *ServiceEndpointName*              | Poort nummer voor het eind punt                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Logboekmap                                                             | C: \\ \\ gegevens \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ logboek      |
| Fabric_Folder_App_Temp                       | De map Temp                                                            | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ temp     |
| Fabric_Folder_App_Work                       | Werkmap                                                            | C: \\ \\ gegevens \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12 \\ \\ werk     |
| Fabric_Folder_Application                    | De basismap van de toepassing                                           | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Een Boole-waarde die aangeeft of het proces een container is                   | onjuist                                                                |
| Fabric_NodeId                                | De knoop punt-ID van het knoop punt waarop het proces wordt uitgevoerd                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Het IP-adres of de FQDN-naam van het knoop punt, zoals opgegeven in het manifest bestand van het cluster. | localhost of 10.0.0.1                                                |
| Fabric_NodeName                              | De knooppunt naam van het knoop punt waarop het proces wordt uitgevoerd                          | _Node_0                                                              |
| Fabric_ServiceName                           | De infrastructuur-URI-naam van de service, als de service wordt gehost in de ExclusiveProcess-modus. Deze variabele waarde is alleen beschikbaar als u de service maakt met ServicePackageActivationMode ExclusiveProcess.  | Fabric:/mijn toepassing/MyService                                               |
| Fabric_ServicePackageActivationId            | De ServicePackageActivationId                                         | EEN GUID                                                               |
| Fabric_ServicePackageName                    | Naam van het service pakket waarvan het proces deel uitmaakt                     | Web1Pkg                                                              |

Interne omgevings variabelen die worden gebruikt door Service Fabric runtime:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
