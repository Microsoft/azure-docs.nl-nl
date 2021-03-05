---
title: 'Zelfstudie: Azure Key Vault gebruiken met een virtuele machine in Python | Microsoft Docs'
description: In deze zelfstudie configureert u een virtuele machine en een Python-toepassing om een geheim uit uw sleutelkluis te lezen.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2fc77d0cdfb6bd8a62555951c0b6dc7e9b732f93
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203535"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Zelfstudie: Azure Key Vault gebruiken met een virtuele machine in Python

Met Azure Key Vault kunt u sleutels, geheimen en certificaten beveiligen, zoals API-sleutels en databaseverbindingsreeksen.

In deze zelfstudie configureert u een Python-toepassing voor het lezen van gegevens uit Azure Key Vault met behulp van beheerde identiteiten voor Azure-resources. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een sleutelkluis maken
> * Een geheim opslaan in Key Vault
> * Een virtuele Linux-machine maken
> * Een [Beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) inschakelen voor de virtuele machine
> * De vereiste machtigingen verlenen aan de consoletoepassing om gegevens te lezen uit Key Vault
> * Een geheim lezen uit Key Vault

Lees voordat u verdergaat eerst [Basisconcepten van Key Vault](basic-concepts.md). 

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

## <a name="prerequisites"></a>Vereisten

Voor Windows, Mac en Linux:
  * [Git](https://git-scm.com/downloads)
  * Voor deze zelfstudie moet u de Azure CLI lokaal uitvoeren. Azure CLI versie 2.0.4 of hoger moet geïnstalleerd zijn. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u zich bij Azure wilt aanmelden met de Azure CLI, voert u het volgende in:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Vul uw sleutelkluis in met een geheim

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Gebruik een van de volgende methoden om een VM met de naam **myVM** te maken:

| Linux | Windows |
|--|--|
| [Azure-CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure-CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure-portal](../../virtual-machines/linux/quick-create-portal.md) | [Azure Portal](../../virtual-machines/windows/quick-create-portal.md) |

Als u een Linux-VM wilt maken met behulp van de Azure CLI, gebruikt u de opdracht [az vm create](/cli/azure/vm).  In het volgende voorbeeld wordt een gebruikersaccount met de naam *azureuser* toegevoegd. De parameter `--generate-ssh-keys` wordt gebruikt om automatisch een SSH-sleutel te genereren en deze te plaatsen in de standaardsleutellocatie ( *~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Noteer de waarde van `publicIpAddress` in de uitvoer.

## <a name="assign-an-identity-to-the-vm"></a>Een identiteit toewijzen aan de virtuele machine

Maak een door het systeem toegewezen identiteit voor de virtuele machine met de opdracht [az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign) van de Azure CLI:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Bekijk de door het systeem toegewezen identiteit die wordt weergegeven in de volgende code. De uitvoer van de vorige opdracht ziet er als volgt uit: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Machtigingen toewijzen aan de VM-identiteit

U kunt nu de eerder gemaakte identiteitsmachtigingen toewijzen aan uw sleutelkluis door de volgende opdracht uit te voeren:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Aanmelden bij de nieuwe virtuele machine

Als u zich wilt aanmelden bij de virtuele machine, volgt u de instructies in [Verbinding maken met en aanmelden bij een virtuele Azure-machine met Linux](../../virtual-machines/linux/login-using-aad.md) of [Verbinding maken en aanmelden bij een virtuele Azure-machine met Windows](../../virtual-machines/windows/connect-logon.md).


Als u zich wilt aanmelden bij een Linux-VM, kunt u de opdracht ssh gebruiken met het <publicIpAddress> dat is opgegeven in de stap [Een virtuele machine maken](#create-a-virtual-machine):

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Python-bibliotheken installeren op de VM

Installeer op de virtuele machine de twee Python-bibliotheken die we gaan gebruiken in het Python-script: `azure-keyvault-secrets` en `azure.identity`.  

Op een Linux-VM kunt u deze bijvoorbeeld installeren met behulp van `pip3`:

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Het Python-voorbeeldscript maken en bewerken

Maak op de virtuele machine een Python-bestand met de naam **sample.py**. Bewerk het bestand zodat het de volgende code bevat, waarbij u <your-unique-keyvault-name> vervangt door de naam van uw sleutelkluis:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>De Python-voorbeeld-app uitvoeren

Voer ten slotte **sample.py** uit. Als alles goed is, moet de waarde van uw geheim worden geretourneerd:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Resources opschonen

Als ze niet meer nodig zijn, verwijdert u de virtuele machine en de sleutelkluis.  U kunt dit snel doen door simpelweg de resource groep te verwijderen waarvan ze deel uitmaken:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

[REST-API van Azure Key Vault](/rest/api/keyvault/)