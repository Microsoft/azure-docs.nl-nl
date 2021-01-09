---
title: 'Quickstart: Azure Key Vault-clientbibliotheek met certificaten voor Java'
description: Een quickstart voor de Azure Key Vault-clientbibliotheek met certificaten voor Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 1890c2a3d4043d43dd890f06942dbe704e3f7689
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733464"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java"></a>Quickstart: Azure Key Vault-clientbibliotheek met certificaten voor Java
Aan de slag met de Azure Key Vault-clientbibliotheek met certificaten voor Java. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Aanvullende bronnen:

* [Broncode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [API-referentiedocumentatie](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Productdocumentatie](index.yml)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

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
Gebruik in een consolevenster de opdracht `mvn` om een nieuwe Java-console-app te maken met de naam `akv-certificates-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

De uitvoer van het project ziet er ongeveer als volgt uit:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Wijzig uw map in de zojuist gemaakte `akv-certificates-java/`-map.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>Het pakket installeren
Open het bestand *pom.xml* in uw teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
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
Maak toegangsbeleid voor de sleutelkluis waarmee certificaatmachtigingen worden verleend aan uw gebruikersaccount.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen
In deze applicatie wordt de naam van uw sleutelkluis gebruikt als een omgevingsvariabele met de naam `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS of Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objectmodel
Met de Azure Key Vault-clientbibliotheek met certificaten voor Java kunt u certificaten beheren. In de sectie [Codevoorbeelden](#code-examples) ziet u hoe u een client en een certificaat maakt, een certificaat ophaalt en verwijdert.

De volledige console-app is [hieronder](#sample-code) beschikbaar.

## <a name="code-examples"></a>Codevoorbeelden
### <a name="add-directives"></a>Instructies toevoegen
Voeg de volgende instructies toe aan het begin van de code:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken
In deze quickstart wordt een aangemelde gebruiker gebruikt voor de verificatie bij Key Vault. Dit is de voorkeursmethode voor lokale ontwikkeling. Voor toepassingen die zijn geïmplementeerd in Azure, moet een beheerde identiteit worden toegewezen aan een App-service of een virtuele machine. Zie [Overzicht van beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie.

In het onderstaande voorbeeld wordt de naam van de sleutelkluis uitgebreid naar de sleutelkluis-URI, met de indeling https://\<your-key-vault-name\>.vault.azure.net. In dit voorbeeld wordt de klasse [DefaultAzureCredential()](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) gebruikt, waarmee u dezelfde code kunt gebruiken in verschillende omgevingen met verschillende opties om identiteiten te bieden. Zie het artikel over [standaardverificatie van Azure-referenties](https://docs.microsoft.com/java/api/overview/azure/identity-readme) (Engelstalig) voor meer informatie.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Een geheim opslaan
Nu uw toepassing is geverifieerd, kunt u een certificaat in uw sleutelkluis maken met behulp van de methode `certificateClient.beginCreateCertificate`. Hiervoor is een naam vereist voor het certificaat en een certificaatbeleid: de waarde myCertificate is in dit voorbeeld toegewezen aan de variabele `certificateName` en er wordt een standaardbeleid gebruikt.

Het maken van een certificaat is een langdurige bewerking, waarvan u de voortgang kunt controleren of wachten totdat deze is voltooid.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Zodra het certificaat is gemaakt, kunt u het ophalen met behulp van de volgende aanroep:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Een certificaat ophalen
U kunt nu het eerder gemaakte certificaat ophalen met de methode `certificateClient.getCertificate`.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

U hebt nu toegang tot de gegevens van het opgehaalde certificaat met bewerkingen als `retrievedCertificate.getName`, `retrievedCertificate.getProperties`, enzovoort. En tevens de inhoud `retrievedCertificate.getCer`.

### <a name="delete-a-certificate"></a>Een certificaat verwijderen
Ten slotte gaan we het certificaat uit de sleutelkluis verwijderen met de methode `certificateClient.beginDeleteCertificate`, wat ook een langdurige bewerking is.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een sleutelkluis gemaakt, een certificaat gemaakt, opgehaald en vervolgens verwijderd. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Instructies voor [veilige toegang tot een sleutelkluis](../general/secure-your-key-vault.md)