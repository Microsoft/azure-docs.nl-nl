---
title: Overzicht van Microsoft Azure Data Box Disk | Microsoft Docs in gegevens
description: Beschrijft Azure Data Box Disk, een cloudoplossing waarmee u grote hoeveelheden gegevens aan Azure kunt overdragen
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: ca46ce3355edf2b77400011d023cd6af330a3b58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770429"
---
# <a name="what-is-azure-data-box-disk"></a>Wat is Azure Data Box Disk?

Met de oplossing van Microsoft Azure Data Box Disk kunt u terabytes aan on-premises gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden. De beveiligde gegevensoverdracht wordt versneld door u één tot vijf SSD's (solid-state drives) te verzenden. Deze versleutelde schijven van 8 TB worden naar uw datacenter gestuurd via een regionale vervoerder.

U kunt deze schijven snel configureren, verbinden en ontgrendelen via de Data Box-service in de Azure-portal. Kopieer uw gegevens op schijven en stuur de schijven terug naar Azure. In het Azure-datacenter worden uw gegevens automatisch van de schijven naar de cloud geüpload met behulp van een snelle en persoonlijke uploadlink.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Gebruik Data Box Disk om terabytes aan gegevens over te dragen in scenario's met geen tot beperkte netwerkconnectiviteit. De gegevensverplaatsing kan eenmalig of periodiek zijn, of eerst een grote gegevensoverdracht, gevolgd door meerdere periodieke overdrachten.

- **Eenmalige migratie**: wanneer grote aantallen on-premises gegevens worden verplaatst naar Azure. Bijvoorbeeld de gegevensoverdracht van offline tapes naar archiefgegevens in statische Azure-opslag.
- **Toenemende overdracht**: wanneer eerst een grote overdracht plaatsvindt met Data Box Disk (seed), gevolgd door toenemende overdrachten via het netwerk. Bijvoorbeeld wanneer Commvault en Data Box Disk worden gebruikt om back-ups te verplaatsen naar Azure. Deze migratie wordt gevolgd door het kopiëren van incrementele gegevens via het netwerk naar Azure Storage.
- **Periodieke uploads**: wanneer er periodiek grote aantallen gegevens worden gegenereerd en verplaatst moeten worden naar Azure. Bijvoorbeeld in de energie-exploitatie, waar er video's worden gegenereerd op olieplatforms en windmolenparken.

### <a name="ingestion-of-data-from-data-box"></a>Opname van gegevens uit de Data Box

Azure-providers en niet-Azure-providers kunnen gegevens opnemen uit Azure Data Box. Dit zijn enkele Azure-services die gegevens kunnen opnemen uit Azure Data Box:

- **SharePoint Online**: gebruik Azure Data Box en SharePoint Migration Tool (SPMT) om de inhoud van bestandsshares te migreren naar SharePoint Online. Met behulp van Data Box bent u niet meer afhankelijk van de WAN-verbinding voor het overzetten van de gegevens. Zie voor meer informatie [Use the Azure Data Box Heavy to migrate your file share content to SharePoint Online](data-box-heavy-migrate-spo.md) (Azure Data Box Heavy gebruiken om inhoud van bestandsshares te migreren naar SharePoint Online).

- **Azure File Sync**: hiermee repliceert u bestanden van uw Data Box naar een Azure-bestandsshare, zodat u uw bestandsservices kunt centraliseren en toch lokale toegang houdt tot uw gegevens. Zie [Azure File Sync implementeren](../storage/file-sync/file-sync-deployment-guide.md) voor meer informatie.

- **HDFS-archieven**: migreer gegevens van een on-premises HDFS-archief (Hadoop Distributed File System) van uw Hadoop-cluster naar Azure Storage met behulp van Data Box. Zie voor meer informatie [Migrate from on-prem HDFS store to Azure Storage with Azure Data Box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md) (Migreren van on-premises HDFS-archief naar Azure Storage met Azure Data Box).

- **Azure Backup**: hiermee kunt u grote back-ups van kritieke bedrijfsgegevens via offline mechanismen verplaatsen naar een kluis van Azure Recovery Services. Zie het [Overzicht van Azure Backup](../backup/backup-overview.md) voor meer informatie.

U kunt uw Data Box-gegevens gebruiken met een groot aantal niet-Azure-serviceproviders. Bijvoorbeeld:

- **[CommVault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** : hiermee kunt u grote hoeveelheden gegevens migreren naar Microsoft Azure met behulp van de Azure Data Box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** : hiermee kunt u een back-up maken van grote hoeveelheden gegevens en de gegevens repliceren van uw Hyper-V-machine naar uw Data Box.

Zie [Azure Data Box-partners](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf) voor een lijst met andere niet-Azure-serviceproviders die ondersteuning bieden voor Data Box.

## <a name="the-workflow"></a>De werkstroom

Een typische stroom bestaat uit de volgende stappen:

1. **Bestellen**: plaats een bestelling in de Azure-portal en geef de verzendingsgegevens en het Azure-doelopslagaccount voor uw gegevens op. Wanneer de schijven beschikbaar zijn, versleutelt Azure de schijven, bereidt Azure deze voor en verstuurt Azure deze met volgnummer.

2. **Ontvangen**: nadat de schijven zijn bezorgd, pakt u de schijven uit en koppelt u deze aan de computer die de te kopiëren gegevens bevat. Ontgrendel de schijven.

3. **Gegevens kopiëren**: sleep en zet de gegevens neer om deze naar de schijven te kopiëren.

4. **Terugsturen**: bereid de schijven voor en stuur deze terug naar het Azure-datacenter.

5. **Uploaden**: de gegevens worden automatisch van de schijf naar Azure gekopieerd. De schijven worden veilig gewist overeenkomstig de richtlijnen van het National Institute of Standards and Technology.

Tijdens dit proces wordt u per e-mail op de hoogte gehouden van alle statuswijzigingen. Ga naar [Data Box Disks in Azure Portal implementeren](data-box-disk-quickstart-portal.md) voor meer informatie over de gedetailleerde werkstroom.

## <a name="benefits"></a>Voordelen

Data Box Disk is ontworpen om grote aantallen gegevens naar Azure te verplaatsen, zonder impact op het netwerk. Deze oplossing biedt de volgende voordelen:

- **Snelheid**: Data Box Disk gebruikt een USB 3.0-verbinding om in minder dan een week tot 35 TB aan gegevens te verplaatsen.

- **Gebruiksvriendelijk**: Data Box is een eenvoudig te gebruiken oplossing.

  - De schijven gebruiken een USB-verbinding met bijna geen insteltijd.
  - De schijven zijn klein en daardoor gemakkelijk te hanteren.
  - De schijven hebben geen externe stroomtoevoer nodig.
  - De schijven kunnen worden gebruikt met een datacenterserver, desktop of laptop.
  - De oplossing biedt tracering van begin tot eind via de Azure-portal.

- **Beveiligd**: Data Box Disk heeft ingebouwde beveiliging voor de schijven, gegevens en de service.
  - Er kan niet met de schijven worden geknoeid en de schijven ondersteunen beveiligde update-functionaliteit.
  - De gegevens op de schijven zijn altijd beveiligd met AES 128-bitsversleuteling.
  - De schijven kunnen alleen worden ontgrendeld met een sleutel die via Azure Portal wordt doorgegeven.
  - De service wordt beschermd door beveiligingsfuncties van Azure.
  - Nadat uw gegevens zijn geüpload naar Azure, worden de schijven gewist overeenkomstig NIST 800-88r1-standaarden.  

Ga naar [Azure Data Box Disk-beveiliging en -gegevensbescherming](data-box-disk-security.md) voor meer informatie.

## <a name="features-and-specifications"></a>Functies en specificaties

| Specificaties                                          | Beschrijving              |
|---------------------------------------------------------|--------------------------|
| Gewicht                                                  | < 2 lbs. per doos. Maximaal 5 schijven in de doos                |
| Dimensies                                              | Schijf: 2,5-inch SSD |
| Kabels                                                  | 1 USB 3.1-kabel per schijf|
| Opslagcapaciteit per bestelling                              | 40 TB (ongeveer 35 TB bruikbaar)|
| Opslagcapaciteit van de schijf                                   | 8 TB (ongeveer 7 TB bruikbaar)|
| Gegevensinterface                                          | USB   |
| Beveiliging                                                | Vooraf versleuteld met BitLocker en beveiligde updates <br> Met wachtwoordsleutels beschermde schijven <br> Gegevens altijd versleuteld  |
| Overdrachtssnelheid                                      | tot 430 MBps, afhankelijk van de bestandsgrootte      |
|Beheer                                               | Azure Portal |

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor meer informatie over regionale beschikbaarheid. Data Box Disk kan ook in de Azure Government Cloud worden geïmplementeerd. Zie [Wat is Azure Government?](../azure-government/documentation-government-welcome.md) voor meer informatie.

## <a name="pricing"></a>Prijzen

Ga naar de [pagina met prijzen](https://azure.microsoft.com/pricing/details/databox/disk/) voor informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

- De [Vereisten voor Data Box Disk](data-box-disk-system-requirements.md) bekijken.
- De [Limieten voor Data Box Disk](data-box-disk-limits.md) begrijpen.
- Snel [Azure Data Box Disk](data-box-disk-quickstart-portal.md) in Azure Portal implementeren.