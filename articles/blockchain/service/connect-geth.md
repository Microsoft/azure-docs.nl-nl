---
title: Geth gebruiken om te koppelen aan Azure Blockchain Service
description: Koppelen aan een Geth-instantie op Azure Blockchain Service-transactieknooppunt
ms.date: 05/26/2020
ms.topic: quickstart
ms.reviewer: maheshna
ms.openlocfilehash: 2c5b6a56d96ac132949052e9879c8f61d41ffb06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83994762"
---
# <a name="quickstart-use-geth-to-attach-to-an-azure-blockchain-service-transaction-node"></a>Quickstart: Geth gebruiken om te koppelen aan een Azure Blockchain Service-transactieknooppunt

In deze quickstart gebruikt u de Geth-client om te koppelen aan een Geth-instantie op een transactieknooppunt van Azure Block Chain Service. Als de koppeling tot stand is gebracht, kunt u de Geth-console gebruiken om een Ethereum JavaScript-API aan te roepen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) installeren
* [Quickstart: Een blockchainlid maken met behulp van de Azure-portal](create-member.md) of [Quickstart: Een blockchainlid in Azure Blockchain Service maken met behulp van de Azure CLI voltooien](create-member-cli.md)

## <a name="get-geth-connection-string"></a>Geth-verbindingsreeks ophalen

U kunt de Geth-verbindingsreeks voor een Azure Block Chain Service-transactieknooppunt ophalen in de Azure-portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar het lid van Azure Block Chain Service. Selecteer **Transactieknooppunten** en de koppeling voor het standaardtransactieknooppunt.

    ![Standaardtransactieknooppunt selecteren](./media/connect-geth/transaction-nodes.png)

1. Selecteer **Verbindingsreeksen**.
1. Kopieer de verbindingsreeks bij **HTTPS (toegangssleutel 1)** . U hebt de reeks nodig in de volgende sectie.

    ![Verbindingsreeks](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Verbinding maken met Geth

1. Open een opdrachtprompt of shell.
1. Gebruik de subopdracht attach van Geth om te koppelen aan de actieve Geth-instantie op uw transactieknooppunt. Plak de verbindingsreeks als een argument voor de subopdracht attach. Bijvoorbeeld:

    ``` bash
    geth attach <connection string>
    ```

1. Als er verbinding is met de Ethereum-console van het transactieknooppunt, kunt u de Ethereum JavaScript-API gebruiken.

    Gebruik bijvoorbeeld de volgende API om de chainId op te vragen.

    ``` bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    In dit voorbeeld is de chainId 661.

    ![Azure Blockchain Service-optie](./media/connect-geth/geth-attach.png)

1. Als u de verbinding met de console wilt verbreken, typt u `exit`.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Geth-client gebruikt om te koppelen aan een Geth-instantie op een transactieknooppunt van Azure Block Chain Service. In de volgende zelfstudie gebruikt u de Azure Blockchain Development Kit for Ethereum om een functie voor een slim contract te maken, samenstellen, implementeren en uitvoeren via een transactie.

> [!div class="nextstepaction"]
> [Slimme contracten maken, bouwen en implementeren in Azure Blockchain Service](send-transaction.md)
