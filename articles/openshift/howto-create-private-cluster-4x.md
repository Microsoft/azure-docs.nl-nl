---
title: Een persoonlijk Azure Red Hat open Shift 4-cluster maken
description: Meer informatie over het maken van een Azure Red Hat open Shift private-cluster waarop open Shift 4 wordt uitgevoerd
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sakthi-vetrivel
ms.author: suvetriv
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 5a6eafa6b2380ee61c0de2e329a1751ec7d59c1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216068"
---
# <a name="create-an-azure-red-hat-openshift-4-private-cluster"></a>Een persoonlijk Azure Red Hat open Shift 4-cluster maken

In dit artikel maakt u een voor bereiding van uw omgeving voor het maken van Azure Red Hat open Shift private-clusters waarop open Shift 4 wordt uitgevoerd. U leert het volgende:

> [!div class="checklist"]
> * De vereisten instellen en het vereiste virtuele netwerk en subnetten maken
> * Een cluster met een persoonlijk API-server eindpunt en een persoonlijke ingangs controller implementeren

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="register-the-resource-providers"></a>De resourceproviders registreren

1. Als u meerdere Azure-abonnementen hebt, geeft u de relevante abonnements-id op:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registreer de resourceprovider `Microsoft.RedHatOpenShift`:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```

1. Registreer de resourceprovider `Microsoft.Compute`:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```

1. Registreer de resourceprovider `Microsoft.Storage`:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Een pull-geheim voor Red Hat ophalen (optioneel)

Met een pull-geheim van Red Hat kan uw cluster toegang krijgen tot Red Hat-containerregisters en aanvullende inhoud. Deze stap is optioneel, maar wordt wel aanbevolen.

1. **[Ga naar uw Red Hat open Shift cluster manager-Portal en meld u aan](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) .**

   U moet zich aanmelden bij uw Red Hat-account of een nieuw Red Hat-account maken met uw zakelijke e-mailadres en de algemene voorwaarden accepteren.

2. **Klik op pull-geheim downloaden.**

Bewaar het opgeslagen `pull-secret.txt`-bestand op een veilige plek; het wordt gebruikt bij het maken van elk cluster.

Wanneer u de opdracht `az aro create` uitvoert, kunt u verwijzen naar uw pull-geheim met behulp van de parameter `--pull-secret @pull-secret.txt`. Voer `az aro create` uit vanuit de map waarin u het `pull-secret.txt`-bestand hebt opgeslagen. Vervang anders `@pull-secret.txt` door `@<path-to-my-pull-secret-file`.

Als u uw pull-geheim kopieert of ernaar verwijst in andere scripts, moet uw pull-geheim worden geformatteerd als een geldige JSON-tekenreeks.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk met twee lege subnetten maken

Nu gaat u een virtueel netwerk met twee lege subnetten maken.

1. **Stel de volgende variabelen in.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    # the name of the resource group where you want to create your cluster
   CLUSTER=aro-cluster             # the name of your cluster
   ```

1. **Een resourcegroep maken**

    Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Wanneer u een resourcegroep maakt, wordt u gevraagd een locatie op te geven. Op deze locatie zijn de metagegevens van de resourcegroep opgeslagen. Dit is ook de locatie waar uw resources worden uitgevoerd in Azure als u tijdens het maken van de resource geen andere regio opgeeft. Maak een resourcegroep met de opdracht [az group create][az-group-create].

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    In de volgende voorbeelduitvoer ziet u dat de resourcegroep is gemaakt:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Maak een virtueel netwerk.**

    Azure Red Hat OpenShift-clusters met OpenShift 4 vereisen een virtueel netwerk met twee lege subnetten voor de hoofd- en werkknooppunten.

    Maak een nieuw virtueel netwerk in de resourcegroep die u eerder hebt gemaakt.

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    In de volgende voorbeelduitvoer ziet u dat het virtuele netwerk is gemaakt:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Voeg een leeg subnet toe voor de hoofdknooppunten.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Voeg een leeg subnet toe voor de werkknooppunten.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Beleid voor privé-eindpunten van subnet uitschakelen](../private-link/disable-private-link-service-network-policy.md) op het hoofdsubnet.** Dit is vereist om verbinding te kunnen maken met het cluster en het te beheren.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Het cluster maken

Voer de volgende opdracht uit om een cluster te maken. U kunt eventueel [uw pull-geheim van Red Hat doorvoeren](#get-a-red-hat-pull-secret-optional), zodat uw cluster toegang krijgt tot Red Hat-containerregisters en aanvullende inhoud.

>[!NOTE]
> Als u de opdrachten kopieert en plakt en een van de optionele parameters gebruikt, moet u de oorspronkelijke hashtags en de navolgende opmerkingstekst verwijderen. Sluit ook het argument op de voorgaande regel van de opdracht af met een afsluitende backslash.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  --apiserver-visibility Private \
  --ingress-visibility Private
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

Nadat de `az aro create`-opdracht is uitgevoerd, duurt het doorgaans ongeveer 35 minuten om een cluster te maken.

>[!IMPORTANT]
> Als u ervoor kiest om een aangepast domein op te geven, bijvoorbeeld **foo.example.com**, is de OpenShift-console beschikbaar op een URL, zoals `https://console-openshift-console.apps.foo.example.com`, in plaats van in het ingebouwde domein `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> Open SHIFT maakt standaard gebruik van zelfondertekende certificaten voor alle routes die zijn gemaakt op `*.apps.<random>.<location>.aroapp.io` .  Als u aangepaste DNS kiest nadat u verbinding hebt gemaakt met het cluster, moet u de open Shift-documentatie volgen om [een aangepaste certificerings instantie voor uw ingangs controller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) en [aangepaste certificerings instantie voor uw API-server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html)te configureren.

## <a name="connect-to-the-private-cluster"></a>Verbinding maken met het persoonlijke cluster

U kunt zich aanmelden bij het cluster met behulp van de `kubeadmin`-gebruiker.  Voer de volgende opdracht uit om het wachtwoord voor de `kubeadmin`-gebruiker te vinden.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

In de volgende voorbeeld ziet u dat het wachtwoord in `kubeadminPassword` is.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

U kunt de URL van de clusterconsole vinden door de volgende opdracht uit te voeren, die eruit ziet als `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

>[!IMPORTANT]
> Als u verbinding wilt maken met een persoonlijk Azure Red Hat open Shift-cluster, moet u de volgende stap uitvoeren vanaf een host die zich bevindt in de Virtual Network die u hebt gemaakt of in een Virtual Network dat is gekoppeld aan de Virtual Network het [cluster is geïmplementeerd](../virtual-network/virtual-network-peering-overview.md) .

Start de console-URL in een browser en meld u aan met de referenties van `kubeadmin`.

![Scherm opname van het aanmeldings scherm van Azure Red Hat open SHIFT.](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>De OpenShift CLI installeren

Zodra u bent aangemeld bij de OpenShift-webconsole, klikt u op **?** in de rechterbovenhoek en klikt u vervolgens op **Opdrachtregel hulpprogrammarogramma's**. Download de versie die geschikt is voor uw computer.

![Afbeelding toont het aanmeldings scherm van Azure Red Hat open Shift](media/aro4-download-cli.png)

U kunt ook de meest recente versie van de CLI die geschikt is voor uw computer downloaden van <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>.

## <a name="connect-using-the-openshift-cli"></a>Verbinding maken met behulp van OpenShift CLI

Haal het adres van de API-server op.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

>[!IMPORTANT]
> Als u verbinding wilt maken met een persoonlijk Azure Red Hat open Shift-cluster, moet u de volgende stap uitvoeren vanaf een host die zich bevindt in de Virtual Network die u hebt gemaakt of in een Virtual Network dat is gekoppeld aan de Virtual Network het [cluster is geïmplementeerd](../virtual-network/virtual-network-peering-overview.md) .

Meld u aan bij de API-server van het OpenShift-cluster met de volgende opdracht. Vervang **\<kubeadmin password>** door het wachtwoord dat u zojuist hebt opgehaald.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een Azure Red Hat open Shift-cluster met open Shift 4 geïmplementeerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De vereisten instellen en het vereiste virtuele netwerk en subnetten maken
> * Een cluster implementeren
> * Verbinding maken met het cluster met behulp van de `kubeadmin` gebruiker

Ga naar het volgende artikel voor meer informatie over het configureren van het cluster voor verificatie met behulp van Azure Active Directory.


* [Verificatie met Azure Active Directory configureren via de opdracht regel](configure-azure-ad-cli.md)


* [Verificatie met Azure Active Directory configureren met behulp van de Azure Portal en open Shift web console](configure-azure-ad-cli.md)
