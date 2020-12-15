---
title: Slimme contracten maken, bouwen en implementeren - Azure Blockchain Service
description: Zelfstudie over het gebruik van de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio Code voor het maken, bouwen en implementeren van een slim contract in Azure Blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: f7605a0c118a40e52210582d2411569795fb25ee
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763686"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>Zelfstudie: Slimme contracten maken, bouwen en implementeren in Azure Blockchain Service

In deze zelfstudie gebruikt u de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio Code voor het maken, bouwen en implementeren van een slim contract in Azure Blockchain Service. U kunt ook de Development Kit gebruiken om een slim-contractfunctie uit te voeren via een transactie.

U gebruikt Azure Blockchain Development Kit voor Ethereum voor het volgende:

> [!div class="checklist"]
> * Een slim contract maken
> * Een slim contract implementeren
> * Een slim-contractfunctie uitvoeren via een transactie

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Quickstart: Visual Studio Code gebruiken om verbinding te maken met een Azure Blockchain Service-consortiumnetwerk](connect-vscode.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x of hoger](https://nodejs.org/download)
* [Git 2.10.x of hoger](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

In Windows is een geïnstalleerde C++-compiler vereist voor de module node-gyp. U kunt de MSBuild-hulpprogramma's gebruiken:

* Als Visual Studio 2017 is geïnstalleerd, configureert u npm voor het gebruik van de MSBuild-hulpprogramma's met de opdracht `npm config set msvs_version 2017 -g`
* Als Visual Studio 2019 is geïnstalleerd, stelt u het pad voor MSBuild-hulpprogramma's in voor npm. Bijvoorbeeld: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Anders installeert u de zelfstandige build-hulpprogramma's van Visual Studio met behulp van `npm install --global windows-build-tools` in een *Als administrator uitvoeren*-opdrachtshell met verhoogde bevoegdheden.

Zie de [opslagplaats node-gyp op GitHub](https://github.com/nodejs/node-gyp) voor meer informatie over node-gyp.

## <a name="create-a-smart-contract"></a>Een slim contract maken

De Azure Block Chain Development Kit voor Ethereum maakt gebruik van projectsjablonen en Truffle-hulpprogramma's voor het ontwerpen, maken en implementeren van contracten. Voordat u begint, moet u deze vereiste quickstart voltooien: [Quickstart: Visual Studio Code gebruiken om verbinding te maken met een Azure Blockchain Service-consortiumnetwerk](connect-vscode.md). De quickstart begeleidt u bij het installeren en configureren van de Azure Block Chain Development Kit voor Ethereum.

1. Kies in het opdrachtpalet van VS Code **Blockchain: New Solidity Project** (Nieuw Solidity-project).
1. Kies **Create basic project** (Basisproject maken).
1. Maak een nieuwe map met de naam `HelloBlockchain` en kies **Select New project path** (Nieuw projectpad selecteren).

De Azure Block Chain Development Kit maakt en initialiseert een nieuw Solidity-project voor u. Het basisproject bevat een voorbeeld van een slim contract met de naam **HelloBlockchain** en alle benodigde bestanden voor het bouwen en implementeren van uw consortiumlid in Azure Blockchain Service. Het kan enkele minuten duren voordat het project is gemaakt. U kunt de voortgang in het terminalvenster van Visual Studio Code controleren door de uitvoer voor Azure Blockchain te selecteren.

De projectstructuur lijkt op het volgende voorbeeld:

   ![Solidity-project](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>Een slim contract bouwen

Slimme contracten bevinden zich in de map **contracts** van het project. U compileert slimme contracten voordat u ze implementeert in een blockchain. Gebruik de opdracht **Build Contracts** (Contracten bouwen) om alle slimme contracten in uw project te compileren.

1. Vouw in de zijbalk van de verkenner van VS Code de map **contracts** in uw project uit.
1. Klik met de rechtermuisknop op **HelloBlockchain.sol** en kies **Build Contracts** in het menu.

    ![Build Contracts kiezen in het menu ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit maakt gebruik van Truffle om de slimme contracten te compileren.

![Uitvoer van de compiler van Truffle](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>Een slim contract implementeren

Truffle maakt gebruik van migratiescripts om uw contracten te implementeren in een Ethereum-netwerk. Migraties zijn JavaScript-bestanden die zich bevinden in de map **migrations** (migraties) van het project.

1. Als u uw slimme contract wilt implementeren, klikt u met de rechtermuisknop op **HelloBlockchain.sol** en kiest u **Deploy Contracts** (Contracten implementeren) in het menu.
1. Kies uw Azure Blockchain-consortiumnetwerk in het opdrachtpalet. Het Blockchain-consortiumnetwerk is toegevoegd aan het Truffle-configuratiebestand van het project toen u het project maakte.
1. Kies **Generate mnemonic** (Geheugensteuntje genereren). Kies een bestandsnaam en sla het geheugensteuntjebestand op in de projectmap. Bijvoorbeeld `myblockchainmember.env`. Het geheugensteuntjebestand wordt gebruikt voor het genereren van een persoonlijke Ethereum-sleutel voor uw blockchainlid.

Azure Block Chain Development Kit maakt gebruik van Truffle om het migratiescript uit te voeren waarmee de contracten worden geïmplementeerd in de blockchain.

![Geïmplementeerd contract](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>Een contractfunctie aanroepen

Met de functie **SendRequest** van het **HelloBlockchain**-contract wordt de **RequestMessage**-statusvariabele gewijzigd. Het wijzigen van de status van een blockchainnetwerk vindt plaats via een transactie. U kunt de Azure Blockchain Development Kit-pagina voor interactie met slimme contracten gebruiken om de **SendRequest**-functie via een transactie aan te roepen.

1. Als u met uw slimme contract wilt werken, klikt u met de rechtermuisknop op **HelloBlockchain.sol** en kiest u **Show Smart Contract Interaction Page** (pagina voor interactie met slimme contracten weergeven) in het menu.

    ![Pagina voor interactie met slimme contracten weergeven kiezen in het menu](./media/send-transaction/contract-interaction.png)

1. Op de interactiepagina kunt u een geïmplementeerde contractversie kiezen, functies aanroepen, de huidige status weergeven en metagegevens weergeven.

    ![Voorbeeld van pagina voor interactie met slimme contracten](./media/send-transaction/interaction-page.png)

1. Als u een functie voor een slim contract wilt aanroepen, selecteert u de actie voor het contract en geeft u uw argumenten door. Kies de actie **SendRequest** (Aanvraag verzenden) voor het contract en voer **Hello, Blockchain!** in als de parameter **requestMessage**. Selecteer **Uitvoeren** om de functie **SendRequest** aan te roepen via een transactie.

    ![SendRequest-actie uitvoeren](./media/send-transaction/sendrequest-action.png)

Zodra de transactie is verwerkt, worden de statuswijzigingen weergegeven in de interactiesectie.

![Contractstatuswijzigingen](./media/send-transaction/contract-state.png)

Met de functie SendRequest worden de velden **RequestMessage** en **State** (Status) ingesteld. De huidige status voor **RequestMessage** is het argument dat u hebt doorgegeven via **Hello, Blockchain**. De waarde van het veld **State** blijft **Request** (Aanvraag).

## <a name="clean-up-resources"></a>Resources opschonen

Als u ze niet meer nodig hebt, kunt u alle resources die u in de vereiste quickstart *Een blockchainlid maken* hebt gemaakt, verwijderen door de resourcegroep `myResourceGroup` te verwijderen.

Zo verwijdert u de resourcegroep:

1. Ga in de Azure Portal naar **Resourcegroep** in het linkernavigatievenster en selecteer de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Solidity-voorbeeldproject gemaakt met behulp van de Azure Blockchain Development Kit. U hebt een slim contract gemaakt en geïmplementeerd en een functie aangeroepen via een transactie in een Blockchain-consortiumnetwerk dat wordt gehost op Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Blockchaintoepassingen ontwikkelen met behulp van Azure Blockchain Service](develop.md)
