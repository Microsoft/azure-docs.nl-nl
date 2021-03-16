---
title: Overzicht van het CAF-blauwdrukvoorbeeld voor de migratielandingszone
description: Overzicht en architectuur van het Cloud Adoption Framework (CAF) voor blauwdrukvoorbeeld van Azure Migration-landingszone.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 9cbf1c1a4ac4cf7a58ed27ec05cc77997da1366d
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472196"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Overzicht van het Microsoft Cloud Adoption Framework-blauwdrukvoorbeeld voor de Azure-migratielandingszone

De Microsoft CAF-blauwdruk (Cloud Adoption Framework) voor de Azure-migratielandingszone is een infrastructuur waarmee u uw eerste workload kunt migreren en uw cloudomgeving kunt beheren in overeenstemming met CAF.

Het [CAF Foundation](../caf-foundation/index.md)-blauwdrukvoorbeeld is een uitbreiding op dit voorbeeld.

## <a name="architecture"></a>Architectuur

Het CAF-blauwdrukvoorbeeld voor de migratielandingszone implementeert fundamentele infrastructuurresources in Azure, die door organisaties kunnen worden gebruikt om hun abonnement voor te bereiden op de migratie van virtuele machines. Het biedt ook ondersteuning bij het instellen van governance-controles die noodzakelijk zijn voor het beheren van de cloudresources. Met dit voorbeeld worden resources, beleid en sjablonen geïmplementeerd en afgedwongen die een organisatie helpen om zorgeloos aan de slag te gaan met Azure.

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="De afbeelding van de C A F-migratielandingszone laat zien wat er wordt geïnstalleerd als onderdeel van C A F-richtlijnen voor de initiële landingszone." border="false":::
   Beschrijft een Azure-architectuur die wordt bereikt door de C A F-migratieblauwdruk te implementeren.  Deze is van toepassing op een abonnement met resourcegroepen dat bestaat uit een virtueel Azure-netwerk en een opslagaccount voor het opslaan van logboeken. De logboekanalyse wordt geconfigureerd om in het opslagaccount te worden opgeslagen.  Toont ook configuratie van Azure Key Vault en eerste installatie van Azure Migrate.  Al deze kerninfrastructuren zijn toegankelijk via Azure Active Directory.     
:::image-end:::

Deze omgeving bestaat uit diverse Azure-services die een veilige, volledig bewaakte en direct inzetbare governance bieden. De omgeving bestaat uit de volgende elementen:

- Een [Azure Key Vault](../../../../key-vault/general/overview.md)-exemplaar om de geheimen te bewaren voor de certificaten, sleutels en geheimen die in de omgeving met gedeelde services worden geïmplementeerd
- [Log Analytics](../../../../azure-monitor/overview.md) wordt geïmplementeerd om te garanderen dat op een centrale locatie logboeken worden bijgehouden voor alle acties en services zodra u met de migratie begint
- Implementeer [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md). Dit biedt een geïsoleerd netwerk en subnetten voor uw virtuele machine.
- Implementeer het [Azure Migrate-project](../../../../migrate/migrate-services-overview.md) voor detectie en evaluatie. Er worden hulpprogramma's toegevoegd voor serverevaluatie, servermigratie, database-evaluatie en databasemigratie.  


Al deze elementen voldoen aan de aanbevolen procedures zoals gepubliceerd in [Azure Architecture Center - Referentiearchitecturen](/azure/architecture/reference-architectures/).

> [!NOTE]
> Met de CAF-migratieblauwdruk wordt een landingszone voor uw workloads ingericht. Naast deze fundamentele architectuur dient u nog wel de evaluatie en migratie van uw virtuele machines/databases uit te voeren.

Zie [Microsoft Cloud Adoption Framework voor Azure: migreren](/azure/architecture/cloud-adoption/migrate/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

U hebt het overzicht en de architectuur van het CAF-blauwdrukvoorbeeld voor de migratielandingszone doorgenomen.

> [!div class="nextstepaction"]
> [CAF-blauwdrukvoorbeeld voor de migratielandingszone - Implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
