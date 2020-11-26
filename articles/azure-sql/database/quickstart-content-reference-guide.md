---
title: Inhoudsverwijzing snelstarts voor individuele database
description: Vind een inhoudsverwijzing met alle quickstarts waarmee u snel aan de slag kunt gaan met individuele databases in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184413"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Aan de slag met individuele databases in Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Een individuele database](../index.yml) is een volledig beheerde DbaaS (Database as a Service) op een PaaS (Platform as a Service) die ideaal is als opslag-engine voor moderne toepassingen in de cloud. In deze sectie leert u hoe u snel een individuele database in Azure SQL Database maakt en configureert.

## <a name="quickstart-overview"></a>Overzicht van quickstarts

Deze sectie bevat een overzicht van beschikbare artikelen waarmee u snel aan de slag kunt met individuele databases. In de volgende snelstarts leert u snel een individuele database te maken, een firewallregel op serverniveau te configureren en vervolgens een database te importeren in de nieuwe individuele database via een `.bacpac`-bestand:

- [Een individuele database maken met Azure Portal](single-database-create-quickstart.md).
- Nadat de database is gemaakt, moet u uw [database beveiligen door firewallregels te configureren](firewall-create-server-level-portal-quickstart.md).
- Als u een bestaande database hebt op SQL Server die u wilt migreren naar Azure SQL Database, moet u [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) installeren. Dat analyseert uw databases op SQL Server en zoekt problemen die de migratie zouden kunnen blokkeren. Als u geen problemen vindt, kunt u uw database als een `.bacpac`-bestand exporteren en [het bestand importeren met de Azure-portal of SqlPackage](database-import.md).


## <a name="automating-management-operations"></a>Beheerbewerkingen automatiseren

U kunt PowerShell of Azure CLI gebruiken om uw database te maken, configureren en schalen.

- [Een individuele database maken en configureren met PowerShell](scripts/create-and-configure-database-powershell.md) of [Azure CLI](scripts/create-and-configure-database-cli.md)
- [Uw individuele database bijwerken en resources schalen met PowerShell](scripts/monitor-and-scale-database-powershell.md) of [Azure CLI](scripts/monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migreren naar een individuele database met minimale downtime

Met de artikelen in deze quickstarts kunt u uw database snel maken of importeren in Azure met een `.bacpac`-bestand. `.bacpac`- en `.dacpac`-bestanden zijn echter ontworpen om snel databases te verplaatsen tussen verschillende versies van SQL Server en binnen Azure SQL of om continue integratie in uw DevOps-pijplijn te implementeren. Deze methode is echter niet ontworpen voor de migratie van uw productiedatabases met minimale downtime, omdat u moet stoppen met het toevoegen van nieuwe gegevens, wachten tot de export van de brondatabase naar een `.bacpac`-bestand voltooid is en vervolgens wachten totdat het importeren in Azure SQL Database voltooid is. Al dit wachten leidt tot downtime van uw toepassing, met name voor grote databases. Als u uw productiedatabase verplaatst, hebt u waarschijnlijk een betere migratiemethode nodig met minimale downtime. Om de database met minimale downtime te migreren, gebruikt u de [Database Migration Service (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json). Dit wordt bereikt doordat DMS de aan de brondatabase aangebrachte wijzigingen stapsgewijs pusht naar de individuele database die wordt teruggezet. Op deze manier kunt u snel uw toepassing van de bron- naar de doeldatabase overzetten met minimale downtime.

## <a name="hands-on-learning-modules"></a>Praktijkgerichte leermodules

Met de volgende Microsoft Learn-modules kunt u gratis leren over Azure SQL Database.

- [Een database inrichten in SQL Database om toepassingsgegevens op te slaan](/learn/modules/provision-azure-sql-db/)
- [Een ASP.NET-toepassing ontwikkelen en configureren waarmee een query wordt uitgevoerd op een database in Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)
- [Uw database beveiligen in Azure SQL Database](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>Volgende stappen

- Zoek een [lijst met ondersteunde functies in Azure SQL Database](features-comparison.md).
- Leer hoe u uw [database veiliger maakt](secure-database-tutorial.md).
- U vindt meer uitgebreide informatie in [Individuele database gebruiken in Azure SQL Database](how-to-content-reference-guide.md).
- Meer voorbeeldscripts zoeken die zijn geschreven in [PowerShell](powershell-script-content-guide.md) en de [Azure CLI](az-cli-script-samples-content-guide.md).
- Meer informatie over de [Management-API](single-database-manage.md) die u kunt gebruiken om uw databases te configureren.
- [Identificeer de juiste Azure SQL Database- of met Azure SQL beheerd exemplaar-SKU voor de on-premises database](/sql/dma/dma-sku-recommend-sql-db/).