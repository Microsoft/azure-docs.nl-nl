---
title: Overzicht van de ontwikkeling van Azure Block Chain Service
description: Kennis Making met het ontwikkelen van oplossingen op de Azure Block Chain-service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80348383"
---
# <a name="azure-blockchain-service-development-overview"></a>Overzicht van de ontwikkeling van Azure Block Chain Service

Met de Azure Block Chain-Service kunt u consortium Block chain-netwerken maken om bedrijfs scenario's zoals het bijhouden van activa, digitale tokens, loyaliteit en beloning, financiële toeleverings ketens en herkomst in te scha kelen. De volgende secties introduceren Azure Block Chain Service Development voor het implementeren van ENTER prise Block chain-oplossingen.

## <a name="connecting-to-azure-blockchain-service"></a>Verbinding maken met de Azure Block Chain-Service

Er zijn verschillende soorten clients voor Block chain-netwerken, waaronder volledige knoop punten, licht knooppunten en externe clients. Azure Block Chain service bouwt een Block chain-netwerk dat knoop punten bevat. U kunt verschillende clients gebruiken als uw gateway naar de Azure Block Chain-Service voor de ontwikkeling van Block chain. De Azure Block Chain-service biedt basis verificatie of toegangs sleutel als ontwikkel eindpunt. Hieronder vindt u populaire clients waarmee u verbinding kunt maken.

### <a name="visual-studio-code"></a>Visual Studio Code

U kunt verbinding maken met consortium leden met behulp van de Azure Block Chain Development Kit Visual Studio code extension. Wanneer u bent verbonden met een consortium, kunt u slimme contracten compileren, bouwen en implementeren voor een Azure Block Chain Service-consortiumlid.

Voor het ontwikkelen van geavanceerde oplossingen voor Enter prise Block Chain is een ontwikkelings raamwerk nodig om verbinding te maken met verschillende Block chain-netwerken en om de levens cyclus van Smart contracten te beheren. De meeste projecten communiceren met ten minste twee Block Chain-knoop punten. Ontwikkel aars gebruiken een lokale Block Chain tijdens de ontwikkeling. Wanneer de toepassing gereed is voor test of release, wordt de ontwikkelaar geïmplementeerd op een Block chain-netwerk. Bijvoorbeeld het belangrijkste open bare Ethereum-netwerk of de Azure Block Chain-service. De Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio code maakt gebruik van truffle. Truffle is een populair Block Chain Development Framework om gedecentraliseerde toepassingen op Ethereum blockchains te schrijven, te compileren, te implementeren en te testen. U kunt Truffle ook beschouwen als een framework dat ervoor probeert slimme contract ontwikkeling en traditionele webontwikkeling naadloos te integreren.

Zie [Quick Start: Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md)voor meer informatie.

### <a name="metamask"></a>Het-gegevensmasker

Het-gegevensmask is een op een browser gebaseerde wallet (externe client), RPC-client en Basic contract Verkenner. In tegens telling tot andere browser Wallet, injecteert het Web3-exemplaar een instantie in de Java script-context van de browser, die fungeert als een RPC-client die verbinding maakt met een groot aantal Ethereum blockchains (*mainnet*, *Ropsten testnet*, *Kovan testnet*, lokaal RPC-knoop punt, enzovoort). U kunt aangepaste RPC eenvoudig instellen om verbinding te maken met de Azure Block Chain-service en block Chain-ontwikkeling te starten met behulp van Remix.

Voor meer informatie raadpleegt u [Quick Start: het gebruik van het opdracht masker om een slim contract te verbinden en te implementeren](connect-metamask.md)

### <a name="geth"></a>Geth

Geth is de opdracht regel interface voor het uitvoeren van een volledig Ethereum-knoop punt dat in go is geïmplementeerd. Het is niet nodig om een volledig knoop punt uit te voeren, maar u kunt ook de interactieve console van een Java Script-runtime omgeving openen om te communiceren met de Azure Block Chain-service.

Voor meer informatie raadpleegt u [Quick Start: Geth gebruiken om aan een Azure Block Chain Service-transactie knooppunt te koppelen](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Persoonlijke Ethereum-quorum transacties

Quorum is een op Ethereum gebaseerd, gedistribueerd grootboek protocol met trans acties en privacy voor contracten en nieuwe consensus mechanismen. De belangrijkste verbeteringen ten opzichte van Go-Ethereum zijn:

* **Privacy** -quorum ondersteunt persoonlijke trans acties en privé contracten via open bare en persoonlijke status scheiding en maakt gebruik van peer-to-peer versleutelde bericht uitwisselingen voor gerichte overdracht van privé gegevens naar netwerk deelnemers.
* **Alternatieve consensus mechanismen** : de proef van het werk-of-stake recht is niet nodig voor een netwerk met machtigingen. Quorum biedt meerdere consensus mechanismen die zijn ontworpen voor consortium ketens, zoals elk en IBFT.  De Azure Block Chain-service maakt gebruik van het IBFT consensus-mechanisme.
* Machtigingen voor **peering** : machtigingen voor knoop punten en peeren met behulp van slimme contracten zorgt ervoor dat alleen bekende partijen kunnen deel nemen aan het netwerk.
* **Hogere prestaties** -quorum biedt betere prestaties dan open bare Geth.

## <a name="block-explorers"></a>Verkenners blok keren

Blok keer verkenners zijn online Block Chain-browsers die afzonderlijke blok inhoud, transactie adres gegevens en geschiedenis weer geven. Basis informatie over blokken is beschikbaar via Azure Monitor in de Azure Block Chain-service. Als u echter meer gedetailleerde informatie nodig hebt tijdens de ontwikkeling, kunnen blok verkenners nuttig zijn.  De volgende blok verkenners werken met de Azure Block Chain-Service:

* [Epirus Azure Block Chain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) van Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

U kunt ook uw eigen blok Verkenner bouwen met behulp van Block Chain Data Manager en Azure Cosmos DB. Zie [zelf studie: block chain Data Manager gebruiken om gegevens te verzenden naar Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>TPS meting

Omdat Block Chain wordt gebruikt in meer bedrijfs scenario's, is de snelheid van trans acties per seconde (TPS) belang rijk om knel punten en inefficiënte systeem storingen te voor komen. Hoge transactie tarieven kunnen lastig zijn om binnen een gedecentraliseerde Block chain te onderhouden. Een nauw keurige meting op TPS kan worden beïnvloed door verschillende factoren zoals server thread, grootte van transactie wachtrij, netwerk latentie en beveiliging. Als u tijdens de ontwikkeling de snelheid van TPS moet meten, is een populair hulp programma voor open source [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Volgende stappen

Probeer een Snelstartgids met Azure Block Chain Development Kit voor Ethereum om aan een consortium te koppelen op de Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Visual Studio Code gebruiken om verbinding te maken met Azure Blockchain Service](connect-vscode.md)