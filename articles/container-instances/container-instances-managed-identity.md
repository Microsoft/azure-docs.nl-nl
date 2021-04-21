---
title: Beheerde identiteit inschakelen in containergroep
description: Meer informatie over het inschakelen van een beheerde identiteit in Azure Container Instances die kan worden geverifieerd met andere Azure-services
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: f8f3c646487d86f4e1bce13ccbf28992b8b1497a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763982"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Beheerde identiteiten gebruiken met Azure Container Instances

Gebruik [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) om code uit te voeren in Azure Container Instances die communiceert met andere Azure-services, zonder dat er geheimen of referenties in code worden onderhouden. De functie biedt een Azure Container Instances implementatie met een automatisch beheerde identiteit in Azure Active Directory.

In dit artikel vindt u meer informatie over beheerde identiteiten in Azure Container Instances en:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een containergroep
> * De identiteit toegang verlenen tot een Azure-sleutelkluis
> * De beheerde identiteit gebruiken voor toegang tot een sleutelkluis vanuit een container die wordt uitgevoerd

Pas de voorbeelden aan om identiteiten in te Azure Container Instances toegang te krijgen tot andere Azure-services. Deze voorbeelden zijn interactief. In de praktijk voeren uw containerafbeeldingen echter code uit voor toegang tot Azure-services.
 
> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt. Momenteel worden beheerde identiteiten op Azure Container Instances, alleen ondersteund met Linux-containers en nog niet met Windows-containers.

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Gebruik een beheerde identiteit in een container die wordt uitgevoerd om te verifiëren bij elke service die [Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) ondersteunt zonder referenties in uw containercode te beheren. Voor services die geen ondersteuning bieden voor AD-verificatie, kunt u geheimen opslaan in een Azure-sleutelkluis en de beheerde identiteit gebruiken om toegang te krijgen tot de sleutelkluis om referenties op te halen. Zie Wat zijn beheerde identiteiten voor [Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie over het gebruik van een beheerde identiteit.

### <a name="enable-a-managed-identity"></a>Een beheerde identiteit inschakelen

 Wanneer u een containergroep maakt, moet u een of meer beheerde identiteiten inschakelen door de eigenschap [ContainerGroupIdentity in te](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) stellen. U kunt beheerde identiteiten ook inschakelen of bijwerken nadat een containergroep wordt uitgevoerd. Beide acties zorgen ervoor dat de containergroep opnieuw wordt gestart. Als u de identiteiten wilt instellen voor een nieuwe of bestaande containergroep, gebruikt u de Azure CLI, een Resource Manager sjabloon, een YAML-bestand of een ander Azure-hulpprogramma. 

Azure Container Instances ondersteunt beide typen beheerde Azure-identiteiten: door de gebruiker toegewezen en door het systeem toegewezen. In een containergroep kunt u een door het systeem toegewezen identiteit, een of meer door de gebruiker toegewezen identiteiten of beide typen identiteiten inschakelen. Zie het overzicht als u niet bekend bent met beheerde identiteiten [voor](../active-directory/managed-identities-azure-resources/overview.md)Azure-resources.

### <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

Als u een beheerde identiteit wilt gebruiken, moet aan de identiteit toegang worden verleend tot een of meer Azure-serviceresources (zoals een web-app, een sleutelkluis of een opslagaccount) in het abonnement. Het gebruik van een beheerde identiteit in een container die wordt uitgevoerd, is vergelijkbaar met het gebruik van een identiteit in een Azure-VM. Zie de VM-richtlijnen voor het gebruik van [een token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell, Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)of de Azure [SDK's.](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

### <a name="limitations"></a>Beperkingen

* U kunt momenteel geen beheerde identiteit gebruiken in een containergroep die is geïmplementeerd in een virtueel netwerk.
* U kunt geen beheerde identiteit gebruiken om een afbeelding op te halen uit een Azure Container Registry bij het maken van een containergroep. De identiteit is alleen beschikbaar in een container die wordt uitgevoerd.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-an-azure-key-vault"></a>Een Azure-sleutelkluis maken

In de voorbeelden in dit artikel wordt een beheerde identiteit in Azure Container Instances om toegang te krijgen tot een Azure Key Vault-geheim. 

Maak eerst een resourcegroep met de naam *myResourceGroup* in de locatie *eastus* met behulp van de opdracht [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de [opdracht az keyvault create om](/cli/azure/keyvault#az_keyvault_create) een sleutelkluis te maken. Zorg ervoor dat u een unieke naam voor de sleutelkluis opgeeft. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Sla een voorbeeldgeheim op in de sleutelkluis met [behulp van de opdracht az keyvault secret set:](/cli/azure/keyvault/secret#az_keyvault_secret_set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Ga verder met de volgende voorbeelden voor toegang tot de sleutelkluis met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Voorbeeld 1: Een door de gebruiker toegewezen identiteit gebruiken voor toegang tot Azure Key Vault

### <a name="create-an-identity"></a>Een identiteit maken

Maak eerst een identiteit in uw abonnement met behulp van [de opdracht az identity create.](/cli/azure/identity#az_identity_create) U kunt dezelfde resourcegroep gebruiken die u hebt gebruikt om de sleutelkluis te maken of een andere gebruiken.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Als u de identiteit in de volgende stappen wilt gebruiken, gebruikt u de opdracht [az identity show](/cli/azure/identity#az_identity_show) om de service-principal-id en resource-id van de identiteit op te slaan in variabelen.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Door de gebruiker toegewezen identiteit toegang verlenen tot de sleutelkluis

Voer de volgende [az keyvault set-policy-opdracht](/cli/azure/keyvault) uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de door de gebruiker toegewezen identiteit geheimen uit de sleutelkluis op halen:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Door de gebruiker toegewezen identiteit inschakelen voor een containergroep

Voer de volgende [az container create-opdracht](/cli/azure/container#az_container_create) uit om een container-exemplaar te maken op basis van de microsoft-afbeelding. `azure-cli` Dit voorbeeld biedt een groep met één container die u interactief kunt gebruiken om de Azure CLI uit te voeren voor toegang tot andere Azure-services. In deze sectie wordt alleen het basisbesturingssysteem gebruikt. Zie Door het systeem toegewezen identiteit inschakelen in een containergroep voor een voorbeeld van het gebruik van de Azure CLI in [de container.](#enable-system-assigned-identity-on-a-container-group) 

De `--assign-identity` parameter geeft uw door de gebruiker toegewezen beheerde identiteit door aan de groep. Met de langlopende opdracht blijft de container actief. In dit voorbeeld wordt dezelfde resourcegroep gebruikt om de sleutelkluis te maken, maar u kunt een andere groep opgeven.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status ervan met de [opdracht az container show.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

De sectie in de uitvoer ziet er ongeveer als volgt uit, waarin wordt weergegeven dat `identity` de identiteit is ingesteld in de containergroep. De `principalID` onder is de `userAssignedIdentities` service-principal van de identiteit die u hebt gemaakt in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Door de gebruiker toegewezen identiteit gebruiken om een geheim op te halen uit de sleutelkluis

U kunt nu de beheerde identiteit in de container-instantie die wordt uitgevoerd, gebruiken om toegang te krijgen tot de sleutelkluis. Start eerst een bash-shell in de container:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Voer de volgende opdrachten uit in de bash-shell in de container. Voer de volgende opdracht uit om een toegangstoken Azure Active Directory te gebruiken voor verificatie bij de sleutelkluis:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Uitvoer:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Voer de volgende opdracht uit om het toegangtoken op te slaan in een variabele die in volgende opdrachten moet worden gebruikt om te verifiëren:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Gebruik nu het toegangstoken om te verifiëren bij de sleutelkluis en een geheim te lezen. Zorg ervoor dat u de naam van uw sleutelkluis vervangt in de URL (*https: \/ /mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Het antwoord ziet er ongeveer als volgt uit, met het geheim . In uw code zou u deze uitvoer parseren om het geheim te verkrijgen. Gebruik vervolgens het geheim in een volgende bewerking om toegang te krijgen tot een andere Azure-resource.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Voorbeeld 2: Een door het systeem toegewezen identiteit gebruiken voor toegang tot Azure Key Vault

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Door het systeem toegewezen identiteit inschakelen voor een containergroep

Voer de volgende [az container create-opdracht](/cli/azure/container#az_container_create) uit om een container-instantie te maken op basis van de microsoft-afbeelding. `azure-cli` Dit voorbeeld biedt een groep met één container die u interactief kunt gebruiken om de Azure CLI uit te voeren voor toegang tot andere Azure-services. 

De `--assign-identity` parameter zonder extra waarde maakt een door het systeem toegewezen beheerde identiteit voor de groep mogelijk. De identiteit is beperkt tot de resourcegroep van de containergroep. Met de langlopende opdracht blijft de container actief. In dit voorbeeld wordt dezelfde resourcegroep gebruikt voor het maken van de sleutelkluis, die binnen het bereik van de identiteit valt.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status ervan met de [opdracht az container show.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

De sectie in de uitvoer ziet er ongeveer als volgt uit, waarin wordt weergegeven dat een door het systeem toegewezen identiteit `identity` wordt gemaakt in Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Stel een variabele in op de waarde `principalId` van (de service-principal-id) van de identiteit, voor gebruik in latere stappen.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Containergroep toegang verlenen tot de sleutelkluis

Voer de volgende [az keyvault set-policy-opdracht](/cli/azure/keyvault) uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de door het systeem beheerde identiteit geheimen uit de sleutelkluis op halen:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Een containergroepidentiteit gebruiken om een geheim op te halen uit de sleutelkluis

U kunt nu de beheerde identiteit gebruiken om toegang te krijgen tot de sleutelkluis binnen de container-instantie die wordt uitgevoerd. Start eerst een bash-shell in de container:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Voer de volgende opdrachten uit in de bash-shell in de container. Meld u eerst aan bij de Azure CLI met behulp van de beheerde identiteit:

```bash
az login --identity
```

Haal het geheim uit de sleutelkluis op uit de container die wordt uitgevoerd:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

De waarde van het geheim wordt opgehaald:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Beheerde identiteit inschakelen met behulp Resource Manager sjabloon

Als u een beheerde identiteit in een containergroep wilt inschakelen met behulp van [Resource Manager sjabloon](container-instances-multi-container-group.md), stelt u de eigenschap van het object in met `identity` een `Microsoft.ContainerInstance/containerGroups` `ContainerGroupIdentity` -object. De volgende codefragmenten tonen de `identity` eigenschap die is geconfigureerd voor verschillende scenario's. Zie de [Resource Manager sjabloonverwijzing](/azure/templates/microsoft.containerinstance/containergroups). Geef minimaal `apiVersion` `2018-10-01` op.

### <a name="user-assigned-identity"></a>Door de gebruiker toegewezen identiteit

Een door de gebruiker toegewezen identiteit is een resource-id van het formulier:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

U kunt een of meer door de gebruiker toegewezen identiteiten inschakelen.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Door het systeem toegewezen identiteit

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Door het systeem en de gebruiker toegewezen identiteiten

In een containergroep kunt u zowel een door het systeem toegewezen identiteit als een of meer door de gebruiker toegewezen identiteiten inschakelen.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Beheerde identiteit inschakelen met behulp van een YAML-bestand

Als u een beheerde identiteit wilt inschakelen in een containergroep die is geïmplementeerd met behulp van een [YAML-bestand,](container-instances-multi-container-yaml.md)neemt u de volgende YAML op.
Geef minimaal `apiVersion` `2018-10-01` op.

### <a name="user-assigned-identity"></a>Door de gebruiker toegewezen identiteit

Een door de gebruiker toegewezen identiteit is een resource-id van het formulier 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

U kunt een of meer door de gebruiker toegewezen identiteiten inschakelen.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Door het systeem toegewezen identiteit

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Door het systeem en de gebruiker toegewezen identiteiten

In een containergroep kunt u zowel een door het systeem toegewezen identiteit als een of meer door de gebruiker toegewezen identiteiten inschakelen.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over beheerde identiteiten in Azure Container Instances en hoe u het volgende kunt doen:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een containergroep
> * De identiteit toegang verlenen tot een Azure-sleutelkluis
> * De beheerde identiteit gebruiken voor toegang tot een sleutelkluis vanuit een container die wordt uitgevoerd

* Meer informatie over [beheerde identiteiten voor Azure-resources.](../active-directory/managed-identities-azure-resources/index.yml)

* Zie een [Azure Go SDK-voorbeeld](https://medium.com/@samkreter/c98911206328) van het gebruik van een beheerde identiteit voor toegang tot een sleutelkluis vanuit Azure Container Instances.
