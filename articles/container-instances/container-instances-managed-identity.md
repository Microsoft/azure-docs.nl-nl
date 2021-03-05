---
title: Beheerde identiteit inschakelen in container groep
description: Meer informatie over het inschakelen van een beheerde identiteit in Azure Container Instances die kan worden geverifieerd met andere Azure-Services
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: a0d029e39122ca7bb858103f4d7f88e2536850d5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198316"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Beheerde identiteiten gebruiken met Azure Container Instances

Gebruik [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) voor het uitvoeren van code in azure container instances die samenwerkt met andere Azure-Services, zonder geheimen of referenties in code te behouden. De functie biedt een Azure Container Instances implementatie met een automatisch beheerde identiteit in Azure Active Directory.

In dit artikel vindt u meer informatie over beheerde identiteiten in Azure Container Instances en:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een container groep
> * De identiteit toegang verlenen tot een Azure-sleutel kluis
> * De beheerde identiteit gebruiken om toegang te krijgen tot een sleutel kluis van een actieve container

De voor beelden aanpassen om identiteiten in Azure Container Instances in te scha kelen en te gebruiken voor toegang tot andere Azure-Services. Deze voor beelden zijn interactief. In de praktijk voeren uw container installatie kopieën echter code uit voor toegang tot Azure-Services.
 
> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt. Beheerde identiteiten op Azure Container Instances worden momenteel alleen ondersteund met Linux-containers en nog niet met Windows-containers.

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Gebruik een beheerde identiteit in een actieve container om te verifiëren bij elke [service die ondersteuning biedt voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) zonder dat u referenties in uw container code hoeft te beheren. Voor services die geen ondersteuning bieden voor AD-verificatie, kunt u geheimen opslaan in een Azure-sleutel kluis en de beheerde identiteit gebruiken voor toegang tot de sleutel kluis om referenties op te halen. Zie [Wat is beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie over het gebruik van een beheerde identiteit.

### <a name="enable-a-managed-identity"></a>Een beheerde identiteit inschakelen

 Wanneer u een container groep maakt, moet u een of meer beheerde identiteiten inschakelen door een eigenschap [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) in te stellen. U kunt beheerde identiteiten ook inschakelen of bijwerken nadat een container groep is uitgevoerd. deze bewerking zorgt ervoor dat de container groep opnieuw wordt gestart. Als u de identiteiten voor een nieuwe of bestaande container groep wilt instellen, gebruikt u de Azure CLI, een resource manager-sjabloon, een YAML-bestand of een ander Azure-hulp programma. 

Azure Container Instances ondersteunt beide typen beheerde Azure-identiteiten: aan de gebruiker toegewezen en het systeem toegewezen. In een container groep kunt u een door het systeem toegewezen identiteit, een of meer door de gebruiker toegewezen identiteiten of beide typen identiteiten inschakelen. Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u het [overzicht](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Een beheerde identiteit gebruiken

Als u een beheerde identiteit wilt gebruiken, moet aan de identiteit toegang worden verleend tot een of meer Azure-service resources (zoals een web-app, een sleutel kluis of een opslag account) in het abonnement. Het gebruik van een beheerde identiteit in een container die wordt uitgevoerd, is vergelijkbaar met het gebruik van een identiteit in een Azure-VM. Zie de VM-richt lijnen voor het gebruik van een [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL of Azure cli](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)of de [Azure sdk's](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### <a name="limitations"></a>Beperkingen

* Op dit moment kunt u geen beheerde identiteit gebruiken in een container groep die is geïmplementeerd in een virtueel netwerk.
* U kunt een beheerde identiteit niet gebruiken voor het ophalen van een installatie kopie van Azure Container Registry bij het maken van een container groep. De identiteit is alleen beschikbaar in een actieve container.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-an-azure-key-vault"></a>Een Azure-sleutel kluis maken

In de voor beelden in dit artikel wordt een beheerde identiteit in Azure Container Instances gebruikt om toegang te krijgen tot een geheim van Azure sleutel kluis. 

Maak eerst een resourcegroep met de naam *myResourceGroup* in de locatie *eastus* met behulp van de opdracht [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create) om een sleutel kluis te maken. Zorg ervoor dat u een unieke naam voor de sleutel kluis opgeeft. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Sla een voor beeld geheim op in de sleutel kluis met behulp van de opdracht [AZ Key kluis Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Ga door met de volgende voor beelden om toegang te krijgen tot de sleutel kluis met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Voor beeld 1: een door de gebruiker toegewezen identiteit gebruiken om toegang te krijgen tot Azure sleutel kluis

### <a name="create-an-identity"></a>Een identiteit maken

Maak eerst een identiteit in uw abonnement met behulp van de opdracht [AZ Identity Create](/cli/azure/identity#az-identity-create) . U kunt dezelfde resource groep gebruiken als voor het maken van de sleutel kluis of een andere gebruiken.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Als u de identiteit in de volgende stappen wilt gebruiken, gebruikt u de opdracht [AZ Identity show](/cli/azure/identity#az-identity-show) om de Service-Principal-id en resource-id van de identiteit op te slaan in variabelen.

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Aan de gebruiker toegewezen identiteits toegang verlenen tot de sleutel kluis

Voer de volgende [AZ-set-Policy](/cli/azure/keyvault) opdracht uit om een toegangs beleid in te stellen op de sleutel kluis. In het volgende voor beeld is het mogelijk dat de door de gebruiker toegewezen identiteit geheimen van de sleutel kluis krijgt:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Door de gebruiker toegewezen identiteit inschakelen voor een container groep

Voer de volgende opdracht [AZ container Create](/cli/azure/container#az-container-create) uit om een container exemplaar te maken op basis van de installatie kopie van micro soft `azure-cli` . Dit voor beeld bevat een groep met één container die u interactief kunt gebruiken om de Azure CLI uit te voeren voor toegang tot andere Azure-Services. In deze sectie wordt alleen het basis besturingssysteem gebruikt. Zie voor een voor beeld van het gebruik van de Azure CLI in de container de door [het systeem toegewezen identiteit inschakelen voor een container groep](#enable-system-assigned-identity-on-a-container-group). 

De `--assign-identity` para meter geeft uw door de gebruiker toegewezen beheerde identiteit door aan de groep. De langlopende opdracht zorgt ervoor dat de container wordt uitgevoerd. In dit voor beeld wordt dezelfde resource groep gebruikt als voor het maken van de sleutel kluis, maar u kunt een andere naam opgeven.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status met de opdracht [AZ container show](/cli/azure/container#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

De `identity` sectie in de uitvoer ziet er ongeveer als volgt uit, waarin wordt weer gegeven dat de identiteit is ingesteld in de container groep. De `principalID` onder `userAssignedIdentities` is de service-principal van de identiteit die u hebt gemaakt in azure Active Directory:

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

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Door gebruiker toegewezen identiteit gebruiken om geheim te halen uit sleutel kluis

U kunt nu de beheerde identiteit binnen het actieve container exemplaar gebruiken voor toegang tot de sleutel kluis. Start eerst een bash-shell in de container:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Voer de volgende opdrachten uit in de bash-shell in de container. Voer de volgende opdracht uit om een toegangs token te verkrijgen voor het gebruik van Azure Active Directory om te verifiëren bij de sleutel kluis:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Uitvoer:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Voer de volgende opdracht uit om het toegangs token op te slaan in een variabele die in de volgende opdrachten moet worden gebruikt om te verifiëren:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Gebruik nu het toegangs token om te verifiëren bij de sleutel kluis en lees een geheim. Zorg ervoor dat u de naam van de sleutel kluis in de URL (*https: \/ /mykeyvault.Vault.Azure.net/...*) vervangt:

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Het antwoord ziet er ongeveer als volgt uit, waarin het geheim wordt weer gegeven. In uw code parseert u deze uitvoer om het geheim te verkrijgen. Gebruik vervolgens het geheim in een volgende bewerking om toegang te krijgen tot een andere Azure-resource.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Voor beeld 2: een door het systeem toegewezen identiteit gebruiken om toegang te krijgen tot de Azure sleutel kluis

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Door het systeem toegewezen identiteit inschakelen voor een container groep

Voer de volgende opdracht [AZ container Create](/cli/azure/container#az-container-create) uit om een container exemplaar te maken op basis van de installatie kopie van micro soft `azure-cli` . Dit voor beeld bevat een groep met één container die u interactief kunt gebruiken om de Azure CLI uit te voeren voor toegang tot andere Azure-Services. 

`--assign-identity`Met de para meter zonder extra waarde kan een door het systeem toegewezen beheerde identiteit voor de groep worden ingeschakeld. De identiteit ligt binnen het bereik van de resource groep van de container groep. De langlopende opdracht zorgt ervoor dat de container wordt uitgevoerd. In dit voor beeld wordt dezelfde resource groep gebruikt voor het maken van de sleutel kluis die zich binnen het bereik van de identiteit bevindt.

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

Binnen enkele seconden krijgt u een reactie van de Azure-CLI die aangeeft dat de implementatie is voltooid. Controleer de status met de opdracht [AZ container show](/cli/azure/container#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

De `identity` sectie in de uitvoer ziet er ongeveer als volgt uit, waarin wordt weer gegeven dat er een door het systeem toegewezen identiteit is gemaakt in azure Active Directory:

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

Stel een variabele in op de waarde van `principalId` (de Service-Principal-id) van de identiteit, die u in latere stappen moet gebruiken.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Container groep toegang verlenen tot de sleutel kluis

Voer de volgende [AZ-set-Policy](/cli/azure/keyvault) opdracht uit om een toegangs beleid in te stellen op de sleutel kluis. In het volgende voor beeld is het mogelijk dat de door het systeem beheerde identiteit geheimen van de sleutel kluis krijgt:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>De identiteit van een container groep gebruiken om geheim te halen uit de sleutel kluis

U kunt nu de beheerde identiteit gebruiken om toegang te krijgen tot de sleutel kluis binnen het actieve container exemplaar. Start eerst een bash-shell in de container:

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

Haal in de actieve container het geheim op uit de sleutel kluis:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

De waarde van het geheim wordt opgehaald:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Beheerde identiteit inschakelen met Resource Manager-sjabloon

Als u een beheerde identiteit in een container groep wilt inschakelen met een [Resource Manager-sjabloon](container-instances-multi-container-group.md), stelt u de `identity` eigenschap van het `Microsoft.ContainerInstance/containerGroups` object in met een- `ContainerGroupIdentity` object. De volgende fragmenten bevatten de `identity` eigenschap die voor verschillende scenario's is geconfigureerd. Zie de [Naslag informatie voor Resource Manager-sjablonen](/azure/templates/microsoft.containerinstance/containergroups). Geef een minimum `apiVersion` van op `2018-10-01` .

### <a name="user-assigned-identity"></a>Door gebruiker toegewezen identiteit

Een door de gebruiker toegewezen identiteit is een resource-ID van het formulier:

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

### <a name="system--and-user-assigned-identities"></a>Systeem-en door de gebruiker toegewezen identiteiten

In een container groep kunt u een door het systeem toegewezen identiteit en een of meer door de gebruiker toegewezen identiteiten inschakelen.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Beheerde identiteit inschakelen met YAML-bestand

Als u een beheerde identiteit wilt inschakelen in een container groep die is geïmplementeerd met behulp van een [yaml-bestand](container-instances-multi-container-yaml.md), neemt u de volgende YAML op.
Geef een minimum `apiVersion` van op `2018-10-01` .

### <a name="user-assigned-identity"></a>Door gebruiker toegewezen identiteit

Een door de gebruiker toegewezen identiteit is een resource-ID van het formulier 

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

### <a name="system--and-user-assigned-identities"></a>Systeem-en door de gebruiker toegewezen identiteiten

In een container groep kunt u een door het systeem toegewezen identiteit en een of meer door de gebruiker toegewezen identiteiten inschakelen.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over beheerde identiteiten in Azure Container Instances en leert u het volgende:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een container groep
> * De identiteit toegang verlenen tot een Azure-sleutel kluis
> * De beheerde identiteit gebruiken om toegang te krijgen tot een sleutel kluis van een actieve container

* Meer informatie over [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/index.yml).

* Bekijk een [Azure go SDK-voor beeld](https://medium.com/@samkreter/c98911206328) van het gebruik van een beheerde identiteit voor toegang tot een sleutel kluis van Azure container instances.
