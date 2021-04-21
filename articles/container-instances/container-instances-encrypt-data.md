---
title: Implementatiegegevens versleutelen
description: Meer informatie over versleuteling van gegevens die persistent zijn voor uw containerresources en hoe u de gegevens versleutelt met een door de klant beheerde sleutel
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 23c81aeab3bf6e9ee7f2d89fbdf8def20dab4aa7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790847"
---
# <a name="encrypt-deployment-data"></a>Implementatiegegevens versleutelen

Bij het Azure Container Instances (ACI)-resources in de cloud verzamelt en persistente gegevens met betrekking tot uw containers. ACI versleutelt deze gegevens automatisch wanneer deze in de cloud worden opgeslagen. Met deze versleuteling worden uw gegevens beschermd om te voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie. ACI biedt u ook de mogelijkheid om deze gegevens te versleutelen met uw eigen sleutel, zodat u meer controle hebt over de gegevens met betrekking tot uw ACI-implementaties.

## <a name="about-aci-data-encryption"></a>Over ACI-gegevensversleuteling 

Gegevens in ACI worden versleuteld en ontsleuteld met 256-bits AES-versleuteling. Deze is ingeschakeld voor alle ACI-implementaties en u hoeft uw implementatie of containers niet te wijzigen om te profiteren van deze versleuteling. Dit omvat metagegevens over de implementatie, omgevingsvariabelen, sleutels die worden doorgegeven aan uw containers en logboeken die worden bewaard nadat uw containers zijn gestopt, zodat u ze nog steeds kunt zien. Versleuteling heeft geen invloed op de prestaties van uw containergroep en er zijn geen extra kosten voor versleuteling.

## <a name="encryption-key-management"></a>Versleutelingssleutelbeheer

U kunt vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw containergegevens of u kunt de versleuteling beheren met uw eigen sleutels. In de volgende tabel worden deze opties vergeleken: 

|    |    Door Microsoft beheerde sleutels     |     Door klant beheerde sleutels     |
|----|----|----|
|    **Versleutelings-/ontsleutelingsbewerkingen**    |    Azure    |    Azure    |
|    **Sleutelopslag**    |    Microsoft-sleutelopslag    |    Azure Key Vault    |
|    **Verantwoordelijkheid voor sleutelrotatie**    |    Microsoft    |    Klant    |
|    **Sleuteltoegang**    |    Alleen Microsoft    |    Microsoft, klant    |

De rest van het document bevat de stappen die nodig zijn om uw ACI-implementatiegegevens te versleutelen met uw sleutel (door de klant beheerde sleutel). 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Gegevens versleutelen met een door de klant beheerde sleutel

### <a name="create-service-principal-for-aci"></a>Service-principal voor ACI maken

De eerste stap is ervoor te zorgen dat aan uw [Azure-tenant](../active-directory/develop/quickstart-create-new-tenant.md) een service-principal is toegewezen voor het verlenen van machtigingen aan Azure Container Instances service. 

> [!IMPORTANT]
> Als u de volgende opdracht wilt uitvoeren en een service-principal wilt maken, controleert u of u machtigingen hebt om service-principals te maken in uw tenant.
>

Met de volgende CLI-opdracht wordt de ACI SP in uw Azure-omgeving ingesteld:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

In de uitvoer van het uitvoeren van deze opdracht ziet u een service-principal die is ingesteld met 'displayName': 'Azure Container Instance Service'.

Als u de service-principal niet kunt maken:
* bevestig dat u machtigingen hebt om dit te doen in uw tenant
* controleer of er al een service-principal in uw tenant bestaat voor implementatie in ACI. U kunt dit doen door die `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` service-principal uit te uitvoeren en in plaats daarvan te gebruiken

### <a name="create-a-key-vault-resource"></a>Een Key Vault-resource maken

Maak een Azure-sleutelkluis met [Azure Portal](../key-vault/general/quick-create-portal.md), [Azure CLI](../key-vault/general/quick-create-cli.md) of [Azure PowerShell](../key-vault/general/quick-create-powershell.md).

Gebruik de volgende richtlijnen voor de eigenschappen van uw sleutelkluis: 
* Naam: geef een unieke naam op. 
* Abonnement: Kies een abonnement.
* Kies onder Resourcegroep een bestaande resourcegroep of maak een nieuwe en voer de naam van een resourcegroep in.
* Kies een locatie in de vervolgkeuzelijst Locatie.
* U kunt de andere opties op de standaardwaarden laten staan of kiezen op basis van aanvullende vereisten.

> [!IMPORTANT]
> Wanneer u door de klant beheerde sleutels gebruikt om een ACI-implementatiesjabloon te versleutelen, is het raadzaam de volgende twee eigenschappen in te stellen voor de sleutelkluis, Soft Delete en Do Not Purge. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met behulp van PowerShell of Azure CLI op een nieuwe of bestaande sleutelkluis.

### <a name="generate-a-new-key"></a>Een nieuwe sleutel genereren 

Nadat de sleutelkluis is gemaakt, gaat u naar de resource in Azure Portal. Klik in het linkernavigatiemenu van de resourceblade onder Instellingen op **Sleutels.** Klik in de weergave voor Sleutels op Genereren/importeren om een nieuwe sleutel te genereren. Gebruik een unieke naam voor deze sleutel en andere voorkeuren op basis van uw vereisten. 

![Een nieuwe sleutel genereren](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Toegangsbeleid instellen

Maak een nieuw toegangsbeleid om de ACI-service toegang te geven tot uw sleutel.

* Zodra uw sleutel is gegenereerd, klikt u op de resourceblade van de sleutelkluis onder Instellingen op **Toegangsbeleid.**
* Klik op de pagina Toegangsbeleid voor uw sleutelkluis op **Toegangsbeleid toevoegen.**
* Stel de *sleutelmachtigingen in om* sleutelmachtigingen **voor sleutelsets** op te nemen en **uit** ![ te pakken](./media/container-instances-encrypt-data/set-key-permissions.png)
* Selecteer *voor Principal selecteren* de optie Azure Container Instance **Service**
* Klik **onderaan** op Toevoegen 

Het toegangsbeleid wordt nu in het toegangsbeleid van uw sleutelkluis weer geven.

![Nieuw toegangsbeleid](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Uw JSON-implementatiesjabloon wijzigen

> [!IMPORTANT]
> Het versleutelen van implementatiegegevens met een door de klant beheerde sleutel is beschikbaar in de nieuwste API-versie (2019-12-01) die momenteel wordt uitgerold. Geef deze API-versie op in uw implementatiesjabloon. Als u hier problemen mee hebt, kunt u contact op met Ondersteuning voor Azure.

Zodra de sleutelkluissleutel en het toegangsbeleid zijn ingesteld, voegt u de volgende eigenschappen toe aan uw ACI-implementatiesjabloon. Meer informatie over het implementeren van [ACI-resources](./container-instances-multi-container-group.md)met een sjabloon in de Zelfstudie: Een groep met meerdere containers implementeren met behulp van een Resource Manager sjabloon . 
* Stel `resources` onder in op `apiVersion` `2019-12-01` .
* Voeg onder de sectie eigenschappen van de containergroep van de implementatiesjabloon een `encryptionProperties` toe die de volgende waarden bevat:
  * `vaultBaseUrl`: de DNS-naam van uw sleutelkluis vindt u op de overzichtsblade van de sleutelkluisresource in de portal
  * `keyName`: de naam van de sleutel die eerder is gegenereerd
  * `keyVersion`: de huidige versie van de sleutel. U kunt dit vinden door op de sleutel zelf te klikken (onder Sleutels in de sectie Instellingen van uw sleutelkluisresource)
* Voeg onder de eigenschappen van de containergroep een `sku` eigenschap toe met de waarde `Standard` . De `sku` eigenschap is vereist in API-versie 2019-12-01.

Het volgende sjabloonfragment bevat de volgende aanvullende eigenschappen voor het versleutelen van implementatiegegevens:

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

Hieronder volgt een volledige sjabloon, aangepast aan de sjabloon in [Zelfstudie: Een groep met meerdere containers](./container-instances-multi-container-group.md)implementeren met behulp van Resource Manager sjabloon . 

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

Als u het sjabloonbestand op uw bureaublad hebt gemaakt en bewerkt, kunt u het uploaden naar uw Cloud Shell map door het bestand naar het bestand te slepen. 

Een resourcegroep maken met de opdracht [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

U ontvangt binnen enkele seconden een eerste reactie van Azure. Zodra de implementatie is voltooid, worden alle gegevens die daaraan zijn gerelateerd, door de ACI-service versleuteld met de sleutel die u hebt opgegeven.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group/#az_deployment_group_create
