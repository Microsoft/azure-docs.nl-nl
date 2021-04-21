---
title: Azure AD gebruiken in Azure Kubernetes Service
description: Meer informatie over het gebruik van Azure AD in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 3db9f8d895b4c13b5f969859f422e7b566722ffc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783067"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integratie van met AKS Azure Active Directory beheerde systemen

Met AKS beheerde Azure AD-integratie is ontworpen om de integratie van Azure AD te vereenvoudigen, waarbij gebruikers voorheen een client-app en een server-app moesten maken en de Azure AD-tenant de machtiging Directory Read moesten verlenen. In de nieuwe versie beheert de AKS-resourceprovider de client- en server-apps voor u.

## <a name="azure-ad-authentication-overview"></a>Overzicht van Azure AD-verificatie

Clusterbeheerders kunnen kubernetes op rollen gebaseerd toegangsbeheer (Kubernetes RBAC) configureren op basis van de identiteit of het lidmaatschap van een directorygroep van een gebruiker. Azure AD-verificatie wordt geleverd aan AKS-clusters met OpenID Connect. OpenID Connect is een identiteitslaag die is gebouwd op het OAuth 2.0-protocol. Zie de open id connect-documentatie OpenID Connect meer [informatie over het maken van een verbinding.][open-id-connect]

Meer informatie over de Azure AD-integratiestroom in de documentatie [Azure Active Directory integratieconcepten](concepts-identity.md#azure-ad-integration).

## <a name="limitations"></a>Beperkingen 

* Met AKS beheerde Azure AD-integratie kan niet worden uitgeschakeld
* Het wijzigen van een met AKS beheerd, geïntegreerd Azure AD-cluster in verouderde AAD wordt niet ondersteund
* clusters zonder Kubernetes RBAC worden niet ondersteund voor door AKS beheerde Azure AD-integratie
* Het wijzigen van de Azure AD-tenant die is gekoppeld aan door AKS beheerde Azure AD-integratie wordt niet ondersteund

## <a name="prerequisites"></a>Vereisten

* Azure CLI versie 2.11.0 of hoger
* Kubectl met minimaal versie [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) of [kubelogin](https://github.com/Azure/kubelogin)
* Als u Helm [gebruikt,](https://github.com/helm/helm)minimaal versie helm 3.3.

> [!Important]
> U moet Kubectl gebruiken met minimaal versie 1.18.1 of kubelogin. Het verschil tussen de secundaire versies van Kubernetes en kubectl mag niet meer zijn dan 1 versie. Als u niet de juiste versie gebruikt, zult u problemen met de verificatie merken.

Gebruik de volgende opdrachten om kubectl en kubelogin te installeren:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Gebruik [deze instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl/) voor andere besturingssystemen.

## <a name="before-you-begin"></a>Voordat u begint

Voor uw cluster hebt u een Azure AD-groep nodig. Deze groep is nodig als beheerdersgroep voor het cluster om clusterbeheerdersmachtigingen te verlenen. U kunt een bestaande Azure AD-groep gebruiken of een nieuwe maken. Neem de object-id van uw Azure AD-groep op.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Gebruik de volgende opdracht om een nieuwe Azure AD-groep te maken voor uw clusterbeheerders:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Een AKS-cluster maken met Azure AD ingeschakeld

Maak een AKS-cluster met behulp van de volgende CLI-opdrachten.

Een Azure-resourcegroep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Een AKS-cluster maken en beheertoegang inschakelen voor uw Azure AD-groep

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Een geslaagde aanmaak van een met AKS beheerd Azure AD-cluster heeft de volgende sectie in de antwoordtekst
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Zodra het cluster is gemaakt, kunt u het openen.

## <a name="access-an-azure-ad-enabled-cluster"></a>Toegang tot een cluster met Azure AD-ondersteuning

U hebt de ingebouwde [Azure Kubernetes Service clustergebruiker](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) nodig om de volgende stappen uit te voeren.

Haal de gebruikersreferenties op voor toegang tot het cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Volg de instructies om u aan te melden.

Gebruik de opdracht kubectl get nodes om knooppunten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configureer [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) om](./azure-ad-rbac.md) extra beveiligingsgroepen voor uw clusters te configureren.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Toegangsproblemen met Azure AD oplossen

> [!Important]
> De onderstaande stappen zijn het omzeilen van de normale Azure AD-groepsverificatie. Gebruik ze alleen in noodgevallen.

Als u permanent wordt geblokkeerd door geen toegang te hebben tot een geldige Azure AD-groep met toegang tot uw cluster, kunt u nog steeds de beheerdersreferenties verkrijgen om rechtstreeks toegang te krijgen tot het cluster.

Als u deze stappen wilt volgen, moet u toegang hebben tot de [Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) ingebouwde rol Clusterbeheerder.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Door AKS beheerde Azure AD-integratie inschakelen op uw bestaande cluster

U kunt door AKS beheerde Azure AD-integratie inschakelen op uw bestaande Kubernetes RBAC-cluster. Zorg ervoor dat u de beheerdersgroep zo instelt dat toegang tot uw cluster behouden blijft.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Een geslaagde activering van een met AKS beheerd Azure AD-cluster heeft de volgende sectie in de antwoordtekst

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Download gebruikersreferenties opnieuw om toegang te krijgen tot uw cluster door de stappen hier [te volgen.][access-cluster]

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgraden naar door AKS beheerde Azure AD-integratie

Als uw cluster gebruikmaakt van verouderde Azure AD-integratie, kunt u upgraden naar door AKS beheerde Azure AD-integratie.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Een geslaagde migratie van een met AKS beheerd Azure AD-cluster heeft de volgende sectie in de antwoordtekst

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Als u toegang wilt krijgen tot het cluster, volgt u deze [stappen.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Niet-interactieve aanmelding met kubelogin

Er zijn enkele niet-interactieve scenario's, zoals pijplijnen voor continue integratie, die momenteel niet beschikbaar zijn met kubectl. U kunt gebruiken [`kubelogin`](https://github.com/Azure/kubelogin) om toegang te krijgen tot het cluster met aanmelding via een niet-interactieve service-principal.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Voorwaardelijke toegang gebruiken met Azure AD en AKS

Wanneer u Azure AD integreert met uw AKS-cluster, kunt u ook voorwaardelijke toegang gebruiken om [de][aad-conditional-access] toegang tot uw cluster te bepalen.

> [!NOTE]
> Voorwaardelijke toegang van Azure AD is een Azure AD Premium functie.

Als u een voorbeeldbeleid voor voorwaardelijke toegang wilt maken voor gebruik met AKS, moet u de volgende stappen voltooien:

1. Zoek en selecteer bovenaan Azure Portal de Azure Active Directory.
1. Selecteer in het menu Azure Active Directory aan de linkerkant *bedrijfstoepassingen.*
1. Selecteer in het menu voor Bedrijfstoepassingen aan de linkerkant de optie *Voorwaardelijke toegang.*
1. Selecteer in het menu voor voorwaardelijke toegang aan de linkerkant beleidsregels *en* vervolgens *Nieuw beleid.*
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Een beleid voor voorwaardelijke toegang toevoegen":::
1. Voer een naam in voor het beleid, zoals *aks-policy.*
1. Selecteer *Gebruikers en groepen en* selecteer vervolgens onder *Opnemen* de optie Gebruikers en *groepen selecteren.* Kies de gebruikers en groepen waarop u het beleid wilt toepassen. Voor dit voorbeeld kiest u dezelfde Azure AD-groep die beheertoegang tot uw cluster heeft.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Gebruikers of groepen selecteren om het beleid voor voorwaardelijke toegang toe te passen":::
1. Selecteer *Cloud-apps of -acties* en selecteer vervolgens onder *Opnemen* *de optie Apps selecteren.* Zoek naar *Azure Kubernetes Service* en selecteer *Azure Kubernetes Service AAD Server.*
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Een Azure Kubernetes Service AD-server selecteren om het beleid voor voorwaardelijke toegang toe te passen":::
1. Onder *Toegangscontroles* selecteert u *Verlenen*. Selecteer *Toegang verlenen en* vervolgens Vereisen dat het apparaat moet worden gemarkeerd als *compatibel.*
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Selecteren om alleen compatibele apparaten toe te staan voor het beleid voor voorwaardelijke toegang":::
1. Selecteer *onder Beleid inschakelen* de *optie Aan* en vervolgens *Maken.*
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Het beleid voor voorwaardelijke toegang inschakelen":::

Haal de gebruikersreferenties op voor toegang tot het cluster, bijvoorbeeld:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Volg de instructies om u aan te melden.

Gebruik de `kubectl get nodes` opdracht om knooppunten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes
```

Volg de instructies om u opnieuw aan te melden. U ziet een foutbericht waarin staat dat u bent aangemeld, maar uw beheerder vereist dat het apparaat dat toegang aanvraagt, wordt beheerd door uw Azure AD voor toegang tot de resource.

Navigeer in Azure Portal naar Azure Active Directory, selecteer *Bedrijfstoepassingen* en  selecteer vervolgens onder Activiteit *de optie Aanmeldingen.* U ziet bovenaan een vermelding met *de status* *Mislukt* en *Voorwaardelijke toegang* *geslaagd.* Selecteer de vermelding en selecteer vervolgens *Voorwaardelijke toegang* in *Details.* U ziet dat uw beleid voor voorwaardelijke toegang wordt weergegeven.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Mislukte aanmelding vanwege beleid voor voorwaardelijke toegang":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Just-In-Time-clustertoegang configureren met Azure AD en AKS

Een andere optie voor clustertoegangsbeheer is het gebruik Privileged Identity Management (PIM) voor Just-In-Time-aanvragen.

>[!NOTE]
> PIM is een Azure AD Premium die een Premium P2-SKU vereist. Zie de prijsgids voor meer informatie over Azure [AD-SKU's.][aad-pricing]

Als u Just-In-Time-toegangsaanvragen wilt integreren met een AKS-cluster met behulp van door AKS beheerde Azure AD-integratie, moet u de volgende stappen uitvoeren:

1. Zoek en selecteer bovenaan Azure Portal de Azure Active Directory.
1. Noteer de tenant-id, die voor de rest van deze instructies wordt aangeduid als In een webbrowser wordt het Azure Portal-scherm voor Azure Active Directory weergegeven met de id van de `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="tenant gemarkeerd.":::
1. Selecteer in het menu Azure Active Directory aan de linkerkant  onder Beheren de optie *Groepen* en *vervolgens Nieuwe groep.*
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Toont het Azure Portal Active Directory-groepen met de optie Nieuwe groep gemarkeerd.":::
1. Zorg ervoor dat het beveiligingstype *Groep* is geselecteerd en voer een groepsnaam in, zoals *myJITGroup.* Onder *Azure AD-rollen kunnen worden toegewezen aan deze groep (preview)* selecteert u *Ja.* Selecteer ten slotte *Maken.*
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Toont het Azure Portal van de nieuwe groep.":::
1. U wordt terug gebracht naar de *pagina* Groepen. Selecteer de zojuist gemaakte groep en noteer de object-id, die voor de rest van deze instructies wordt aangeduid als `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Toont het Azure Portal voor de zojuist gemaakte groep, met de object-id":::
1. Implementeer een AKS-cluster met door AKS beheerde Azure AD-integratie met behulp van de `<tenant-id>` waarden `<object-id>` en van eerder:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Terug in de Azure Portal selecteert u  in het menu voor Activiteit aan de linkerkant de optie Bevoegde toegang *(preview)* en selecteert u Bevoegde toegang *inschakelen.*
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="De Azure Portal privileged access (Preview) wordt weergegeven, met 'Enable privileged access' gemarkeerd":::
1. Selecteer *Toewijzingen toevoegen om* toegang te verlenen.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Na Azure Portal wordt het scherm Bevoegde toegang (preview) van de Azure Portal weergegeven. De optie Toewijzingen toevoegen is gemarkeerd.":::
1. Selecteer een rol van *lid* en selecteer de gebruikers en groepen aan wie u clustertoegang wilt verlenen. Deze toewijzingen kunnen op elk moment door een groepsbeheerder worden gewijzigd. Wanneer u klaar bent om door te gaan, selecteert u *Volgende.*
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Het Azure Portal toewijzingen toevoegen wordt weergegeven, met een voorbeeldgebruiker geselecteerd om te worden toegevoegd als lid. De optie Volgende is gemarkeerd.":::
1. Kies het toewijzingstype *Actief,* de gewenste duur en geef een reden op. Wanneer u klaar bent om door te gaan, selecteert u *Toewijzen.* Zie Geschiktheid toewijzen voor een groep met uitgebreide toegang (preview) in Privileged Identity Management voor [meer informatie over toewijzingstypen.][aad-assignments]
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Het Azure Portal toewijzingen toevoegen van de Azure Portal wordt weergegeven. Er wordt een toewijzingstype 'Actief' geselecteerd en er is een voorbeeld van een reden gegeven. De optie Toewijzen is gemarkeerd.":::

Zodra de toewijzingen zijn gemaakt, controleert u of Just-In-Time-toegang werkt door toegang te krijgen tot het cluster. Bijvoorbeeld:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Volg de stappen om u aan te melden.

Gebruik de `kubectl get nodes` opdracht om knooppunten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes
```

Noteer de verificatievereiste en volg de stappen om te verifiëren. Als dit lukt, ziet de uitvoer er ongeveer als volgt uit:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Problemen oplossen

Als `kubectl get nodes` een fout retourneert die lijkt op het volgende:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Zorg ervoor dat de beheerder van de beveiligingsgroep uw account een actieve *toewijzing heeft* gegeven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure RBAC-integratie voor Kubernetes-autorisatie][azure-rbac-integration]
* Meer informatie [over Azure AD-integratie met Kubernetes RBAC.][azure-ad-rbac]
* Gebruik [kubelogin voor](https://github.com/Azure/kubelogin) toegang tot functies voor Azure-verificatie die niet beschikbaar zijn in kubectl.
* Meer informatie over [AKS- en Kubernetes-identiteitsconcepten.][aks-concepts-identity]
* Gebruik [Azure Resource Manager (ARM)-sjablonen om ][aks-arm-template] door AKS beheerde Azure AD-clusters te maken.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
