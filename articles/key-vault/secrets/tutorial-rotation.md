---
title: Zelfstudie over roulatie voor resources met één set in Azure Key Vault opgeslagen verificatiereferenties
description: Gebruik deze zelfstudie om te leren hoe u het rouleren van een geheim kunt automatiseren voor resources die gebruikmaken van één set verificatiereferenties.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: cd90043c8f03361244d3500f83f6df5347f1d867
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539397"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Het rouleren van een geheim voor resources met één set verificatiereferenties automatiseren

De beste manier om te verifiëren bij Azure-services is met behulp van een [beheerde identiteit](../general/authentication.md), maar in enkele gevallen is dat niet mogelijk. In dergelijke gevallen worden toegangssleutels of geheimen gebruikt. U moet toegangssleutels of geheimen regelmatig rouleren.

In deze zelfstudie leert u hoe u de periodieke roulatie van geheimen voor databases en services die gebruikmaken van één set verificatiereferenties kunt automatiseren. Deze zelfstudie laat met name zien hoe SQL Server-wachtwoorden die in Azure Key Vault zijn opgeslagen, worden gerouleerd met behulp van een functie die wordt geactiveerd door een melding van Azure Event Grid:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagram van roulatieoplossing":::

1. Dertig dagen voor de vervaldatum van een geheim publiceert Key Vault deze "bijna verlopende" gebeurtenis naar Event Grid.
1. Event Grid controleert de gebeurtenisabonnementen en maakt gebruik van HTTP POST om het eindpunt voor de functie-app aan te roepen die is geabonneerd op de gebeurtenis.
1. De functie-app ontvangt de informatie over het geheim, genereert een nieuw willekeurig wachtwoord en maakt een nieuwe versie voor het geheim met het nieuwe wachtwoord in Key Vault.
1. De functie-app werkt SQL Server bij met het nieuwe wachtwoord.

> [!NOTE]
> Er kan een vertraging optreden tussen stap 3 en 4. Gedurende die tijd kan het geheim in Key Vault niet worden geverifieerd bij SQL Server. In het geval van een storing tijdens een van de stappen blijft Event Grid het twee uur opnieuw proberen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Azure Key Vault
* SQL Server

U kunt de onderstaande implementatiekoppeling gebruiken als u geen bestaande sleutelkluis en SQL Server hebt:

[![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Selecteer onder **Resourcegroep** de optie **Nieuwe maken**. Groep een naam geven, gebruiken we **akvrotation** in deze zelf studie.
1. Typ bij **Aanmeldgegevens SQL-beheerder** de aanmeldings-id van de SQL-beheerder. 
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**

:::image type="content" source="../media/rotate-2.png" alt-text="Een resourcegroep maken":::

U hebt nu een sleutelkluis en een SQL Server-exemplaar. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table -g akvrotation
```

Het resultaat ziet er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Een roulatiefunctie voor een SQL Server-wachtwoord maken en implementeren
> [!IMPORTANT]
> Voor de onderstaande sjabloon moeten Key Vault, de SQL-Server en de Azure-functie zich in dezelfde resource groep betreden

Maak vervolgens een functie-app met een door het systeem beheerde identiteit, naast de andere vereiste onderdelen, en implementeer functies voor het rouleren van SQL Server-wachtwoorden

Voor de functie-app zijn de volgende onderdelen vereist:
- Een Azure App Service-plan
- Een functie-app met functies voor het rouleren van SQL-wachtwoorden met gebeurtenistrigger en HTTP-trigger 
- Er is een opslagaccount vereist voor het beheer van de functie-app-trigger
- Een toegangsbeleid om de functie-app-id toegang te geven tot geheimen in Key Vault
- Een EventGrid-gebeurtenisabonnement voor de gebeurtenis **SecretNearExpiry**

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **akvrotation**.
1. Typ in **SQL Server-naam** de naam van de SQL Server met het wachtwoord dat u wilt rouleren
1. Typ in **Naam voor Key Vault** de naam van de sleutelkluis
1. Typ in **Naam van de functie-app** de naam van de functie-app
1. Typ in **Geheime naam** de naam van het geheim waarin het wachtwoord moet worden opgeslagen
1. Typ in **Opslagplaats-URL** de GitHub-locatie van de functiecode ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

:::image type="content" source="../media/rotate-3.png" alt-text="Selecteer Beoordelen en maken":::
  

Nadat u de voorgaande stappen hebt voltooid, hebt u een opslagaccount, een serverfarm en een functie-app. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table -g akvrotation
```

Het resultaat ziet er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Zie [Een functie-app maken vanuit de Azure-portal](../../azure-functions/functions-create-function-app-portal.md), [Beheerde identiteit gebruiken voor App Service en Azure Functions](../../app-service/overview-managed-identity.md) en [Key Vault-toegangsbeleid toewijzen met behulp van de Azure-portal](../general/assign-access-policy-portal.md) voor informatie over het maken van een functie-app en het gebruik van beheerde identiteit voor toegang tot Key Vault.

### <a name="rotation-function"></a>Roulatiefunctie
De in de vorige stap geïmplementeerde functie gebruikt een gebeurtenis om de roulatie van een geheim te activeren door Key Vault en de SQL database bij te werken. 

#### <a name="function-trigger-event"></a>Gebeurtenis om functie te activeren

Deze functie leest gebeurtenisgegevens en voert de roulatielogica uit:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logica voor geheimroulatie
Met deze roulatiemethode worden databasegegevens uit het geheim gelezen, wordt een nieuwe versie van het geheim gemaakt en wordt de database met het nieuwe geheim bijgewerkt:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
U vindt de volledige code [op GitHub](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Het geheim toevoegen aan Key Vault
Stel uw toegangsbeleid in om machtigingen voor het *beheren van geheimen* te verlenen aan gebruikers:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Maak een nieuw geheim met tags die de SQL Server-resource-id, de SQL Server-aanmeldings-id en de geldigheidsperiode voor het geheim in dagen bevatten. Geef de naam op van het geheim, het aanvankelijke wachtwoord van SQL database ('Simple123', in ons voorbeeld) en neem een vervaldatum op die is ingesteld op morgen.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Als u een geheim met een korte vervaldatum maakt, wordt een `SecretNearExpiry`-gebeurtenis binnen 15 minuten gepubliceerd, waardoor de functie voor roulatie van het geheim wordt geactiveerd.

## <a name="test-and-verify"></a>Testen en verifiëren

Als u wilt controleren of het geheim is gerouleerd, gaat u naar **Key Vault** > **Geheimen**:

:::image type="content" source="../media/rotate-8.png" alt-text="Naar Geheimen gaan":::

Open het **sqlPassword**-geheim en bekijk de oorspronkelijke en gerouleerde versies:

:::image type="content" source="../media/rotate-9.png" alt-text="Naar Geheimen gaan":::

### <a name="create-a-web-app"></a>Een webtoepassing maken

Maak een web-app om de SQL-referenties te controleren. Met deze web-app wordt het geheim opgehaald uit Key Vault, worden SQL databasegegevens en -referenties uit het geheim opgehaald en wordt de verbinding met SQL Server getest.

Voor de web-app zijn de volgende onderdelen vereist:
- Een web-app met door het systeem beheerde identiteit
- Een toegangsbeleid voor het openen van geheimen in Key Vault via een door de web-app beheerde identiteit

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Selecteer de resourcegroep **akvrotation**.
1. Typ in **SQL Server-naam** de naam van de SQL Server met het wachtwoord dat u wilt rouleren
1. Typ in **Naam voor Key Vault** de naam van de sleutelkluis
1. Typ in **Geheime naam** de naam van het geheim waarin het wachtwoord wordt opgeslagen
1. Typ in **Opslagplaats-URL** de GitHub-locatie van de web-appcode ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.


### <a name="open-the-web-app"></a>De web-app openen

Ga naar de URL van de geïmplementeerde toepassing:
 
'https://akvrotation-app.azurewebsites.net/'

Wanneer de toepassing in de browser wordt geopend, ziet u de **gegenereerde waarde van het geheim** en de waarde voor de **databaseverbinding** ingesteld op *true*.

## <a name="learn-more"></a>Meer informatie

- Zelfstudie: [Roulatie voor resources met twee sets referenties](tutorial-rotation-dual.md)
- Overzicht: [Key Vault bewaken met Azure Event Grid](../general/event-grid-overview.md)
- Procedure: [E-mail ontvangen wanneer een sleutelkluisgeheim verandert](../general/event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
