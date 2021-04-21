---
title: Service-principals voor AKS (Azure Kubernetes Services)
description: Een Azure Active Directory-service-principal maken en beheren voor een cluster in AKS (Azure Kubernetes Service)
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2f32ce96097e008ac1100c62c04b6bb7001ae972
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779629"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Service-principals met AKS (Azure Kubernetes Service)

Voor interactie met Azure-API's is voor een AKS-cluster een [service-principal van Azure Active Directory (AD)][aad-service-principal] of een [beheerde identiteit vereist.](use-managed-identity.md) Een service-principal of beheerde identiteit is nodig voor het dynamisch maken en beheren van andere Azure-resources, zoals een Azure load balancer of containerregister (ACR).

In dit artikel ziet u hoe u een service-principal voor uw AKS-clusters maakt en gebruikt.

## <a name="before-you-begin"></a>Voordat u begint

Als u een service-principal voor Azure AD wilt maken, moet u beschikken over machtigingen voor het registreren van een toepassing bij de Azure AD-tenant. U moet ook machtigingen hebben om de toepassing aan een rol toe te wijzen in uw abonnement. Als u niet beschikt over de benodigde machtigingen, moet u mogelijk de Azure AD- of abonnementsbeheerder vragen om de benodigde machtigingen toe te wijzen, of vooraf een service-principal maken voor gebruik met het AKS-cluster.

Als u een service-principal van een andere Azure AD-tenant gebruikt, zijn er aanvullende overwegingen met het oog op de machtigingen die beschikbaar zijn wanneer u het cluster implementeert. Mogelijk hebt u niet de juiste machtigingen om mapgegevens te lezen en te schrijven. Zie What [are the default user permissions in Azure Active Directory? (Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory?][azure-ad-permissions]

Ook moet Azure CLI versie 2.0.59 of hoger zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="automatically-create-and-use-a-service-principal"></a>Automatisch een service-principal maken en gebruiken

Wanneer u een AKS-cluster in de Azure Portal maakt of de opdracht [az aks create][az-aks-create] gebruikt, kan Azure automatisch een service-principal genereren.

In het volgende Azure CLI-voorbeeld is geen service-principal opgegeven. In dit scenario maakt de Azure CLI een service-principal voor het AKS-cluster. Om deze bewerking te kunnen voltooien, moet uw Azure-account beschikken over de juiste rechten voor het maken van een service-principal.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Handmatig een service-principal maken

Gebruik de opdracht [az ad sp create-for-rbac][az-ad-sp-create] als u handmatig een service-principal wilt maken met de Azure CLI. In het volgende voorbeeld wordt met de parameter `--skip-assignment` voorkomen dat eventuele extra standaardtoewijzingen worden toegewezen:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer uw eigen `appId` en `password`. Deze waarden worden gebruikt wanneer u in de volgende sectie een AKS-cluster maakt.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Een service-principal opgeven voor een AKS-cluster

Als u een bestaande service-principal wilt gebruiken wanneer u een AKS-cluster maakt met de opdracht [az aks create][az-aks-create], gebruikt u de parameters `--service-principal` en `--client-secret` om de `appId` en het `password` uit de uitvoer van de opdracht [az ad sp create-for-rbac][az-ad-sp-create] op te geven:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Als u een bestaande service-principal met aangepast geheim gebruikt, moet u ervoor zorgen dat het geheim niet langer is dan 190 bytes.

Als u een AKS-cluster implementeert met behulp van de Azure Portal, kiest u op de pagina *Verificatie* van het dialoogvenster **Kubernetes-cluster maken** de optie **Service-principal configureren**. Selecteer **Bestaande gebruiken** en geef de volgende waarden op:

- **Client-id van de service-principal** is uw *appId*
- **Clientgeheim van de service-principal** is de waarde van het *wachtwoord*

![Afbeelding van browsen naar Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Machtiging afgeven voor toegang tot andere Azure-resources

De service-principal voor het AKS-cluster kan worden gebruikt voor toegang tot andere resources. Als u bijvoorbeeld uw AKS-cluster wilt implementeren in een bestaand subnet van een virtueel Azure-netwerk of verbinding wilt maken met Azure Container Registry (ACR), moet u de toegang tot deze resources delegeren aan de service-principal.

Als u machtigingen wilt delegeren, maakt u een roltoewijzing [met behulp van de opdracht az role assignment create.][az-role-assignment-create] Wijs `appId` de toe aan een bepaald bereik, zoals een resourcegroep of virtuele netwerkresource. Op basis van de rol wordt gedefinieerd welke machtigingen de service-principal heeft voor de resource, zoals in het volgende voorbeeld wordt weergegeven:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

De voor een resource moet een volledige `--scope` resource-id zijn, zoals */subscriptions/ \<guid\> /resourceGroups/myResourceGroup* of */subscriptions/ \<guid\> /resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Als u de roltoewijzing Inzender hebt verwijderd uit de knooppuntresourcegroep, kunnen de onderstaande bewerkingen mislukken.  

In de volgende secties wordt meer uitleg gegeven over algemene machtigingen die u mogelijk moet afgeven.

### <a name="azure-container-registry"></a>Azure Container Registry

Als u Azure Container Registry (ACR) gebruikt als uw container-containeropslag, moet u machtigingen verlenen aan de service-principal voor uw AKS-cluster om afbeeldingen te lezen en op te halen. De aanbevolen configuratie is momenteel om de [opdracht az aks create][az-aks-create] of az [aks update][az-aks-update] te gebruiken om te integreren met een register en de juiste rol voor de service-principal toe te wijzen. Zie Verifiëren met Azure Container Registry van Azure Kubernetes Service voor [gedetailleerde Azure Kubernetes Service.][aks-to-acr]

### <a name="networking"></a>Netwerken

U kunt gebruikmaken van geavanceerde netwerkmogelijkheden als het virtuele netwerk en het subnet of de openbare IP-adressen zich in een andere resourcegroep bevinden. Wijs [de ingebouwde][rbac-network-contributor] rol Inzender voor netwerken toe aan het subnet in het virtuele netwerk. U kunt ook een aangepaste rol [maken met][rbac-custom-role] machtigingen voor toegang tot de netwerkresources in die resourcegroep. Zie [AKS-servicemachtigingen][aks-permissions] voor meer informatie.

### <a name="storage"></a>Storage

Mogelijk hebt u toegang nodig tot bestaande schijfresources in een andere resourcegroep. Wijs een van de volgende sets rolmachtigingen toe:

- Maak een [aangepaste rol][rbac-custom-role] en definieer de volgende rolmachtigingen:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- U kunt ook de ingebouwde rol [Inzender voor opslagaccounts][rbac-storage-contributor] toewijzen aan de resourcegroep

### <a name="azure-container-instances"></a>Azure Container Instances

Als u Virtual Kubelet gebruikt om te integreren met AKS en ervoor kiest Azure Container Instances (ACI) uit te voeren in de resourcegroep, los van de AKS-cluster, dan moet u de AKS-service-principal de machtiging *Inzender* voor de ACI-resourcegroep verlenen.

## <a name="additional-considerations"></a>Aanvullende overwegingen

Houd rekening met het volgende wanneer u werkt met AKS en Azure AD-service-principals.

- De service-principal voor Kubernetes is een onderdeel van de configuratie van het cluster. Gebruik echter niet de id voor het implementeren van het cluster.
- De referenties voor de service-principal zijn standaard één jaar geldig. U kunt [de referenties van de service-principal op elk][update-credentials] moment bijwerken of roteren.
- Elke service-principal is gekoppeld aan een Azure AD-toepassing. De service-principal voor een Kubernetes-cluster kan worden gekoppeld aan elke geldige Azure AD-toepassingsnaam (bijvoorbeeld: *https://www.contoso.org/example* ). De URL van de toepassing hoeft geen echt eindpunt te zijn.
- Gebruik bij het opgeven van de **client-id** van de service-principal de waarde van de `appId`.
- Op de VM's van het agentknooppunt in het Kubernetes-cluster worden de referenties van de service-principal opgeslagen in het bestand `/etc/kubernetes/azure.json`
- Wanneer u de opdracht [az aks create][az-aks-create] gebruikt om de service-principal automatisch te genereren, worden de referenties voor de service-principal naar het bestand `~/.azure/aksServicePrincipal.json` geschreven op de computer die wordt gebruikt om de opdracht uit te voeren.
- Als u niet specifiek een service-principal door geeft in aanvullende AKS CLI-opdrachten, wordt de standaardservice-principal in `~/.azure/aksServicePrincipal.json` gebruikt.  
- U kunt eventueel ook de aksServicePrincipal.jsin het bestand verwijderen en AKS maakt een nieuwe service-principal.
- Wanneer u een AKS-cluster verwijdert dat is gemaakt met [az aks create][az-aks-create], wordt de automatisch gemaakte service-principal niet verwijderd.
    - Als u de service-principal wilt verwijderen, moet u een query uitvoeren voor uw *clusterservicePrincipalProfile.clientId* en vervolgens verwijderen met [az ad sp delete.][az-ad-sp-delete] Vervang de volgende brongroeps- en clusternamen door uw eigen waarden:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Problemen oplossen

De referenties van de service-principal voor een AKS-cluster worden in de cache opgeslagen door de Azure CLI. Als deze referenties zijn verlopen, worden er fouten bij het implementeren van AKS-clusters aangetroffen. Het volgende foutbericht bij het uitvoeren [van az aks create][az-aks-create] kan duiden op een probleem met de referenties van de service-principal in de cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Controleer de leeftijd van het referentiebestand met behulp van de volgende opdracht:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

De standaardverlooptijd voor de referenties van de service-principal is één jaar. Als uw *aksServicePrincipal.jsbestand* ouder is dan één jaar, verwijdert u het bestand en probeert u opnieuw een AKS-cluster te implementeren.

## <a name="next-steps"></a>Volgende stappen

Zie Toepassings- en service-principalobjecten voor meer informatie over Azure Active Directory [service-principals.][service-principal]

Zie De referenties voor een [service-principal][update-credentials]bijwerken of roteren in AKS voor meer informatie over het bijwerken van de referenties.

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az_ad_app_list
[az-ad-app-delete]: /cli/azure/ad/app#az_ad_app_delete
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
