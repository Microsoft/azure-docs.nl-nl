---
title: Bekende problemen/migratie beperkingen bij het gebruik van de hybride modus
description: Meer informatie over bekende problemen/migratie beperkingen bij het gebruik van Azure Database Migration Service in hybride modus.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91291840"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Bekende problemen/migratie beperkingen bij het gebruik van de hybride modus

Bekende problemen en beperkingen die zijn gekoppeld aan het gebruik van Azure Database Migration Service in hybride modus, worden beschreven in de volgende secties.

## <a name="installer-fails-to-authenticate"></a>Het installatie programma kan niet worden geverifieerd

Nadat het certificaat is geüpload naar uw AdApp, is er een vertraging van Maxi maal enkele minuten voordat de verificatie met Azure kan worden uitgevoerd. Het installatie programma zal proberen om het opnieuw te proberen, maar het is mogelijk dat de vertraging van de doorgifte langer is dan de nieuwe poging en dat er een **FailedToGetAccessTokenException** -bericht wordt weer gegeven. Als het certificaat is geüpload naar de juiste AdApp en de juiste AppId is gegeven in dmsSettings.js, voert u de installatie opdracht opnieuw uit.

## <a name="service-offline-after-successful-installation"></a>Service ' offline ' na een geslaagde installatie

Als de service als offline wordt weer gegeven nadat het installatie proces is voltooid, probeert u de volgende stappen uit te voeren.

1. Ga in het Azure Portal naar uw exemplaar van Azure Database Migration Service en navigeer naar het tabblad **Hybrid** Settings en controleer of de werk nemer is geregistreerd door het raster van geregistreerde werk rollen te controleren.

    De status van deze werk nemer moet **online** zijn, maar kan worden weer gegeven als **offline** als er een probleem is.

2. Controleer de status van de service op de werk computer door de volgende Power shell-opdracht uit te voeren:

    ```
    Get-Service Scenario*
    ```

    Met deze opdracht geeft u de status van de Windows-service die de werk nemer uitvoert. Er mag slechts één resultaat zijn. Als de werk nemer is gestopt, kunt u proberen deze opnieuw te starten met behulp van de volgende Power shell-opdracht:

    ```
    Start-Service Scenario*
    ```

    U kunt ook de service in de gebruikers interface van Windows-Services controleren.

3. Als de Windows-service cycli tussen actieve en gestopt, heeft de werk nemer problemen ondervonden die kunnen worden gestart. Raadpleeg de Azure Database Migration Service Hybrid worker-Logboeken om het probleem te bepalen.

    - Logboeken voor installatie processen worden opgeslagen in de map Logs in de map van waaruit het uitvoer bare installatie programma voor Installer is uitgevoerd.
    - Azure Database Migration Service Hybrid worker-logboeken worden opgeslagen in de map **WorkerLogs** , in de map waarin de werk nemer is geïnstalleerd. De standaard locatie voor de logboek bestanden van de Hybrid Worker is **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Uw eigen ondertekende certificaat gebruiken

Het certificaat dat is gegenereerd door de actie GenerateCert is een zelfondertekend certificaat dat mogelijk niet acceptabel is op basis van uw interne beveiligings beleid. In plaats van dit certificaat te gebruiken, kunt u uw eigen certificaat opgeven en de vinger afdruk opgeven in dmsSettings.jsop. Dit certificaat moet worden geüpload naar uw AdApp en geïnstalleerd op de computer waarop u de Azure Database Migration Service Hybrid worker installeert. Installeer vervolgens dit certificaat met de persoonlijke sleutel in het certificaat archief van de lokale computer.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>De Worker-service uitvoeren als een account met beperkte bevoegdheden

Standaard wordt de Azure Database Migration Service Hybrid Worker-service uitgevoerd als het lokale systeem account. U kunt het account dat wordt gebruikt voor deze service wijzigen zolang het account dat u gebruikt netwerk machtigingen heeft. Gebruik het volgende proces om het run as-account van de service te wijzigen.

1. Stop de service via Windows-Services of met behulp van de Stop-Service opdracht in Power shell.

2. Werk de service bij voor het gebruik van een ander aanmeldings account.

3. Geef in certmgr voor lokale computer certificaten persoonlijke sleutel machtigingen voor het nieuwe account voor de **sleutel paar** certificaten van de **DMS hybride app-sleutel** en de DMS-scenario-engine.

    a. Open certmgr om de volgende sleutels weer te geven:

    - Code van DMS hybride app
    - Setup-sleutel voor het DMS-Hybrid Worker
    - Sleutel paar van de DMS-scenario-engine

    b. Klik met de rechter muisknop op de sleutel vermelding voor de **DMS hybride-app** , wijs **alle taken** aan en selecteer **persoonlijke sleutels beheren**.

    c. Selecteer op het tabblad **beveiliging** de optie **toevoegen** en voer vervolgens de naam van het account in.

    d. Gebruik dezelfde stappen om een persoonlijke sleutel machtiging te verlenen voor het nieuwe account aan het **sleutel paar** certificaat van de DMS-scenario-engine.

## <a name="unregistering-the-worker-manually"></a>De registratie van de werk nemer hand matig ongedaan maken

Als u geen toegang meer hebt tot de werk computer, kunt u de registratie van de werk nemer ongedaan maken en uw Azure Database Migration Service-exemplaar opnieuw gebruiken door de volgende stappen uit te voeren:

1. Ga in het Azure Portal naar uw Azure Database Migration Service-exemplaar en navigeer vervolgens naar de pagina met **hybride** instellingen.

   Uw werk item wordt weer gegeven in de lijst, met de status **offline** weer gegeven.

2. Rechts van de vermelding van het werk item selecteert u de weglatings tekens en selecteert u vervolgens **registratie ongedaan maken**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Problemen met specifieke migratie scenario's aanpakken

In de volgende secties worden scenario-specifieke problemen met betrekking tot het gebruik van Azure Database Migration Service hybride modus beschreven om een online migratie uit te voeren.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Online migraties naar Azure SQL Managed instance

**Hoog CPU-gebruik**

**Probleem**: als er te veel back-ups zijn of als de back-ups te groot zijn voor online migraties naar een SQL-beheerd exemplaar, wordt er een hoog CPU-gebruik uitgevoerd op de computer met de Hybrid Worker.

**Risico beperking**: als u dit probleem wilt verhelpen, gebruikt u gecomprimeerde back-ups, splitst u de migratie zodat deze meerdere shares gebruikt of breidt u de computer met de Hybrid worker uit.
