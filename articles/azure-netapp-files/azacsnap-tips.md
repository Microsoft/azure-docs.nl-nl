---
title: Tips en trucs voor het gebruik van Azure-toepassing consistent moment opname van het hulp programma voor Azure NetApp Files | Microsoft Docs
description: Biedt tips en trucs voor het gebruik van het Azure-toepassing consistente moment opname van het hulp programma dat u kunt gebruiken met Azure NetApp Files.
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
ms.openlocfilehash: d73bfd19a4135d09e9e19fcbcfedd50dbc1f7067
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632677"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Tips en trucs voor het gebruik van Azure-toepassing consistent momentopname programma (preview-versie)

Dit artikel bevat tips en trucs die handig kunnen zijn wanneer u AzAcSnap gebruikt.

## <a name="limit-service-principal-permissions"></a>Machtigingen voor Service-Principal beperken

Het kan nodig zijn om het bereik van de Service-Principal AzAcSnap te beperken.  Raadpleeg de [documentatie van Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/) voor meer informatie over een nauw keurig toegangs beheer van Azure-resources.  

Hier volgt een voor beeld van een roldefinitie met de mini maal vereiste acties die nodig zijn om AzAcSnap te laten functioneren.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

## <a name="take-snapshots-manually"></a>Moment opnamen hand matig maken

Voordat u een back-upopdracht ( `azacsnap -c backup` ) uitvoert, controleert u de configuratie door de test opdrachten uit te voeren en te controleren of deze met succes worden uitgevoerd.  De juiste uitvoering van deze tests `azacsnap` kan communiceren met de geïnstalleerde SAP Hana-data base en het onderliggende opslag systeem van de SAP Hana op een **groot Azure-exemplaar** of **Azure NetApp files** systeem.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Voer vervolgens de volgende opdracht uit om een hand matige back-up van de database momentopname te maken:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Automatische back-up van moment opname instellen

Het is gebruikelijk dat de UNIX-en Linux-systemen worden gebruikt `cron` om actieve opdrachten op een systeem te automatiseren. De standaard procedure voor de momentopname hulpprogramma's is het instellen van de gebruiker `crontab` .

Hieronder vindt u een voor beeld van een `crontab` voor de gebruiker `azacsnap` om moment opnamen te automatiseren.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Uitleg van de bovenstaande crontab.

- `MAILTO=""`: door een lege waarde op te nemen, voor komt u dat cron automatisch probeert de gebruiker te e-mailen wanneer de crontab-vermelding wordt uitgevoerd, omdat deze waarschijnlijk uiteindelijk in het e-mail bestand van de lokale gebruiker wordt weer gegeven.
- Steno versies van timing voor crontab-vermeldingen zijn geen uitleg:
  - `@monthly` = Run eenmaal per maand, dat wil zeggen "0 0 1 * *".
  - `@weekly`  = Run eenmaal per week, dat wil zeggen "0 0 * * 0".
  - `@daily`   = Run eenmaal per dag, dat wil zeggen "0 0 * * *".
  - `@hourly`  = Run eenmaal per uur, dat wil zeggen "0 * * * *".
- De eerste vijf kolommen worden gebruikt voor het aanwijzen van tijden. Raadpleeg de kolom voorbeelden hieronder:
  - `0,15,30,45`: Om de 15 minuten
  - `0-23`: Elk uur
  - `*` : Elke dag
  - `*` : Elke maand
  - `*` : Elke dag van de week
- Opdracht regel die moet worden uitgevoerd onder de vier Kante haken "()"
  - `. /home/azacsnap/.profile` = pull in het profiel van de gebruiker om hun omgeving in te stellen, inclusief $PATH, enzovoort.
  - `cd /home/azacsnap/bin` = Wijzig de uitvoerings Directory in de locatie '/Home/azacsnap/bin ', waarbij de configuratie bestanden zijn.
  - `azacsnap -c .....` = de volledige azacsnap-opdracht die moet worden uitgevoerd, inclusief alle opties.

Meer uitleg over cron en de indeling van het crontab-bestand: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Gebruikers zijn verantwoordelijk voor het bewaken van de cron-taken om ervoor te zorgen dat moment opnamen worden gegenereerd.

## <a name="monitor-the-snapshots"></a>De moment opnamen bewaken

De volgende voor waarden moeten worden bewaakt om te zorgen voor een goed systeem:

1. Beschik bare schijf ruimte. Moment opnamen nemen trage schijf ruimte in beslag omdat oudere schijf blokken blijven behouden in de moment opname.
    1. Gebruik de `--retention` `--trim` Opties en om de oude moment opnamen en database logboek bestanden automatisch op te schonen om het beheer van schijf ruimte te automatiseren.
1. Geslaagde uitvoering van de momentopname hulpprogramma's
    1. Controleer het `*.result` bestand op het slagen of mislukken van de meest recente uitvoering van `azacsnap` .
    1. Controleer `/var/log/messages` op uitvoer van de `azacsnap` opdracht.
1. Consistentie van de moment opnamen door deze periodiek naar een ander systeem te herstellen.

> [!NOTE]
> Voer de opdracht uit om de details van de moment opname weer te geven `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Een moment opname verwijderen

Als u een moment opname wilt verwijderen, voert u de opdracht uit `azacsnap -c delete` . Het is niet mogelijk om moment opnamen van het niveau van het besturings systeem te verwijderen. U moet de juiste opdracht ( `azacsnap -c delete` ) gebruiken om de opslag momentopnamen te verwijderen.

> [!IMPORTANT]
> Wees Vigilant wanneer u een moment opname verwijdert. Nadat het is verwijderd, is het **onmogelijk** om de verwijderde moment opnamen te herstellen.

## <a name="restore-a-snapshot"></a>Een momentopname herstellen

Een moment opname van een opslag volume kan worden teruggezet naar een nieuw volume ( `-c restore --restore snaptovol` ).  Voor een grote Azure-instantie kan het volume worden teruggezet naar een moment opname ( `-c restore --restore revertvolume` ).

> [!NOTE]
> Er is **geen** opdracht voor het herstellen van de data base.

Een moment opname kan worden teruggezet naar het gegevens gebied SAP HANA, maar SAP HANA moet niet worden uitgevoerd als er een kopie wordt gemaakt ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

Voor een grote Azure-instantie kunt u contact opnemen met het micro soft Operations-team door een service aanvraag te openen om een gewenste moment opname te herstellen vanuit de bestaande beschik bare moment opnamen. U kunt een service aanvraag openen via Azure Portal: <https://portal.azure.com.>

Als u besluit de nood herstel failover uit te voeren, `azacsnap -c restore --restore revertvolume` maakt de opdracht op de Dr-site automatisch de meest recente ( `/hana/data` en `/hana/logbackups` ) moment opnamen van volumes om een SAP Hana herstel mogelijk te maken. Gebruik deze opdracht om te zorgen dat de replicatie tussen productie-en DR-sites wordt verbroken.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Moment opnamen alleen instellen voor opstart volumes

> [!IMPORTANT]
> Deze bewerking is alleen van toepassing op een groot Azure-exemplaar.

In sommige gevallen beschikken klanten al over hulpprogram ma's om SAP HANA te beveiligen en willen ze alleen moment opnamen van opstart volumes configureren.  In dit geval is de taak vereenvoudigd en moeten de volgende stappen worden uitgevoerd.

1. Voer de stappen 1-4 van de vereisten voor de installatie uit.
1. Communicatie met opslag inschakelen.
1. Down load het installatie programma uitvoeren om de momentopname hulpprogramma's te installeren.
1. Setup van snap shot tools volt ooien.
1. Maak als volgt een nieuw configuratie bestand. De details van het opstart volume moeten zich in de OtherVolume stanza (gebruikers vermeldingen in <span style="color:red">rood</span>) bestaan:
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Controleer het configuratie bestand, Raadpleeg het volgende voor beeld:

    Gebruik `cat` de opdracht om de inhoud van het configuratie bestand weer te geven:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Back-ups van opstart volumes testen

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Controleer of het wordt weer gegeven. Let op de toevoeging van de `--snapshotfilter` optie om de geretourneerde momentopname lijst te beperken.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Uitvoer van de opdracht:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Automatische back-up van moment opnamen instellen.

> [!NOTE]
> Het is niet nodig om communicatie met SAP HANA in te stellen.

## <a name="restore-a-boot-snapshot"></a>Een moment opname van ' boot ' herstellen

> [!IMPORTANT]
> Deze bewerking is voor Azure large instance alleen.
> De server wordt hersteld naar het moment waarop de moment opname werd gemaakt.

Een ' boot '-moment opname kan als volgt worden hersteld:

1. De klant moet de server afsluiten.
1. Nadat de server is afgesloten, moet de klant een service aanvraag openen die de machine-ID en de moment opname bevat die moet worden hersteld.
    > Klanten kunnen een service aanvraag openen vanuit de Azure Portal: <https://portal.azure.com.>
1. Micro soft herstelt het LUN van het besturings systeem met de opgegeven computer-ID en moment opname en start vervolgens de server op.
1. De klant moet vervolgens controleren of de server is opgestart en in orde is.

Er zijn geen extra stappen die moeten worden uitgevoerd na het herstellen.

## <a name="key-facts-to-know-about-snapshots"></a>Belang rijke feiten over moment opnamen

Belangrijkste kenmerken van moment opnamen van opslag volume:

- **Locatie van moment opnamen**: moment opnamen zijn te vinden in een virtuele map ( `.snapshot` ) binnen het volume.  Zie de volgende voor beelden voor een **grote Azure-instantie**:
  - Enddatabase `/hana/data/<SID>/mnt00001/.snapshot`
  - Share `/hana/shared/<SID>/.snapshot`
  - Hout `/hana/logbackups/<SID>/.snapshot`
  - Opstarten: opstart momentopnamen voor HLI zijn **niet zichtbaar** op het niveau van het besturings systeem, maar kunnen wel worden weer gegeven met `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` is een verborgen *virtuele* map met het kenmerk alleen-lezen die alleen-lezen toegang biedt tot de moment opnamen.

- **Max. moment opname:** De hardware kan Maxi maal 250 moment opnamen per volume hebben. De moment opname-opdracht houdt een maximum aantal moment opnamen voor het voor voegsel in op basis van de Bewaar termijn op de opdracht regel. de oudste moment opname wordt verwijderd als deze voorbij het maximum aantal komt dat moet worden behouden.
- **Naam van moment opname:** De naam van de moment opname bevat het label van het voor voegsel dat is geleverd door de klant.
- **Grootte van de moment opname:** Afhankelijk van de grootte/wijzigingen op het database niveau.
- **Locatie van logboek bestand:** De logboek bestanden die door de opdrachten worden gegenereerd, worden uitgevoerd naar mappen zoals gedefinieerd in het JSON-configuratie bestand. Dit is standaard een submap onder waar de opdracht wordt uitgevoerd (bijvoorbeeld `./logs` ).

## <a name="next-steps"></a>Volgende stappen

- [Problemen oplossen](azacsnap-troubleshoot.md)
