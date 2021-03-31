---
title: Een subnet verwijderen na het verwijderen van een SQL Managed instance
description: Meer informatie over het verwijderen van een virtueel Azure-netwerk na het verwijderen van een Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496ff6c7ec39706a99bb40447b6443ca71f19e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99093673"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Een subnet verwijderen na het verwijderen van een door Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dit artikel bevat richt lijnen voor het hand matig verwijderen van een subnet na het verwijderen van het laatste beheerde exemplaar van Azure SQL.

SQL Managed instances worden geïmplementeerd in [virtuele clusters](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Elk virtueel cluster is gekoppeld aan een subnet. Het virtuele cluster wordt gedurende 12 uur na het verwijderen van de laatste instantie bewaard, zodat u sneller SQL-beheerde instanties in hetzelfde subnet kunt maken. Er worden geen kosten in rekening gebracht voor het bewaren van een leeg virtueel cluster. Gedurende deze periode kan het subnet dat is gekoppeld aan het virtuele-cluster, niet worden verwijderd.

Als u 12 uur niet wilt wachten en de voor keur geeft om het virtuele cluster en het subnet eerder te verwijderen, kunt u dit hand matig doen. Verwijder het virtuele cluster hand matig met behulp van de Azure Portal of de API voor virtuele clusters.

> [!IMPORTANT]
> - Het virtuele cluster mag geen SQL Managed instances bevatten om de verwijdering te kunnen volt ooien. 
> - Het verwijderen van een virtueel cluster is een langlopende bewerking die ongeveer 1,5 uur duurt (Zie [bewerkingen voor het beheer van SQL-beheerde exemplaren](./sql-managed-instance-paas-overview.md#management-operations) voor de actuele tijd voor het verwijderen van virtuele clusters). Het virtuele cluster wordt nog steeds weer gegeven in de portal totdat dit proces is voltooid.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Een virtueel cluster verwijderen uit het Azure Portal

Als u een virtueel cluster wilt verwijderen met behulp van de Azure Portal, zoekt u de virtuele cluster resources.

![Scherm afbeelding van de Azure Portal, waarbij het zoekvak is gemarkeerd](./media/virtual-cluster-delete/virtual-clusters-search.png)

Nadat u het virtuele cluster dat u wilt verwijderen hebt gevonden, selecteert u deze resource en selecteert u **verwijderen**. U wordt gevraagd om het verwijderen van het virtuele cluster te bevestigen.

![Scherm opname van het dash board van de Azure Portal virtuele clusters, met de optie verwijderen gemarkeerd](./media/virtual-cluster-delete/virtual-clusters-delete.png)

In Azure Portal meldingen wordt een bevestiging weer gegeven dat de aanvraag voor het verwijderen van het virtuele cluster is verzonden. De Verwijder bewerking zelf duurt ongeveer 1,5 uur, waardoor het virtuele cluster nog steeds zichtbaar is in de portal. Zodra het proces is voltooid, is het virtuele cluster niet meer zichtbaar en wordt het bijbehorende subnet vrijgegeven voor hergebruik.

> [!TIP]
> Als er geen door SQL beheerde exemplaren worden weer gegeven in het virtuele cluster en u het virtuele cluster niet kunt verwijderen, moet u ervoor zorgen dat er geen actieve exemplaar-implementatie wordt uitgevoerd. Dit omvat gestarte en geannuleerde implementaties die nog worden uitgevoerd. Dit komt doordat deze bewerkingen nog steeds gebruikmaken van het virtuele cluster, zodat het niet meer kan worden verwijderd. Door het tabblad **implementaties** van de resource groep te bekijken waarop het exemplaar is geïmplementeerd, wordt aangegeven welke implementaties worden uitgevoerd. Wacht in dit geval tot de implementatie is voltooid, verwijder het SQL Managed instance en verwijder vervolgens het virtuele cluster.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Een virtueel cluster verwijderen met de API

Als u een virtueel cluster wilt verwijderen via de API, gebruikt u de URI-para meters die zijn opgegeven in de methode voor het [verwijderen van virtuele clusters](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Meer informatie over [connectiviteits architectuur in SQL Managed instance](connectivity-architecture-overview.md).
- Meer informatie over het [wijzigen van een bestaand virtueel netwerk voor een door SQL beheerd exemplaar](vnet-existing-add-subnet.md).
- Voor een zelf studie waarin wordt getoond hoe u een virtueel netwerk maakt, een Azure SQL Managed instance maakt en een Data Base herstelt vanuit een back-up van een Data Base, raadpleegt u [een Azure SQL Managed instance (Portal) maken](instance-create-quickstart.md).
- Zie [Configure a Custom DNS](custom-dns-configure.md)(Engelstalig) voor DNS-problemen.