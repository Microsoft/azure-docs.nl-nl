---
title: Zelfstudie – Beheerde identiteit gebruiken om Key Vault te koppelen met een Azure Spring Cloud-app
description: Beheerde identiteit instellen om Key Vault te koppelen met een Azure Spring Cloud-app
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7e02bfb295460797edf46eac57afa628cd1544be
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212923"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Zelfstudie: Een beheerde identiteit gebruiken om Key Vault te koppelen met een Azure Spring Cloud-app

**Dit artikel is van toepassing op:** ✔️ Java

Dit artikel laat zien hoe u een beheerde identiteit kunt maken voor een Azure Spring Cloud-app en deze kunt gebruiken om toegang te krijgen tot Azure Key Vault.

Met Azure Key Vault kunt u veilig de toegang tot tokens, wachtwoorden, certificaten, API-sleutels en andere geheimen voor uw app opslaan en strikt beheren. U kunt een beheerde identiteit in Azure Active Directory (AAD) maken en laten verifiëren bij alle services die ondersteuning bieden voor Azure AD-verificatie, waaronder Key Vault, zonder dat u referenties in uw code hoeft weer te geven.

## <a name="prerequisites"></a>Vereisten

* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* [Azure CLI-versie 2.0.67 of hoger installeren](/cli/azure/install-azure-cli)
* [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep die zowel de Key Vault als de Spring Cloud bevat met behulp van de opdracht [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Uw sleutelkluis instellen
Als u een sleutelkluis wilt maken, gebruikt u de opdracht [az keyvault create](/cli/azure/keyvault#az-keyvault-create):

> [!Important]
> Elke sleutelkluis moet een unieke naam hebben. Vervang <your-keyvault-name> in de volgende voorbeelden door de naam van uw sleutelkluis.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Noteer de geretourneerde `vaultUri`, die de notatie "https://<your-keyvault-name>.vault.azure.net/" heeft. Deze wordt in de volgende stap gebruikt.

U kunt nu met de opdracht [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) een geheim in uw sleutelkluis plaatsen:

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud-service en -app maken
Nadat u de bijbehorende uitbreiding hebt geïnstalleerd, maakt u een Azure Spring Cloud-exemplaar met de Azure CLI-opdracht `az spring-cloud create`. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
In het volgende voorbeeld wordt een app met de naam `springapp` gemaakt met een door het systeem toegewezen beheerde identiteit, zoals is gevraagd met de parameter `--assign-identity`.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --assign-endpoint true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Noteer de geretourneerde `url` , die de indeling heeft `https://<your-app-name>.azuremicroservices.io` . Deze wordt in de volgende stap gebruikt.


## <a name="grant-your-app-access-to-key-vault"></a>Uw app toegang verlenen tot Key Vault
Gebruik `az keyvault set-policy` om uw app de juiste toegang te verlenen tot Key Vault.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Gebruik `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` om de toegang voor uw app te verwijderen, nadat de met het systeem toegewezen beheerde identiteit is uitgeschakeld.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Een voorbeeld van een Spring Boot-app bouwen met een Spring Boot-starter
Deze app heeft toegang om geheimen uit Azure Key Vault op te halen. De starter-app gebruiken: [Spring Boot-starter Azure Key Vault-geheimen](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  Azure Key Vault is als een exemplaar van Spring **PropertySource** toegevoegd.  Geheimen die in Azure Key Vault zijn opgeslagen, kunnen eenvoudig worden geopend en gebruikt, net als elke externe configuratie-eigenschap, zoals eigenschappen in bestanden. 

1. Genereer een voorbeeldproject vanuit start.spring.io met Azure Key Vault Spring Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Geef uw sleutelkluis op in uw app. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Als u beheerde identiteit voor Azure Spring Cloud-apps wilt gebruiken, voegt u eigenschappen met onderstaande inhoud toe aan src/main/resources/application.properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > U moet zoals hierboven de URL van de sleutelkluis toevoegen in `application.properties`. Anders kan het gebeuren dat de URL van de sleutelkluis niet wordt vastgelegd in runtime.

3. Voeg het codevoorbeeld toe aan src/main/Java/com/example/demo/DemoApplication.java. Hiermee wordt de verbindingstekenreeks opgehaald uit de sleutelkluis. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Als u pom.xml opent, ziet u de afhankelijkheid van `azure-keyvault-secrets-spring-boot-starter`. Voeg deze afhankelijkheid in pom.xml toe aan uw project. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Verpak uw voorbeeld-app. 

    ```azurecli
    mvn clean package
    ```

5. Nu kunt u uw app implementeren in Azure met de Azure CLI-opdracht `az spring-cloud app deploy`. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Als u de app wilt testen, opent u het openbare eindpunt of testeindpunt.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    U ziet het bericht "De waarde van de geheime connectionString is opgehaald uit Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Voorbeeld van Spring Boot-app bouwen met Java SDK

Met dit voorbeeld kunt u geheimen van Azure Key Vault instellen en ophalen. De [Azure Key Vault Secret-clientbibliotheek voor Java](/java/api/overview/azure/security-keyvault-secrets-readme) biedt ondersteuning voor Azure Active Directory-tokenverificatie in de Azure SDK. Deze bevat een reeks **TokenCredential**-implementaties die kunnen worden gebruikt om Azure SDK-clients te bouwen ter ondersteuning van AAD-tokenverificatie.

Met de Azure Key Vault Secret-clientbibliotheek kunt u op een veilige manier tokens, wachtwoorden, API-sleutels en andere geheimen opslaan en de toegang daartoe beheren. De bibliotheek biedt bewerkingen voor het maken, ophalen, bijwerken, verwijderen, opschonen, het maken en herstellen van back-ups en het weergeven van de geheimen en de bijbehorende versies.

1. Kloon een voorbeeldproject. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Geef uw sleutelkluis op in uw app. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Als u beheerde identiteit voor Azure Spring Cloud-apps wilt gebruiken, voegt u eigenschappen met de volgende inhoud toe aan *src/main/resources/application.properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Neem [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder) op om een token van Azure Active Directory en [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder) op te halen om geheimen uit Key Vault in uw code in te stellen of te verkrijgen.

    Bekijk het voorbeeld van [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) van het gekloonde voorbeeldproject.

    Neem ook `azure-identity` en `azure-security-keyvault-secrets` als afhankelijkheid op in uw pom.xml. Haal uit [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) het voorbeeld van het gekloonde voorbeeldproject op. 

4. Verpak uw voorbeeld-app. 

    ```azurecli
    mvn clean package
    ```

5. Nu kunt u de app met de Azure CLI-opdracht `az spring-cloud app deploy` implementeren in Azure. 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Open het openbare eindpunt of testeindpunt om de app te testen. 

    Haal eerst de waarde op van het geheim dat u hebt ingesteld in Azure Key Vault. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    U ziet het bericht "De waarde van de geheime connectionString is opgehaald uit Key Vault https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

    Maak nu een geheim en haal het vervolgens op met de Java-SDK. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    U ziet het bericht: "De waarde van de geheime test is opgehaald uit Key Vault https://<your-keyvault-name>.vault.azure.net: geslaagd". 

## <a name="next-steps"></a>Volgende stappen

* [Toegang tot de Storage-blob met een beheerde identiteit in een Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Door het systeem toegewezen beheerde identiteit inschakelen voor de Azure Spring Cloud-toepassing](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Meer informatie over beheerde identiteiten voor Azure-resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure Spring Cloud verifiëren met Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md)
