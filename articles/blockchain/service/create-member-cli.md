---
title: Een Azure Blockchain Service-lid maken - Azure CLI
description: Maak een Azure Blockchain Service-lid voor een blockchain-consortium met behulp van de Azure CLI.
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3442c3b6023edcde97aabcb13e91120ba6811027
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323070"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Quickstart: Een Azure Blockchain Service-blockchainlid maken met behulp van Azure CLI

In deze quickstart implementeert u een nieuw blockchainlid en -consortium in Azure Blockchain Service met behulp van Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Geen.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.51 of hoger van Azure CLI nodig. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Meld u aan.

    Meld u aan met behulp van de opdracht [az login](/cli/azure/reference-index#az-login) als u een lokale installatie van de CLI gebruikt.

    ```azurecli
    az login
    ```

    Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

1. Installeer de Azure CLI-extensie.

    Wanneer u met extensieverwijzingen voor de Azure-CLI werkt, moet u eerst de extensie installeren.  Azure CLI-extensies geven u toegang tot experimentele opdrachten en opdrachten in een evaluatieversie die nog niet zijn verzonden als onderdeel van de kern-CLI.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies, waaronder het bijwerken en verwijderen ervan.

    Installeer de [extensie voor Azure Blockchain Service](/cli/azure/ext/blockchain/blockchain) door de volgende opdracht uit te voeren:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. Maak een resourcegroep.

    Azure Blockchain Service moet, net zoals alle Azure-resources, in een resourcegroep zijn geïmplementeerd. Met resourcegroepen kunt u gerelateerde Azure-resources organiseren en beheren.

    Maak voor deze quickstart eerst een resourcegroep met de naam _myResourceGroup_ op de locatie _eastus_ met behulp van de volgende [az group create](/cli/azure/group#az-group-create)-opdracht:

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Een lid van de Azure Blockchain Service is een blockchain-knooppunt in een privé-blockchain-netwerk van een consortium. Bij het inrichten van een lid kunt u een consortium-netwerk maken of het lid aan een netwerk toevoegen. U hebt voor een consortium-netwerk ten minste één lid nodig. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortium-deelnemers kunnen een of meer blockchain-leden hebben of ze kunnen leden delen met andere deelnemers. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.

Er zijn verschillende parameters en eigenschappen die u moet doorgeven. Vervang de voorbeeldparameters met uw waarden.

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep waarin Azure Blockchain Service-resources worden gemaakt. Gebruik de resourcegroep die u in de vorige sectie hebt gemaakt.
| **name** | A unieke naam waarmee uw Azure Blockchain Service-blockchainlid wordt geïdentificeerd. De naam wordt gebruikt voor het openbare adres van het eindpunt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`.
| **location** | Azure-regio waarin het blockchainlid wordt gemaakt. Bijvoorbeeld `westus2`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. Functies zijn mogelijk niet in alle regio's beschikbaar. Azure Blockchain Data Manager is beschikbaar in de volgende Azure-regio's: VS - oost en Europa - west.
| **password** | Het wachtwoord voor het standaardtransactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie als u verbinding maakt met het openbare eindpunt van het standaardtransactieknooppunt van het blockchain-lid.
| **protocol** | Het blockchainprotocol. Momenteel wordt het *Quorum*-protocol ondersteund.
| **consortium** | De naam van het consortium waaraan u kunt deelnemen of dat u kunt maken. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums.
| **consortium-management-account-password** | Het wachtwoord voor het consortiumaccount wordt ook wel het lidaccountwachtwoord genoemd. Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums.
| **sku** | Het type servicelaag. *Standard* of *Basic*. Gebruik de categorie *Basic* voor ontwikkeling, tests en het testen van concepten. Gebruik de categorie *Standard* voor implementaties van productiekwaliteit. U moet de categorie *Standaard* ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privétransacties verzendt. Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom.

Het duurt meer dan 10 minuten om het blockchainlid en de ondersteunende resources te maken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt voor de volgende quickstart of zelfstudie het blockchain-lid gebruiken dat u hebt gemaakt. Als u ze niet meer nodig hebt, kunt u alle resources die u voor de snelstart hebt gemaakt, verwijderen door resourcegroep `myResourceGroup` te verwijderen.

Voer de volgende opdracht uit om de resourcegroep en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een lid van de Azure Blockchain Service en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om de Azure Blockchain Development Kit voor Ethereum te gebruiken om een koppeling te maken met een lid van de Azure Block Chain Service.

> [!div class="nextstepaction"]
> [Visual Studio Code gebruiken om verbinding te maken met Azure Blockchain Service](connect-vscode.md)
