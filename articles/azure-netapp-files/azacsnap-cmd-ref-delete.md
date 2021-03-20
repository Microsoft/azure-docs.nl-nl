---
title: Verwijderen met Azure-toepassing consistent snap shot tool voor Azure NetApp Files | Microsoft Docs
description: Dit onderwerp bevat een hand leiding voor het uitvoeren van de Verwijder opdracht van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632725"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Verwijderen met Azure-toepassing consistent snap shot tool (preview)

Dit artikel bevat een hand leiding voor het uitvoeren van de Verwijder opdracht van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

## <a name="introduction"></a>Inleiding

Het is mogelijk om volume momentopnamen en database catalogus vermeldingen te verwijderen met de `azacsnap -c delete` opdracht.

> [!IMPORTANT]
> Moment opnamen die minder dan tien minuten zijn gemaakt voordat u deze opdracht uitvoert, moeten niet worden verwijderd vanwege de mogelijke interferentie met momentopname replicatie.

## <a name="command-options"></a>Opdracht Opties

De `-c delete` opdracht heeft de volgende opties:

- `--delete hana` bij gebruik met de opties `--hanasid <SID>` en `--hanabackupid <HANA backup id>` worden vermeldingen uit de SAP Hana back-upcatalogus verwijderd die voldoen aan de criteria.

- `--delete storage` Wanneer u met de optie gebruikt `--snapshot <snapshot name>` , wordt de moment opname uit het back-end-opslag systeem verwijderd.

- `--delete sync` bij gebruik met opties `--hanasid <SID>` en `--hanabackupid <HANA backup id>` wordt de naam van de opslag momentopname opgehaald uit de back-upcatalogus voor de `<HANA backup id>` , en wordt de vermelding vervolgens verwijderd uit de back-upcatalogus _en_ de moment opname van de volumes met de benoemde moment opname.

- `--delete sync` Wanneer wordt gebruikt met `--snapshot <snapshot name>` om te controleren of er vermeldingen in de back-upcatalogus voor de worden weer `<snapshot name>` gegeven, haalt de SAP Hana back-up-id op en verwijdert beide de vermelding in de back-upcatalogus _en_ de moment opname van de volumes met de benoemde moment opname.

- `[--force]` Beschrijving Wees *voorzichtig*.  Met deze bewerking wordt de verwijdering geforceerd zonder dat om bevestiging wordt gevraagd.

- `[--configfile <config filename>]` is een optionele para meter voor het toestaan van aangepaste configuratie bestandsnamen.

### <a name="delete-a-snapshot-using-sync-option"></a>Een moment opname verwijderen met de `sync` optie

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Controleert op vermeldingen in de back-upcatalogus voor de SAP HANA back-up-ID 157979797979, haalt de naam van de opslag momentopname op en verwijdert zowel de vermelding in de back-upcatalogus als de moment opname van alle volumes met de benoemde moment opname.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Controleert op vermeldingen in de back-upcatalogus voor de moment opname met de naam hana_hourly .2020-01 -22 _2358, haalt de SAP HANA back-upid op en verwijdert beide vermelding in de back-upcatalogus en de moment opname van de volumes met de benoemde moment opname.

### <a name="delete-a-snapshot-using-hana-option"></a>Een moment opname verwijderen met de `hana` optie

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Hiermee verwijdert u de SAP HANA back-up-ID 157979797979 uit de back-catalogus voor de SID-h80.

### <a name="delete-a-snapshot-using-storage-option"></a>Een moment opname verwijderen met de `storage` optie

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Hiermee verwijdert u de moment opname van alle volumes met een moment opname met de naam hana_hourly .2020-01 -22 _2358.

**Uitvoer met behulp van de `--delete storage` optie**

De gebruiker wordt gevraagd de verwijdering te bevestigen.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

Het is mogelijk om de gebruikers bevestiging te voor komen door de optionele `--force` para meter als volgt te gebruiken:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Volgende stappen

- [Details van de moment opname ophalen](azacsnap-cmd-ref-details.md)
- [Maak een back-up](azacsnap-cmd-ref-backup.md)
