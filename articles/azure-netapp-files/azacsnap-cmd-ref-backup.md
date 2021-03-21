---
title: Back-up maken met Azure-toepassing consistent snap shot tool voor Azure NetApp Files | Microsoft Docs
description: Dit onderwerp bevat een hand leiding voor het uitvoeren van de back-upopdracht van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
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
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730935"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Back-up maken met Azure-toepassing consistent snap shot tool (preview)

Dit artikel bevat een hand leiding voor het uitvoeren van de back-upopdracht van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

## <a name="introduction"></a>Inleiding

Een back-up op basis van een opslag momentopname wordt uitgevoerd met de `azacsnap -c backup` opdracht.  Met deze opdracht wordt de indeling van een consistente opslag momentopname van een Data Base op de gegevens volumes en een opslag momentopname (zonder database consistentie-instelling) op de andere volumes uitgevoerd.  

Voor gegevens volumes `azacsnap` wordt de data base gereedgemaakt voor een moment opname van de opslag, waarna de moment opname wordt gemaakt voor alle geconfigureerde volumes, ten slotte wordt de data base door de moment opname voltooid.  Ook worden alle database gegevens beheerd waarmee back-upactiviteiten van moment opnamen worden vastgelegd (bijvoorbeeld SAP HANA back-upcatalogus).

## <a name="command-options"></a>Opdracht Opties

De `-c backup` opdracht heeft de volgende argumenten:

- `--volume=` type van het volume waarnaar de moment opname moet worden gemaakt, deze para meter mag bevatten `data` of `other`
  - `data` maakt moment opnamen van de volumes in de dataVolume-stanza van het configuratie bestand.
  - `other` maakt moment opnamen van de volumes in de otherVolume-stanza van het configuratie bestand.
  
  > [!NOTE]
  > Door een afzonderlijk configuratie bestand te maken met het opstart volume als de otherVolume, is het mogelijk dat `boot` moment opnamen worden uitgevoerd op een volledig ander schema (bijvoorbeeld dagelijks).

- `--prefix=` het voor voegsel van de klant momentopname voor de naam van de moment opname. Deze para meter heeft twee doel einden. Eerst moet u een unieke naam opgeven voor het groeperen van moment opnamen. Ten tweede om het `--retention` aantal moment opnamen te bepalen dat voor de opgegeven opslag momentopnamen wordt bewaard `--prefix` .

    > [!IMPORTANT]
    > Alleen alfanumerieke tekens (A-Z, A-z, 0-9), onderstrepings teken (_) en streepjes (-) zijn toegestaan.

- `--retention` het aantal moment opnamen van de definitie `--prefix` die moet worden bewaard. Eventuele extra moment opnamen worden verwijderd nadat er een nieuwe moment opname is gemaakt `--prefix` .

- `--trim` deze optie is beschikbaar voor SAP HANA v2 en hoger, maar de back-catalogus en de back-up van de schijf en logboeken worden bewaard. Het aantal vermeldingen dat in de back-upcatalogus moet worden bewaard, is afhankelijk van de `--retention` bovenstaande optie, en verwijdert oudere vermeldingen voor het gedefinieerde voor voegsel ( `--prefix` ) uit de back-upcatalogus en de bijbehorende back-up van de fysieke Logboeken. Hiermee verwijdert u ook alle logboek back-upitems die ouder zijn dan de oudste vermelding voor niet-logboek back-ups. Met deze bewerking kunt u voor komen dat de back-ups van het logboek alle beschik bare schijf ruimte gebruiken.

  > [!NOTE]
  > Met de volgende voorbeeld opdracht worden negen opslag momentopnamen bewaard en wordt ervoor gezorgd dat de back-upcatalogus continu wordt verwijderd zodat deze overeenkomt met de negen opslag momentopnamen die worden bewaard.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` een optionele para meter waarmee de versleutelings methode wordt gedefinieerd die wordt gebruikt om te communiceren met SAP HANA, hetzij `openssl` of `commoncrypto` . Als u dit hebt gedefinieerd, `azacsnap -c backup` verwacht de opdracht om twee bestanden te vinden in dezelfde map. deze bestanden moeten worden benoemd na de bijbehorende sid. Raadpleeg [SSL gebruiken voor communicatie met SAP Hana](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). In het volgende voor beeld `hana` wordt een type momentopname met een voor voegsel van gemaakt `hana_TEST` en blijven `9` deze communiceren met SAP Hana met behulp van SSL ( `openssl` ).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` is een optionele para meter voor het toestaan van aangepaste configuratie bestandsnamen.

## <a name="snapshot-backups-are-fast"></a>Back-ups van moment opnamen zijn snel

De duur van een back-up van een moment opname is onafhankelijk van de grootte van het volume, waarbij een volume van 10 TB binnen dezelfde tijd wordt vastgemaakt als een volume van 10 GB.  

De belangrijkste factoren die van invloed zijn op de algehele uitvoerings tijd zijn het aantal volumes dat moment opname moet zijn en eventuele wijzigingen in de `--retention` para meter (waarbij een verlaging de uitvoerings tijd kan verg Roten als overtollige moment opnamen worden verwijderd).

In de bovenstaande voorbeeld configuratie (voor een **grote Azure-instantie**) duren moment opnamen voor de twee volumes minder dan 5 seconden om te volt ooien. Voor **Azure NetApp files** nemen moment opnamen voor de twee volumes ongeveer 60 seconden in beslag.

> [!NOTE]
> Als de `--retention` vorige tijd aanzienlijk wordt verkleind `azacsnap` (bijvoorbeeld van `--retention 50` tot `--retention 5` ), neemt de benodigde tijd toe wanneer `azacsnap` de extra moment opnamen moeten worden verwijderd.

## <a name="example-with-data-parameter"></a>Voor beeld met `data` para meter

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

De opdracht wordt niet uitgevoerd naar de-console, maar schrijft naar een logboek bestand, een resultaat bestand en `/var/log/messages` .

Het *logboek bestand* bestaat uit de opdracht naam + de-c optie en de configuratie bestandsnaam. Standaard een logboek bestandsnaam voor een `-c backup` Run met een standaard configuratie bestands naam `azacsnap-backup-azacsnap.log` .

Het *resultaat* bestand heeft dezelfde basis naam als het logboek bestand, met `.result` als achtervoegsel, bijvoorbeeld met `azacsnap-backup-azacsnap.result` de volgende uitvoer:

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

Het `/var/log/messages` bestand bevat dezelfde uitvoer als het `.result` bestand. Zie het volgende voor beeld (uitvoeren als root):

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Voor beeld met `other` para meter

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

De opdracht wordt niet uitgevoerd naar de-console, maar schrijft alleen naar een logboek bestand.  Er wordt _niet_ naar een resultaat bestand geschreven of `/var/log/messages` .

Het *logboek bestand* bestaat uit de opdracht naam + de-c optie en de configuratie bestandsnaam. Standaard een logboek bestandsnaam voor een `-c backup` Run met een standaard configuratie bestands naam `azacsnap-backup-azacsnap.log` .

## <a name="example-with-other-parameter-to-backup-host-os"></a>Voor beeld met `other` para meter (voor het besturings systeem backup-host)

> [!NOTE]
> Het gebruik van een ander configuratie bestand ( `--configfile bootVol.json` ) dat alleen de opstart volumes bevat.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

De opdracht wordt niet uitgevoerd naar de-console, maar schrijft alleen naar een logboek bestand.  Er wordt _niet_ naar een resultaat bestand geschreven of `/var/log/messages` .

De naam van het *logboek bestand* in dit voor beeld is `azacsnap-backup-bootVol.log` .

> [!NOTE]
> De naam van het logboek bestand bestaat uit de "(opdracht naam-(de `-c` optie)-(de naam van het configuratie bestand)".  Als u bijvoorbeeld de `-c backup` optie met de naam van het logboek bestand van gebruikt `h80.json` , wordt het logboek bestand aangeroepen `azacsnap-backup-h80.log` .  Of als u de `-c test` optie met hetzelfde configuratie bestand gebruikt, wordt het logboek bestand aangeroepen `azacsnap-test-h80.log` .

- HANA grote instantie type: er zijn twee geldige waarden met `TYPEI` of `TYPEII` afhankelijk van de Hana-eenheid voor grote exemplaren.
- Bekijk [beschik bare sku's voor Hana grote instanties](../virtual-machines/workloads/sap/hana-available-skus.md) om de beschik bare sku's te bevestigen.

## <a name="next-steps"></a>Volgende stappen

- [Details van de moment opname ophalen](azacsnap-cmd-ref-details.md)
- [Moment opnamen verwijderen](azacsnap-cmd-ref-delete.md)