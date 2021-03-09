---
title: REGI ster versleutelen met een door de klant beheerde sleutel
description: Meer informatie over het versleutelen van uw Azure container Registry en het versleutelen van uw Premium-REGI ster met een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: aad9419fdb139ff615bfe07075be78a2ca4ee4ac
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489069"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>REGI ster versleutelen met een door de klant beheerde sleutel

Wanneer u afbeeldingen en andere artefacten opslaat in een Azure container Registry, versleutelt Azure automatisch de register inhoud op rest met door [service beheerde sleutels](../security/fundamentals/encryption-models.md). U kunt standaard versleuteling aanvullen met een extra versleutelings laag met behulp van een sleutel die u in Azure Key Vault maakt en beheert (een door de klant beheerde sleutel). Dit artikel begeleidt u stapsgewijs door de stappen met behulp van de Azure CLI, de Azure Portal of een resource manager-sjabloon.

Versleuteling aan de server zijde met door de klant beheerde sleutels wordt ondersteund via integratie met [Azure Key Vault](../key-vault/general/overview.md): 

* U kunt uw eigen versleutelings sleutels maken en deze opslaan in een sleutel kluis of de Api's van Azure Key Vault gebruiken om sleutels te genereren. 
* Met Azure Key Vault kunt u ook het sleutel gebruik controleren.
* Azure Container Registry ondersteunt automatische rotatie van register versleutelings sleutels wanneer een nieuwe sleutel versie beschikbaar is in Azure Key Vault. U kunt ook hand matig register versleutelings sleutels draaien.

Deze functie is beschikbaar in de service tier van het **Premium** -container register. Zie [Azure container Registry service lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten van het REGI ster.


## <a name="things-to-know"></a>Dingen die u moet weten

* U kunt momenteel alleen een door de klant beheerde sleutel inschakelen wanneer u een REGI ster maakt. Wanneer u de sleutel inschakelt, configureert u een door de *gebruiker toegewezen* beheerde identiteit voor toegang tot de sleutel kluis.
* Nadat u versleuteling hebt ingeschakeld met een door de klant beheerde sleutel in een REGI ster, kunt u de versleuteling niet uitschakelen.  
* Azure Container Registry ondersteunt alleen RSA-of RSA-HSM-sleutels. Elliptische curve sleutels worden momenteel niet ondersteund.
* [Inhouds vertrouwen](container-registry-content-trust.md) wordt momenteel niet ondersteund in een REGI ster dat is versleuteld met een door de klant beheerde sleutel.
* In een REGI ster dat is versleuteld met een door de klant beheerde sleutel, worden logboeken voor [ACR-taken](container-registry-tasks-overview.md) alleen 24 uur bewaard. Als u logboeken gedurende een langere periode wilt bewaren, raadpleegt u richt lijnen voor het [exporteren en opslaan van Logboeken](container-registry-tasks-logs.md#alternative-log-storage)voor het uitvoeren van taken.


> [!IMPORTANT]
> Als u van plan bent de register versleutelings sleutel op te slaan in een bestaande Azure-sleutel kluis die open bare toegang weigert en alleen een privé-eind punt of geselecteerde virtuele netwerken toestaat, zijn er extra configuratie stappen nodig. Zie [Advanced scenario: Key Vault firewall](#advanced-scenario-key-vault-firewall) in dit artikel.

## <a name="automatic-or-manual-update-of-key-versions"></a>Automatische of hand matige update van belang rijke versies

Een belang rijke overweging voor de beveiliging van een REGI ster dat is versleuteld met een door de klant beheerde sleutel is de frequentie waarmee u de versleutelings sleutel bijwerkt (roteert). Uw organisatie heeft mogelijk een nalevings beleid waarvoor de sleutel [versies](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) die in azure Key Vault zijn opgeslagen, regel matig moeten worden bijgewerkt wanneer deze worden gebruikt als door de klant beheerde sleutels. 

Wanneer u register versleuteling configureert met een door de klant beheerde sleutel, hebt u twee opties voor het bijwerken van de sleutel versie die wordt gebruikt voor versleuteling:

* **De sleutel versie automatisch bijwerken** : als u een door de klant beheerde sleutel automatisch wilt bijwerken wanneer een nieuwe versie beschikbaar is in azure Key Vault, laat u de sleutel versie weg wanneer u register versleuteling inschakelt met een door de klant beheerde sleutel. Wanneer een REGI ster is versleuteld met een niet-versie sleutel, controleert Azure Container Registry regel matig de sleutel kluis voor een nieuwe sleutel versie en werkt de door de klant beheerde sleutel binnen één uur bij. Azure Container Registry maakt automatisch gebruik van de meest recente versie van de sleutel.

* **De sleutel versie hand matig bijwerken** : als u een specifieke versie van een sleutel voor register versleuteling wilt gebruiken, geeft u die sleutel versie op wanneer u register versleuteling inschakelt met een door de klant beheerde sleutel. Wanneer een REGI ster is versleuteld met een specifieke sleutel versie, Azure Container Registry deze versie gebruiken voor versleuteling totdat u de door de klant beheerde sleutel hand matig roteert.

Zie voor meer informatie [sleutel-id kiezen met of zonder sleutel versie](#choose-key-id-with-or-without-key-version) en de [sleutel versie bijwerken](#update-key-version)verderop in dit artikel.

## <a name="prerequisites"></a>Vereisten

Als u de stappen van Azure CLI in dit artikel wilt gebruiken, hebt u Azure CLI versie 2.2.0 of hoger nodig of Azure Cloud Shell. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="enable-customer-managed-key---cli"></a>Door de klant beheerde sleutel inschakelen-CLI

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Als dat nodig is, voert u de opdracht [AZ Group Create][az-group-create] uit om een resource groep te maken voor het maken van de sleutel kluis, container register en andere vereiste resources.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) met de opdracht [AZ Identity Create][az-identity-create] . Deze identiteit wordt gebruikt door uw REGI ster om toegang te krijgen tot de Key Vault-service.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

In de uitvoer van de opdracht noteert u de volgende waarden: `id` en `principalId` . U hebt deze waarden in latere stappen nodig om register toegang tot de sleutel kluis te configureren.

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

Voor het gemak slaat u deze waarden op in omgevings variabelen:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Maak een sleutel kluis met [AZ Key kluis Create][az-keyvault-create] om een door de klant beheerde sleutel op te slaan voor register versleuteling. 

De instelling **voorlopig verwijderen** wordt standaard automatisch ingeschakeld in een nieuwe sleutel kluis. Als u gegevens verlies wilt voor komen dat wordt veroorzaakt door onbedoelde sleutel-of sleutel kluis verwijderingen, schakelt u ook de instelling **beveiliging opschonen** in.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Voor gebruik in latere stappen moet u de resource-ID van de sleutel kluis ophalen:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Sleutel kluis toegang inschakelen

Configureer een beleid voor de sleutel kluis zodat de identiteit er toegang toe heeft. In de volgende opdracht [AZ set-Policy][az-keyvault-set-policy] , geeft u de principal-id van de beheerde identiteit door die u hebt gemaakt, die eerder is opgeslagen in een omgevings variabele. Stel de sleutel machtigingen in op **Get**, **sleutel uitpakken** en **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

U kunt ook [Azure RBAC gebruiken voor Key Vault](../key-vault/general/rbac-guide.md) om machtigingen toe te wijzen aan de identiteit voor toegang tot de sleutel kluis. Wijs bijvoorbeeld de Key Vault versleutelings functie van crypto grafie-service toe aan de identiteit met behulp van de opdracht [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) :

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Sleutel maken en sleutel-ID ophalen

Voer de opdracht [AZ sleutel kluis Key Create][az-keyvault-key-create] uit om een sleutel te maken in de sleutel kluis.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

In de uitvoer van de opdracht noteert u de sleutel-ID `kid` . U gebruikt deze ID in de volgende stap:

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

### <a name="choose-key-id-with-or-without-key-version"></a>Sleutel-ID kiezen met of zonder sleutel versie

Voor het gemak slaat u de indeling op die u voor de sleutel-ID hebt gekozen in de omgevings variabele $keyID. U kunt een sleutel-ID gebruiken met een versie of een sleutel zonder versie.

#### <a name="manual-key-rotation---key-id-with-version"></a>Hand matige sleutel wisseling-sleutel-ID met versie

Wanneer u gebruikt voor het versleutelen van een REGI ster met een door de klant beheerde sleutel, staat deze sleutel alleen hand matige rotatie van de sleutel in Azure Container Registry toe.

In dit voor beeld wordt de eigenschap van de sleutel opgeslagen `kid` :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatische rotatie van sleutels-sleutel-ID die versie weglaat 

Wanneer u gebruikt voor het versleutelen van een REGI ster met een door de klant beheerde sleutel, wordt met deze sleutel de functie voor het automatisch draaien van sleutels ingeschakeld wanneer een nieuwe sleutel versie wordt gedetecteerd in Azure Key Vault.

In dit voor beeld wordt de versie van de `kid` eigenschap Key verwijderd:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Een REGI ster maken met door de klant beheerde sleutel

Voer de opdracht [AZ ACR Create][az-acr-create] uit om een REGI ster te maken in de laag Premium-Service en de door de klant beheerde sleutel in te scha kelen. Geef de beheerde identiteits-ID en de sleutel-ID op die eerder zijn opgeslagen in omgevings variabelen:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Versleutelings status weer geven

Als u wilt weer geven of register versleuteling met een door de klant beheerde sleutel is ingeschakeld, voert u de opdracht [AZ ACR Encryption show][az-acr-encryption-show] uit:

```azurecli
az acr encryption show --name <registry-name>
```

Afhankelijk van de sleutel die wordt gebruikt om het REGI ster te versleutelen, is de uitvoer vergelijkbaar met:

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

## <a name="enable-customer-managed-key---portal"></a>Door de klant beheerde sleutel inschakelen-Portal

### <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Maak een door de gebruiker toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in de Azure Portal. Zie [een door de gebruiker toegewezen identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)voor instructies.

U gebruikt de naam van de identiteit in latere stappen.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Een door de gebruiker toegewezen identiteit maken in de Azure Portal":::

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Voor stappen voor het maken van een sleutel kluis raadpleegt u [Quick Start: een sleutel kluis maken met behulp van de Azure Portal](../key-vault/general/quick-create-portal.md).

Wanneer u een sleutel kluis maakt voor een door de klant beheerde sleutel, schakelt u op het tabblad **basis beginselen** de instelling **beveiliging opschonen** in. Deze instelling helpt gegevens verlies te voor komen dat wordt veroorzaakt door onopzettelijke sleutel-of sleutel kluis verwijderingen.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Een sleutel kluis maken in de Azure Portal":::

### <a name="enable-key-vault-access"></a>Sleutel kluis toegang inschakelen

Configureer een beleid voor de sleutel kluis zodat de identiteit er toegang toe heeft.

1. Navigeer naar uw sleutel kluis.
1. Selecteer **instellingen**  >  **toegangs beleid > + toegangs beleid toe te voegen**.
1. Selecteer **belang rijke machtigingen** en selecteer **ophalen**, **uitpakken sleutel** en **Terugloop sleutel**.
1. Selecteer in **Principal selecteren** de resource naam van de door de gebruiker toegewezen beheerde identiteit.  
1. Selecteer **toevoegen** en selecteer vervolgens **Opslaan**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Een sleutel kluis toegangs beleid maken":::

U kunt ook [Azure RBAC gebruiken voor Key Vault](../key-vault/general/rbac-guide.md) om machtigingen toe te wijzen aan de identiteit voor toegang tot de sleutel kluis. Wijs bijvoorbeeld de Key Vault versleutelings functie van crypto grafie-service toe aan de identiteit.

1. Navigeer naar uw sleutel kluis.
1. Selecteer **toegangs beheer (IAM)**  >  **+**  >  **toewijzing toevoegen** toevoegen.
1. In het venster **roltoewijzing toevoegen** :
    1. Selecteer **Key Vault versleutelings gebruikersrol van crypto grafie** . 
    1. Toegang toewijzen aan door de **gebruiker toegewezen beheerde identiteit**.
    1. Selecteer de resource naam van de door de gebruiker toegewezen beheerde identiteit en selecteer **Opslaan**.

### <a name="create-key-optional"></a>Sleutel maken (optioneel)

Maak eventueel een sleutel in de sleutel kluis die u kunt gebruiken om het REGI ster te versleutelen. Volg deze stappen als u een specifieke sleutel versie als een door de klant beheerde sleutel wilt selecteren. 

1. Navigeer naar uw sleutel kluis.
1. Selecteer **instellingen**  >  **sleutels**.
1. Selecteer **+ genereren/importeren** en voer een unieke naam in voor de sleutel.
1. Accepteer de resterende standaard waarden en selecteer **maken**.
1. Nadat u hebt gemaakt, selecteert u de sleutel en selecteert u vervolgens de huidige versie. Kopieer de **sleutel-id** voor de sleutel versie.

### <a name="create-azure-container-registry"></a>Azure Container Registry maken

1. Selecteer **Een resource maken** > **Containers** > **Container Registry**.
1. Op het tabblad **basis beginselen** selecteert of maakt u een resource groep en voert u een register naam in. Selecteer **Premium** in **SKU**.
1. Selecteer op het tabblad **versleuteling** in door de **klant beheerde sleutel** **ingeschakeld**.
1. Selecteer in **identiteit** de beheerde identiteit die u hebt gemaakt.
1. Kies in **versleuteling** een van de volgende opties:
    * Selecteer **selecteren in Key Vault** en selecteer een bestaande sleutel kluis en sleutel, of **Maak een nieuwe**. De sleutel die u selecteert, heeft geen versie en maakt automatische rotatie van de sleutel mogelijk.
    * Selecteer **sleutel-URI invoeren** en geef rechtstreeks een sleutel-id op. U kunt een sleutel-URI van een versie opgeven (voor een sleutel die hand matig moet worden gedraaid) of een sleutel-URI zonder versie (waardoor het automatisch draaien van sleutels mogelijk wordt). 
1. Selecteer in het tabblad **versleuteling** de optie **controleren + maken**.
1. Selecteer **maken** om het register exemplaar te implementeren.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Versleuteld REGI ster maken in de Azure Portal":::

Als u de versleutelings status van het REGI ster in de portal wilt bekijken, gaat u naar het REGI ster. Selecteer bij **instellingen** de optie  **versleuteling**.

## <a name="enable-customer-managed-key---template"></a>Door de klant beheerde sleutel inschakelen-sjabloon

U kunt ook een resource manager-sjabloon gebruiken om een REGI ster te maken en versleuteling in te scha kelen met een door de klant beheerde sleutel.

Met de volgende sjabloon maakt u een nieuw container register en een door de gebruiker toegewezen beheerde identiteit. Kopieer de volgende inhoud naar een nieuw bestand en sla het op met een bestands naam zoals `CMKtemplate.json` .

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

* Sleutel kluis, geïdentificeerd op naam
* Sleutel kluis sleutel, geïdentificeerd door sleutel-ID

Voer de volgende opdracht [AZ Deployment Group Create][az-deployment-group-create] uit om het REGI ster te maken met het voor gaande sjabloon bestand. Geef, indien aangegeven, een nieuwe register naam en beheerde identiteits naam op, evenals de naam van de sleutel kluis en de sleutel-ID die u hebt gemaakt.

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

### <a name="show-encryption-status"></a>Versleutelings status weer geven

Als u de status van register versleuteling wilt weer geven, voert u de opdracht [AZ ACR Encryption show][az-acr-encryption-show] uit:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Het REGI ster gebruiken

Nadat u een door de klant beheerde sleutel in een REGI ster hebt ingeschakeld, kunt u dezelfde register bewerkingen uitvoeren die u uitvoert in een REGI ster dat niet is versleuteld met een door de klant beheerde sleutel. U kunt bijvoorbeeld verifiëren met de installatie kopieën van het REGI ster en push docker. Bekijk de voorbeeld opdrachten in [push en pull een installatie kopie](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Toets draaien

Werk de sleutel versie in Azure Key Vault bij, of maak een nieuwe sleutel en werk vervolgens het REGI ster bij om gegevens te versleutelen met behulp van de sleutel. U kunt deze stappen uitvoeren met behulp van de Azure CLI of in de portal.

Wanneer u een sleutel draait, geeft u meestal dezelfde identiteit op die wordt gebruikt bij het maken van het REGI ster. U kunt desgewenst een nieuwe door de gebruiker toegewezen identiteit configureren voor sleutel toegang of de door het systeem toegewezen identiteit van het REGI ster inschakelen en opgeven.

> [!NOTE]
> Zorg ervoor dat de vereiste [sleutel kluis toegang](#enable-key-vault-access) is ingesteld voor de identiteit die u configureert voor toegang tot sleutels.

### <a name="update-key-version"></a>Sleutel versie bijwerken

Een veelvoorkomend scenario is het bijwerken van de versie van de sleutel die wordt gebruikt als een door de klant beheerde sleutel. Afhankelijk van hoe de register versleuteling is geconfigureerd, wordt de door de klant beheerde sleutel in Azure Container Registry automatisch bijgewerkt of moet deze hand matig worden bijgewerkt.

### <a name="azure-cli"></a>Azure CLI

Gebruik de opdracht [AZ-sleutel kluis][az-keyvault-key] om uw sleutel kluis sleutels te maken of te beheren. Als u een nieuwe sleutel versie wilt maken, voert u de opdracht [AZ sleutel kluis Key Create][az-keyvault-key-create] uit:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

De volgende stap is afhankelijk van de configuratie van de register versleuteling:

* Als het REGI ster is geconfigureerd voor het detecteren van belang rijke versie-updates, wordt de door de klant beheerde sleutel automatisch bijgewerkt binnen 1 uur.

* Als het REGI ster zo is geconfigureerd dat hand matig moet worden bijgewerkt voor een nieuwe sleutel versie, voert u de opdracht [AZ ACR Encryption Rotate][az-acr-encryption-rotate-key] uit, waarbij de nieuwe sleutel-id en de identiteit die u wilt configureren, worden door gegeven:

De door de klant beheerde sleutel versie hand matig bijwerken:

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
> Wanneer u uitvoert `az acr encryption rotate-key` , kunt u een sleutel-id van de versie of een niet-versie sleutel-id door geven. Als u een sleutel-ID gebruikt die niet is geversied, wordt het REGI ster vervolgens geconfigureerd om updates van latere sleutel versies automatisch te detecteren.

### <a name="portal"></a>Portal

Gebruik de **versleutelings** instellingen van het REGI ster om de sleutel kluis, sleutel of identiteits instellingen bij te werken die worden gebruikt voor de door de klant beheerde sleutel.

Als u bijvoorbeeld een nieuwe sleutel wilt configureren:

1. Navigeer in de portal naar het REGI ster.
1. Selecteer onder **instellingen** de optie **versleutelings**  >  **sleutel wijzigen**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="De sleutel in de Azure Portal draaien":::
1. Kies bij **versleuteling** een van de volgende opties:
    * Selecteer **selecteren in Key Vault** en selecteer een bestaande sleutel kluis en sleutel, of **Maak een nieuwe**. De sleutel die u selecteert, heeft geen versie en maakt automatische rotatie van de sleutel mogelijk.
    * Selecteer **sleutel-URI invoeren** en geef rechtstreeks een sleutel-id op. U kunt een sleutel-URI van een versie opgeven (voor een sleutel die hand matig moet worden gedraaid) of een sleutel-URI zonder versie (waardoor het automatisch draaien van sleutels mogelijk wordt).
1. Voltooi de sleutel selectie en selecteer **Opslaan**.

## <a name="revoke-key"></a>Sleutel intrekken

Trek de door de klant beheerde versleutelings sleutel in door het toegangs beleid of de machtigingen voor de sleutel kluis te wijzigen of door de sleutel te verwijderen. Gebruik bijvoorbeeld de opdracht [AZ sleutel kluis delete-policy][az-keyvault-delete-policy] om het toegangs beleid te wijzigen van de beheerde identiteit die wordt gebruikt door uw REGI ster:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Als u de sleutel intrekt, wordt de toegang tot alle register gegevens geblokkeerd omdat het REGI ster geen toegang kan krijgen tot de versleutelings sleutel. Als de toegang tot de sleutel is ingeschakeld of de verwijderde sleutel is hersteld, wordt de sleutel door het REGI ster gekozen, zodat u opnieuw toegang tot de versleutelde register gegevens kunt krijgen.

## <a name="advanced-scenario-key-vault-firewall"></a>Geavanceerd scenario: Key Vault firewall

U kunt de versleutelings sleutel opslaan met een bestaande Azure-sleutel kluis die is geconfigureerd met een [Key Vault firewall](../key-vault/general/network-security.md), die open bare toegang weigert en alleen een privé-eind punt of geselecteerde virtuele netwerken toestaat. 

Voor dit scenario maakt u eerst een nieuwe door de gebruiker toegewezen identiteit, sleutel kluis en container register die is versleuteld met een door de klant beheerde sleutel, met behulp van de [Azure cli](#enable-customer-managed-key---cli), [Portal](#enable-customer-managed-key---portal)of [sjabloon](#enable-customer-managed-key---template). Gedetailleerde stappen vindt u in de voor gaande secties in dit artikel.
   > [!NOTE]
   > De nieuwe sleutel kluis wordt geïmplementeerd buiten de firewall. Deze wordt alleen tijdelijk gebruikt om de door de klant beheerde sleutel op te slaan.

Ga na het maken van het REGI ster verder met de volgende stappen. Meer informatie vindt u in de volgende secties.

1. De door het systeem toegewezen identiteit van het REGI ster inschakelen.
1. Verleen de door het systeem toegewezen identiteits machtigingen voor toegang tot sleutels in de sleutel kluis die is beperkt met de Key Vault firewall.
1. Zorg ervoor dat de Key Vault firewall bypass toestaat door vertrouwde services. Op dit moment kan een Azure container Registry alleen de firewall overs Laan wanneer de door het systeem beheerde identiteit wordt gebruikt. 
1. Draai de door de klant beheerde sleutel door deze te selecteren in de sleutel kluis die is beperkt met de Key Vault firewall.
1. Wanneer u deze niet meer nodig hebt, kunt u de sleutel kluis verwijderen die buiten de firewall is gemaakt.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Stap 1: de door het systeem toegewezen identiteit van het REGI ster inschakelen

1. Navigeer in de portal naar het REGI ster.
1. Selecteer **instellingen**  >   **identiteit**.
1. Stel onder **toegewezen systeem** **status** in **op aan**. Selecteer **Opslaan**.
1. Kopieer de **object-id** van de identiteit.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Stap 2: aan het systeem toegewezen identiteits toegang verlenen tot uw sleutel kluis

1. Navigeer in de portal naar uw sleutel kluis.
1. Selecteer **instellingen**  >  **toegangs beleid > + toegangs beleid toe te voegen**.
1. Selecteer **belang rijke machtigingen** en selecteer **ophalen**, **uitpakken sleutel** en **Terugloop sleutel**.
1. Kies **Principal selecteren** en zoek naar de object-id van de door het systeem toegewezen beheerde identiteit of de naam van het REGI ster.  
1. Selecteer **toevoegen** en selecteer vervolgens **Opslaan**.

### <a name="step-3---enable-key-vault-bypass"></a>Stap 3: Schakel overs laan voor sleutel kluis

Om toegang te krijgen tot een sleutel kluis die is geconfigureerd met een Key Vault firewall, moet het REGI ster de firewall omzeilen. Zorg ervoor dat de sleutel kluis zo is geconfigureerd dat deze toegang biedt tot een [vertrouwde service](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Container Registry is een van de vertrouwde services.

1. Navigeer in de portal naar uw sleutel kluis.
1. Selecteer **instellingen**  >  **netwerken**.
1. De instellingen van het virtuele netwerk bevestigen, bijwerken of toevoegen. Zie [Azure Key Vault firewalls en virtuele netwerken configureren](../key-vault/general/network-security.md)voor gedetailleerde stappen.
1. In **micro soft-vertrouwde services toestaan deze firewall te omzeilen**, selecteert u **Ja**. 

### <a name="step-4---rotate-the-customer-managed-key"></a>Stap 4: de door de klant beheerde sleutel draaien

Nadat u de voor gaande stappen hebt voltooid, draait u naar een sleutel die is opgeslagen in de sleutel kluis achter een firewall.

1. Navigeer in de portal naar het REGI ster.
1. Selecteer onder **instellingen** de optie **versleutelings**  >  **sleutel wijzigen**.
1. Selecteer in **identiteit** de optie **systeem toegewezen**.
1. Selecteer **selecteren in Key Vault** en selecteer de naam van de sleutel kluis die zich achter een firewall bevindt.
1. Selecteer een bestaande sleutel of **Maak een nieuwe**. De sleutel die u selecteert, heeft geen versie en maakt automatische rotatie van de sleutel mogelijk.
1. Voltooi de sleutel selectie en selecteer **Opslaan**.

## <a name="troubleshoot"></a>Problemen oplossen

### <a name="removing-managed-identity"></a>Beheerde identiteit verwijderen


Als u probeert een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit te verwijderen uit een REGI ster dat wordt gebruikt voor het configureren van versleuteling, ziet u mogelijk een fout bericht dat lijkt op het volgende:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
U kunt de versleutelings sleutel ook niet wijzigen (draaien). De oplossings stappen zijn afhankelijk van het type identiteit dat voor versleuteling wordt gebruikt.

**Door gebruiker toegewezen identiteit**

Als dit probleem optreedt met een door de gebruiker toegewezen identiteit, moet u de identiteit eerst opnieuw toewijzen met behulp van de GUID die wordt weer gegeven in het fout bericht. Bijvoorbeeld:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Nadat u de sleutel hebt gewijzigd en een andere identiteit hebt toegewezen, kunt u de oorspronkelijke door de gebruiker toegewezen identiteit verwijderen.

**Door het systeem toegewezen identiteit**

Als dit probleem optreedt met een door het systeem toegewezen identiteit, [maakt u een ondersteunings ticket voor Azure](https://azure.microsoft.com/support/create-ticket/) om u te helpen bij het herstellen van de identiteit.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [versleuteling in rust in azure](../security/fundamentals/encryption-atrest.md).
* Meer informatie over toegangs beleid en het [beveiligen van de toegang tot een sleutel kluis](../key-vault/general/secure-your-key-vault.md).


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
