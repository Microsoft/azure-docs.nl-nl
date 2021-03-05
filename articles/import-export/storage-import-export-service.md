---
title: Azure import/export gebruiken om gegevens over te dragen van en naar Azure Storage | Microsoft Docs
description: Meer informatie over het maken van import-en export taken in de Azure Portal voor het overbrengen van gegevens van en naar Azure Storage.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: b1f1560fc1a00577e1e4b30d922fc7d4cae0ab92
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181832"
---
# <a name="what-is-azure-importexport-service"></a>Wat is Azure import/export-service?

Azure import/export-service wordt gebruikt voor het veilig importeren van grote hoeveel heden gegevens naar Azure Blob-opslag en Azure Files door het verzenden van schijf stations naar een Azure-Data Center. Deze service kan ook worden gebruikt om gegevens over te dragen van Azure Blob-opslag naar schijfstations en te verzenden naar uw on-premises-locaties. Gegevens van een of meer schijf stations kunnen worden geïmporteerd naar Azure Blob-opslag of Azure Files.

Geef uw eigen schijf stations op en breng gegevens over naar de Azure import/export-service. U kunt ook schijf stations gebruiken die worden geleverd door micro soft.

Als u gegevens wilt overdragen met behulp van schijf stations die door micro soft worden geleverd, kunt u [Azure data Box Disk](../databox/data-box-disk-overview.md) gebruiken om gegevens te importeren in Azure. Micro soft stuurt Maxi maal 5 versleutelde Solid-State Disk-schijven (Ssd's) met een totale capaciteit van 40 TB per bestelling, naar uw Data Center via een regionale luchtvaart maatschappij. U kunt snel schijf stations configureren, gegevens naar schijf stations kopiëren via een USB 3,0-verbinding en de schijf stations weer naar Azure verzenden. Ga naar [Azure data Box Disk-overzicht](../databox/data-box-disk-overview.md)voor meer informatie.

## <a name="azure-importexport-use-cases"></a>Azure import/export-use cases

U kunt de Azure import/export-service gebruiken wanneer het uploaden of downloaden van gegevens via het netwerk te langzaam is, of als u extra netwerk bandbreedte nodig hebt. Gebruik deze service in de volgende scenario's:

* **Gegevens migratie naar de Cloud**: Verplaats grote hoeveel heden gegevens snel en kosten effectief naar Azure.
* **Inhouds distributie**: snel gegevens verzenden naar uw sites van klanten.
* **Back-up**: Maak back-ups van uw on-premises gegevens om in azure Storage op te slaan.
* **Gegevens herstel**: een grote hoeveelheid gegevens die is opgeslagen in de opslag herstellen en deze aan uw on-premises locatie laten leveren.

## <a name="importexport-components"></a>Import/export-onderdelen

Import/export-service maakt gebruik van de volgende onderdelen:

* **Import/export-service**: deze service die beschikbaar is in azure Portal helpt de gebruiker bij het maken en bijhouden van gegevens import (upload) en voor het exporteren (downloaden) van taken.  

* **Hulp programma WAImportExport**: dit is een opdracht regel programma dat het volgende doet:
  * Bereidt uw schijf stations voor die worden verzonden om te worden geïmporteerd.
  * Vereenvoudigt het kopiëren van uw gegevens naar het station.
  * Hiermee worden de gegevens op het station versleuteld met AES 256-bits BitLocker. U kunt een externe sleutel beveiliging gebruiken om uw BitLocker-sleutel te beveiligen.
  * Genereert de logboek bestanden van het station die worden gebruikt tijdens het maken van het importeren.
  * Helpt bij het identificeren van het aantal stations dat nodig is voor het exporteren van taken.

> [!NOTE]
> Het hulp programma WAImportExport is beschikbaar in twee versies, versie 1 en 2. U kunt het beste het volgende gebruiken:
>
> * Versie 1 voor importeren/exporteren naar Azure Blob-opslag.
> * Versie 2 voor het importeren van gegevens in azure files.
>
> Het hulpprogramma WAImportExport is alleen compatibel met een 64-bits Windows-besturingssysteem. Voor specifieke ondersteunde versies van besturings systemen gaat u naar [Azure import/export-vereisten](storage-import-export-requirements.md#supported-operating-systems).

* **Schijf stations**: u kunt Ssd's (Solid-state drives) of harde schijven (hdd's) verzenden naar het Azure-Data Center. Wanneer u een import taak maakt, verzendt u schijf stations met uw gegevens. Wanneer u een export taak maakt, verzendt u lege stations naar het Azure-Data Center. Voor specifieke schijf typen gaat u naar [ondersteunde schijf typen](storage-import-export-requirements.md#supported-hardware).

## <a name="how-does-importexport-work"></a>Hoe werkt Import/Export?

Met Azure import/export-service kunt u gegevens overdragen naar Azure-blobs en Azure Files door taken te maken. Gebruik Azure Portal of Azure Resource Manager REST API om taken te maken. Elke taak is gekoppeld aan één opslag account.

De taken kunnen import-of export taken zijn. Met een import taak kunt u gegevens importeren in azure blobs of Azure files, terwijl de export taak toestaat dat gegevens worden geëxporteerd uit Azure-blobs. Voor een import taak levert u stations met uw gegevens. Wanneer u een export taak maakt, verzendt u lege stations naar een Azure-Data Center. In elk geval kunt u Maxi maal tien schijf stations per taak verzenden.

### <a name="inside-an-import-job"></a>Binnen een import taak

Op hoog niveau omvat een import taak de volgende stappen:

1. Bepaal de gegevens die moeten worden geïmporteerd, het aantal stations dat u nodig hebt, doel-BLOB-locatie voor uw gegevens in azure Storage.
2. Gebruik het hulp programma WAImportExport om gegevens naar schijf stations te kopiëren. Versleutel de schijf stations met BitLocker.
3. Maak een import taak in uw doel-opslag account in Azure Portal. Upload de logboek bestanden van het station.
4. Geef het retour adres en het account nummer van de transporteur op om de schijven terug te sturen.
5. Verzend de schijf stations naar het verzend adres dat wordt vermeld tijdens het maken van de taak.
6. Werk het tracking nummer van de levering bij in de taak Details importeren en verzend de import taak.
7. De stations worden ontvangen en verwerkt in het Azure-Data Center.
8. De stations worden verzonden met behulp van uw vervoerders account naar het retour adres dat in de import taak is opgenomen.

> [!NOTE]
> Voor lokale verzen dingen (binnen het land/regio van het Data Center) moet u een account voor een binnenlandse transporteur delen.
>
> Voor verzen dingen in het buiten land, een internationaal gegevens centrum, moet u een account voor internationale telecomers delen.

 ![Afbeelding 1: taak stroom importeren](./media/storage-import-export-service/import-job.png)

Voor stapsgewijze instructies over het importeren van gegevens gaat u naar:

* [Gegevens importeren in azure blobs](storage-import-export-data-to-blobs.md)
* [Gegevens importeren in Azure Files](storage-import-export-data-to-files.md)

### <a name="inside-an-export-job"></a>Binnen een export taak

> [!IMPORTANT]
> De service ondersteunt alleen het exporteren van Azure-blobs. Het exporteren van Azure files wordt niet ondersteund.

Op hoog niveau omvat een export taak de volgende stappen:

1. Bepaal de gegevens die moeten worden geëxporteerd, het aantal stations dat u nodig hebt, bron-blobs of container paden van uw gegevens in Blob Storage.
2. Maak een export taak in uw bron-opslag account in Azure Portal.
3. Geef de bron-blobs of container paden op voor de gegevens die moeten worden geëxporteerd.
4. Geef het retour adres en het account nummer van de transporteur op om de schijven terug te sturen.
5. Verzend de schijf stations naar het verzend adres dat wordt vermeld tijdens het maken van de taak.
6. Werk het tracking nummer van de levering bij in de export taak Details en verzend de export taak.
7. De stations worden ontvangen en verwerkt in het Azure-Data Center.
8. De stations zijn versleuteld met BitLocker en de sleutels zijn beschikbaar via de Azure Portal.  
9. De stations worden verzonden met behulp van uw vervoerders account naar het retour adres dat in de import taak is opgenomen.

> [!NOTE]
> Voor lokale verzen dingen (binnen het land/regio van het Data Center) moet u een account voor een binnenlandse transporteur delen.
>
> Voor verzen dingen in het buiten land, een internationaal gegevens centrum, moet u een account voor internationale telecomers delen.
  
 ![Afbeelding 2: taak stroom exporteren](./media/storage-import-export-service/export-job.png)

Ga voor stapsgewijze instructies voor het exporteren van gegevens naar [gegevens exporteren vanuit Azure blobs](storage-import-export-data-from-blobs.md).

## <a name="region-availability"></a>Beschikbaarheid in regio’s

De Azure import/export-service ondersteunt het kopiëren van gegevens van en naar alle Azure Storage-accounts. U kunt schijf stations verzenden naar een van de vermelde locaties. Als uw opslag account zich in een Azure-locatie bevindt die hier niet is opgegeven, wordt er een alternatieve verzend locatie opgegeven wanneer u de taak maakt.

### <a name="supported-shipping-locations"></a>Ondersteunde verzend locaties

|Land/regio  |Land/regio  |Land/regio  |Land/regio  |
|---------|---------|---------|---------|
|VS - oost    | Europa - noord        | India - centraal        |US Gov - Iowa         |
|VS - west     |Europa -west         | India - zuid        | US DoD East        |
|VS - oost 2    | Azië - oost        |  India - west        | US DoD Central        |
|VS - west 2     | Azië - zuidoost        | Canada - midden        | China East         |
|VS - centraal     | Australië - oost        | Canada - oost        | China - noord        |
|VS - noord-centraal     |  Australië - zuidoost       | Brazilië - zuid        | Verenigd Koninkrijk Zuid        |
|VS - zuid-centraal     | Japan - west        |Korea - centraal         | Duitsland - centraal        |
|VS - west-centraal     |  Japan - oost       | VS (overheid) - Virginia        | Duitsland - noordoost        |
|Zuid-Afrika - west   |  Zuid-Afrika - noord |

## <a name="security-considerations"></a>Beveiligingsoverwegingen

De gegevens op het station worden versleuteld met AES 256-bits BitLocker-stationsversleuteling. Deze versleuteling beschermt uw gegevens tijdens de overdracht.

Voor import taken worden stations op twee manieren versleuteld.  

* Geef de optie op wanneer u *dataset.csv* -bestand gebruikt tijdens het uitvoeren van het hulp programma WAImportExport tijdens de voor bereiding van het station.

* BitLocker-versleuteling hand matig inschakelen op het station. Geef de versleutelings sleutel op in de *driveset.csv* tijdens het uitvoeren van de opdracht regel van het WAImportExport-programma tijdens het voorbereiden van het station De BitLocker-versleutelings sleutel kan verder worden beveiligd met behulp van een externe-sleutel beveiliging (ook wel bekend als de door micro soft beheerde sleutel) of een door de klant beheerde sleutel. Zie [een door de klant beheerde sleutel gebruiken voor het beveiligen van uw BitLocker-sleutel](storage-import-export-encryption-key-portal.md)voor meer informatie.

Wanneer de gegevens zijn gekopieerd naar de stations, wordt het station door de service met BitLocker versleuteld voordat het naar u wordt verzonden. De versleutelings sleutel wordt u via de Azure Portal verschaft. Het station moet worden ontgrendeld met het hulp programma WAImporExport met behulp van de-sleutel.

[!INCLUDE [storage-import-export-delete-personal-info.md](../../includes/storage-import-export-delete-personal-info.md)]

### <a name="pricing"></a>Prijzen

**Verwerkings kosten voor station**

Er zijn kosten voor het verwerken van stations voor elk station dat is verwerkt als onderdeel van uw import-of export taak. Bekijk de details van de [Azure import/export-prijzen](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Verzend kosten**

Wanneer u stations naar Azure verzendt, betaalt u de verzend kosten voor de vervoerder. Wanneer micro soft de stations naar u retourneert, worden de verzend kosten in rekening gebracht voor het vervoerder account dat u hebt geleverd op het moment dat de taak wordt gemaakt.

**Transactiekosten**

[Standaard kosten voor opslag transacties](https://azure.microsoft.com/pricing/details/storage/) gelden tijdens het importeren en exporteren van gegevens. Standaard kosten voor uitgaand verkeer zijn ook van toepassing op kosten voor opslag transacties wanneer gegevens worden geëxporteerd uit Azure Storage. Zie [prijzen voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/)voor meer informatie over de kosten van de uitgang.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de import/export-service voor:

* [Gegevens importeren in azure blobs](storage-import-export-data-to-blobs.md)
* [Gegevens exporteren uit Azure-blobs](storage-import-export-data-from-blobs.md)
* [Gegevens importeren in Azure Files](storage-import-export-data-to-files.md)
