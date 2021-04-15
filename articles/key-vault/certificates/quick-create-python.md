---
title: 'Quickstart: Azure Key Vault Python-clientbibliotheek - certificaten beheren'
description: Meer informatie over het maken, ophalen en verwijderen van certificaten van een Azure-sleutelkluis met behulp van de clientbibliotheek voor Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b8f36d7490e24247f1f8482c9dece86b68015d73
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376100"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Quickstart: Azure Key Vault-clientbibliotheek met certificaten voor Python

Ga aan de slag met de Azure Key Vault-clientbibliotheek met certificaten voor Python. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Door Key Vault te gebruiken om certificaten op te slaan, voorkomt u dat certificaten in uw code worden opgeslagen en is uw app dus beter beveiligd.

[API-referentiedocumentatie](/python/api/overview/azure/keyvault-certificates-readme) | [Broncode van de bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Pakket (Python Package Index)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- [Python 2.7+ of 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure-CLI](/cli/azure/install-azure-cli)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli) uitvoert in een Linux-terminalvenster.

## <a name="set-up-your-local-environment"></a>Uw lokale omgeving instellen

Deze quickstart maakt gebruik van de Azure Identity-bibliotheek met Azure CLI om de gebruiker te verifiëren bij Azure-services. Ontwikkelaars kunnen ook Visual Studio of Visual Studio Code gebruiken om hun oproepen te verifiëren: zie [De client verifiëren met de Azure Identity-clientbibliotheek](/python/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Voer de opdracht `login` uit.

    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

    Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

2. Meldt u zich in de browser aan met uw accountreferenties.

### <a name="install-the-packages"></a>De pakketten installeren

1. In een terminal- of opdrachtprompt maakt u een geschikte projectmap en maakt en activeert u een virtuele Python-omgeving, zoals wordt beschreven in [Virtuele Python-omgevingen gebruiken](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installeer Azure Active Directory Identity-bibliotheek:

    ```terminal
    pip install azure.identity
    ```


1. Installeer de Key Vault-clientbibliotheek met certificaten:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis

Maak een toegangsbeleid voor de sleutelkluis waarmee certificaatmachtigingen aan uw gebruikersaccount worden verleend

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
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

Met de Azure Key Vault-clientbibliotheek met certifcaten voor Python kunt u certificaten beheren. In de onderstaande codevoorbeelden ziet u hoe u een client maakt en een certificaat instelt, ophaalt en verwijdert.

Maak een bestand met de naam *kv_certificates.py* dat deze code bevat.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>De code uitvoeren

Zorg ervoor dat de code in de vorige sectie in een bestand met de naam *kv_certificates.py* staat. Voer de code vervolgens uit met de volgende opdracht:

```terminal
python kv_certificates.py
```

- Als er machtigingsfouten optreden, controleer dan of u de [`az keyvault set-policy`-opdracht](#grant-access-to-your-key-vault) hebt uitgevoerd.
- Als u de code opnieuw uitvoert met dezelfde sleutelnaam, treedt mogelijk deze fout op: '(Conflict) Certificaat <name> heeft momenteel de status Verwijderd maar herstelbaar.' Gebruik een andere sleutelnaam.

## <a name="code-details"></a>Codedetails

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

In deze quickstart wordt de aangemelde gebruiker gebruikt voor de verificatie bij de sleutelkluis. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet beheerde identiteit worden toegewezen aan App Service of aan Virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/python/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie. 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Een certificaat opslaan

Zodra u het clientobject voor de sleutelkluis hebt verkregen, kunt u een certificaat maken met behulp van de [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-)-methode: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Hier vereist het certificaat een beleid dat is verkregen met behulp van de [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--)-methode.

Wanneer een `begin_create_certificate`-methode wordt aangeroepen, wordt er een asynchrone aanroep bij de Azure REST-API voor de sleutelkluis gegenereerd. De asynchrone aanroep retourneert een poller-object. Roep de `result`-methode van de poller aan als u op het resultaat van de bewerking wilt wachten.

Bij het verwerken van de aanvraag verifieert Azure de identiteit van de aanroepende functie (de service-principal) met behulp van het referentieobject dat u aan de client hebt verstrekt.


### <a name="retrieve-a-certificate"></a>Een certificaat ophalen

Gebruik de [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-)-methode voor het lezen van een certificaat uit Key Vault:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

U kunt ook controleren of het certificaat is ingesteld met de Azure CLI-opdracht [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Een certificaat verwijderen

Gebruik de [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-)-methode om een certificaat te verwijderen:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

De methode `begin_delete_certificate` is asynchroon en retourneert een pollerobject. Wanneer de methode `result` van de poller wordt aangeroepen, wordt er gewacht totdat deze voltooid is.

U kunt controleren of het certificaat is verwijderd met de Azure CLI-opdracht [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show).

Zodra een certificaat is verwijderd, heeft dit nog een tijdje de status Verwijderd maar herstelbaar. Gebruik een andere certificaatnaam als u de code nogmaals uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ook met [geheimen](../secrets/quick-create-python.md) en [sleutels](../keys/quick-create-python.md) wilt experimenteren, kunt u de Key Vault hergebruiken die in dit artikel is gemaakt.

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
