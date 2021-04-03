---
title: Azure Data Box Heavy gebruiken om de inhoud van een bestands share naar share point online te verplaatsen
description: Gebruik deze zelf studie om te leren hoe u de inhoud van een bestands share naar share point online kunt migreren met behulp van uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: b00a944b69b8492d4aa00258154bf00f1c66ad33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92127111"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>De Azure Data Box Heavy gebruiken om de inhoud van de bestands share te migreren naar share point online

Gebruik uw Azure Data Box Heavy en het hulp programma voor migratie van share point (SPMT) om uw bestands share-inhoud eenvoudig te migreren naar share point online en OneDrive. Met behulp van Data Box Heavy kunt u de afhankelijkheid van uw WAN-koppeling (Wide Area Network) verwijderen om de gegevens over te dragen.

De Microsoft Azure Data Box is een service waarmee u een apparaat vanuit de Microsoft Azure-portal kunt best Ellen. U kunt vervolgens terabytes aan gegevens van uw servers naar het apparaat kopiëren. Nadat het is teruggestuurd naar micro soft, worden uw gegevens naar Azure gekopieerd. Afhankelijk van de grootte van de gegevens die u wilt overdragen, kunt u kiezen uit:

- [Data Box Disk](./data-box-disk-overview.md) met 35-TB bruikbare capaciteit per bestelling voor kleine tot middel grote gegevens sets.
- [Data Box](./data-box-overview.md) met 80-TB bruikbare capaciteit per apparaat voor gemiddeld tot grote gegevens sets.
- [Data Box Heavy](./data-box-heavy-overview.md) met 770-TB bruikbare capaciteit per apparaat voor grote gegevens sets.

In dit artikel wordt uitgelegd hoe u de Data Box Heavy kunt gebruiken om de bestands share-inhoud naar share point online te migreren.

## <a name="requirements-and-costs"></a>Vereisten en kosten

### <a name="for-data-box-heavy"></a>Voor Data Box Heavy

- Data Box Heavy is alleen beschikbaar voor Enterprise Overeenkomst (EA), Cloud Solution Provider (CSP) of Azure sponsoring-aanbiedingen. Als uw abonnement geen van de bovenstaande typen bevat, neemt u contact op met Microsoft Ondersteuning om uw abonnement bij te werken of de [prijzen van Azure-abonnementen](https://azure.microsoft.com/pricing/)te bekijken.
- Er zijn kosten verbonden aan het gebruik van Data Box Heavy. Zorg ervoor dat u de [Data Box Heavy prijzen](https://azure.microsoft.com/pricing/details/databox/heavy/)bekijkt.


### <a name="for-sharepoint-online"></a>Voor share point online

- Bekijk de [minimale vereisten voor het hulp programma voor migratie van share point (SPMT)](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Overzicht werk stroom

Voor deze werk stroom moet u de stappen uitvoeren op Azure Data Box Heavy, evenals op share point online.
De volgende stappen hebben betrekking op uw Azure Data Box Heavy.

1. Volg orde Azure Data Box Heavy.
2. Uw apparaat ontvangen en instellen.
3. Kopieer gegevens van uw on-premises bestands share naar een map voor Azure Files op het apparaat.
4. Nadat de Kopieer bewerking is voltooid, moet u het apparaat terugsturen volgens de instructies.
5. Wacht tot de gegevens volledig zijn geüpload naar Azure.

De volgende stappen hebben betrekking op share point online.

6. Maak een virtuele machine in de Azure Portal en koppel de Azure-bestands share erop.
7. Installeer het SPMT-hulp programma op de virtuele Azure-machine.
8. Voer het SPMT-hulp programma uit met behulp van de Azure-bestands share als *bron*.
9. Voer de laatste stappen van het hulp programma uit.
10. Controleer en bevestig uw gegevens.

## <a name="use-data-box-heavy-to-copy-data"></a>Data Box Heavy gebruiken om gegevens te kopiëren

Voer de volgende stappen uit om gegevens te kopiëren naar uw Data Box Heavy.

1. [Bestel uw data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Nadat u uw Data Box Heavy hebt ontvangen, [stelt u de data Box Heavy](data-box-heavy-deploy-set-up.md)in. U kunt zowel de knoop punten op het apparaat bekabelen en configureren.
3. [Gegevens kopiëren naar Azure data Box Heavy](data-box-heavy-deploy-copy-data.md). Zorg ervoor dat u tijdens het kopiëren de volgende handelingen uitvoert:

    - Gebruik alleen de map *StorageAccountName_AzFile* in het data Box Heavy om de gegevens te kopiëren. Dit is omdat u wilt dat de gegevens eindigen in een Azure-bestands share, niet in blok-blobs of pagina-blobs.
    - Bestanden kopiëren naar een map in *StorageAccountName_AzFile* map. Een submap in *StorageAccountName_AzFile* map maakt een bestands share. Bestanden die rechtstreeks naar *StorageAccountName_AzFile* map worden gekopieerd, mislukken en worden geüpload als blok-blobs. Dit is de bestands share die u in de volgende stap op uw virtuele machine gaat koppelen.
    - Gegevens kopiëren naar beide knoop punten van uw Data Box Heavy.
3. Voer [voorbereiding voor verzending](data-box-heavy-deploy-picked-up.md#prepare-to-ship) uit op het apparaat. Een succes volle voor bereiding op verzen ding zorgt ervoor dat bestanden naar Azure kunnen worden geüpload.
4. [Het apparaat retour neren](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Controleer of de gegevens uploadt naar Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>SPMT gebruiken om gegevens te migreren

Nadat u een bevestiging van het Azure-gegevens team hebt ontvangen dat het kopiëren van de gegevens is voltooid, gaat u door met het migreren van uw gegevens naar share point online.

Voor de beste prestaties en connectiviteit wordt u aangeraden een virtuele Azure-machine (VM) te maken.

1. Meld u aan bij de Azure Portal en [Maak een virtuele machine](../virtual-machines/windows/quick-create-portal.md).
2. [Koppel de Azure-bestands share aan de VM](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Down load het hulp programma voor migratie van share point](https://spmtreleasescus.blob.core.windows.net/install/default.htm) en installeer het op uw virtuele Azure-machine.
4. Start het hulp programma voor migratie van share point. Klik op aanmelden en voer de gebruikers naam en het wacht woord **in** voor uw werk-of school account.
5. Wanneer u wordt gevraagd **waar uw gegevens zijn?**, selecteert u **Bestands share**. Geef het pad op naar uw Azure-bestands share waar uw gegevens zich bevinden.
6. Volg de resterende vragen als normaal, inclusief de doel locatie. Ga voor meer informatie naar [het gebruik van het hulp programma voor migratie van share point](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - De snelheid waarmee gegevens worden opgenomen in share point online, wordt beïnvloed door verschillende factoren, ongeacht of uw gegevens al in azure zijn. Als u deze factoren begrijpt, kunt u de efficiëntie van uw migratie plannen en maximaliseren.  Ga naar de [migratie snelheid van share point online en OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed)voor meer informatie.
> - Er is een risico dat bestaande machtigingen voor bestanden verloren gaan wanneer de gegevens naar share point online worden gemigreerd. U kunt ook bepaalde meta gegevens kwijt raken, zoals *gemaakt door* en de *datum gewijzigd door*.

## <a name="next-steps"></a>Volgende stappen

[Uw Data Box Heavy best Ellen](./data-box-heavy-deploy-ordered.md)