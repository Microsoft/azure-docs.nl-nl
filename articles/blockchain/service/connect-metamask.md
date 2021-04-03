---
title: MetaMask verbinden met een Azure Blockchain Service-netwerk
description: Maak verbinding met een Azure Block Chain Service-netwerk met behulp van MetaMask en implementeer een slim contract.
ms.date: 09/12/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 4a45e02a861ff20a4dc774668a4e008f9b42aeea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530434"
---
# <a name="quickstart-use-metamask-to-connect-and-deploy-a-smart-contract"></a>MetaMask gebruiken om verbinding te maken en een slim contract te implementeren

In deze quickstart gebruikt u MetaMask om verbinding te maken met een Azure Blockchain Service-netwerk, en gebruikt u Remix om een slim contract te implementeren. MetaMask is een browseruitbreiding voor het beheren van een Ether-wallet en het uitvoeren van acties voor slimme contracten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Quickstart: Een blockchainlid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: Een blockchainlid in Azure Blockchain Service maken met behulp van de Azure CLI voltooien](create-member-cli.md)
* [MetaMask-browseruitbreiding](https://metamask.io) installeren
* Een MetaMask-[wallet](https://metamask.zendesk.com/hc/en-us/articles/360015488971-New-to-MetaMask-Learn-How-to-Setup-MetaMask-the-First-Time) genereren

## <a name="get-endpoint-address"></a>Eindpuntadres ophalen

U hebt het Azure Blockchain Service-eindpuntadres nodig om verbinding te maken met het blockchainnetwerk. Het eindpuntadres en de toegangssleutels bevinden zich in de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Navigeer naar het lid van Azure Block Chain Service.
1. Selecteer **Transactieknooppunten** en de koppeling voor het standaardtransactieknooppunt.

    ![Standaardtransactieknooppunt selecteren](./media/connect-metamask/transaction-nodes.png)

1. Selecteer **Verbindingsreeksen > Toegangssleutels**.
1. Kopieer het eindpuntadres van **HTTPS (toegangssleutel 1)**.

    ![Verbindingsreeks](./media/connect-metamask/connection-string.png)

## <a name="connect-metamask"></a>MetaMask verbinden

1. Open de MetaMask-browseruitbreiding en meld u aan.
1. Selecteer in de netwerkvervolgkeuzelijst **Custom RPC** (aangepaste RPC).

    ![Aangepaste RPC](./media/connect-metamask/custom-rpc.png)

1. Plak het eindpuntadres dat u hierboven hebt gekopieerd in **New Network > New RPC URL** (Nieuw netwerk > Nieuwe RPC-URL).
1. Selecteer **Opslaan**.

    Als de verbinding is geslaagd, wordt het privénetwerk weergegeven in de netwerkvervolgkeuzelijst.

    ![Nieuw netwerk](./media/connect-metamask/new-network.png)

## <a name="deploy-smart-contract"></a>Slim contract implementeren

Remix is een browserontwikkelingsomgeving voor Solidity. Door MetaMask en Remix samen te gebruiken, kunt u slimme contracten implementeren en daar acties op uitvoeren.

1. Ga in de browser naar `https://remix.ethereum.org`.
1. Selecteer **New file** (Nieuw bestand) op het tabblad **Home** (Start) onder **File** (Bestand).

    Geef het nieuwe bestand de naam `simple.sol`.

    ![Bestand maken](./media/connect-metamask/create-file.png)

    Selecteer **OK**.
1. Plak in de Remix-editor de volgende code voor een **eenvoudig slim contract**.

    ```solidity
    pragma solidity ^0.5.0;
             
    contract simple {
        uint balance;
                 
        constructor() public{
            balance = 0;
        }
                 
        function add(uint _num) public {
            balance += _num;
        }
                 
        function get() public view returns (uint){
            return balance;
        }
    }
    ```

    In het **eenvoudige contract** wordt een statusvariabele met de naam **balance** (saldo) gedeclareerd. Er worden twee functies gedefinieerd. De functie **add** (optellen) telt een getal op bij **balance**. De functie **get** (ophalen) retourneert de waarde van **balance**.
1. Selecteer om het contract te compileren eerst het compilervenster van Solidity en selecteer vervolgens **Compile simple.sol**.

    ![Schermopname met een contract dat wordt gecompileerd.](./media/connect-metamask/compile.png)

1. Selecteer het deelvenster **Deploy & Run** (Implementeren en uitvoeren) en stel vervolgens de **Environment** (Omgeving) in op **Injected Web3** om via MetaMask verbinding te maken met het blockchainlid.

    ![Tabblad Run (Uitvoeren)](./media/connect-metamask/injected-web3.png)

1. Selecteer het **simple** contract en vervolgens **Deploy** (Implementeren).

    ![Schermopname van het implementeren en uitvoeren van transacties met een geselecteerd contract, en Deploy geselecteerd.](./media/connect-metamask/deploy.png)


1. Een bericht van MetaMask waarschuwt u dat u onvoldoende middelen hebt om de transactie uit te voeren.

    Voor een openbaar blockchainnetwerk zou u Ether moeten hebben om de transactiekosten te betalen. Aangezien dit een privénetwerk in een consortium is, kunt u de benzineprijs instellen op nul.

1.  Selecteer **Gas Fee > Edit > Advanced** (Benzinekosten > Bewerken > Geavanceerd) en stel de **Gas Price** (Benzineprijs) in op 0.

    ![Benzineprijs](./media/connect-metamask/gas-price.png)

    Selecteer **Opslaan**.

1. Selecteer **Confirm** (Bevestigen) om het slimme contract te implementeren op de blockchain.
1. Vouw in de sectie **Deployed Contracts** (Geïmplementeerde contracten) het **simple** contract uit.

    ![Geïmplementeerd contract](./media/connect-metamask/deployed-contract.png)

    Twee acties, **add** (optellen) en **get** (ophalen), komen overeen met de functies die in het contract zijn gedefinieerd.

1. Om een **add**-transactie uit te voeren op de blockchain voert u een op te tellen getal in en selecteert u **add**. Mogelijk krijgt u van Remix een foutmelding voor de benzineschatting: "You are sending the transaction to a private blockchain that does not require gas" (U stuurt de transactie naar een privéblockchain waarvoor geen benzine vereist is). Selecteer **Send transaction** (Transactie verzenden) om de transactie af te dwingen.
1. Net als toen u het contract implementeerde, waarschuwt een bericht van MetaMask u dat u onvoldoende middelen hebt om de transactie uit te voeren.

    Aangezien dit een privénetwerk in een consortium is, kunnen we de benzineprijs instellen op nul.

1. Selecteer **Gas Fee > Edit > Advanced** (Benzinekosten > Bewerken > Geavanceerd) en stel de **Gas Price** (Benzineprijs) in op 0 en selecteer **Save** (Opslaan).
1. Selecteer **Confirm** (Bevestigen) om de transactie naar de blockchain te verzenden.
1. Selecteer de actie **get** (ophalen). Dit is een aanroep voor het opvragen van knooppuntgegevens. Er is geen transactie nodig.

Het foutopsporingsvenster van Remix laat details zien over de transacties op de blockchain:

![Geschiedenis van foutopsporing](./media/connect-metamask/debug.png)

U ziet het maken van het contract **simple**, de transactie voor **simple.add** en de aanroep van **simple.get**.

Als u de transactiegeschiedenis in MetaMask wilt zien, opent u de MetaMask-browseruitbreiding en zoekt u in de sectie **History** (Geschiedenis) naar een logboek van het geïmplementeerde contract en de transacties.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de browseruitbreiding MetaMask gebruikt om verbinding te maken met een transactieknooppunt in Azure Blockchain Service, een slim contract te implementeren en een transactie naar de blockchain te verzenden. In de volgende zelfstudie gebruikt u de Azure Blockchain Development Kit voor Ethereum en Truffle om een functie voor een slim contract te maken, samen te stellen, te implementeren en uit te voeren via een transactie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in Azure Blockchain Service](send-transaction.md)