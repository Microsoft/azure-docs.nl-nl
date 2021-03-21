---
title: Zelfstudie voor het gebruik van Azure App Configuration Key Vault-verwijzingen in een Java Spring Boot-app | Microsoft Docs
description: In deze zelfstudie leert u hoe u Key Vault-verwijzingen van Azure App Configuration kunt gebruiken vanuit een Java Spring Boot-app
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 04d9c7a343570349851a206fd69fdda822f790a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981473"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Zelfstudie: Key Vault-referenties gebruiken in een Java Spring-app

In deze zelfstudie leert u hoe u de Azure App Configuration-service kunt gebruiken in combinatie met Azure Key Vault. App Configuration en Key Vault zijn complementaire services die naast elkaar worden gebruikt in de meeste toepassingsimplementaties.

Met App Configuration kunt u de services gezamenlijk gebruiken door sleutels te maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer App Configuration dergelijke sleutels maakt, worden de URI's van Key Vault-waarden opgeslagen in plaats van de waarden zelf.

In uw toepassing wordt gebruikgemaakt van de clientprovider van App Configuration om Key Vault-verwijzingen op te halen, net zoals bij andere sleutels die zijn opgeslagen in App Configuration. In dit geval zijn de waarden die zijn opgeslagen in App Configuration URI's die verwijzen naar de waarden in de Key Vault. Het zijn geen Key Vault-waarden of -referenties. Omdat de clientprovider de sleutels als Key Vault-verwijzingen herkent, wordt Key Vault gebruikt om de waarden op te halen.

Uw toepassing is verantwoordelijk voor het correct verifiëren van zowel App Configuration als Key Vault. De twee services communiceren niet rechtstreeks.

In deze zelfstudie wordt uitgelegd hoe u Key Vault-verwijzingen in uw code kunt implementeren. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voor u verdergaat, moet u eerst een [Java Spring-app maken met App Configuration](./quickstart-java-spring-app.md).

U kunt elke code-editor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is bijvoorbeeld een platformoverschrijdende code-editor die beschikbaar is voor Windows-, macOS- en Linux-besturingssystemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een App Configuration-sleutel maken die verwijst naar een waarde die is opgeslagen in Key Vault.
> * Toegang tot de waarde van deze sleutel vanuit een Java Spring-toepassing.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
* Een ondersteunde [Java Development Kit (JDK)](/java/azure/jdk) met versie 8.
* [Apache Maven](https://maven.apache.org/download.cgi) versie 3.0 of hoger.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **Een resource maken** in de linkerbovenhoek van Azure Portal:

    ![Schermopname van de optie Een resource maken, in de Microsoft Azure-portal.](./media/quickstarts/search-services.png)
1. Typ **Key Vault** in het zoekvak.
1. Selecteer in de lijst met resultaten **Key Vaults** aan de linkerkant.
1. Selecteer **Toevoegen** in **Key Vaults**.
1. Geef rechts in **Key Vault maken** de volgende gegevens op:
    * Selecteer **Abonnement** om een abonnement te kiezen.
    * Selecteer in **Resourcegroep** **Nieuwe maken** en voer een naam in voor de resourcegroep.
    * In **Key Vault-naam** is een unieke naam vereist. Voer voor deze zelfstudie **Contoso-vault2** in.
    * Kies een locatie in de vervolgkeuzelijst **Regio**.
1. Zorg ervoor dat de andere opties in **Key Vault maken** de standaardwaarden hebben.
1. Selecteer **Maken**.

Vanaf nu is uw Azure-account als enige gemachtigd om bewerkingen op deze nieuwe Key Vault uit te voeren.

![Schermopname van uw sleutelkluis.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de Key Vault, hoeft u maar een paar extra stappen uit te voeren. In dit geval voegt u een bericht toe dat u kunt gebruiken om het ophalen van Key Vault te testen. Het bericht wordt **Bericht** genoemd en u slaat er de waarde Hello from Key Vault in op.

1. Selecteer vanuit de eigenschappenpagina's van de Key Vault **Geheimen**.
1. Selecteer **Genereren/importeren**.
1. Voer in het deelvenster **Een geheim maken** een van de volgende waarden in:
    * **Uploadopties**: Voer **Handmatig** in.
    * **Naam**: **Bericht** invoeren.
    * **Waarde**: Voer **Hello from Key Vault** in.
1. Zorg ervoor dat de andere eigenschappen van **Een geheim maken** de standaardwaarden hebben.
1. Selecteer **Maken**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een sleutelkluisverwijzing toevoegen aan App Configuration

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Alle resources** en selecteer vervolgens de instantie van het App Configuration-archief dat u in de quickstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**.

1. Selecteer **+ Maken** > **Sleutelkluisverwijzing** en geef de volgende waarden op:
    * **Sleutel**: Selecteer **/application/config.keyvaultmessage**
    * **Label**: Laat deze waarde leeg.
    * **Abonnement**, **Resourcegroep** en **Sleutelkluis**: Voer de waarden in die overeenkomen met de waarden in de Key Vault die u in de vorige sectie hebt gemaakt.
    * **Geheim**: Selecteer het geheim genaamd **Bericht** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. In deze zelfstudie gebruikt u een service-principal voor verificatie voor Key Vault. Om deze service-principal te maken gebruikt u de Azure CLI-opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac):

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd:

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Voer de volgende opdracht uit om de service-principal toegang te geven tot uw Key Vault:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Voer de volgende opdracht uit om de object-id op te halen en vervolgens toe te voegen aan App Configuration.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Maak de omgevingsvariabelen **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** en **AZURE_TENANT_ID**. Gebruik de waarden voor de service-principal die in de vorige stap zijn weergegeven. Voer op de opdrachtregel de volgende opdrachten uit en start de opdrachtprompt opnieuw om de wijziging door te voeren:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Deze Key Vault-referenties worden alleen in uw toepassing gebruikt.  Uw toepassing verifieert rechtstreeks met Key Vault met behulp van deze referenties zonder de App Configuration-service te gebruiken.  De Key Vault biedt verificatie voor zowel uw toepassing als uw App Configuration-service zonder sleutels te delen of weer te geven.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault-referentie te gebruiken

1. Maak een omgevingsvariabele met de naam **APP_CONFIGURATION_ENDPOINT**. Stel de waarde ervan in op het eindpunt van uw App Configuration-opslag. U kunt het eindpunt vinden op de blade **Toegangssleutels** in Azure Portal. Start de opdrachtprompt opnieuw op om de wijziging door te voeren. 


1. Open *bootstrap.properties* in de map *resources*. Werk dit bestand bij om de waarde **APP_CONFIGURATION_ENDPOINT** te gebruiken. Verwijder alle verwijzingen naar een verbindingstekenreeks in dit bestand. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Open *MessageProperties.java*. Voeg een nieuwe variabele toe met de naam *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Open *HelloController.java*. Werk de *getMessage*-methode bij om het bericht dat is opgehaald uit Key Vault toe te voegen.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Maak een nieuw bestand met de naam *AzureCredentials.java* en voeg de onderstaande code toe.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Maak een nieuw bestand met de naam *AppConfiguration.java*. En voeg de onderstaande code toe.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Maak een nieuw bestand in uw resources META-INF-map met de naam *spring.factories* en voeg de onderstaande code toe.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Maak de Spring Boot-toepassing met Maven en voer deze uit; bijvoorbeeld:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nadat uw toepassing wordt uitgevoerd, kunt u *curl* gebruiken om uw toepassing te testen; bijvoorbeeld:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Er wordt een bericht weergegeven dat u zich in het archief van App Configuration bevindt. U ziet ook het bericht dat u hebt ingevoerd in Key Vault.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie maakt u een App Configuration-sleutel die verwijst naar een waarde die is opgeslagen in Key Vault. Ga door naar de volgende zelfstudie voor meer informatie over het gebruik van functievlaggen in uw Java Spring-toepassing.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./quickstart-feature-flag-spring-boot.md)
