---
title: Slimme contracten maken, bouwen en implementeren - Azure Blockchain Service
description: Zelfstudie over het gebruik van de Azure Block Chain Development Kit voor Ethereum-extensie in Visual Studio Code voor het maken, bouwen en implementeren van een slim contract in Azure Blockchain Service.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 4c2df952480d2c30de10838c3d0f7714fc7e6126
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628642"
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
Met de functie **SendRequest** van het **HelloBlockchain**-contract wordt de **RequestMessage**-statusvariabele gewijzigd. Het wijzigen van de status van een blockchainnetwerk vindt plaats via een transactie. U kunt een script maken om de functie **SendRequest** uit te voeren via een trans actie.

1. Maak een nieuw bestand in de hoofdmap van uw Truffle-project en noem het `sendrequest.js` . Voeg de volgende Web3 java script-code toe aan het bestand.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Wanneer Azure Block Chain Development Kit een project maakt, wordt het Truffle-configuratie bestand gegenereerd met de details van uw consortium Block chain-netwerk eindpunt. Open **truffle-config.js** in uw project. In het configuratie bestand worden twee netwerken weer gegeven: een met de naam ontwikkeling en een met dezelfde namen als het consortium.
1. Gebruik in het Terminal venster van VS code Truffle om het script uit te voeren op uw consortium Block chain-netwerk. Selecteer in de menu balk van het Terminal venster het tabblad **Terminal** en **Power shell** in de vervolg keuzelijst.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    Vervang door \<blockchain network\> de naam van het block chain-netwerk dat is gedefinieerd in de **truffle-config.js**.

Truffle voert het script uit op uw Block chain-netwerk.

![De uitvoer met een trans actie is verzonden](./media/send-transaction/execute-transaction.png)

Wanneer u de functie van een contract uitvoert via een trans actie, wordt de trans actie niet verwerkt totdat er een blok is gemaakt. Functies die via een trans actie moeten worden uitgevoerd, retour neren een trans actie-ID in plaats van een retour waarde.

## <a name="query-contract-state"></a>Query contract status

Met de functies van een slimme opdracht kan de huidige waarde van de status variabelen worden geretourneerd. We gaan een functie toevoegen om de waarde van een status variabele te retour neren.

1. Voeg in **HelloBlockchain. Sol** een **GetMessage** -functie toe aan het slimme **HelloBlockchain** -contract.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    De functie retourneert het bericht dat is opgeslagen in een status variabele op basis van de huidige status van het contract.

1. Klik met de rechter muisknop op **HelloBlockchain. Sol** en kies **contracten maken** in het menu om de wijzigingen in het slimme contract te compileren.
1. Als u wilt implementeren, klikt u met de rechter muisknop op **HelloBlockchain. Sol** en kiest u **contracten implementeren** in het menu. Kies uw Azure Block Chain consortium-netwerk in het opdracht palet wanneer u hierom wordt gevraagd.
1. Maak vervolgens een script dat wordt gebruikt om de functie **GetMessage** aan te roepen. Maak een nieuw bestand in de hoofdmap van uw Truffle-project en noem het `getmessage.js` . Voeg de volgende Web3 java script-code toe aan het bestand.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Gebruik in het Terminal venster van VS code Truffle om het script uit te voeren op uw Block chain-netwerk. Selecteer in de menu balk van het Terminal venster het tabblad **Terminal** en **Power shell** in de vervolg keuzelijst.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    Vervang door \<blockchain network\> de naam van het block chain-netwerk dat is gedefinieerd in de **truffle-config.js**.

Het script voert een query uit op het slimme contract door de getMessage-functie aan te roepen. De huidige waarde van de status variabele **RequestMessage** wordt geretourneerd.

![Uitvoer van de GetMessage-query met de huidige waarde van de RequestMessage-status variabele](./media/send-transaction/execute-get.png)

Let op: de waarde is niet **Hello, Block Chain!**. In plaats daarvan is de geretourneerde waarde een tijdelijke aanduiding. Wanneer u het contract wijzigt en implementeert, wordt het gewijzigde contract geïmplementeerd op een nieuw adres en worden de status variabelen toegewezen waarden in de Smart contract-constructor. Het Truffle-voor beeld **2_deploy_contracts.js** migratie script implementeert het slimme contract en geeft als argument een waarde van een tijdelijke aanduiding door. De constructor stelt de **RequestMessage** -status variabele in op de waarde van de tijdelijke aanduiding en dat is wat er wordt geretourneerd.

1. Voer de **sendrequest.js** -en **getmessage.js** -scripts opnieuw uit om de status variabele **RequestMessage** in te stellen en de waarde op te vragen.

    ![Uitvoer van SendRequest-en GetMessage-scripts met RequestMessage is ingesteld](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** stelt de **RequestMessage** -status variabele in op **Hello, Block Chain!** en **getmessage.js** vraagt het contract voor waarde van de variabele **RequestMessage** en retourneert **Hello, Block Chain!**.
## <a name="clean-up-resources"></a>Resources opschonen

Als u ze niet meer nodig hebt, kunt u alle resources die u in de vereiste quickstart *Een blockchainlid maken* hebt gemaakt, verwijderen door de resourcegroep `myResourceGroup` te verwijderen.

Zo verwijdert u de resourcegroep:

1. Ga in de Azure Portal naar **Resourcegroep** in het linkernavigatievenster en selecteer de resourcegroep die u wilt verwijderen.
1. Selecteer **Resourcegroep verwijderen**. Controleer de verwijdering door de naam van de resourcegroep in te voeren en **Verwijderen** te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Solidity-voorbeeldproject gemaakt met behulp van de Azure Blockchain Development Kit. U hebt een slim contract gemaakt en geïmplementeerd en een functie aangeroepen via een transactie in een Blockchain-consortiumnetwerk dat wordt gehost op Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Blockchaintoepassingen ontwikkelen met behulp van Azure Blockchain Service](develop.md)
