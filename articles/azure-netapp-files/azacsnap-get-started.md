---
title: Aan de slag met Azure-toepassing consistent snap shot tool voor Azure NetApp Files | Microsoft Docs
description: Dit onderwerp bevat een hand leiding voor het installeren van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: c8532637e695b506e372817e6f4531f9a323936b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632695"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Aan de slag met Azure-toepassing consistent momentopname programma (preview-versie)

Dit artikel bevat een hand leiding voor het installeren van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>De momentopname hulpprogramma's ophalen

Aanbevolen wordt om de meest recente versie van het AzAcSnap- [installatie programma](https://aka.ms/azacsnapdownload) van micro soft te downloaden.

Het zelf-installatie bestand heeft een bijbehorend [AzAcSnap Installer-handtekening bestand](https://aka.ms/azacsnapdownloadsignature) dat is ondertekend met de open bare sleutel van micro soft om gpg verificatie van het gedownloade installatie programma mogelijk te maken.

Zodra deze down loads zijn voltooid, volgt u de stappen in deze hand leiding om te installeren.

### <a name="verifying-the-download"></a>Het downloaden controleren

Het installatie programma, dat kan worden gedownload per hierboven, heeft een bijbehorend PGP-handtekening bestand met een `.asc` bestandsnaam extensie. Dit bestand kan worden gebruikt om ervoor te zorgen dat het installatie programma dat wordt gedownload, een geverifieerd micro soft-bestand is. De open bare sleutel van micro soft PGP die wordt gebruikt voor het ondertekenen van Linux-pakketten, is hier ( <https://packages.microsoft.com/keys/microsoft.asc> ) en is gebruikt om het handtekening bestand te ondertekenen.

De open bare sleutel van micro soft PGP kan als volgt worden geïmporteerd naar een lokale gebruiker:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Met de volgende opdrachten wordt de open bare sleutel van micro soft PGP vertrouwd:

1. De sleutels in de Store weer geven.
2. Bewerk de micro soft-sleutel.
3. De vinger afdruk controleren met `fpr`
4. Onderteken de sleutel om deze te vertrouwen.

```bash
gpg --list-keys
```

Vermelde sleutels:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Uitvoer van interactieve `gpg` sessie micro soft open bare sleutel ondertekenen:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Het PGP-handtekening bestand voor het installatie programma kan als volgt worden gecontroleerd:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Voor meer informatie over het gebruik van GPG raadpleegt u [het GNU-privacybeleid](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De momentopname hulpprogramma's kunnen worden gebruikt in de volgende scenario's.

- Enkele SID
- Meervoudige SID
- HSR
- Uitschalen
- MDC (alleen één Tenant ondersteund)
- Enkele container
- SUSE-besturings systeem
- RHEL-besturings systeem
- SKU-TYPE I
- SKU-TYPE II

Zie [ondersteunde scenario's voor Hana grote instanties](/azure/virtual-machines/workloads/sap/hana-supported-scenario)

## <a name="snapshot-support-matrix-from-sap"></a>Ondersteunings matrix voor moment opnamen van SAP

De volgende matrix wordt gegeven als richt lijn voor het ondersteunen van de back-ups van opslag momentopnamen van SAP HANA.

| Database versies       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Eén container database| √       | √      | -      | -      | -      | -      |
|MDC Eén Tenant        | -       | -      | √      | √      | √      | √      |
|Meerdere tenants MDC     | -       | -      | -      | -      | -      | √      |
> √ = <small>ondersteund door SAP voor moment opnamen van opslag</small>

## <a name="important-things-to-remember"></a>Belang rijke zaken die u moet onthouden

- Na de installatie van de momentopname hulpprogramma's bewaakt continu de beschik bare opslag ruimte en verwijdert u de oude moment opnamen zo nodig regel matig om te voor komen dat opslag wordt ingevuld.
- Gebruik altijd de nieuwste hulp middelen voor moment opnamen.
- Gebruik dezelfde versie van de momentopname hulpprogramma's op het hele land.
- Test de momentopname hulpprogramma's en wees vertrouwd met de vereiste para meters en de uitvoer van de opdracht voordat u in het productie systeem gebruikt.
- Bij het instellen van de HANA-gebruiker voor back-up (details hieronder in dit document), moet u de gebruiker instellen voor elk HANA-exemplaar. Maak een SAP HANA gebruikers account voor toegang tot HANA-instantie onder de SYSTEMDB (en niet in de SID-data base) voor MDC. In de omgeving met één container kan deze worden ingesteld onder de Tenant-data base.
- Klanten moeten de open bare SSH-sleutel voor toegang tot opslag opgeven. Deze actie moet worden uitgevoerd per knoop punt en voor elke gebruiker waarbij de opdracht wordt uitgevoerd.
- Het aantal moment opnamen per volume is beperkt tot 250.
- Als u het configuratie bestand hand matig wilt bewerken, moet u altijd een Linux-tekst editor gebruiken, zoals "VI" en niet Windows-editors zoals Klad blok. Het gebruik van de Windows-editor kan de bestands indeling beschadigen.
  - Stel `hdbuserstore` in dat de SAP Hana gebruiker moet communiceren met SAP Hana.
- Voor DR: de momentopname hulpprogramma's moeten worden getest op het DR-knoop punt voordat DR is ingesteld.
- Schijf ruimte regel matig bewaken, automatisch verwijderen van Logboeken wordt beheerd met de `--trim` optie   `azacsnap -c backup` voor de versies voor SAP Hana 2 en hoger.
- **Risico dat er geen moment opnamen worden gemaakt** : de momentopname hulpprogramma's werken alleen met het knoop punt van het SAP Hana systeem dat is opgegeven in het configuratie bestand.  Als dit knoop punt niet beschikbaar is, is er geen mechanisme om automatisch te communiceren met een ander knoop punt.  
  - Voor een **SAP HANA Scale-Out met een stand-by** -scenario is het gebruikelijk om de momentopname hulpprogramma's op het hoofd knooppunt te installeren en te configureren. Maar als het hoofd knooppunt niet beschikbaar is, neemt het stand-by-knoop punt de rol van het hoofd knooppunt over. In dit geval moet het implementatie team de momentopname hulpprogramma's configureren op beide knoop punten (Master en stand-by) om gemiste moment opnamen te voor komen. In de normale status neemt het hoofd knooppunt HANA-moment opnamen op die worden geïnitieerd door crontab, maar na failover van hoofd knooppunt moeten deze moment opnamen worden uitgevoerd vanaf een ander knoop punt, zoals het nieuwe hoofd knooppunt (voormalige stand-by). Om dit resultaat te halen, moet het knoop punt stand-by het moment dat het momentopname programma is geïnstalleerd, opslag communicatie ingeschakeld, hdbuserstore geconfigureerd, `azacsnap.json` geconfigureerd en crontab opdrachten die vóór de failover zijn klaargezet, worden uitgevoerd.
  - In het geval van een **SAP Hana HSR ha** wordt aanbevolen de momentopname hulpprogramma's op beide (primaire en secundaire knoop punten) te installeren, te configureren en te plannen. Als het primaire knoop punt niet meer beschikbaar is, neemt het secundaire knoop punt over met moment opnamen die op de secundaire worden uitgevoerd. In de normale status neemt het primaire knoop punt HANA-moment opnamen op die worden geïnitieerd door crontab. het secundaire knoop punt zou proberen moment opnamen te maken, maar er is een fout opgetreden omdat de primaire node goed werkt.  Maar na een failover van het primaire knoop punt worden deze moment opnamen vanuit het secundaire knoop punt uitgevoerd. Om dit resultaat te krijgen, moet het secundaire knoop punt het momentopname programma geïnstalleerd hebben, is de opslag communicatie ingeschakeld, `hdbuserstore` geconfigureerd, azacsnap.jsop geconfigureerd en crontab ingeschakeld voordat de failover wordt uitgevoerd.

## <a name="guidance-provided-in-this-document"></a>Richt lijnen die in dit document worden beschreven

De volgende richt lijnen worden gegeven om het gebruik van de momentopname hulpprogramma's te illustreren.

### <a name="taking-snapshot-backups"></a>Back-ups maken van moment opnamen

- [Wat zijn de vereisten voor de moment opname van de opslag?](azacsnap-installation.md#prerequisites-for-installation)
  - [Communicatie met opslag inschakelen](azacsnap-installation.md#enable-communication-with-storage)
  - [Communicatie met SAP HANA inschakelen](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Hand matig moment opnamen maken](azacsnap-tips.md#take-snapshots-manually)
- [Automatische back-up van moment opnamen instellen](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [De moment opnamen bewaken](azacsnap-tips.md#monitor-the-snapshots)
- [Een moment opname verwijderen?](azacsnap-tips.md#delete-a-snapshot)
- [Een moment opname herstellen](azacsnap-tips.md#restore-a-snapshot)
- [Een `boot` moment opname herstellen](azacsnap-tips.md#restore-a-boot-snapshot)
- [Wat zijn belang rijke feiten die u moet weten over de moment opnamen](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Moment opnamen worden getest voor zowel een enkele SID als een meervoudige SID.

### <a name="performing-disaster-recovery"></a>Herstel na nood geval uitvoeren

- [Wat zijn de vereisten voor de installatie van DR](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Herstel na nood geval instellen](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [De gegevens replicatie van de primaire naar een DR-site controleren](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Een failover uitvoeren naar een DR-site?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Volgende stappen

- [Azure-toepassing consistent momentopname programma installeren](azacsnap-installation.md)
