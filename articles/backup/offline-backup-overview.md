---
title: Overzicht van offline back-up
description: Meer informatie over de onderdelen van offline back-ups. Ze omvatten offline back-ups op basis van Azure Data Box en offline back-ups op basis van de Azure import/export-service.
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: references_regions
ms.openlocfilehash: 7c65cf6b36af3057fb06c6a6584fa458b1030c72
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704132"
---
# <a name="overview-of-offline-backup"></a>Overzicht van offline back-up

In dit artikel vindt u een overzicht van offline back-ups.

Bij de eerste volledige back-ups naar Azure worden doorgaans grote hoeveel heden gegevens online overgebracht en is er meer netwerk bandbreedte nodig in vergelijking met de volgende back-ups die alleen incrementele wijzigingen overdragen. Externe kant oren of data centers in bepaalde geografische gebieden hebben niet altijd voldoende netwerk bandbreedte. Daarom nemen deze eerste back-ups enkele dagen in beslag. Gedurende deze periode gebruiken de back-ups continu hetzelfde netwerk dat is ingericht voor toepassingen die worden uitgevoerd in het on-premises Data Center.

Azure Backup ondersteunt offline back-ups, waarbij de eerste back-upgegevens offline worden gezet zonder gebruik van netwerk bandbreedte. Het biedt een mechanisme voor het kopiëren van back-upgegevens naar fysieke opslag apparaten. De apparaten worden vervolgens verzonden naar een nabijgelegen Azure-Data Center en geüpload naar een Recovery Services kluis. Dit proces zorgt voor een robuuste overdracht van back-upgegevens zonder gebruik te maken van netwerk bandbreedte.

## <a name="offline-backup-options"></a>Opties voor offline back-ups

Offline back-up wordt aangeboden in twee modi op basis van het eigendom van de opslag apparaten:

- Offline back-up op basis van Azure Data Box (preview-versie)
- Offline back-up op basis van de Azure import/export-service

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Offline back-up op basis van Azure Data Box (preview-versie)

Deze modus wordt momenteel ondersteund door de MARS-agent (Microsoft Azure Recovery Services) in de preview-versie. Deze optie maakt gebruik van [Azure data Box](https://azure.microsoft.com/services/databox/) voor het verzenden van micro soft-eigendoms-en veilige overdrachts apparaten met USB-connectors naar uw Data Center of externe kantoor. Back-upgegevens worden rechtstreeks naar deze apparaten geschreven. Met deze optie bespaart u de inspanningen die nodig zijn om uw eigen Azure-compatibele schijven en connectors te kopen of om tijdelijke opslag in te richten als een faserings locatie. Micro soft verwerkt ook de end-to-end-overdracht logistiek, die u via de Azure Portal kunt volgen.

Hier ziet u een architectuur waarin de verplaatsing van back-upgegevens met deze optie wordt beschreven.

![Data Box architectuur Azure Backup](./media/offline-backup-overview/azure-backup-databox-architecture.png)

Hier volgt een samen vatting van de architectuur:

1. Azure Backup kopieert rechtstreeks back-upgegevens naar deze vooraf geconfigureerde apparaten.
2. U kunt deze apparaten vervolgens weer verzenden naar een Azure-Data Center.
3. Azure Data Box kopieert de gegevens naar een opslag account dat eigendom is van de klant.
4. Azure Backup kopieert automatisch back-upgegevens van het opslag account naar de aangewezen Recovery Services kluis. Incrementele online back-ups worden gepland.

Als u offline back-ups wilt gebruiken op basis van Azure Data Box, raadpleegt u [offline back-up met Azure data Box](offline-backup-azure-data-box.md).

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Offline back-up op basis van de Azure import/export-service

Deze optie wordt ondersteund door Microsoft Azure Backup Server (MABS), System Center Data Protection Manager (DPM) DPM-A en de MARS-agent. Er wordt gebruikgemaakt van de [Azure import/export-service](../import-export/storage-import-export-service.md). U kunt initiële back-upgegevens naar Azure overdragen met behulp van uw eigen Azure-compatibele schijven en connectors. Voor deze benadering moet u tijdelijke opslag inrichten die bekend staat als de faserings locatie en gebruikmaken van vooraf ontwikkelde hulpprogram ma's voor het format teren en kopiëren van de back-upgegevens op schijven die eigendom zijn van de klant.

Hier ziet u een architectuur waarin de verplaatsing van back-upgegevens met deze optie wordt beschreven.

![Service architectuur van Azure Backup importeren/exporteren](./media/offline-backup-overview/azure-backup-import-export.png)

Hier volgt een samen vatting van de architectuur:

1. In plaats van de back-upgegevens via het netwerk te verzenden, schrijft Azure Backup de back-upgegevens naar een faserings locatie.
2. De gegevens in de faserings locatie worden naar een of meer SATA-schijven geschreven met behulp van een aangepast hulp programma.
3. Als onderdeel van de voorbereidende werkzaamheden maakt het hulp programma een Azure import-taak. De SATA-schijven worden verzonden naar het dichtstbijzijnde Azure-Data Center en verwijzen naar de import taak om de activiteiten te verbinden.
4. In het Azure-Data Center worden de gegevens op de schijven gekopieerd naar een Azure-opslag account.
5. Azure Backup kopieert de back-upgegevens van het opslag account naar de Recovery Services kluis. Incrementele back-ups worden gepland.

Als u offline back-ups wilt gebruiken op basis van de Azure import/export-service met de MARS-agent, raadpleegt u [offline back-upwerk stroom in azure backup](./backup-azure-backup-import-export.md).

Als u hetzelfde wilt gebruiken in combi natie met MABS of DPM-A, raadpleegt u [offline back-upwerk stroom voor dpm en Azure backup server](./backup-azure-backup-server-import-export.md).

## <a name="offline-backup-support-summary"></a>Samen vatting van offline back-upondersteuning

In de volgende tabel worden de twee beschik bare opties vergeleken, zodat u de juiste keuzes kunt maken op basis van uw scenario.

| **Overweging**                                            | **Offline back-up op basis van Azure Data Box**                     | **Offline back-up op basis van de Azure import/export-service**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup implementatie modellen                              | MARS-agent (preview-versie)                                              | MARS-agent, MABS, DPM-A                                           |
| Maximum aantal back-upgegevens per server (MARS) of per beveiligings groep (MABS, DPM-A) | [Azure data Box schijf](../databox/data-box-disk-overview.md) -7,2 TB <br> [Azure data Box](../databox/data-box-overview.md) -80 TB       | 80 TB (Maxi maal 10 schijven van 8 TB elk)                          |
| Beveiliging (gegevens, apparaat en service)                           | [Data](../databox/data-box-security.md#data-box-data-protection) -AES 256-bits versleuteld <br> [Apparaat](../databox/data-box-security.md#data-box-device-protection) -robuuste behuizing, bedrijfs eigen, op referenties gebaseerde interface voor het kopiëren van gegevens <br> [Service](../databox/data-box-security.md#data-box-service-protection) -beveiligd door Azure-beveiligings functies | Door BitLocker versleutelde gegevens                                 |
| Tijdelijke opslag locatie inrichten                     | Niet vereist                                                | Meer dan of gelijk aan de geschatte grootte van de back-upgegevens        |
| Ondersteunde regio’s                                           | [Azure Data Box schijf regio's](../databox/data-box-disk-overview.md#region-availability) <br> [Azure Data Box regio's](../databox/data-box-disk-overview.md#region-availability) | [Azure import/export-service regio's](../import-export/storage-import-export-service.md#region-availability) |
| Verzen ding tussen landen                                     | Niet ondersteund  <br>    Het bron adres en het doel-Azure-Data Center moeten zich in hetzelfde land/dezelfde regio bevinden * | Ondersteund                                                    |
| Logistiek overdragen (levering, Trans Port, ophalen)           | Volledig door micro soft beheerd                                     | Door de klant beheerd                                            |
| Prijzen                                                      | [Azure Data Box prijzen](https://azure.microsoft.com/pricing/details/databox/) <br> [Prijzen Azure Data Box schijf](https://azure.microsoft.com/pricing/details/databox/disk/) | [Prijzen voor Azure import/export-service](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* Als uw land/regio geen Azure-Data Center heeft, moet u uw schijven verzenden naar een Azure-Data Center in een ander land/regio.

## <a name="next-steps"></a>Volgende stappen

- [Offline back-up Azure Backup met behulp van Azure Data Box](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Werk stroom voor offline back-up in Azure Backup](backup-azure-backup-import-export.md)
- [Werk stroom voor offline back-ups voor DPM en Azure Backup Server](backup-azure-backup-server-import-export.md)
