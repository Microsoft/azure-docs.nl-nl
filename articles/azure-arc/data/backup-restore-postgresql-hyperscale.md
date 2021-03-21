---
title: Back-up en herstel voor Azure Database for PostgreSQL Hyperscale-servergroepen
description: Back-up en herstel voor Azure Database for PostgreSQL Hyperscale-servergroepen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686696"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Back-ups maken en herstellen van Azure Arc ingeschakelde PostgreSQL grootschalige-Server groepen

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Wanneer u een back-up maakt of herstelt van uw Azure-PostgreSQL grootschalige-Server groep, wordt een back-up gemaakt van de hele set data bases op alle PostgreSQL-knoop punten van de Server groep.

## <a name="take-a-manual-full-backup"></a>Een hand matige volledige back-up maken

Voer de volgende opdracht uit om een volledige back-up te maken van de volledige gegevens en logboek mappen van uw server groep:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Waar:
- __naam__ geeft de naam van een back-up aan
- __Server naam__ geeft een server groep aan
- __no-wait__ geeft aan dat de opdracht regel niet wacht totdat de back-up is voltooid, zodat u dit opdracht regel venster kunt blijven gebruiken

Met deze opdracht wordt een gedistribueerde volledige back-up gecoördineerd op alle knoop punten die de PostgreSQL grootschalige-Server groep voor Azure Arc ondersteunen. Met andere woorden, Hiermee maakt u een back-up van alle gegevens in uw coördinator en worker-knoop punten.

Bijvoorbeeld:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Wanneer de back-up is voltooid, worden de ID, naam, grootte, status en tijds tempel van de back-up geretourneerd. Bijvoorbeeld:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` Hiermee wordt de tijd zone aangegeven voor het tijdstip waarop de back-up is gemaakt. In dit voor beeld betekent ' + 00:00 ' de UTC-tijd (UTC + 00 uur 00 minuten).

> [!NOTE]
> Het is nog niet mogelijk om:
> - Automatische back-ups plannen
> - De voortgang van een back-up weer geven terwijl deze wordt uitgevoerd

## <a name="list-backups"></a>Back-ups weergeven

Voer de volgende opdracht uit om de back-ups weer te geven die beschikbaar zijn voor herstel:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Bijvoorbeeld:

```console
azdata arc postgres backup list --server-name postgres01
```

Retourneert een uitvoer als:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

De time stamp-kolom geeft het punt aan van de tijd UTC waarop de back-up is gemaakt.

## <a name="restore-a-backup"></a>Een back-up terugzetten
In deze sectie wordt uitgelegd hoe u een volledige herstel bewerking of een herstel tijdstip kunt uitvoeren. Wanneer u een volledige back-up herstelt, herstelt u de volledige inhoud van de back-up. Wanneer u een herstel punt in de tijd maakt, herstelt u het tijdstip dat u opgeeft. Trans acties die later zijn uitgevoerd dan dit tijdstip, worden niet hersteld.

### <a name="restore-a-full-backup"></a>Een volledige back-up herstellen
Als u de volledige inhoud van een back-up wilt herstellen, voert u de volgende opdracht uit:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Waar:
- __Backup-id__ is de id van de back-up die wordt weer gegeven in de lijst met weer gegeven back-upopdrachten hierboven.
Hiermee coördineert u een gedistribueerd volledig herstel op alle knoop punten die de PostgreSQL grootschalige-Server groep van Azure Arc ondersteunen. Met andere woorden, Hiermee worden alle gegevens in uw coördinator en worker-knoop punten hersteld.

#### <a name="examples"></a>Voorbeelden:

__Herstel de postgres01 van de Server groep op zichzelf:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Deze bewerking wordt alleen ondersteund voor PostgreSQL versie 12 en hoger.

__Herstel de postgres01 van de Server groep naar een andere server groep postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Deze bewerking wordt ondersteund voor alle versies van PostgreSQL vanaf versie 11. De doel server groep moet worden gemaakt voordat de herstel bewerking moet dezelfde configuratie hebben en moet dezelfde back-uppvc gebruiken als de bron Server groep.

Wanneer de herstel bewerking is voltooid, wordt de uitvoer als volgt geretourneerd naar de opdracht regel:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Het is nog niet mogelijk om:
> - Een back-up herstellen door de naam ervan aan te geven
> - De voortgang van een herstel bewerking weer geven


### <a name="do-a-point-in-time-restore"></a>Een herstel punt in de tijd herstellen

Voer de volgende opdracht uit om een server groep te herstellen tot een specifieke punt tijd:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Als u de algemene indeling van de opdracht herstellen wilt lezen, voert u uit: `azdata arc postgres backup restore --help` .

Waar `time` is het tijdstip waarop u wilt herstellen. Geef een tijds tempel of een nummer en achtervoegsel ( `m` voor minuten, `h` uren, `d` dagen of `w` weken) op. Gaat bijvoorbeeld `1.5h` 90 minuten terug.

#### <a name="examples"></a>Voorbeelden:
__Maak een herstel punt van de Server groep postgres01 op zichzelf:__

Het is nog niet mogelijk om een herstel punt van een server groep naar zichzelf te herstellen.

__Maak een herstel punt van de Server groep postgres01 naar een andere server groep postgres02 naar een specifieke tijds tempel:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

In dit voor beeld wordt de status van de postgres02-Server groep in de Server groep teruggezet op postgres01 december 2020 om 04:23:48.75 UTC. Houd er rekening mee dat "+ 00" de tijd zone aangeeft van het punt in de tijd dat u opgeeft. Als u geen tijd zone opgeeft, wordt de tijd zone van de client van waaruit u de herstel bewerking uitvoert, gebruikt.

Bijvoorbeeld:
- `2020-12-08 04:23:48.751326+00` wordt geïnterpreteerd als `2020-12-08 04:23:48.751326` UTC
- Als u zich in de Pacific Standard Time zone (PST = UTC + 08) bevindt, `2020-12-08 04:23:48.751326` wordt geïnterpreteerd als `2020-12-08 12:23:48.751326` UTC. deze bewerking wordt ondersteund voor alle versies van postgresql vanaf versie 11. De doel server groep moet worden gemaakt vóór de herstel bewerking en moet dezelfde back-uppvc gebruiken als de bron Server groep.


__Maak een herstel punt van de Server groep postgres01 naar een andere server groep postgres02 naar een specifieke hoeveelheid tijd in het verleden:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

In dit voor beeld wordt in Server groep teruggezet postgres02 de status van de Server groep postgres01 22 minuten geleden.
Deze bewerking wordt ondersteund voor alle versies van PostgreSQL vanaf versie 11. De doel server groep moet worden gemaakt vóór de herstel bewerking en moet dezelfde back-uppvc gebruiken als de bron Server groep.

> [!NOTE]
> Het is nog niet mogelijk om:
> - De voortgang van een herstel bewerking weer geven

## <a name="delete-backups"></a>Back-ups verwijderen

Het bewaren van back-ups kan niet worden ingesteld in de preview-versie. U kunt back-ups die u niet nodig hebt, echter hand matig verwijderen.
De algemene opdracht voor het verwijderen van back-ups is:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

Hierbij
- `--server-name` is de naam van de Server groep waaruit de gebruiker een back-up wil verwijderen
- `--name` is de naam van de back-up die u wilt verwijderen
- `-id`is de ID van de back-up die u wilt verwijderen

> [!NOTE]
> `--name` en `-id` sluiten elkaar wederzijds uit.

Bijvoorbeeld:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

U kunt de naam en de ID van uw back-ups ophalen door de opdracht lijst back-up uit te voeren, zoals wordt beschreven in de vorige alinea.

Voor meer informatie over de opdracht verwijderen voert u het volgende uit:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [uitschalen (werk knooppunten toevoegen)](scale-out-postgresql-hyperscale-server-group.md) voor uw server groep
- Meer informatie over het [schalen van omhoog of omlaag (verg Roten/verkleinen van geheugen/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) uw server groep
