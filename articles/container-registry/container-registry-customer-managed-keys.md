---
title: Register versleutelen met een door de klant beheerde sleutel
description: Meer informatie over versleuteling-at-rest van uw Azure-containerregister en hoe u uw Premium-register versleutelt met een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: c4e23acde9e3640da83b993c8c0c8c0818ccaa4f
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750154"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Register versleutelen met behulp van een door de klant beheerde sleutel

Wanneer u afbeeldingen en andere artefacten opgeslagen in een Azure-containerregister, versleutelt Azure automatisch de registerinhoud at rest met [door de service beheerde sleutels](../security/fundamentals/encryption-models.md). U kunt standaardversleuteling aanvullen met een extra versleutelingslaag met behulp van een sleutel die u maakt en beheert in Azure Key Vault (een door de klant beheerde sleutel). In dit artikel worden de stappen beschreven met behulp van de Azure CLI, de Azure Portal of een Resource Manager sjabloon.

Versleuteling aan de serverzijde met door de klant beheerde sleutels wordt ondersteund door integratie met [Azure Key Vault](../key-vault/general/overview.md): 

* U kunt uw eigen versleutelingssleutels maken en deze opslaan in een sleutelkluis of de API's van Azure Key Vault gebruiken om sleutels te genereren. 
* Met Azure Key Vault kunt u ook het sleutelgebruik controleren.
* Azure Container Registry ondersteunt automatische rotatie van registerversleutelingssleutels wanneer een nieuwe sleutelversie beschikbaar is in Azure Key Vault. U kunt registerversleutelingssleutels ook handmatig roteren.

Deze functie is beschikbaar in de **servicelaag Premium** Container Registry. Zie Azure Container Registry servicelagen voor meer [informatie over registerservicelagen en -limieten.](container-registry-skus.md)


## <a name="things-to-know"></a>Dingen die u moet weten

* U kunt momenteel alleen een door de klant beheerde sleutel inschakelen wanneer u een register maakt. Wanneer u de sleutel inschakelen, configureert u een *door de gebruiker toegewezen* beheerde identiteit voor toegang tot de sleutelkluis.
* Nadat u versleuteling hebt inschakelen met een door de klant beheerde sleutel in een register, kunt u de versleuteling niet uitschakelen.  
* Azure Container Registry ondersteunt alleen RSA- of RSA-HSM-sleutels. Elliptische curvesleutels worden momenteel niet ondersteund.
* [Inhoud vertrouwen](container-registry-content-trust.md) wordt momenteel niet ondersteund in een register dat is versleuteld met een door de klant beheerde sleutel.
* In een register dat is versleuteld met een [](container-registry-tasks-overview.md) door de klant beheerde sleutel, worden logboeken voor ACR-taken momenteel slechts 24 uur bewaard. Als u logboeken voor een langere periode wilt bewaren, bekijkt u de richtlijnen voor het [exporteren en opslaan van taakuitvoerlogboeken.](container-registry-tasks-logs.md#alternative-log-storage)


> [!IMPORTANT]
> Als u van plan bent om de registerversleutelingssleutel op te slaan in een bestaande Azure-sleutelkluis die openbare toegang toestaat en alleen privé-eindpunten of geselecteerde virtuele netwerken toestaat, zijn extra configuratiestappen nodig. Zie [Geavanceerd scenario: Key Vault firewall](#advanced-scenario-key-vault-firewall) in dit artikel.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatisch of handmatig bijwerken van sleutelversies

Een belangrijke overweging voor de beveiliging van een register dat is versleuteld met een door de klant beheerde sleutel is hoe vaak u de versleutelingssleutel bij werkt (roteert). Uw organisatie heeft mogelijk nalevingsbeleidsregels die vereisen dat [sleutelversies](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) die zijn opgeslagen in Azure Key Vault worden gebruikt als door de klant beheerde sleutels. 

Wanneer u registerversleuteling configureert met een door de klant beheerde sleutel, hebt u twee opties voor het bijwerken van de sleutelversie die wordt gebruikt voor versleuteling:

* **De** sleutelversie automatisch bijwerken: als u automatisch een door de klant beheerde sleutel wilt bijwerken wanneer er een nieuwe versie beschikbaar is in Azure Key Vault, laat u de sleutelversie weg wanneer u registerversleuteling inschakelen met een door de klant beheerde sleutel. Wanneer een register is versleuteld met een sleutel zonder versie, controleert Azure Container Registry regelmatig de sleutelkluis op een nieuwe sleutelversie en werkt de door de klant beheerde sleutel binnen één uur bij. Azure Container Registry gebruikt automatisch de nieuwste versie van de sleutel.

* **De sleutelversie handmatig** bijwerken: als u een specifieke versie van een sleutel voor registerversleuteling wilt gebruiken, geeft u die sleutelversie op wanneer u registerversleuteling inschakelen met een door de klant beheerde sleutel. Wanneer een register is versleuteld met een specifieke sleutelversie, gebruikt Azure Container Registry die versie voor versleuteling totdat u de door de klant beheerde sleutel handmatig roteert.

Zie Sleutel-id [kiezen met of zonder sleutelversie](#choose-key-id-with-or-without-key-version) en [Sleutelversie bijwerken](#update-key-version)verder in dit artikel voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-stappen in dit artikel wilt gebruiken, hebt u Azure CLI versie 2.2.0 of hoger of Azure Cloud Shell. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="enable-customer-managed-key---cli"></a>Door de klant beheerde sleutel inschakelen - CLI

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voer indien nodig de opdracht [az group create uit][az-group-create] om een resourcegroep te maken voor het maken van de sleutelkluis, het containerregister en andere vereiste resources.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) met de [opdracht az identity create.][az-identity-create] Deze identiteit wordt door uw register gebruikt voor toegang tot de Key Vault service.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Noteer de volgende waarden in de uitvoer van de opdracht: `id` en `principalId` . U hebt deze waarden in latere stappen nodig om registertoegang tot de sleutelkluis te configureren.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Voor het gemak kunt u deze waarden opslaan in omgevingsvariabelen:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Maak een sleutelkluis [met az keyvault create om][az-keyvault-create] een door de klant beheerde sleutel voor registerversleuteling op te slaan. 

Standaard wordt de **instelling voor het** verwijderen van de functie voor het verwijderen van gegevens automatisch ingeschakeld in een nieuwe sleutelkluis. Schakel ook de beveiligingsinstelling voor opsluizen in om gegevensverlies te voorkomen als gevolg van onbedoeld verwijderen van een sleutel of **sleutelkluis.**

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Haal de resource-id van de sleutelkluis op voor gebruik in latere stappen:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Toegang tot key vault inschakelen

Configureer een beleid voor de sleutelkluis zodat de identiteit er toegang toe heeft. In de volgende [az keyvault set-policy-opdracht][az-keyvault-set-policy] kunt u de principal-id doorgeven van de beheerde identiteit die u eerder in een omgevingsvariabele hebt opgeslagen. Stel sleutelmachtigingen in **om ,** **unwrapKey** en **wrapKey op te halen.**  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

U kunt ook [Azure RBAC gebruiken](../key-vault/general/rbac-guide.md) voor Key Vault om machtigingen aan de identiteit toe te wijzen voor toegang tot de sleutelkluis. Wijs bijvoorbeeld de rol cryptoserviceversleuteling Key Vault aan de identiteit met behulp van [de opdracht az role assignment create:](/cli/azure/role/assignment#az-role-assignment-create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Sleutel maken en sleutel-id op halen

Voer de [opdracht az keyvault key create][az-keyvault-key-create] uit om een sleutel te maken in de sleutelkluis.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Noteer in de uitvoer van de opdracht de id van de sleutel, `kid` . U gebruikt deze id in de volgende stap:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Sleutel-id kiezen met of zonder sleutelversie

Sla voor het gemak de indeling die u kiest voor de sleutel-id op in $keyID omgevingsvariabele. U kunt een sleutel-id gebruiken met een versie of een sleutel zonder versie.

#### <a name="manual-key-rotation---key-id-with-version"></a>Handmatige sleutelrotatie - sleutel-id met versie

Wanneer deze sleutel wordt gebruikt om een register te versleutelen met een door de klant beheerde sleutel, staat deze sleutel alleen handmatige sleutelrotatie toe in Azure Container Registry.

In dit voorbeeld wordt de eigenschap van de sleutel `kid` op slaat:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatische sleutelrotatie - sleutel-id die versie weglaten 

Wanneer deze sleutel wordt gebruikt om een register te versleutelen met een door de klant beheerde sleutel, wordt automatische sleutelrotatie mogelijk wanneer een nieuwe sleutelversie wordt gedetecteerd in Azure Key Vault.

In dit voorbeeld wordt de versie verwijderd uit de eigenschap van de `kid` sleutel:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Een register maken met door de klant beheerde sleutel

Voer de [opdracht az acr create uit][az-acr-create] om een register te maken in de Premium-servicelaag en schakel de door de klant beheerde sleutel in. Geef de id van de beheerde identiteit en de sleutel-id door, die eerder zijn opgeslagen in omgevingsvariabelen:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Versleutelingsstatus weergeven

Als u wilt zien of registerversleuteling met een door de klant beheerde sleutel is ingeschakeld, moet u de [opdracht az acr encryption show][az-acr-encryption-show] uitvoeren:

```azurecli
az acr encryption show --name <registry-name>
```

Afhankelijk van de sleutel die wordt gebruikt om het register te versleutelen, is de uitvoer vergelijkbaar met:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Door de klant beheerde sleutel inschakelen - portal

### <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Maak een door de gebruiker toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in de Azure Portal. Zie Create [a user-assigned identity (Een door de gebruiker toegewezen identiteit maken) voor stappen.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

In latere stappen gebruikt u de naam van de identiteit.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Door de gebruiker toegewezen identiteit maken in de Azure Portal":::

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Zie [Quickstart:](../key-vault/general/quick-create-portal.md)Een sleutelkluis maken met behulp van de Azure Portal .

Schakel bij het maken van een sleutelkluis voor een door de klant beheerde sleutel op het tabblad Basisinformatie de instelling **Beveiliging opsluizen** in.  Met deze instelling voorkomt u gegevensverlies als gevolg van onbedoeld verwijderen van een sleutel of sleutelkluis.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Een sleutelkluis maken in Azure Portal":::

### <a name="enable-key-vault-access"></a>Toegang tot key vault inschakelen

Configureer een beleid voor de sleutelkluis zodat de identiteit er toegang toe heeft.

1. Navigeer naar uw sleutelkluis.
1. Selecteer **Instellingen**  >  **Toegangsbeleid > +Toegangsbeleid toevoegen.**
1. Selecteer **Sleutelmachtigingen** en selecteer **Get,** **Unwrap Key** en **Wrap Key.**
1. Selecteer **in Principal selecteren** de resourcenaam van uw door de gebruiker toegewezen beheerde identiteit.  
1. Selecteer **Toevoegen** en vervolgens **Opslaan.**

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Toegangsbeleid voor key vault maken":::

U kunt ook [Azure RBAC gebruiken](../key-vault/general/rbac-guide.md) voor Key Vault om machtigingen aan de identiteit toe te wijzen voor toegang tot de sleutelkluis. Wijs bijvoorbeeld de Key Vault cryptoserviceversleuteling toe aan de identiteit.

1. Navigeer naar uw sleutelkluis.
1. Selecteer **Toegangsbeheer (IAM)**  >  **+Roltoewijzing**  >  **toevoegen.**
1. In het **venster Roltoewijzing** toevoegen:
    1. Selecteer **Key Vault cryptoserviceversleutelingsgebruikersrol.** 
    1. Wijs toegang toe **aan door de gebruiker toegewezen beheerde identiteit**.
    1. Selecteer de resourcenaam van uw door de gebruiker toegewezen beheerde identiteit en selecteer **Opslaan.**

### <a name="create-key-optional"></a>Sleutel maken (optioneel)

Maak eventueel een sleutel in de sleutelkluis voor gebruik om het register te versleutelen. Volg deze stappen als u een specifieke sleutelversie wilt selecteren als een door de klant beheerde sleutel. 

1. Navigeer naar uw sleutelkluis.
1. Selecteer   >  **Instellingensleutels.**
1. Selecteer **+Genereren/importeren** en voer een unieke naam in voor de sleutel.
1. Accepteer de overige standaardwaarden en selecteer **Maken.**
1. Selecteer na het maken de sleutel en selecteer vervolgens de huidige versie. Kopieer de **sleutel-id** voor de sleutelversie.

### <a name="create-azure-container-registry"></a>Azure Container Registry maken

1. Selecteer **Een resource maken** > **Containers** > **Container Registry**.
1. Selecteer of **maak op** het tabblad Basisinformatie een resourcegroep en voer een registernaam in. Selecteer premium in  **SKU.**
1. Selecteer op **het tabblad** Versleuteling in Door de klant **beheerde** sleutel **de optie Ingeschakeld.**
1. Selecteer **in Identiteit** de beheerde identiteit die u hebt gemaakt.
1. Kies **in** Versleuteling een van de volgende opties:
    * Selecteer **Selecteren in Key Vault** en selecteer een bestaande sleutelkluis en sleutel of Nieuwe **maken.** De sleutel die u selecteert, heeft geen versie en maakt automatische sleutelrotatie mogelijk.
    * Selecteer **Sleutel-URI invoeren** en geef rechtstreeks een sleutel-id op. U kunt een versie-URI (voor een sleutel die handmatig moet worden geroteerd) of een sleutel-URI zonder versie (die automatische sleutelrotatie mogelijk maakt) verstrekken. 
1. Selecteer op **het tabblad** Versleuteling de optie Controleren **en maken.**
1. Selecteer **Maken om** het register-exemplaar te implementeren.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Een versleuteld register maken in de Azure Portal":::

Als u de versleutelingsstatus van uw register in de portal wilt zien, gaat u naar het register. Selecteer **versleuteling** onder **Instellingen.**

## <a name="enable-customer-managed-key---template"></a>Door de klant beheerde sleutel inschakelen - sjabloon

U kunt ook een Resource Manager gebruiken om een register te maken en versleuteling in te stellen met een door de klant beheerde sleutel.

Met de volgende sjabloon maakt u een nieuw containerregister en een door de gebruiker toegewezen beheerde identiteit. Kopieer de volgende inhoud naar een nieuw bestand en sla deze op met een bestandsnaam zoals `CMKtemplate.json` .

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Volg de stappen in de vorige secties om de volgende resources te maken:

* Sleutelkluis, geïdentificeerd met naam
* Sleutelkluissleutel, geïdentificeerd op basis van sleutel-id

Voer de volgende [opdracht az deployment group create uit om][az-deployment-group-create] het register te maken met behulp van het voorgaande sjabloonbestand. Geef, indien aangegeven, een nieuwe registernaam en beheerde identiteitsnaam op, evenals de naam van de sleutelkluis en de sleutel-id die u hebt gemaakt.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Versleutelingsstatus weergeven

Voer de opdracht [az acr encryption show][az-acr-encryption-show] uit om de status van registerversleuteling weer te geven:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Het register gebruiken

Nadat u een door de klant beheerde sleutel in een register hebt inschakelen, kunt u dezelfde registerbewerkingen uitvoeren die u ook in een register hebt uitgevoerd dat niet is versleuteld met een door de klant beheerde sleutel. U kunt bijvoorbeeld verifiëren met het register en Docker-afbeeldingen pushen. Zie voorbeeldopdrachten in Push and pull an image (Een [afbeelding pushen en pullen).](container-registry-get-started-docker-cli.md)

## <a name="rotate-key"></a>Sleutel roteren

Werk de sleutelversie in Azure Key Vault of maak een nieuwe sleutel en werk vervolgens het register bij om gegevens te versleutelen met behulp van de sleutel. U kunt deze stappen uitvoeren met behulp van de Azure CLI of in de portal.

Bij het roteren van een sleutel geeft u doorgaans dezelfde identiteit op die u hebt gebruikt bij het maken van het register. Configureer eventueel een nieuwe door de gebruiker toegewezen identiteit voor sleuteltoegang of schakel de door het systeem toegewezen identiteit van het register in en geef deze op.

> [!NOTE]
> Zorg ervoor dat de vereiste [toegang tot de sleutelkluis](#enable-key-vault-access) is ingesteld voor de identiteit die u configureert voor sleuteltoegang.

### <a name="update-key-version"></a>Sleutelversie bijwerken

Een veelvoorkomende situatie is het bijwerken van de versie van de sleutel die wordt gebruikt als een door de klant beheerde sleutel. Afhankelijk van hoe de registerversleuteling is geconfigureerd, wordt de door de klant beheerde sleutel in Azure Container Registry automatisch bijgewerkt of moet deze handmatig worden bijgewerkt.

### <a name="azure-cli"></a>Azure CLI

Gebruik [az keyvault key commands][az-keyvault-key] om uw sleutelkluissleutels te maken of beheren. Voer de opdracht [az keyvault key create][az-keyvault-key-create] uit om een nieuwe sleutelversie te maken:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

De volgende stap is afhankelijk van hoe de registerversleuteling is geconfigureerd:

* Als het register is geconfigureerd voor het detecteren van sleutelversie-updates, wordt de door de klant beheerde sleutel automatisch binnen één uur bijgewerkt.

* Als het register zo is geconfigureerd dat het handmatig moet worden bijgewerkt voor een nieuwe sleutelversie, moet u de opdracht [az acr encryption rotate-key][az-acr-encryption-rotate-key] uitvoeren en de nieuwe sleutel-id en de identiteit doorgeven die u wilt configureren:

De door de klant beheerde sleutelversie handmatig bijwerken:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Wanneer u `az acr encryption rotate-key` gebruikt, kunt u een versie-sleutel-id of een sleutel-id zonder versie doorgeven. Als u een sleutel-id zonder versie gebruikt, wordt het register geconfigureerd om updates van latere sleutelversies automatisch te detecteren.

### <a name="portal"></a>Portal

Gebruik de versleutelingsinstellingen **van het** register om de sleutelkluis-, sleutel- of identiteitsinstellingen bij te werken die worden gebruikt voor de door de klant beheerde sleutel.

Als u bijvoorbeeld een nieuwe sleutel wilt configureren:

1. Navigeer in de portal naar uw register.
1. Selecteer **onder Instellingen** de optie **Versleutelingssleutel**  >  **wijzigen.**

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Sleutel roteren in de Azure Portal":::
1. Kies **in Versleuteling** een van de volgende opties:
    * Selecteer **Selecteren in Key Vault** en selecteer een bestaande sleutelkluis en -sleutel of Nieuwe **maken.** De sleutel die u selecteert, heeft geen versie en maakt automatische sleutelrotatie mogelijk.
    * Selecteer **Sleutel-URI invoeren** en geef rechtstreeks een sleutel-id op. U kunt een sleutel-URI met versie (voor een sleutel die handmatig moet worden geroteerd) of een sleutel-URI zonder versie (die automatische sleutelrotatie mogelijk maakt).
1. Voltooi de sleutelselectie en selecteer **Opslaan.**

## <a name="revoke-key"></a>Sleutel intrekken

De door de klant beheerde versleutelingssleutel intrekken door het toegangsbeleid of de machtigingen voor de sleutelkluis te wijzigen of door de sleutel te verwijderen. Gebruik bijvoorbeeld de opdracht [az keyvault delete-policy][az-keyvault-delete-policy] om het toegangsbeleid te wijzigen van de beheerde identiteit die door uw register wordt gebruikt:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Het inroepen van de sleutel blokkeert de toegang tot alle registergegevens, omdat het register geen toegang heeft tot de versleutelingssleutel. Als toegang tot de sleutel is ingeschakeld of de verwijderde sleutel wordt hersteld, kiest het register de sleutel zodat u opnieuw toegang hebt tot de versleutelde registergegevens.

## <a name="advanced-scenario-key-vault-firewall"></a>Geavanceerd scenario: Key Vault firewall

Mogelijk wilt u de versleutelingssleutel opslaan met behulp van een bestaande Azure-sleutelkluis die is geconfigureerd met een [Key Vault-firewall,](../key-vault/general/network-security.md)die openbare toegang en alleen privé-eindpunten of geselecteerde virtuele netwerken toestaat. 

Maak voor dit scenario eerst een nieuwe door de gebruiker toegewezen identiteit, sleutelkluis en containerregister die zijn versleuteld met een door de klant beheerde sleutel met behulp van [de Azure CLI](#enable-customer-managed-key---cli), [portal](#enable-customer-managed-key---portal)of [sjabloon](#enable-customer-managed-key---template). Gedetailleerde stappen staan in de voorgaande secties in dit artikel.
   > [!NOTE]
   > De nieuwe sleutelkluis wordt buiten de firewall geïmplementeerd. Deze wordt alleen tijdelijk gebruikt om de door de klant beheerde sleutel op te slaan.

Nadat het register is gemaakt, gaat u verder met de volgende stappen. Details zijn in de volgende secties.

1. Schakel de door het systeem toegewezen identiteit van het register in.
1. Verleen de door het systeem toegewezen identiteit machtigingen voor toegang tot sleutels in de sleutelkluis die is beperkt tot Key Vault firewall.
1. Zorg ervoor dat de Key Vault firewall bypass door vertrouwde services toestaat. Op dit moment kan een Azure-containerregister de firewall alleen omzeilen wanneer de door het systeem beheerde identiteit wordt gebruikt. 
1. Roteren van de door de klant beheerde sleutel door er een te selecteren in de sleutelkluis die is beperkt met Key Vault firewall.
1. U kunt de sleutelkluis die buiten de firewall is gemaakt, verwijderen wanneer u deze niet meer nodig hebt.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Stap 1: de door het register toegewezen identiteit inschakelen

1. Navigeer in de portal naar uw register.
1. Selecteer **Instellingen**  >   **Identiteit.**
1. Stel **status onder Door systeem** toegewezen **in** op **Aan.** Selecteer **Opslaan**.
1. Kopieer de **object-id** van de identiteit.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Stap 2: door het systeem toegewezen identiteit toegang verlenen tot uw sleutelkluis

1. Navigeer in de portal naar uw sleutelkluis.
1. Selecteer **Instellingen**  >  **Toegangsbeleid > +Toegangsbeleid toevoegen.**
1. Selecteer **Sleutelmachtigingen** en selecteer **Get,** **Unwrap Key** en **Wrap Key.**
1. Kies **Principal selecteren** en zoek naar de object-id van uw door het systeem toegewezen beheerde identiteit of de naam van het register.  
1. Selecteer **Toevoegen** en selecteer vervolgens **Opslaan.**

### <a name="step-3---enable-key-vault-bypass"></a>Stap 3: bypass van sleutelkluis inschakelen

Voor toegang tot een sleutelkluis die is geconfigureerd met Key Vault firewall, moet het register de firewall omzeilen. Zorg ervoor dat de sleutelkluis is geconfigureerd om toegang toe te staan door [een vertrouwde service.](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) Azure Container Registry is een van de vertrouwde services.

1. Navigeer in de portal naar uw sleutelkluis.
1. Selecteer **Instellingen**  >  **Netwerken.**
1. Bevestig, werk instellingen voor virtuele netwerken bij of voeg deze toe. Zie Configure [Azure Key Vault firewalls and virtual networks (Firewalls en virtuele netwerken configureren) voor gedetailleerde stappen.](../key-vault/general/network-security.md)
1. Selecteer **ja in Vertrouwde Microsoft-services** toestaan om deze firewall over te **laten.** 

### <a name="step-4---rotate-the-customer-managed-key"></a>Stap 4: De door de klant beheerde sleutel roteren

Nadat u de voorgaande stappen hebt doorlopen, roteert u naar een sleutel die is opgeslagen in de sleutelkluis achter een firewall.

1. Navigeer in de portal naar uw register.
1. Selecteer **onder Instellingen** de optie **Versleutelingssleutel**  >  **wijzigen.**
1. Selecteer **in Identiteit** de optie Systeem **toegewezen.**
1. Selecteer **Selecteren in Key Vault** en selecteer de naam van de sleutelkluis achter een firewall.
1. Selecteer een bestaande sleutel of **Nieuwe maken.** De sleutel die u selecteert, heeft geen versie en maakt automatische sleutelrotatie mogelijk.
1. Voltooi de sleutelselectie en selecteer **Opslaan.**

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="removing-managed-identity"></a>Beheerde identiteit verwijderen


Als u een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit probeert te verwijderen uit een register dat wordt gebruikt om versleuteling te configureren, ziet u mogelijk een foutbericht dat lijkt op:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
U kunt de versleutelingssleutel ook niet wijzigen (draaien). De oplossingsstappen zijn afhankelijk van het type identiteit dat wordt gebruikt voor versleuteling.

**Door de gebruiker toegewezen identiteit**

Als dit probleem optreedt met een door de gebruiker toegewezen identiteit, moet u de identiteit eerst opnieuw toewijzen met behulp van de GUID die wordt weergegeven in het foutbericht. Bijvoorbeeld:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Nadat u de sleutel hebt veranderd en een andere identiteit hebt toegewezen, kunt u de oorspronkelijke door de gebruiker toegewezen identiteit verwijderen.

**Door het systeem toegewezen identiteit**

Als dit probleem optreedt met een door het systeem toegewezen identiteit, maakt u een ondersteuning voor Azure [voor](https://azure.microsoft.com/support/create-ticket/) hulp bij het herstellen van de identiteit.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [versleuteling-at-rest in Azure](../security/fundamentals/encryption-atrest.md).
* Meer informatie over toegangsbeleid en het [beveiligen van toegang tot een sleutelkluis.](../key-vault/general/security-overview.md)


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
