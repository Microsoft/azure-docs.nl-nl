---
title: 'Quickstart: Clientbibliotheek Azure Key Vault voor geheimen voor Java'
description: Bevat een quickstart voor de clientbibliotheek Azure Key Vault voor geheimen voor Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 804c4732ce979cd9ec46c69456b3f731284c9c73
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748522"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-java"></a>Quickstart: Clientbibliotheek Azure Key Vault voor geheimen voor Java
Aan de slag met de clientbibliotheek Azure Key Vault voor geheimen voor Java. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Aanvullende bronnen:

* [Broncode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)
* [API-referentiedocumentatie](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Productdocumentatie](index.yml)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

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
Gebruik in een consolevenster de opdracht `mvn` om een nieuwe Java-console-app te maken met de naam `akv-secrets-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.secrets.quickstart
                       -DartifactId=akv-secrets-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

De uitvoer van het project ziet er ongeveer als volgt uit:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.secrets.quickstart
[INFO] Parameter: artifactId, Value: akv-secrets-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-secrets-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Wijzig uw map in de zojuist gemaakte map `akv-secrets-java/`.

```console
cd akv-secrets-java
```

### <a name="install-the-package"></a>Het pakket installeren
Open het bestand *pom.xml* in uw teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
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
Maak toegangsbeleid voor de sleutelkluis waarmee geheimmachtigingen worden verleend aan uw gebruikersaccount.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --secret-permissions delete get list set purge
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
Met de clientbibliotheek Azure Key Vault voor geheimen voor Java kunt u geheimen beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De volledige console-app is [hieronder](#sample-code) beschikbaar.

## <a name="code-examples"></a>Codevoorbeelden
### <a name="add-directives"></a>Instructies toevoegen
Voeg de volgende instructies toe aan het begin van de code:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken
In deze quickstart wordt een aangemelde gebruiker gebruikt voor de verificatie bij Key Vault. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet een beheerde identiteit worden toegewezen aan een App-service of een virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse [DefaultAzureCredential()](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/java/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Een geheim opslaan
Nu uw toepassing is geverifieerd, kunt u een geheim toevoegen aan uw sleutelkluis met behulp van de methode `secretClient.setSecret`. Hiervoor is een naam voor het geheim vereist: we hebben in dit voorbeeld de waarde 'mySecret' toegewezen aan de variabele `secretName`.

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

U kunt controleren of het geheim is ingesteld met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen
U kunt nu het eerder ingestelde geheim ophalen met de methode `secretClient.getSecret`.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

U hebt nu toegang tot de waarde van het opgehaalde geheim met `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>Een geheim verwijderen
Ten slotte verwijderen we het geheim uit uw sleutelkluis met de `secretClient.beginDeleteSecret`-methode.

Het verwijderen van een geheim is een langdurige bewerking, waarvan u de voortgang kunt controleren of wachten totdat deze is voltooid.

```java
SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
deletionPoller.waitForCompletion();
```

U kunt controleren of het geheim is verwijderd met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-key-vault-name> --name mySecret
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
package com.keyvault.secrets.quickstart;

import java.io.Console;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.DeletedSecret;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(keyVaultUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();

        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Please provide the value of your secret > ");
        
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");
        System.out.println("Forgetting your secret.");
        
        secretValue = "";
        System.out.println("Your secret's value is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret's value is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        SyncPoller<DeletedSecret, Void> deletionPoller = secretClient.beginDeleteSecret(secretName);
        deletionPoller.waitForCompletion();

        System.out.println("done.");
    }
}
```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim vervolgens verwijderd. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Instructies voor [veilige toegang tot een sleutelkluis](../general/security-overview.md)
