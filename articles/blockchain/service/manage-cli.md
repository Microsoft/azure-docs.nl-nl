---
title: Azure Blockchain Service beheren met behulp van Azure CLI
description: Een Azure Blockchain Service beheren met Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 55df56274aa5baa946b60c27cf49723d59c928a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865923"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Azure Blockchain Service beheren met behulp van Azure CLI

Naast de Azure Portal kunt u Azure CLI gebruiken voor het beheren van blockchain-leden en transactieknooppunten voor uw Azure Blockchain Service.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Zie Azure CLI installeren als u liever de CLI lokaal installeert [en gebruikt.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

1. Meld u aan.

    Meld u aan met behulp van de opdracht [az login](/cli/azure/reference-index#az_login) als u een lokale installatie van de CLI gebruikt.

    ```azurecli
    az login
    ```

    Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

1. Installeer de Azure CLI-extensie.

    Wanneer u met extensieverwijzingen voor de Azure-CLI werkt, moet u eerst de extensie installeren.  Azure CLI-extensies geven u toegang tot experimentele opdrachten en opdrachten in een evaluatieversie die nog niet zijn verzonden als onderdeel van de kern-CLI.  Zie [Extensies gebruiken met Azure CLI](/cli/azure/azure-cli-extensions-overview) voor meer informatie over extensies, waaronder het bijwerken en verwijderen ervan.

    Installeer de [extensie voor Azure Blockchain Service](/cli/azure/blockchain) door de volgende opdracht uit te voeren:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Blockchain-lid maken

Voorbeeld: [hiermee maakt u een blockchain-lid](/cli/azure/blockchain/member#az_blockchain_member_create) in Azure Blockchain Service het quorum-grootboekprotocol in een nieuw consortium wordt uitgevoerd.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep waarin Azure Blockchain Service-resources worden gemaakt. |
| **name** | A unieke naam waarmee uw Azure Blockchain Service-blockchainlid wordt geïdentificeerd. De naam wordt gebruikt voor het openbare adres van het eindpunt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`. |
| **location** | Azure-regio waarin het blockchainlid wordt gemaakt. Bijvoorbeeld `eastus`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt. Functies zijn mogelijk niet in alle regio's beschikbaar. |
| **password** | Het wachtwoord voor het standaardtransactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie als u verbinding maakt met het openbare eindpunt van het standaardtransactieknooppunt van het blockchain-lid. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine letters, 1 hoofdletter, 1 getal en 1 speciaal teken zijn dat geen getalteken(#), procent(%) komma(,), star(*), back quote( \` ), double quote("), single quote('), dash(-) en semicolumn(;)|
| **protocol** | Het blockchainprotocol. Momenteel wordt het *Quorum*-protocol ondersteund. |
| **consortium** | De naam van het consortium waaraan u kunt deelnemen of dat u kunt maken. Zie [Azure Blockchain Service-consortium](consortium.md) voor meer informatie over consortiums. |
| **consortium-management-account-password** | Het wachtwoord voor het consortiumaccount wordt ook wel het lidaccountwachtwoord genoemd. Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums. |
| **sku** | Het type servicelaag. *Standard* of *Basic*. Gebruik de categorie *Basic* voor ontwikkeling, tests en het testen van concepten. Gebruik de categorie *Standard* voor implementaties van productiekwaliteit. U moet de categorie *Standard* ook gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privé transacties verzendt. Wanneer een lid is gemaakt, kan de prijscategorie niet meer worden gewijzigd van Basic in Standard en andersom. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Wachtwoorden van blockchain-leden of firewallregels wijzigen

In [dit voorbeeld worden het wachtwoord, het](/cli/azure/blockchain/member#az_blockchain_member_update)wachtwoord voor consortiumbeheer en de firewallregel van een blockchain-lid bijgewerkt.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep waarin Azure Blockchain Service-resources worden gemaakt. |
| **name** | Naam die uw Azure Blockchain Service identificeert. |
| **password** | Het wachtwoord voor het standaardtransactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie als u verbinding maakt met het openbare eindpunt van het standaardtransactieknooppunt van het blockchain-lid. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine letters, 1 hoofdletter, 1 getal en 1 speciaal teken zijn dat geen getalteken(#), procent(%) komma(,), star(*), back quote( \` ), double quote("), single quote('), dash(-) en semicolumn(;)|
| **consortium-management-account-password** | Het wachtwoord voor het consortiumaccount wordt ook wel het lidaccountwachtwoord genoemd. Het wachtwoord van het lidaccount wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor het lid wordt gemaakt. U gebruikt het lidaccount en het wachtwoord van het lidaccount voor het beheer van consortiums. |
| **firewallregels** | Lijst met toegestane IP-adressen voor begin- en eind-IP-adressen. |

## <a name="create-transaction-node"></a>Transactie-knooppunt maken

[Maak een transactie-knooppunt](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_create) binnen een bestaand blockchainlid. Door transactieknooppunten toe te voegen, kunt u de beveiligingsisolatie verhogen en de belasting verdelen. U kunt bijvoorbeeld een transactie-knooppunt-eindpunt hebben voor verschillende clienttoepassingen.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep waarin Azure Blockchain Service-resources worden gemaakt. |
| **location** | Azure-regio van het blockchainlid. |
| **member-name** | Naam die uw Azure Blockchain Service identificeert. |
| **password** | Het wachtwoord voor het transactie-knooppunt. Gebruik het wachtwoord voor basisverificatie wanneer u verbinding maakt met het openbare eindpunt van het transactie-knooppunt. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine letters, 1 hoofdletter, 1 getal en 1 speciaal teken dat geen nummerteken(#), procent(%) komma(,), ster(*), back quote( \` ), double quote("), single quote('), dash(-) en semicolumn(;)|
| **name** | Naam van transactie-knooppunt. |

## <a name="change-transaction-node-password"></a>Wachtwoord voor transactie-knooppunt wijzigen

Voorbeeld [werkt een wachtwoord voor een transactie-knooppunt](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_update) bij.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep Azure Blockchain Service resources bestaan. |
| **member-name** | Naam die uw Azure Blockchain Service identificeert. |
| **password** | Het wachtwoord voor het transactie-knooppunt. Gebruik het wachtwoord voor basisverificatie wanneer u verbinding maakt met het openbare eindpunt van het transactie-knooppunt. Het wachtwoord moet voldoen aan drie van de volgende vier vereisten: de lengte moet tussen 12 & 72 tekens, 1 kleine letters, 1 hoofdletter, 1 getal en 1 speciaal teken dat geen nummerteken(#), procent(%) komma(,), ster(*), back quote( \` ), double quote("), single quote('), dash(-) en semicolumn(;)|
| **name** | Naam van transactie-knooppunt. |

## <a name="list-api-keys"></a>API-sleutels opslijst

API-sleutels kunnen worden gebruikt voor knooppunttoegang die vergelijkbaar is met de gebruikersnaam en het wachtwoord. Er zijn twee API-sleutels ter ondersteuning van sleutelrotatie. Gebruik de volgende opdracht om uw [API-sleutels weer te geven.](/cli/azure/blockchain/member#az_blockchain_transaction_node_list-api-key)

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep Azure Blockchain Service resources bestaan. |
| **name** | Naam van het Azure Blockchain Service blockchainlid |

## <a name="regenerate-api-keys"></a>API-sleutels opnieuw maken

Gebruik de volgende opdracht om [uw API-sleutels opnieuw te maken.](/cli/azure/blockchain/member#az_blockchain_transaction_node_regenerate-api-key)

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep Azure Blockchain Service resources bestaan. |
| **name** | Naam van het Azure Blockchain Service blockchainlid. |
| **keyName** | Vervang \<keyValue\> door key1, key2 of beide. |

## <a name="delete-a-transaction-node"></a>Een transactie-knooppunt verwijderen

Voorbeeld: [hiermee verwijdert u een blockchain-lidtransactie-knooppunt](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep Azure Blockchain Service resources bestaan. |
| **member-name** | Naam van het Azure Blockchain Service blockchainlid dat ook de naam van het transactie-knooppunt bevat dat moet worden verwijderd. |
| **name** | Naam van transactie-knooppunt dat moet worden verwijderd. |

## <a name="delete-a-blockchain-member"></a>Een blockchain-lid verwijderen

In [dit voorbeeld wordt een blockchain-lid verwijderd.](/cli/azure/blockchain/member#az_blockchain_member_delete)

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-group** | De naam van de resourcegroep Azure Blockchain Service resources bestaan. |
| **name** | Naam van het Azure Blockchain Service blockchainlid dat moet worden verwijderd. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Toegang verlenen aan Azure AD-gebruiker

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parameter | Beschrijving |
|---------|-------------|
| **Role** | Naam van de Azure AD-rol. |
| **Gevolmachtigde** | Azure AD-gebruikers-id. Bijvoorbeeld: `user@contoso.com` |
| **Scope** | Bereik van de roltoewijzing. Kan een blockchain-lid of transactie-knooppunt zijn. |

**Voorbeeld:**

Verleen een Azure AD-gebruiker toegang tot knooppunttoegang tot **blockchain-lid:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Voorbeeld:**

Knooppunttoegang verlenen aan Azure AD-gebruiker voor **blockchaintransactie-knooppunt:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Knooppunttoegang verlenen voor Azure AD-groep of -toepassingsrol

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parameter | Beschrijving |
|---------|-------------|
| **Role** | Naam van de Azure AD-rol. |
| **assignee-object-id** | Azure AD-groeps-id of toepassings-id. |
| **Scope** | Bereik van de roltoewijzing. Kan een blockchain-lid of transactie-knooppunt zijn. |

**Voorbeeld:**

Knooppunttoegang verlenen voor **toepassingsrol**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Toegang tot Azure AD-knooppunt verwijderen

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parameter | Beschrijving |
|---------|-------------|
| **Role** | Naam van de Azure AD-rol. |
| **Gevolmachtigde** | Azure AD-gebruikers-id. Bijvoorbeeld: `user@contoso.com` |
| **Scope** | Bereik van de roltoewijzing. Kan een blockchain-lid of transactie-knooppunt zijn. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren Azure Blockchain Service transactieknooppunten met de Azure Portal](configure-transaction-nodes.md).
