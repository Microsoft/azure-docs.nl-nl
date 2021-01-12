---
title: Ondersteuningsmatrix voor SAP HANA Backup
description: In dit artikel vindt u meer informatie over de ondersteunde scenario's en beperkingen bij het gebruik van Azure Backup om back-ups te maken van SAP HANA-data bases op Azure-Vm's.
ms.topic: conceptual
ms.date: 11/7/2019
ms.custom: references_regions
ms.openlocfilehash: 7f287206c40e4556db2ee4b38bb46ef8e5352102
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072187"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Ondersteuningsmatrix voor back-up van SAP HANA-databases in virtuele Azure-machines

Azure Backup ondersteunt de back-up van SAP HANA-data bases naar Azure. In dit artikel vindt u een overzicht van de scenario's die worden ondersteund en beperkingen die aanwezig zijn wanneer u Azure Backup gebruikt om back-ups te maken van SAP HANA-data bases op virtuele

> [!NOTE]
> De frequentie van de back-uplogboeken kan nu worden ingesteld op mini maal 15 minuten. Logboek back-ups gaan alleen naar de stroom nadat een volledige back-up voor de data base is voltooid.

## <a name="scenario-support"></a>Scenario-ondersteuning

| **Scenario**               | **Ondersteunde configuraties**                                | **Niet-ondersteunde configuraties**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Topologie**               | Alleen SAP HANA die worden uitgevoerd in virtuele machines van Azure Linux                    | HANA grote instanties (HLI)                                   |
| **Regio's**                   | **Ga**<br> **Amerikaans-Amerika** : VS-midden, VS-Oost 2, VS-Oost, Noord-Centraal VS, Zuid-Centraal VS, VS-West 2, West-Centraal VS, VS-west, Canada-centraal, Canada-oost, Brazilië-Zuid <br> **Azië en Stille Oceaan** – Australië-centraal, Australië-centraal 2, Australië-oost, Australië-Zuidoost, Japan-Oost, Japan-West, Korea-centraal, Korea-zuid, Azië-Oost, Zuidoost-Azië, centraal-india, India-Zuid, West-india, China-oost, China-Noord, China oost2, China-Noord 2 <br> **Europa** – Europa-west, Europa-noord, Frankrijk-centraal, UK-zuid, UK-west, Duitsland-noord, Duitsland-west-centraal, Zwitserland-noord, Zwitserland-West, Centraal Zwitserland-Noord, Noor wegen Oost, Noor wegen West <br> **Afrika/me** -Zuid-Afrika-noord, Zuid-Afrika-west, UAE-noord, UAE-centraal  <BR>  **Azure Government-regio's** | Frankrijk-zuid, Duitsland-centraal, Duitsland-noordoost, US Gov IOWA |
| **Versies van besturings systemen**            | SLES 12 met SP2, SP3, SP4 en SP5; SLES 15 met SP0, SP1, SP2 <br><br>  Per 1 augustus 2020 is SAP HANA backup for RHEL (7.4, 7.6, 7.7 en 8.1) algemeen beschikbaar.                |                                             |
| **HANA-versies**          | Dit SDC op HANA 1. x, MDC op HANA 2. x <= SPS04 Rev 48, SPS05 (nog moeten worden gevalideerd voor scenario's waarbij versleuteling is ingeschakeld)      |                                                            |
| **HANA-implementaties**       | SAP HANA op één Azure VM: alleen omhoog schalen. <br><br> Voor implementaties met een hoge Beschik baarheid worden de knoop punten op de twee verschillende machines beschouwd als afzonderlijke knoop punten met afzonderlijke gegevens ketens.               | Uitschalen <br><br> Bij implementaties met een hoge Beschik baarheid wordt er niet automatisch een failover naar het secundaire knoop punt gemaakt. Het configureren van de back-up moet afzonderlijk worden uitgevoerd voor elk knoop punt.                                           |
| **HANA-instanties**         | Eén SAP HANA-exemplaar op één Azure VM: alleen omhoog schalen | Meerdere SAP HANA exemplaren op één virtuele machine. U kunt slechts één van deze meerdere exemplaren tegelijk beveiligen.                  |
| **HANA-database typen**    | Individuele database container (dit SDC) op 1. x, meerdere database container (MDC) op 2. x | MDC in HANA 1. x                                              |
| **HANA-database grootte**     | HANA-data bases van grootte <= 2 TB (dit is niet de geheugen grootte van het HANA-systeem)               |                                                              |
| **Back-uptypen**           | Volledig, Differentieel, incrementeel (preview) en logboek back-ups                          |  Momentopnamen                                       |
| **Hersteltypen**          | Raadpleeg de SAP HANA opmerking [1642148](https://launchpad.support.sap.com/#/notes/1642148) voor meer informatie over de ondersteunde typen herstel bewerkingen |                                                              |
| **Back-uplimieten**          | Maxi maal 2 TB volledige back-upgrootte per SAP HANA-exemplaar (zachte limiet)         |                                                              |
| **Speciale configuraties** |                                                              | SAP HANA en dynamische lagen <br>  Klonen via LaMa        |

------

>[!NOTE]
>Azure Backup past de tijd niet automatisch aan de zomertijd aan wanneer u een back-up van een SAP HANA-database maakt die op een virtuele Azure-machine wordt uitgevoerd.
>
>Pas het beleid waar nodig handmatig aan.

> [!NOTE]
> U kunt nu [de back-up-en herstel](./sap-hana-db-manage.md#monitor-manual-backup-jobs-in-the-portal) taken (op dezelfde computer) die zijn geactiveerd vanuit Hana-systeem eigen clients (SAP Hana Studio/cockpit/DBA-cockpit) in de Azure Portal bewaken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups SAP Hana data bases die worden uitgevoerd op virtuele machines](./backup-azure-sap-hana-database.md)
* Meer informatie over [het herstellen van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](./sap-hana-db-restore.md)
* Meer informatie over [het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure Backup](sap-hana-db-manage.md)
* Meer informatie over [het oplossen van algemene problemen tijdens het maken van back-ups van SAP HANA-databases](./backup-azure-sap-hana-database-troubleshoot.md)
