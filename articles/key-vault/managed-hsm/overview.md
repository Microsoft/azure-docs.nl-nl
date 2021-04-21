---
title: Overzicht van Azure Managed HSM - Azure Managed HSM Managed HSM | Microsoft Docs
description: Azure Managed HSM is een cloudservice die uw cryptografische sleutels voor cloudtoepassingen waarborgt.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: a9df83535bf0ee7a6c484a7682392d2ad0ef59f7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815320"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Wat is beheerde HSM van Azure Key Vault?

Azure Key Vault Managed HSM is een cloudservice van hoge beschikbaarheid en één tenant, die volledig beheerd is en aan de standaarden voldoet. Met deze cloudservice kunt u cryptografische sleutels voor uw cloudtoepassingen waarborgen met behulp van met **FIPS 140-2 Level 3** gevalideerde HSM's. Zie de sectie Beheerde HSM-pools op de pagina met prijzen Azure Key Vault [informatie over prijzen.](https://azure.microsoft.com/pricing/details/key-vault/) 

## <a name="why-use-managed-hsm"></a>Waarom zou u Managed HSM gebruiken?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Volledig beheerde,maximaal beschikbare HSM met één tenant als een service

- **Volledig beheerd**: Het inrichten, configureren, toepassen van patches en onderhouden worden door de service verwerkt. 
- **Maximaal beschikbare en zone-tolerant** (met ondersteuning van beschikbaarheidszones): Elk HSM-cluster bestaat uit meerdere HSM-partities die ten minste twee beschikbaarheidszones bereiken. Als er een storing optreedt in de hardware, worden de lidpartities voor uw HSM-cluster automatisch naar knooppunten met een goede status gemigreerd.
- **Eén tenant**: Elk beheerd HSM-exemplaar wordt toegewezen aan één klant en bestaat uit een cluster met meerdere HSM-partities. Elk HSM-cluster maakt gebruik van een afzonderlijk, klantspecifiek beveiligingsdomein dat het HSM-cluster van elke klant cryptografisch isoleert.


### <a name="access-control-enhanced-data-protection--compliance"></a>Toegangsbeheer, uitgebreide gegevensbescherming en compliance

- **Gecentraliseerd sleutelbeheer**: U kunt essentiële, hoogwaardige sleutels binnen uw organisatie op één plek beheren. Met gedetailleerde machtigingen per sleutel beheert u de toegang tot elke sleutel op basis van het principe 'minst bevoegde toegang'.
- **Geïsoleerd toegangsbeheer**: Met toegangsbeheer op basis van lokale RBAC van beheerde HSM kunnen toegewezen HSM-clusterbeheerders volledige controle hebben over de HSM's, die zelfs beheerders van beheergroepen, abonnementen of resourcegroepen niet kunnen overschrijven.
- **Met FIPS 140-2 Level 3 gevalideerde HSM's**: Uw gegevens worden beschermd en er wordt voldaan aan de nalevingsvereisten met HSM's die zijn gevalideerd met FIPS (Federal Information Protection Standard) 140-2 Level 3. Beheerde HSM's maken gebruik van de Marvell LiquidSecurity HSM-adapters.
- **Bewaken en controleren**: volledig geïntegreerd met Azure Monitor. U kunt volledige logboeken van alle activiteiten ophalen via Azure Monitor. Gebruik Azure Log Analytics voor analyse en waarschuwingen.
- **Gegevensopslag:** met beheerde HSM worden geen klantgegevens opgeslagen/verwerkt buiten de regio waarin de klant het HSM-exemplaar implementeert.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Geïntegreerd met PaaS-/SaaS-services van Azure en Microsoft 

- Genereer (of importeer met behulp van [BYOK](hsm-protected-keys-byok.md)) sleutels en gebruik deze om uw data-at-rest te versleutelen in Azure-Services, zoals [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) en [Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Maakt gebruik van dezelfde API en beheerinterfaces als Key Vault

- Migreer eenvoudig uw bestaande toepassingen die gebruikmaken van een kluis (een multitenant) voor het gebruik van beheerde HSM's.
- Gebruik voor al uw toepassingen dezelfde patronen voor toepassingsontwikkeling en implementatie, ongeacht de gebruikte oplossing voor sleutelbeheer: kluizen met een tenant of beheerde HSM's met meerdere tenants

### <a name="import-keys-from-your-on-premise-hsms"></a>Importeer sleutels uit uw on-premises HSM's

- Genereer met HSM beveiligde sleutels in uw on-premises HSM en importeer deze veilig in Managed HSM

## <a name="next-steps"></a>Volgende stappen
- Zie [Quickstart: Een beheerde HSM inrichten en activeren met behulp van de Azure CLI](quick-create-cli.md) om een beheerde HSM te maken en te activeren
- Zie [Best practices met gebruik van Azure Key Vault Managed HSM](best-practices.md)
