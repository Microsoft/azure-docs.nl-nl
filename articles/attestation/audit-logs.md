---
title: Audit logboeken van Azure Attestation
description: Hierin worden de volledige audit Logboeken beschreven die worden verzameld voor Azure-Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95758995"
---
# <a name="audit-logs-for-azure-attestation"></a>Audit logboeken voor Azure Attestation

Audit logboeken zijn beveiligde, onveranderlijke, getimede records met afzonderlijke gebeurtenissen die in de loop van de tijd zijn opgetreden. In deze logboeken worden belang rijke gebeurtenissen vastgelegd die van invloed kunnen zijn op de functionaliteit van uw Attestation-exemplaar.

Met Azure Attestation worden Attestation-instanties en het bijbehorende beleid beheerd. Acties die zijn gekoppeld aan instantie beheer en beleids wijzigingen worden gecontroleerd en geregistreerd.

Dit artikel bevat informatie over de gebeurtenissen die worden geregistreerd, de verzamelde gegevens en de locatie van deze logboeken.

## <a name="about-audit-logs"></a>Over audit logboeken

Azure Attestation gebruikt code voor het produceren van audit logboeken voor gebeurtenissen die van invloed zijn op de manier waarop Attestation wordt uitgevoerd. Dit wordt doorgaans gekook om te bepalen hoe of wanneer er beleids wijzigingen worden aangebracht in uw Attestation-exemplaar en bepaalde acties van de beheerder.

### <a name="auditable-events"></a>Controleer bare gebeurtenissen
Hier volgen enkele van de audit logboeken die worden verzameld:

|     Gebeurtenis/API                              |     Beschrijving van de gebeurtenis                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Exemplaar maken                        |     Hiermee maakt u een nieuw exemplaar van een Attestation-service. |
|     Exemplaar vernietigen                       |     Vernietigt een exemplaar van een Attestation-service. |
|     Beleids certificaat toevoegen                 |     Toevoeging van een certificaat aan de huidige set beleids beheer certificaten. |
|     Beleids certificaat verwijderen              |     Een certificaat verwijderen uit de huidige set beleids beheer certificaten. |
|     Huidige beleid instellen                     |     Hiermee stelt u het Attestation-beleid voor een bepaald t-type in. |
|     Attestation-beleid opnieuw instellen               |     Hiermee stelt u het Attestation-beleid voor een bepaald t-type opnieuw in. |
|     Voorbereiden op het bijwerken van beleid               |     Voorbereiden op het bijwerken van het Attestation-beleid voor een bepaald t-type. |
|     Tenants na nood geval opnieuw inhydraten       |     Hiermee worden alle Attestation-tenants op dit exemplaar van de Attestation-service opnieuw verzegeld. Dit kan alleen worden uitgevoerd door Attestation-service beheerders. |

### <a name="collected--information"></a>Verzamelde gegevens
Voor elk van deze gebeurtenissen verzamelt Azure attestation de volgende informatie:

- Naam van bewerking
- Bewerking is voltooid
- Dit kan een van de volgende zijn:
    - Azure AD-UPN
    - Object-id
    - Certificaat
    - Azure AD-tenant-id
- Doel van de bewerking. Dit kan een van de volgende zijn:
    - Omgeving
    - Service regio
    - Service functie
    - Instantie van service-rol
    - Resource-id
    - Resource regio

### <a name="sample-audit-log"></a>Voor beeld van een audit logboek

Audit logboeken worden in JSON-indeling gegeven. Hier volgt een voor beeld van hoe een audit logboek eruit kan zien.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Access-controle logboeken

Deze logboeken worden opgeslagen in Azure en kunnen niet rechtstreeks worden geopend. Als u deze logboeken wilt openen, moet u een ondersteunings ticket indienen. Zie [contact opnemen met Microsoft ondersteuning](https://azure.microsoft.com/support/options/)voor meer informatie. 

Zodra het ondersteunings ticket is opgeslagen, wordt micro soft gedownload en hebt u toegang tot deze logboeken.
