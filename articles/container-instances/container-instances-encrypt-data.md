---
title: Implementatiegegevens versleutelen
description: Meer informatie over het versleutelen van gegevens die zijn opgeslagen voor de resources van de container instantie en hoe u de gegevens versleutelt met een door de klant beheerde sleutel
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1b73ce5c994231a1c7b2f26ad702f2ad5880ba44
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686273"
---
# <a name="encrypt-deployment-data"></a>Implementatiegegevens versleutelen

Bij het uitvoeren van Azure Container Instances (ACI)-resources in de cloud worden met de ACI-service gegevens verzameld en persistent gemaakt met betrekking tot uw containers. Met ACI worden deze gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Deze versleuteling beveiligt uw gegevens om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. ACI biedt u ook de mogelijkheid om deze gegevens te versleutelen met uw eigen sleutel, zodat u meer controle hebt over de gegevens met betrekking tot uw ACI-implementaties.

## <a name="about-aci-data-encryption"></a>Over ACI-gegevens versleuteling 

Gegevens in ACI worden versleuteld en ontsleuteld met 256-bits AES-versleuteling. Deze functie is ingeschakeld voor alle ACI-implementaties en u hoeft uw implementatie of containers niet te wijzigen om te profiteren van deze versleuteling. Dit omvat meta gegevens over de implementatie, omgevings variabelen, sleutels die worden door gegeven aan uw containers en logboeken die persistent zijn gemaakt nadat de containers zijn gestopt, zodat u ze nog steeds kunt zien. Versleuteling heeft geen invloed op de prestaties van de container groep en er zijn geen extra kosten voor versleuteling.

## <a name="encryption-key-management"></a>Versleutelings sleutel beheer

U kunt gebruikmaken van door micro soft beheerde sleutels voor het versleutelen van de container gegevens of u kunt de versleuteling beheren met uw eigen sleutels. De volgende tabel vergelijkt deze opties: 

|    |    Door Microsoft beheerde sleutels     |     Door klant beheerde sleutels     |
|----|----|----|
|    **Bewerkingen voor versleuteling/ontsleuteling**    |    Azure    |    Azure    |
|    **Sleutel opslag**    |    Micro soft-sleutel archief    |    Azure Key Vault    |
|    **Verantwoordelijkheid voor sleutel rotatie**    |    Microsoft    |    Klant    |
|    **Sleutel toegang**    |    Alleen micro soft    |    Micro soft, klant    |

In de rest van het document worden de stappen beschreven die nodig zijn voor het versleutelen van uw ACI-implementatie gegevens met uw sleutel (door de klant beheerde sleutel). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Gegevens versleutelen met een door de klant beheerde sleutel

### <a name="create-service-principal-for-aci"></a>Een service-principal maken voor ACI

De eerste stap is om ervoor te zorgen dat uw [Azure-Tenant](../active-directory/develop/quickstart-create-new-tenant.md) beschikt over een service-principal die is toegewezen voor het verlenen van machtigingen aan de Azure container instances-service. 

> [!IMPORTANT]
> Als u de volgende opdracht wilt uitvoeren en een Service-Principal wilt maken, controleert u of u gemachtigd bent om service-principals in uw Tenant te maken.
>

Met de volgende CLI-opdracht wordt de ACI-SP in uw Azure-omgeving ingesteld:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Bij de uitvoer van deze opdracht moet u een Service-Principal weer geven die is ingesteld met ' displayName ': ' Azure container instance-service '.

Als u de Service-Principal niet kunt maken, doet u het volgende:
* Bevestig dat u gemachtigd bent om dit te doen in uw Tenant
* Controleer of er al een Service-Principal in uw Tenant bestaat voor de implementatie naar ACI. U kunt dit doen door `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` deze service-principal uit te voeren en te gebruiken

### <a name="create-a-key-vault-resource"></a>Een Key Vault-resource maken

Maak een Azure Key Vault met behulp van [Azure Portal](../key-vault/general/quick-create-portal.md), [Azure cli](../key-vault/general/quick-create-cli.md)of [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

Gebruik de volgende richt lijnen voor de eigenschappen van uw sleutel kluis: 
* Naam: geef een unieke naam op. 
* Abonnement: Kies een abonnement.
* Onder resource groep kiest u een bestaande resource groep of maakt u een nieuwe en voert u de naam van een resource groep in.
* Kies een locatie in de vervolgkeuzelijst Locatie.
* U kunt de standaard waarden van de andere opties wijzigen of kiezen op basis van aanvullende vereisten.

> [!IMPORTANT]
> Wanneer door de klant beheerde sleutels worden gebruikt voor het versleutelen van een ACI-implementatie sjabloon, wordt aangeraden de volgende twee eigenschappen in te stellen op de sleutel kluis, zacht verwijderen en niet leeg te maken. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of Azure CLI in een nieuwe of bestaande sleutel kluis.

### <a name="generate-a-new-key"></a>Een nieuwe sleutel genereren 

Als uw sleutel kluis is gemaakt, gaat u naar de resource in Azure Portal. Klik in het navigatie menu aan de linkerkant van de Blade resource onder instellingen op **sleutels**. Klik in de weer gave voor ' sleutels ' op genereren/importeren ' om een nieuwe sleutel te genereren. Gebruik een unieke naam voor deze sleutel en andere voor keuren op basis van uw vereisten. 

![Een nieuwe sleutel genereren](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Toegangs beleid instellen

Maak een nieuw toegangs beleid om de ACI-service toegang tot uw sleutel te geven.

* Als uw sleutel is gegenereerd, klikt u op de Blade sleutel kluis resource onder instellingen op **toegangs beleid**.
* Klik op **toegangs beleid toevoegen** op de pagina toegangs beleid voor uw sleutel kluis.
* Stel de *sleutel machtigingen* in om sleutel machtigingen voor **Get** en **Unwrap** op te geven ![](./media/container-instances-encrypt-data/set-key-permissions.png)
* Selecteer voor *Select Principal* de **service Azure container instance**
* Klik onderaan op **toevoegen** 

Het toegangs beleid wordt nu weer gegeven in het toegangs beleid van uw sleutel kluis.

![Nieuw toegangs beleid](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>De JSON-implementatie sjabloon wijzigen

> [!IMPORTANT]
> Het versleutelen van implementatie gegevens met een door de klant beheerde sleutel is beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt geïmplementeerd. Geef deze API-versie op in uw implementatie sjabloon. Neem contact op met de ondersteuning van Azure als u problemen ondervindt.

Wanneer de sleutel kluis sleutel en het toegangs beleid zijn ingesteld, voegt u de volgende eigenschappen toe aan uw ACI-implementatie sjabloon. Meer informatie over het implementeren van ACI-resources met een sjabloon in de [zelf studie: een groep met meerdere containers implementeren met behulp van een resource manager-sjabloon](./container-instances-multi-container-group.md). 
* `resources`Stel onder in `apiVersion` op `2019-12-01` .
* Voeg onder de sectie eigenschappen van container groep van de implementatie sjabloon een toe `encryptionProperties` , die de volgende waarden bevat:
  * `vaultBaseUrl`: de DNS-naam van uw sleutel kluis kunt u vinden op de Blade overzicht van de sleutel kluis bron in de portal
  * `keyName`: de naam van de sleutel die u eerder hebt gegenereerd
  * `keyVersion`: de huidige versie van de sleutel. U kunt dit vinden door te klikken op de sleutel zelf (onder sleutels in de sectie instellingen van uw sleutel kluis resource).
* Voeg onder de eigenschappen van de container groep een `sku` eigenschap met waarde toe `Standard` . De `sku` eigenschap is vereist in API-versie 2019-12-01.

In het volgende sjabloon fragment worden de volgende aanvullende eigenschappen weer gegeven voor het versleutelen van implementatie gegevens:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Hieronder volgt een volledige sjabloon, aangepast aan de hand van de sjabloon in [zelf studie: een groep met meerdere containers implementeren met een resource manager-sjabloon](./container-instances-multi-container-group.md). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Uw resources implementeren

Als u het sjabloon bestand op uw bureau blad hebt gemaakt en bewerkt, kunt u het uploaden naar uw Cloud Shell Directory door het bestand naar de map te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Zodra de implementatie is voltooid, worden alle gegevens die betrekking hebben op deze door de ACI-service bewaard, versleuteld met de sleutel die u hebt ingevoerd.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
