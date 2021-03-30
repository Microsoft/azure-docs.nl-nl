---
title: Azure Block Chain Service-grootboek versies, patches, & upgrade
description: Overzicht van de ondersteunde versies van groot boek in azure Block Chain service. Inclusief beleids regels voor systeem patches en upgrades.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85807737"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Ondersteunde Azure Block Chain Service-grootboek versies

De Azure Block Chain-service maakt gebruik van het op Ethereum gebaseerde [quorum](https://www.goquorum.com/developers) grootboek dat is ontworpen voor de verwerking van persoonlijke trans acties binnen een groep bekende deel nemers, geïdentificeerd als een consortium in azure Block Chain service.

Op dit moment ondersteunt de Azure Block Chain-service [quorum versie 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) en [Tessera-transactie beheerder](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Updates en upgrades beheren

Het maken van versies in quorum geschiedt via een belang rijke, secundaire en patch releases. Als de quorum versie bijvoorbeeld 2.0.1 is, wordt release type als volgt gecategoriseerd:

|Primair | Secundair  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

De Azure Block Chain-service werkt patch releases van quorum automatisch bij van bestaande actieve leden binnen 30 dagen na de beschik baarheid van quorum.

## <a name="availability-of-new-ledger-versions"></a>Beschik baarheid van nieuwe grootboek versies

De Azure Block Chain-service biedt de meest recente primaire en secundaire versies van het quorum grootboek binnen 60 dagen na de beschik baarheid van de quorum fabrikant. Er zijn Maxi maal vier secundaire releases beschikbaar voor consortia waaruit u kunt kiezen bij het inrichten van een nieuw lid en consortium. Een upgrade van naar een primaire of secundaire versie wordt momenteel niet ondersteund. Als u bijvoorbeeld versie 2. x uitvoert, wordt een upgrade naar versie 3. x op dit moment niet ondersteund. Op dezelfde manier geldt dat als u versie 2,2 uitvoert, een upgrade naar versie 2,3 momenteel niet wordt ondersteund.

## <a name="how-to-check-quorum-ledger-version"></a>De versie van het quorum grootboek controleren

U kunt de quorum versie controleren op het lid van de Azure Block Chain-service door aan uw knoop punt te koppelen met behulp van Geth of door Diagnostische logboeken weer te geven.

### <a name="using-geth"></a>Geth gebruiken

Koppel aan uw Azure Block Chain Service-knoop punt met behulp van Geth. Bijvoorbeeld `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

Wanneer het knoop punt is verbonden, rapporteert Geth de quorum versie die vergelijkbaar is met de volgende uitvoer:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Voor meer informatie over het gebruik van Geth raadpleegt u [Quick Start: gebruik Geth om te koppelen aan een service transactie knooppunt van Azure Block Chain](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Diagnostische logboeken gebruiken

Als u Diagnostische logboeken inschakelt, wordt de quorum versie gerapporteerd voor transactie knooppunten. Het volgende informatie logboek bericht bevat bijvoorbeeld de quorum versie.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Zie de [Azure Block Chain-Service bewaken via Azure monitor](monitor-azure-blockchain-service.md#diagnostic-settings)voor meer informatie over Diagnostische logboeken.

## <a name="how-to-check-genesis-file-content"></a>De inhoud van het Genesis-bestand controleren

Als u de inhoud van het Genesis-bestand van het block Chain-knoop punt wilt controleren, kunt u de volgende Ethereum java script-API gebruiken:

``` bash
admin.nodeInfo.protocols
```
U kunt de API aanroepen met behulp van een Geth-console of een Web3-bibliotheek. Voor meer informatie over het gebruik van Geth raadpleegt u [Quick Start: gebruik Geth om te koppelen aan een service transactie knooppunt van Azure Block Chain](connect-geth.md).

## <a name="next-steps"></a>Volgende stappen

[Limieten in azure Block Chain Service](limits.md)
