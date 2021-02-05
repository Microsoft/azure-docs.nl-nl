---
title: Toepassen van een Azure-reserveringskorting
description: Dit artikel helpt u te begrijpen hoe kortingen voor gereserveerde instanties over het algemeen worden toegepast.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574869"
---
# <a name="how-a-reservation-discount-is-applied"></a>Toepassen van een reserveringskorting

Dit artikel helpt u te begrijpen hoe kortingen voor gereserveerde instanties over het algemeen worden toegepast. De reserveringskorting is van toepassing op het resourcegebruik dat overeenkomt met de kenmerken die u selecteert wanneer u de reservering koopt. Kenmerken zijn onder andere het bereik waarop de overeenkomende VM's, SQL-databases, Azure Cosmos DB of andere resources worden uitgevoerd. Als u bijvoorbeeld reserveringskorting voor vier Standard D2 virtuele machines in de regio VS - west wilt gebruiken, selecteert u het abonnement waarop de VM's worden uitgevoerd.

Voor een reserveringskorting geldt: *gebruiken of verliezen*. Als u geen overeenkomende resources voor eender welk uur hebt, verliest u een reserveringshoeveelheid voor dat uur. U kunt niet-gebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomende resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

Stel dat u bijvoorbeeld later een resource wilt maken en dat u een ondergebruikte overeenkomende reservering hebt. De reserveringskorting is automatisch van toepassing op de nieuwe overeenkomende resource.

Als de virtuele machines worden uitgevoerd in verschillende abonnementen binnen uw inschrijving/account, selecteert u het bereik als Gedeeld. Dankzij gedeeld bereik kan de reserveringskorting worden toegepast op verschillende abonnementen. U kunt het bereik wijzigen nadat u een reservering hebt gekocht. Zie [Azure-reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie.

Reserveringskorting is alleen van toepassing op resources die zijn gekoppeld met Enterprise, Microsoft-klantovereenkomst, CSP of abonnementen met tarieven voor betalen per gebruik. Voor resources die voor een abonnement met andere typen aanbiedingen worden uitgevoerd, wordt geen reserveringskorting verkregen.

## <a name="when-the-reservation-term-expires"></a>Wanneer de reserveringstermijn verloopt

Aan het einde van de reserveringtermijn verloopt de factureringskorting en worden kosten voor resources in rekening gebracht op basis van gebruik. De standaardinstelling is dat reserveringen niet automatisch worden verlengd. U kunt ervoor kiezen om een reservering automatisch te verlengen door de optie te selecteren in de verlengingsinstellingen. Met automatische verlenging wordt een vervangingsreservering aangeschaft wanneer de bestaande reservering verloopt. De vervangende reservering heeft standaard dezelfde kenmerken als de verlopende reservering. U kunt de frequentie, termijn of kwantiteit van de facturering aanpassen in de verlengingsinstellingen. Gebruikers met eigenaarstoegang tot de reservering en het abonnement dat wordt gebruikt voor facturering, kunnen verlenging instellen.  

## <a name="discount-applies-to-different-sizes"></a>De korting is van toepassing op verschillende grootten

Wanneer u een reservering koopt, kan de korting ook worden toegepast op andere instanties met kenmerken binnen de groep met VM's van dezelfde grootte. Deze functie wordt flexibiliteit van de instantiegrootte genoemd. De flexibiliteit van de kortingsdekking hangt af van het type reservering en de kenmerken die u kiest wanneer u de reservering koopt.

Service-abonnementen:

- Gereserveerde VM-instanties: Wanneer u de reservering koopt en **Geoptimaliseerd voor flexibiliteit van de instantiegrootte** selecteert, hangt de kortingsdekking af van de VM-grootte die u selecteert. De reservering kan worden toegepast op de VM-grootten in de groep met series van dezelfde grootte. Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../../virtual-machines/reserved-vm-instance-size-flexibility.md) voor meer informatie.
- Gereserveerde Azure Storage-capaciteit: U kunt gereserveerde capaciteit voor standaard Azure Storage-accounts aanschaffen in eenheden van 100 TiB of 1 PiB per maand. Raadpleeg [Blok-blob-prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/) voor informatie over welke regio's gereserveerde Azure Storage-capaciteit ondersteunen. Gereserveerde Azure Storage-capaciteit is beschikbaar voor alle lagen (dynamisch, statisch en archief) en voor alle replicatieconfiguraties (LRS, GRS of ZRS).
- Gereserveerde capaciteit voor SQL Database: De kortingsdekking hangt af van de prestatielaag die u kiest. Zie [Begrip van de manier waarop Azure-reserveringskorting wordt toegepast](understand-reservation-charges.md) voor meer informatie.
- Gereserveerde Azure Cosmos DB-capaciteit: De kortingsdekking hangt af van de ingerichte doorvoer. Zie [Begrip van de manier waarop Azure Cosmos DB-reserveringskorting wordt toegepast](understand-cosmosdb-reservation-charges.md) voor meer informatie.

## <a name="how-discounts-apply-to-specific-azure-services"></a>Hoe kortingen van toepassing zijn op specifieke Azure-services

Lees de volgende artikelen die gaan over hoe kortingen van toepassing zijn op een specifieke Azure-service:

- [App Service](reservation-discount-app-service.md)
- [Azure Cache voor Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Hosts](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Softwareabonnementen](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Virtuele machines](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Volgende stappen

- [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Kosten van Windows-software zijn niet inbegrepen in reserveringen](reserved-instance-windows-software-costs.md)