---
title: Servicelaag voor Algemeen gebruik
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Meer informatie over de Algemeen-servicelaag voor Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85986638"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Algemeen service tier: Azure SQL Database en Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> De Algemeen servicelaag in het op vCore gebaseerde aankoop model wordt de Standard-servicelaag genoemd in het op DTU gebaseerde aankoop model. Zie [Inkoop modellen en resources](purchasing-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.

Azure SQL Database en Azure SQL Managed instance zijn gebaseerd op de architectuur van de SQL Server data base-engine die is aangepast voor de cloud omgeving, zodat de beschik baarheid van 99,99% ook in het geval van infrastructuur fouten wordt gegarandeerd. 

Er worden twee service lagen gebruikt door Azure SQL Database en SQL Managed instance: 

- Algemeen gebruik
- Bedrijfskritiek

Azure SQL Database heeft ook een derde servicelaag, die momenteel niet beschikbaar is voor Azure SQL Managed instance:

- Hyperscale

Het architectuur model voor de Algemeen servicelaag is gebaseerd op een schei ding van Compute en opslag. Dit architectuur model is afhankelijk van hoge Beschik baarheid en betrouw baarheid van Azure Blob-opslag waarmee database bestanden op transparante wijze worden gerepliceerd en er geen gegevens verloren gaan als er een onderliggende infrastructuur fout optreedt.

In de volgende afbeelding ziet u de vier knoop punten in het standaard model architectuur met de gescheiden reken-en opslag lagen.

![Schei ding van Compute en opslag](./media/service-tier-general-purpose/general-purpose-service-tier.png)

In het architectuur model voor de Algemeen servicelaag bevinden zich twee lagen:

- Een stateless Compute-laag die het `sqlservr.exe` proces uitvoert en alleen tijdelijke en in de cache opgeslagen gegevens bevat (bijvoorbeeld: plan cache, buffer groep, Column Store-groep). Dit stateless knoop punt wordt gebruikt door Azure Service Fabric die het proces initialiseert, de status van het knoop punt beheert en failover naar een andere locatie uitvoert, indien nodig.
- Een stateful gegevenslaag met database bestanden (MDF/. ldf) die zijn opgeslagen in Azure Blob Storage. Azure Blob-opslag garandeert dat er geen gegevens verloren gaan van alle records die in een database bestand worden geplaatst. Azure Storage heeft ingebouwde Beschik baarheid/redundantie van gegevens die ervoor zorgt dat elke record in het logboek bestand of de pagina in het gegevens bestand blijft behouden, zelfs als het proces vastloopt.

Wanneer de data base-engine of het besturings systeem wordt geüpgraded, mislukt een deel van de onderliggende infra structuur, of als er een kritiek probleem wordt gedetecteerd in het `sqlservr.exe` proces, verplaatst Azure service Fabric het stateless proces naar een ander stateless Compute-knoop punt. Er is een set reserve knoop punten die wachten op het uitvoeren van een nieuwe compute-service als er een failover van het primaire knoop punt wordt uitgevoerd om de failover-tijd te minimaliseren. Gegevens in azure Storage laag worden niet beïnvloed en gegevens/logboek bestanden zijn gekoppeld aan het zojuist geïnitialiseerde proces. Dit proces garandeert een Beschik baarheid van 99,99%, maar het kan enkele prestatie gevolgen hebben voor zware workloads die worden uitgevoerd als gevolg van de overgangs tijd en het feit dat het nieuwe knoop punt begint met koude cache.

## <a name="when-to-choose-this-service-tier"></a>Wanneer u deze servicelaag kiest

De servicelaag Algemeen is een standaardservicelaag in Azure SQL Database en Azure SQL Managed instance die is ontworpen voor de meeste algemene werk belastingen. Als u een volledig beheerde data base-engine nodig hebt met een SLA met 99,99% en een opslag latentie tussen 5 en 10 MS die overeenkomt met SQL Server op een virtuele Azure-machine in de meeste gevallen, is de Algemeen-laag de optie voor u.

## <a name="next-steps"></a>Volgende stappen

- Zoek bron kenmerken (aantal kernen, I/O, geheugen) van de laag Algemeen/Standard in [SQL Managed instance](../managed-instance/resource-limits.md#service-tier-characteristics), Single Data Base [in VCore model](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) of [DTU-model](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes), of elastische pool in [vCore model](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) en [DTU-model](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits).
- Meer informatie over [bedrijfskritiek](service-tier-business-critical.md) -en [grootschalige](service-tier-hyperscale.md) -lagen.
- Meer informatie over [service Fabric](../../service-fabric/service-fabric-overview.md).
- Zie [bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.
