---
title: Quickstart - Azure Key Vault-clientbibliotheek voor sleutels voor Java
description: Bevat een quickstart voor de Azure Key Vault-clientbibliotheek voor sleutels voor Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 75cb7b6c9225e8579561f980df10da8994257133
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777181"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Quickstart: Azure Key Vault-clientbibliotheek voor sleutels voor Java
Ga aan de slag met de Azure Key Vault-clientbibliotheek voor sleutels voor Java. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Aanvullende bronnen:

* [Broncode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [API-referentiedocumentatie](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Productdocumentatie](index.yml)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- [Java Development Kit (JDK)](/java/azure/jdk/)-versie 8 of hoger
- [Apache Maven](https://maven.apache.org)
- [Azure-CLI](/cli/azure/install-azure-cli)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli) en [Apache Maven](https://maven.apache.org) uitvoert in een Linux-terminalvenster.

## <a name="setting-up"></a>Instellen
Deze quickstart maakt gebruik van de Azure Identity-bibliotheek met Azure CLI om de gebruiker te verifiëren bij Azure-services. Ontwikkelaars kunnen ook Visual Studio of Visual Studio Code gebruiken om hun oproepen te verifiëren: zie [De client verifiëren met de Azure Identity-clientbibliotheek](/java/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure
1. Voer de opdracht `login` uit.

    ```azurecli-interactive
    az login
    ```

   Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

   Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in de terminal.

2. Meldt u zich in de browser aan met uw accountreferenties.

### <a name="create-a-new-java-console-app"></a>Nieuwe Java-console-app maken
Gebruik in een consolevenster de opdracht `mvn` om een nieuwe Java-console-app te maken met de naam `akv-keys-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

De uitvoer van het project ziet er ongeveer als volgt uit:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Wijzig uw map in de zojuist gemaakte map `akv-keys-java/`.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Het pakket installeren
Open het bestand *pom.xml* in uw teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutelkluis
Maak toegangsbeleid voor de sleutelkluis waarmee sleutelmachtigingen worden verleend aan uw gebruikersaccount.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen
In deze applicatie wordt de naam van uw sleutelkluis gebruikt als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

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

## <a name="object-model"></a>Objectmodel
Met de Azure Key Vault-clientbibliotheek voor sleutels voor Java kunt u sleutels beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client maakt, een sleutel maakt, een sleutel ophaalt en een sleutel verwijdert.

De volledige console-app is [hieronder](#sample-code) beschikbaar.

## <a name="code-examples"></a>Codevoorbeelden
### <a name="add-directives"></a>Instructies toevoegen
Voeg de volgende instructies toe aan het begin van de code:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken
In deze quickstart wordt een aangemelde gebruiker gebruikt voor de verificatie bij Key Vault. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet een beheerde identiteit worden toegewezen aan een App-service of een virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse [DefaultAzureCredential()](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/java/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Een sleutel maken
Nu uw toepassing is geverifieerd, kunt u een sleutel in uw sleutelkluis maken met behulp van de methode `keyClient.createKey`. Hiervoor is een naam vereist voor de sleutel en een sleuteltype -- we hebben de waarde 'myKey ' toegewezen aan de variabele `keyName` en hebben een RSA `KeyType` gebruikt in dit voorbeeld.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

U kunt controleren of de sleutel is ingesteld met de opdracht [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show):

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Een sleutel ophalen
U kunt nu de eerder gemaakte sleutel ophalen met de methode `keyClient.getKey`.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

U hebt nu toegang tot de gegevens van de opgehaalde sleutel met bewerkingen als `retrievedKey.getProperties`, `retrievedKey.getKeyOperations`, enzovoort.

### <a name="delete-a-key"></a>Een sleutel verwijderen
Ten slotte verwijderen we de sleutel geheim uit uw sleutelkluis met de `keyClient.beginDeleteKey`-methode.

Het verwijderen van een sleutel is een langdurige bewerking, waarvan u de voortgang kunt controleren of wachten totdat deze is voltooid.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

U kunt controleren of de sleutel is verwijderd met de opdracht [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show):

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer u de sleutelkluis en de bijbehorende resourcegroep niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om ze te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Voorbeeldcode
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een sleutelkluis gemaakt, een sleutel gemaakt, opgehaald en vervolgens verwijderd. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Lees het [Overzicht van de Key Vault-beveiliging](../general/security-overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Instructies voor [veilige toegang tot een sleutelkluis](../general/security-overview.md)