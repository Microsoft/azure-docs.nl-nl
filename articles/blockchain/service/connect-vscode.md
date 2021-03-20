---
title: Visual Studio Code gebruiken om verbinding te maken met Azure Blockchain Service
description: Verbinding maken met een Azure Block Chain Service-consortiumnetwerk met behulp van de Azure Block Chain Development Kit for Ethereum-extensie in Visual Studio Code
ms.date: 12/04/2020
ms.topic: quickstart
ms.reviewer: caleteet
ms.openlocfilehash: 6e94d93d91f25c15743c4c467e31de49fd9da41d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763295"
---
# <a name="quickstart-use-visual-studio-code-to-connect-to-an-azure-blockchain-service-consortium-network"></a>Snelstart: Visual Studio Code gebruiken om verbinding te maken met een Azure Blockchain Service-consortiumnetwerk

In deze quickstart installeert en gebruikt u de extensie Azure Block Chain Development Kit for Ethereum Visual Studio Code (VS code)-extensie om een consortium te koppelen via de Azure Block Chain-service. De Azure Block Chain Development Kit vereenvoudigt het maken, verbinden, bouwen en implementeren van slimme contracten voor Ethereum Block Chain-grootboeken.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Quickstart: Een blockchainlid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: Een blockchainlid in Azure Blockchain Service maken met behulp van de Azure CLI voltooien](create-member-cli.md)
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit voor Ethereum-extensie](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x of hoger](https://nodejs.org)
* [Git 2.10.x of hoger](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

In Windows is een geïnstalleerde C++-compiler vereist voor de module node-gyp. U kunt de MSBuild-hulpprogramma's gebruiken:

* Als Visual Studio 2017 is geïnstalleerd, configureert u npm voor het gebruik van de MSBuild-hulpprogramma's met de opdracht `npm config set msvs_version 2017 -g`
* Als Visual Studio 2019 is geïnstalleerd, stelt u het pad voor MSBuild-hulpprogramma's in voor npm. Bijvoorbeeld: `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* Anders installeert u de zelfstandige build-hulpprogramma's van Visual Studio met behulp van `npm install --global windows-build-tools` in een *Als administrator uitvoeren*-opdrachtshell met verhoogde bevoegdheden.

Zie de [opslagplaats node-gyp op GitHub](https://github.com/nodejs/node-gyp) voor meer informatie over node-gyp.

### <a name="verify-azure-blockchain-development-kit-environment"></a>De Azure Block Chain Development Kit-omgeving controleren

De Azure Block Chain Development Kit controleert of uw ontwikkelomgevind aan de vereisten voldoet. De ontwikkelomgeving controleren:

Kies in het opdrachtpalet van VS Code **Blockchain: Welkomstpagina weergeven**.

Azure Block Chain Development Kit voert een validatiescript uit dat ongeveer een minuut in beslag neemt. U kunt de uitvoer weergeven door **Terminal > Nieuwe terminal** te selecteren. Selecteer in de menubalk van de Terminal het tabblad **Uitvoer** en **Azure Block Chain** in de vervolgkeuzelijst. In de volgende afbeelding ziet u hoe een geslaagde validatie eruit ziet:

![Geldige ontwikkelomgeving](./media/connect-vscode/valid-environment.png)

 Als er een vereist hulpprogramma ontbreekt, wordt er in een nieuw tabblad met de naam **Azure Block Chain Development Kit - Preview** een lijst met de vereiste hulpprogramma's met downloadkoppelingen weer gegeven.

![Vereiste apps voor ontwikkelkit](./media/connect-vscode/required-apps.png)

Installeer de ontbrekende vereiste onderdelen voordat u doorgaat met de quickstart.

## <a name="connect-to-consortium-member"></a>Verbinding maken met consortiumleden

U kunt verbinding maken met consortiumleden met behulp van de Azure Block Chain Development Kit VS Code-extensie. Wanneer u bent verbonden met een consortium, kunt u slimme contracten compileren, bouwen en implementeren voor een Azure Block Chain Service-consortiumlid.

Als u geen toegang hebt tot een Azure Block Chain Service-consortiumlid, voltooit u de vereiste [Quickstart: Een blockchainlid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: Een Azure Blockchain Service-blockchainlid maken met behulp van Azure CLI](create-member-cli.md).

1. Vouw in het verkennervenster VS Code de **Azure Block Chain**-extensie uit.
1. Selecteer **Verbinding maken met netwerk**.

   ![Verbinding maken met netwerk](./media/connect-vscode/connect-consortium.png)

    Als u wordt gevraagd om Azure-verificatie, volgt u de prompts voor verificatie met een browser.
1. Kies in de vervolgkeuzelijst van het opdrachtpalet **Azure Block Chain Service** .
1. Kies het abonnement en de resourcegroep die aan uw Azure Block Chain Service-consortiumlid zijn gekoppeld.
1. Kies uw consortium in de lijst.

De consortium- en blockchainleden worden weergegeven in de VS Code-verkennerbalk.

![Consortium in verkenner](./media/connect-vscode/consortium-node.png)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Azure Block Chain Development Kit for Ethereum VS code-extensie gebruikt om een consortium te koppelen via de Azure Block Chain-service. In de volgende zelfstudie gebruikt u de Azure Blockchain Development Kit for Ethereum om een functie voor een slim contract te maken, samenstellen, implementeren en uitvoeren via een transactie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in Azure Blockchain Service](send-transaction.md)