---
title: Beheerd virtueel netwerk
description: Een artikel waarin het beheerde virtuele netwerk in Azure Synapse Analytics wordt toegelicht
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/18/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f55251932c8aa8f632bd3b498943ac722f006dee
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569900"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Beheerd virtueel netwerk in Azure Synapse Analytics

In dit artikel wordt het beheerde virtuele netwerk in Azure Synapse Analytics toegelicht.

## <a name="managed-workspace-virtual-network"></a>Een beheerd, aan werkruimte gekoppeld virtueel netwerk

Wanneer u een Azure Synapse-werkruimte maakt, kunt u ervoor kiezen om deze te koppelen aan een Microsoft Azure Virtual Network. Het virtuele netwerk dat is gekoppeld aan uw werkruimte wordt beheerd door Azure Synapse. Dit virtuele netwerk wordt een *beheerd, aan werkruimte gekoppeld virtueel netwerk* genoemd.

Een beheerd, aan werkruimte gekoppeld virtueel netwerk is handig om vier redenen:

- Met een beheerd, aan werkruimte gekoppeld virtueel netwerk kunt u de beheertaak voor het virtuele netwerk overlaten aan Azure Synapse.
- U hoeft geen inkomende NSG-regels in te stellen op uw eigen virtuele netwerken om Azure Synapse-beheerverkeer in uw virtuele netwerk toe te staan. Een onjuiste configuratie van deze NSG-regels leidt ertoe dat de service wordt onderbroken voor klanten.
- U hoeft geen subnet voor uw Spark-clusters te maken op basis van de piekbelasting.
- Met een beheerd, aan werkruimte gekoppeld virtueel netwerk in combinatie met beheerde privé-eindpunten wordt u beschermd tegen gegevensexfiltratie. U kunt alleen beheerde privé-eindpunten maken in een werkruimte waaraan een beheerd virtueel netwerk is gekoppeld.

Als u een werkruimte maakt waaraan een beheerd virtueel netwerk is gekoppeld, zorgt u ervoor dat uw werkruimte binnen het netwerk is afgeschermd van andere werkruimten. Azure Synapse biedt verschillende analysemogelijkheden in een werkruimte: Gegevensintegratie, serverloze Apache Spark-pool, toegewezen SQL-pool, en serverloze SQL-pool.

Als uw werkruimte beschikt over een beheerd virtueel netwerk, worden hierin gegevensintegratie en Spark-resources geïmplementeerd. Een beheerd, aan werkruimte gekoppeld virtueel netwerk biedt ook afscherming op gebruikersniveau voor Spark-activiteiten, omdat elk Spark-cluster zich in een eigen subnet bevindt.

Toegewezen SQL-pools en serverloze SQL-pools bieden mogelijkheden voor meerdere tenants, en bevinden zich daarom buiten het beheerde virtuele werkruimtenetwerk. Voor communicatie tussen werkruimten naar een toegewezen SQL-pool en een serverloze SQL-pool wordt gebruikgemaakt van Azure Private Links. Deze privélinks worden automatisch gemaakt wanneer u een werkruimte maakt waaraan een beheerd virtueel netwerk is gekoppeld.

>[!IMPORTANT]
>U kunt deze werkruimteconfiguratie niet wijzigen nadat de werkruimte is gemaakt. U kunt een werkruimte waaraan geen beheerd virtueel netwerk is gekoppeld bijvoorbeeld niet opnieuw configureren en er een virtueel netwerk aan koppelen. Evenzo kunt u een werkruimte waaraan geen beheerd virtueel netwerk is gekoppeld niet opnieuw configureren en het virtueel netwerk ervan ontkoppelen.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Een Azure Synapse-werkruimte maken met een beheerd, aan de werkruimte gekoppeld virtueel netwerk

Registreer de resourceprovider van het netwerk, als u dit nog niet hebt gedaan. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider. Kies *Microsoft.Network* in de lijst met resourceproviders tijdens het [registreren](../../azure-resource-manager/management/resource-providers-and-types.md).

Als u een Azure Synapse-werkruimte wilt maken waaraan een beheerd virtueel werkruimtenetwerk is gekoppeld, selecteert u het tabblad **Beveiliging** in Azure Portal, en schakelt u het selectievakje **Beheerd virtueel netwerk inschakelen** in.

Als u het selectievakje uitgeschakeld laat, wordt er geen virtueel netwerk gekoppeld aan de werkruimte.

>[!IMPORTANT]
>U kunt alleen privélinks gebruiken in een werkruimte die een beheerd, aan deze werkruimte gekoppeld virtueel netwerk heeft.

![Een beheerd, aan werkruimte gekoppeld virtueel netwerk inschakelen](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

Nadat u ervoor hebt gekozen om een beheerd, aan een werkruimte gekoppeld virtueel netwerk aan uw werkruimte te koppelen, kunt u deze beveiligen tegen gegevensexfiltratie door uitgaande verbindingen van het beheerde, aan de werkruimte gekoppeld virtueel netwerk alleen toe te staan voor goedgekeurde doelen met behulp van [Beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md). Selecteer **Ja** om uitgaand verkeer van het beheerde, aan uw werkruimte gekoppelde virtuele netwerk te beperken tot doelen via beheerde privé-eindpunten. 


>[!IMPORTANT]
>De metastore is uitgeschakeld in Synapse-werkruimten met een beheerd virtueel netwerk waarvoor bescherming van gegevensexfiltratie is ingeschakeld. In deze werkruimten kunt u geen Spark SQL gebruiken.

![Uitgaand verkeer dat gebruikmaakt van beheerde privé-eindpunten](./media/synapse-workspace-managed-vnet/select-outbound-connectivity.png)

Selecteer **Nee** om uitgaand verkeer van de werkruimte naar een willekeurig doel toe te staan.

U kunt ook de doelen beheren waarmee beheerde privé-eindpunten worden gemaakt vanuit uw Azure Synapse-werkruimte. Beheerde privé-eindpunten naar resources in dezelfde AAD-tenant waartoe uw abonnement behoort, worden standaard toegestaan. Als u een beheerd privé-eindpunt wilt maken voor een resource in een AAD-tenant die afwijkt van de tenant waartoe uw abonnement behoort, kunt u die AAD-tenant toevoegen door **+ Toevoegen** te selecteren. U kunt de AAD-tenant selecteren in de vervolgkeuzelijst of de id van de AAD-tenant handmatig invoeren.

![Extra AAD-tenants toevoegen](./media/synapse-workspace-managed-vnet/add-additional-azure-active-directory-tenants.png)

Nadat de werkruimte is gemaakt, kunt u controleren of uw Azure Synapse-werkruimte is gekoppeld aan een beheerd virtueel netwerk door in Azure Portal **Overzicht** te selecteren.

![Overzicht van werkruimten in de Azure-portal](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Volgende stappen

Een [Azure Synapse-werkruimte](../quickstart-create-workspace.md) maken

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)
