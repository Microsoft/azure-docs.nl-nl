---
title: 'Quickstart: Azure Key Vault Python-clientbibliotheek - sleutels beheren'
description: Meer informatie over het maken, ophalen en verwijderen van sleutels van een Azure-sleutelkluis met behulp van de clientbibliotheek voor Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 7d1e4018382d26e4df289838ffbb03f1c87505e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935016"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Quickstart: Azure Key Vault-sleutelclientbibliotheek voor Python

Aan de slag met de Azure Key Vault-clientbibliotheek voor Python. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Door Key Vault te gebruiken om cryptografische sleutels op te slaan, voorkomt u dat dergelijke sleutels in uw code worden opgeslagen en is uw app dus beter beveiligd.

[API-referentiedocumentatie](/python/api/overview/azure/keyvault-keys-readme) | [Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Pakket (Python Package Index)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- [Python 2.7+ of 3.5.3+](/azure/developer/python/configure-local-development-environment)
- [Azure-CLI](/cli/azure/install-azure-cli)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli) uitvoert in een Linux-terminalvenster.

## <a name="set-up-your-local-environment"></a>Uw lokale omgeving instellen

Deze quickstart maakt gebruik van de Azure Identity-bibliotheek met Azure CLI om de gebruiker te verifiëren bij Azure-services. Ontwikkelaars kunnen ook Visual Studio of Visual Studio Code gebruiken om hun oproepen te verifiëren: zie [De client verifiëren met de Azure Identity-clientbibliotheek](/java/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Voer de opdracht `login` uit.

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

2. Meldt u zich in de browser aan met uw accountreferenties.

### <a name="install-the-packages"></a>De pakketten installeren

1. In een terminal- of opdrachtprompt maakt u een geschikte projectmap en maakt en activeert u een virtuele Python-omgeving, zoals wordt beschreven in [Virtuele Python-omgevingen gebruiken](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Installeer Azure Active Directory Identity-bibliotheek:

    ```terminal
    pip install azure.identity
    ```


1. Installeer de Key Vault-clientbibliotheek:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak een toegangsbeleid voor de sleutelkluis waarmee geheime machtigingen aan uw gebruikersaccount worden verleend.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

In deze applicatie wordt de naam van de sleutelkluis gebruikt als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS of Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>De voorbeeldcode maken

Met de Azure Key Vault-clientbibliotheek met sleutels voor Python kunt u cryptografische sleutels beheren. Het volgende codevoorbeeld laat zien hoe u een client maakt, een sleutel instelt, een sleutel ophaalt en een sleutel verwijdert.

Maak een bestand met de naam *kv_keys.py* dat deze code bevat.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>De code uitvoeren

Zorg ervoor dat de code in de vorige sectie in een bestand met de naam *kv_keys.py* staat. Voer de code vervolgens uit met de volgende opdracht:

```terminal
python kv_keys.py
```

- Als er machtigingsfouten optreden, controleer dan of u de [`az keyvault set-policy`-opdracht](#grant-access-to-your-key-vault) hebt uitgevoerd.
- Als u de code opnieuw uitvoert met dezelfde sleutelnaam, treedt mogelijk deze fout op: "(Conflict) Sleutel <name> heeft momenteel de status Verwijderd maar herstelbaar." Gebruik een andere sleutelnaam.

## <a name="code-details"></a>Codedetails

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/python/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie. 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Een sleutel opslaan

Zodra u het clientobject voor de sleutelkluis hebt opgehaald, kunt u een sleutel opslaan met behulp van de methode [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-): 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

U kunt ook [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) of [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-) gebruiken.

Wanneer een `create`-methode wordt aangeroepen, wordt er een aanroep bij de Azure REST-API voor de sleutelkluis gegenereerd.

Bij het verwerken van de aanvraag verifieert Azure de identiteit van de aanroepende functie (de service-principal) met behulp van het referentieobject dat u aan de client hebt verstrekt.

## <a name="retrieve-a-key"></a>Een sleutel ophalen

Om een sleutel uit Key Vault te lezen, gebruikt u de methode [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-):

```python
retrieved_key = client.get_key(keyName)
 ```

U kunt ook verifiëren dat de sleutel is ingesteld met de Azure CLI-opdracht [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show).

### <a name="delete-a-key"></a>Een sleutel verwijderen

Om een sleutel te verwijderen, gebruikt u de methode [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-):

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

De methode `begin_delete_key` is asynchroon en retourneert een pollerobject. Wanneer de methode `result` van de poller wordt aangeroepen, wordt er gewacht totdat deze voltooid is.

U kunt verifiëren dat de sleutel is verwijderd met de Azure CLI-opdracht [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show).

Zodra een sleutel is verwijderd, heeft deze nog een tijdje de status Verwijderd maar herstelbaar. Gebruik een andere sleutelnaam als u de code nogmaals uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ook met [certificaten](../certificates/quick-create-python.md) en [geheimen](../secrets/quick-create-python.md) wilt experimenteren, kunt u de Key Vault hergebruiken die in dit artikel is gemaakt.

Anders, wanneer u klaar bent met de resources die in dit artikel zijn gemaakt, gebruikt u de volgende opdracht om de resourcegroep en alle resources daarin te verwijderen:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Key Vault](../general/overview.md)
- [Veilige toegang tot een sleutelkluis](../general/secure-your-key-vault.md)
- [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- [Overzicht van Key Vault-beveiliging](../general/security-overview.md)
- [Verifiëren met Key Vault](../general/authentication.md)