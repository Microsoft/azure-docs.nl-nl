---
title: Azure-toepassing consistent momentopname hulpmiddel testen voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u de test opdracht uitvoert van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632680"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Azure-toepassing consistent momentopname hulpmiddel testen (preview)

In dit artikel wordt uitgelegd hoe u de test opdracht uitvoert van het hulp programma Azure-toepassing consistente moment opname dat u kunt gebruiken met Azure NetApp Files.

## <a name="introduction"></a>Inleiding

Een test van de configuratie wordt uitgevoerd met behulp van de `azacsnap -c test` opdracht.

## <a name="command-options"></a>Opdracht Opties

De `-c test` opdracht heeft de volgende opties:

- `--test hana`  Test de verbinding met het SAP HANA-exemplaar.

- `--test storage` Test de communicatie met de onderliggende opslag interface door een tijdelijke opslag momentopname te maken op alle geconfigureerde `data` volumes en deze vervolgens te verwijderen. 

- `--test all` zowel de als- `hana` tests worden uitgevoerd `storage` .

- `[--configfile <config filename>]` is een optionele para meter voor het toestaan van aangepaste configuratie bestandsnamen.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Controleer de verbinding met SAP HANA `azacsnap -c test --test hana`

Met deze opdracht wordt de HANA-verbinding gecontroleerd voor alle HANA-instanties in het configuratie bestand. De HDBuserstore wordt gebruikt om verbinding te maken met de SYSTEMDB en de SID-gegevens op te halen.

Voor SSL kan deze opdracht het volgende optionele argument aannemen:

- `--ssl=` Hiermee wordt een versleutelde verbinding met de data base geforceerd en wordt de versleutelings methode gedefinieerd die wordt gebruikt om te communiceren met SAP HANA, hetzij `openssl` of `commoncrypto` . Indien gedefinieerd, verwacht deze opdracht twee bestanden in dezelfde map te vinden. deze bestanden moeten worden benoemd na de bijbehorende SID. Raadpleeg [SSL gebruiken voor communicatie met SAP Hana](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Uitvoer van de `azacsnap -c test --test hana` opdracht

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Connectiviteit met opslag controleren `azacsnap -c test --test storage`

De `azacsnap` opdracht maakt een moment opname van alle gegevens volumes die zijn geconfigureerd om te controleren of deze de juiste toegang heeft tot de volumes voor elk SAP Hana exemplaar. Er wordt een tijdelijke moment opname gemaakt en vervolgens verwijderd voor elk gegevens volume om de toegang tot de moment opname voor elk bestands systeem te controleren.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Uitvoer van de `azacsnap -c test --test storage` opdracht

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Voor een grote Azure-instantie `azacsnap -c test --test storage` extrapolatie de opdracht de opslag generatie en de HLI-SKU.  Op basis van deze informatie biedt deze richt lijnen over het configureren van moment opnamen van het opstarten (Zie de regel beginnend met `Action:` output).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Volgende stappen

- [Back-up van moment opname met Azure-toepassing consistent momentopname programma](azacsnap-cmd-ref-backup.md)
