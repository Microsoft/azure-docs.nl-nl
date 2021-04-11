---
title: SQL Server instanties detecteren in een bestaand Azure Migrate-project
description: Meer informatie over het detecteren van SQL Server exemplaren in een bestaand Azure Migrate-project.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 2de60880b511e43ffb2949a15fec2cf2a94f62fa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567149"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>SQL Server exemplaren in een bestaand project detecteren 

In dit artikel wordt beschreven hoe u SQL Server instanties en data bases detecteert in een [Azure migrate](./migrate-services-overview.md) project dat is gemaakt vóór de preview-versie van de Azure SQL-evaluatie functie.

Het detecteren van SQL Server instanties en data bases die worden uitgevoerd op on-premises machines, helpt bij het identificeren en aanpassen van een migratie naar Azure SQL. Het Azure Migrate-apparaat voert deze detectie uit met behulp van de domein referenties of SQL Server verificatie referenties die toegang hebben tot de SQL Server instanties en data bases die worden uitgevoerd op de doel servers. Dit detectie proces is zonder agent, maar er is niets geïnstalleerd op de doel servers.

## <a name="before-you-start"></a>Voordat u begint

- Zorg ervoor dat u:
    - Een [Azure migrate project](./create-manage-projects.md) gemaakt vóór de aankondiging van de functie voor SQL-evaluatie voor uw regio
    - De Azure Migrate: hulp programma voor [detectie en evaluatie](./how-to-assess.md) is toegevoegd aan een project
- Bekijk [ondersteuning en vereisten voor app-detectie](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Zorg ervoor dat de servers waarop u app-detectie uitvoert, Power shell versie 2,0 of hoger hebben geïnstalleerd en dat er VMware-Hulpprogram Ma's (later dan 10.2.0) is geïnstalleerd.
- Controleer de [vereisten](./migrate-appliance.md) voor het implementeren van het Azure migrate apparaat.
- Controleer of u de [vereiste rollen](./create-manage-projects.md#verify-permissions) hebt in het abonnement om resources te maken.
- Zorg ervoor dat uw apparaat toegang heeft tot Internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Detectie van SQL Server instanties en data bases inschakelen

1. In uw Azure Migrate project
    - Selecteer **niet ingeschakeld** op de hub-tegel of   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Azure migrate hub-tegel met SQL-detectie niet ingeschakeld":::
    - Selecteer **niet ingeschakeld** voor een item op de pagina Server detectie onder SQL-instanties kolom   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Azure migrate gedetecteerde servers-BLADe met SQL-detectie niet ingeschakeld":::
2. Voer in Discover SQL Server-instanties en-data bases de volgende stappen uit:
    - Selecteer **upgrade** om de vereiste resource te maken.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Knop voor het bijwerken van het Azure Migrate apparaat":::
    - Controleer of de services die op het apparaat worden uitgevoerd, zijn bijgewerkt naar de meest recente versie. Hiertoe start u het configuratie beheer van het toestel vanaf uw toestel server en selecteert u in het deel venster installatie vereisten de optie apparaten weer geven.
        - Het apparaat en de bijbehorende onderdelen worden automatisch bijgewerkt :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="de versie van het apparaat controleren":::
    - Voeg in het paneel referenties en detectie bronnen beheren van het configuratie beheer voor Apparaatbeheer domein-of SQL Server authenticatie referenties toe die de sysadmin-toegang hebben op het SQL Server exemplaar en de data bases die moeten worden gedetecteerd.
    U kunt gebruikmaken van de functie voor automatische referentie toewijzing van het apparaat of de referenties hand matig toewijzen aan de betreffende server, zoals [hier](./tutorial-discover-vmware.md#start-continuous-discovery)is gemarkeerd.

    Enkele punten om te noteren:
    - Zorg ervoor dat de software-inventarisatie al is ingeschakeld of geef domein-of niet-domein referenties op om hetzelfde in te scha kelen. Software-inventaris moet worden uitgevoerd om SQL Server exemplaren te detecteren.
    - Het apparaat zal proberen de domein referenties te valideren met AD, omdat deze worden toegevoegd. Zorg ervoor dat de toestel server de netwerk regel heeft voor de AD-server die aan de referenties is gekoppeld. Referenties die zijn gekoppeld aan SQL Server authenticatie, worden niet gevalideerd.

3. Zodra de gewenste referenties zijn toegevoegd, selecteert u detectie starten om de scan te starten.

> [!Note]
>Zorg ervoor dat SQL Discovery enige tijd wordt uitgevoerd voordat u evaluaties voor Azure SQL maakt. Als de detectie van SQL Server instanties en data bases niet mag worden voltooid, worden de respectieve exemplaren in het beoordelings rapport als **onbekend** gemarkeerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van een [Azure SQL-evaluatie](./how-to-create-azure-sql-assessment.md)
- Meer informatie over [Azure SQL-evaluaties](./concepts-azure-sql-assessment-calculation.md)