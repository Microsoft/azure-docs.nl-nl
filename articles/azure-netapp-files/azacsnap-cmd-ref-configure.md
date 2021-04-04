---
title: Azure-toepassing consistent snap shot tool configureren voor Azure NetApp Files | Microsoft Docs
description: Dit onderwerp bevat een hand leiding voor het uitvoeren van de opdracht configureren van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
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
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632758"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Azure-toepassing consistent snap shot tool configureren (preview-versie)

Dit artikel bevat een hand leiding voor het uitvoeren van de opdracht configureren van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

## <a name="introduction"></a>Introductie

Het configuratie bestand kan worden gemaakt of bewerkt met behulp van de `azacsnap -c configure` opdracht.

## <a name="command-options"></a>Opdracht Opties

De `-c configure` opdracht heeft de volgende opties:

- `--configuration new` een nieuw configuratie bestand maken.

- `--configuration edit` een bestaand configuratie bestand bewerken.

- `[--configfile <config filename>]` is een optionele para meter voor het toestaan van aangepaste configuratie bestandsnamen.

## <a name="configuration-file-for-snapshot-tools"></a>Configuratie bestand voor momentopname hulpprogramma's

Een configuratie bestand kan worden gemaakt door uit te voeren `azacsnap -c configure --configuration new` .  De naam van de configuratie is standaard `azacsnap.json` .  Een aangepaste bestands naam kan worden gebruikt in combi natie met de `--configfile=` para meter (bijvoorbeeld `--configfile=<customname>.json` ) het volgende voor beeld is voor de configuratie van een groot Azure-exemplaar:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Details van de vereiste waarden

De volgende secties bevatten gedetailleerde richt lijnen voor de verschillende waarden die nodig zijn voor het configuratie bestand.

### <a name="sap-hana-values"></a>SAP HANA waarden

Bij het toevoegen van een *Data Base* aan de configuratie zijn de volgende waarden vereist:

- **Adres van Hana-server** = de hostnaam of het IP-adres van de SAP Hana server.
- **Hana-sid** = de SAP Hana systeem-id.
- **Hana-instantie nummer** = het SAP Hana-exemplaar nummer.
- **Hana HDB-gebruikers archief sleutel** = de SAP Hana gebruiker geconfigureerd met machtigingen voor het uitvoeren van database back-ups.

- Eén knoop punt: IP en hostnaam van het knoop punt
- HSR met STONITH: IP en hostnaam van het knoop punt
- Scale-out (N + N, N + M): huidig hoofd knooppunt IP-adres en hostnaam
- HSR zonder STONITH: IP en hostnaam van het knoop punt
- Meervoudige SID op één knoop punt: hostnaam en IP-adres van het knoop punt dat als host fungeert voor deze Sid's

### <a name="azure-large-instance-hli-storage-values"></a>Opslag waarden voor Azure large instance (HLI)

Bij het toevoegen van een *HLI-opslag* aan een Data Base-sectie zijn de volgende waarden vereist:

- **Naam van opslag gebruiker** = deze waarde is de gebruikers naam die wordt gebruikt om de SSH-verbinding met de opslag tot stand te brengen.
- **IP-adres van opslag** = het adres van het opslag systeem.
- **Naam van opslag volume** = de volume naam voor de moment opname.  Deze waarde kan op meerdere manieren worden bepaald, misschien de eenvoudigste is om de volgende shell-opdracht te proberen:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Opslag waarden van Azure NetApp Files (ANF)

Wanneer u *ANF-opslag* toevoegt aan een Data Base-sectie, zijn de volgende waarden vereist:

- **Bestands naam van Service-Principal-verificatie** = dit is het `authfile.json` bestand dat in de Cloud shell is gegenereerd bij het configureren van de communicatie met Azure NetApp files storage.
- **Bron-id van het volledige ANF-opslag volume** = de volledige resource-id van het volume dat als moment opname wordt gemaakt.  Dit kan worden opgehaald uit: Azure Portal – > ANF >-volume – > instellingen/eigenschappen – > Resource-ID

## <a name="configuration-file-overview-azacsnapjson"></a>Overzicht van configuratie bestanden ( `azacsnap.json` )

In het volgende voor beeld `azacsnap.json` is de geconfigureerd met de één sid.

De parameter waarden moeten worden ingesteld op de specifieke SAP HANA omgeving van de klant.
Voor **Azure large instance** System wordt deze informatie verstrekt door micro soft service management tijdens de oproep-overdracht en wordt deze beschikbaar gesteld in een Excel-bestand dat is opgegeven tijdens overdracht. Open een service aanvraag als u deze gegevens opnieuw moet verstrekken.

Hier volgt een voor beeld: werk alle waarden dienovereenkomstig bij.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> Voor een nood herstel scenario waarbij back-ups op de DR-site moeten worden uitgevoerd, moet de HANA-server naam die is geconfigureerd in het nood herstel bestand (bijvoorbeeld `DR.json` ) op de Dr-site, overeenkomen met de naam van de productie server.

> [!NOTE]
> Voor een groot Azure-exemplaar moet uw opslag-IP-adres zich in hetzelfde subnet bevinden als uw server groep. In dit geval is het subnet van de Server groep bijvoorbeeld 172. 18,0. 18.0/24 en onze toegewezen opslag-IP is 172.18.18.11.

## <a name="next-steps"></a>Volgende stappen

- [Azure-toepassing consistent momentopname programma testen](azacsnap-cmd-ref-test.md)
