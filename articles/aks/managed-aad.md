---
title: Azure AD gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van Azure AD in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: b7918ecc31fe152bd25153ac8c899ce3ff8fdacb
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640598"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS-beheerde Azure Active Directory-integratie

Azure AD-integratie met AKS is ontworpen om de Azure AD-integratie ervaring te vereenvoudigen, waar gebruikers eerder moesten een client-app, een server-app en de Azure AD-Tenant nodig hebben om Lees machtigingen te verlenen aan de Directory. In de nieuwe versie beheert de AKS-resource provider de client-en server-apps voor u.

## <a name="azure-ad-authentication-overview"></a>Overzicht van Azure AD-verificatie

Cluster beheerders kunnen Kubernetes op rollen gebaseerd toegangs beheer (Kubernetes RBAC) configureren op basis van de identiteit van een gebruiker of het lidmaatschap van de Directory groep. Azure AD-verificatie wordt geleverd voor AKS-clusters met OpenID Connect Connect. OpenID Connect Connect is een id-laag die boven op het OAuth 2,0-protocol is gebouwd. Voor meer informatie over OpenID Connect Connect raadpleegt u de [Open-ID Connect-documentatie][open-id-connect].

Meer informatie over de Azure AD-integratie stroom vindt u in de [documentatie over integratie concepten van Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Beperkingen 

* AKS-beheerde Azure AD-integratie kan niet worden uitgeschakeld
* Het wijzigen van een door AKS beheerd Azure AD geïntegreerd cluster in verouderde AAD wordt niet ondersteund
* niet-Kubernetes RBAC ingeschakelde clusters worden niet ondersteund voor door AKS beheerde Azure AD-integratie
* Het wijzigen van de Azure AD-Tenant die is gekoppeld aan AKS beheerde Azure AD-integratie, wordt niet ondersteund

## <a name="prerequisites"></a>Vereisten

* De Azure CLI-versie 2.11.0 of hoger
* Kubectl met een minimum versie van [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) of [kubelogin](https://github.com/Azure/kubelogin)
* Als u [helm](https://github.com/helm/helm), minimale versie van helm 3,3, gebruikt.

> [!Important]
> U moet Kubectl gebruiken met een minimum versie van 1.18.1 of kubelogin. Het verschil tussen de secundaire versies van Kubernetes en kubectl mag niet meer zijn dan 1 versie. Als u niet de juiste versie gebruikt, zult u verificatie problemen ondervinden.

Gebruik de volgende opdrachten om kubectl en kubelogin te installeren:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Gebruik [deze instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl/) voor andere besturings systemen.

## <a name="before-you-begin"></a>Voordat u begint

Voor uw cluster hebt u een Azure AD-groep nodig. Deze groep is vereist als beheerders groep voor het cluster om cluster beheerders machtigingen te verlenen. U kunt een bestaande Azure AD-groep gebruiken of een nieuwe maken. Registreer de object-ID van uw Azure AD-groep.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Gebruik de volgende opdracht om een nieuwe Azure AD-groep voor uw cluster beheerders te maken:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Een AKS-cluster maken met Azure AD ingeschakeld

Maak een AKS-cluster met behulp van de volgende CLI-opdrachten.

Een Azure-resource groep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Een AKS-cluster maken en beheer toegang inschakelen voor uw Azure AD-groep

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Als u een Azure AD-cluster met AKS-beheer hebt gemaakt, is de volgende sectie in de antwoord tekst
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

## <a name="access-an-azure-ad-enabled-cluster"></a>Toegang tot een Azure AD-cluster

U hebt de [Azure Kubernetes service-cluster gebruiker](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) ingebouwde rol nodig om de volgende stappen uit te voeren.

De gebruikers referenties ophalen voor toegang tot het cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Volg de instructies om u aan te melden.

Gebruik de opdracht kubectl Get nodes om knoop punten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configureer [Azure RBAC (op rollen gebaseerd toegangs beheer)](./azure-ad-rbac.md) voor het configureren van extra beveiligings groepen voor uw clusters.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Problemen met toegang tot Azure AD oplossen

> [!Important]
> De normale Azure AD-groeps verificatie wordt omzeild met de stappen die hieronder worden beschreven. Gebruik ze alleen in een nood geval.

Als u permanent bent geblokkeerd door geen toegang te hebben tot een geldige Azure AD-groep die toegang heeft tot uw cluster, kunt u nog steeds de beheerders referenties verkrijgen voor toegang tot het cluster.

Als u deze stappen wilt uitvoeren, moet u toegang hebben tot de ingebouwde rol [Azure Kubernetes service cluster admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Door AKS beheerde Azure AD-integratie inschakelen op uw bestaande cluster

U kunt AKS-beheerde Azure AD-integratie inschakelen op uw bestaande Kubernetes RBAC-cluster. Zorg ervoor dat u uw beheerders groep instelt om toegang te houden tot uw cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Een geslaagde activering van een Azure AD-cluster dat door AKS wordt beheerd, heeft de volgende sectie in de antwoord tekst

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

Down load de gebruikers referenties opnieuw om toegang te krijgen tot uw cluster door de stappen [hier][access-cluster]te volgen.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade uitvoeren naar AKS-beheerde Azure AD-integratie

Als uw cluster gebruikmaakt van verouderde Azure AD-integratie, kunt u een upgrade uitvoeren naar AKS-beheerde Azure AD-integratie.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Een geslaagde migratie van een Azure AD-cluster met AKS-beheer heeft de volgende sectie in de antwoord tekst

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

Als u toegang wilt krijgen tot het cluster, voert u de [volgende stappen uit][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Niet-interactief aanmelden met kubelogin

Er zijn een aantal niet-interactieve scenario's, zoals continue integratie pijplijnen, die momenteel niet beschikbaar zijn in kubectl. U kunt gebruiken [`kubelogin`](https://github.com/Azure/kubelogin) om toegang te krijgen tot het cluster met niet-interactieve Service-Principal-aanmelding.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Voorwaardelijke toegang gebruiken met Azure AD en AKS

Wanneer u Azure AD integreert met uw AKS-cluster, kunt u ook [voorwaardelijke toegang][aad-conditional-access] gebruiken om de toegang tot uw cluster te beheren.

> [!NOTE]
> Voorwaardelijke toegang van Azure AD is een Azure AD Premium mogelijkheid.

Voer de volgende stappen uit om een voor beeld van een voorwaardelijk toegangs beleid te maken voor gebruik met AKS:

1. Zoek en selecteer Azure Active Directory aan de bovenkant van de Azure Portal.
1. Selecteer in het menu voor Azure Active Directory aan de linkerkant de optie *bedrijfs toepassingen*.
1. Selecteer in het menu voor zakelijke toepassingen aan de linkerkant *voorwaardelijke toegang*.
1. Selecteer in het menu voor voorwaardelijke toegang aan de linkerkant de optie *beleid* en vervolgens *Nieuw beleid*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Beleid voor voorwaardelijke toegang toevoegen":::
1. Voer een naam in voor het beleid, zoals *AKS-Policy*.
1. Selecteer *gebruikers en groepen* en selecteer onder *insluiten* selecteren de *optie gebruikers en groepen*. Kies de gebruikers en groepen waarop u het beleid wilt Toep assen. Kies voor dit voor beeld dezelfde Azure AD-groep met beheerders toegang tot uw cluster.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Gebruikers of groepen selecteren om het beleid voor voorwaardelijke toegang toe te passen":::
1. Selecteer *Cloud-apps of-acties* en selecteer vervolgens onder *insluit* selecteren de *optie Apps selecteren*. Zoek naar de *Azure Kubernetes-service* en selecteer *Azure Kubernetes service Aad server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="De Azure Kubernetes service AD-server selecteren voor het Toep assen van het beleid voor voorwaardelijke toegang":::
1. Onder *Toegangscontroles* selecteert u *Verlenen*. Selecteer *toegang verlenen* en stel *in dat het apparaat moet worden gemarkeerd als compatibel*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Selecteren om alleen compatibele apparaten toe te staan voor het beleid voor voorwaardelijke toegang":::
1. Onder *beleid inschakelen* selecteert u *aan* en vervolgens *maken*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Het beleid voor voorwaardelijke toegang inschakelen":::

Haal de gebruikers referenties op voor toegang tot het cluster, bijvoorbeeld:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Volg de instructies om u aan te melden.

Gebruik de `kubectl get nodes` opdracht voor het weer geven van knoop punten in het cluster:

```azurecli-interactive
kubectl get nodes
```

Volg de instructies om u opnieuw aan te melden. Er wordt een fout bericht weer gegeven waarin wordt vermeld dat u bent aangemeld, maar uw beheerder vereist dat het apparaat dat toegang vraagt om te worden beheerd door uw Azure AD om toegang te krijgen tot de resource.

Ga in het Azure Portal naar Azure Active Directory, selecteer *zakelijke toepassingen* en selecteer vervolgens *aanmeldingen* onder *activiteit* . U ziet een vermelding bovenaan met de *status* *mislukt* en een *voorwaardelijke toegang tot* *geslaagde pogingen*. Selecteer de vermelding en selecteer vervolgens *voorwaardelijke toegang* in *Details*. U ziet dat het beleid voor voorwaardelijke toegang wordt weer gegeven.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Mislukte aanmeldings vermelding vanwege beleid voor voorwaardelijke toegang":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Just-in-time-cluster toegang configureren met Azure AD en AKS

Een andere optie voor cluster toegangs beheer is het gebruik van Privileged Identity Management (PIM) voor Just-in-time-aanvragen.

>[!NOTE]
> PIM is een Azure AD Premium capaciteit waarvoor een Premium P2-SKU is vereist. Zie de [prijs handleiding][aad-pricing]voor meer informatie over Azure AD sku's.

Voer de volgende stappen uit om just-in-time-toegangs aanvragen te integreren met een AKS-cluster met behulp van AKS-beheerde Azure AD-integratie:

1. Zoek en selecteer Azure Active Directory aan de bovenkant van de Azure Portal.
1. Noteer de Tenant-ID, waarnaar wordt verwezen voor de rest van deze instructies `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text=", zoals in een webbrowser, het Azure portal scherm voor Azure Active Directory wordt weer gegeven met de id van de Tenant gemarkeerd.":::
1. Klik in het menu voor Azure Active Directory aan de linkerkant onder Selecteer *groepen* *beheren* vervolgens *nieuwe groep*.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Toont het scherm Azure Portal Active Directory groepen met de optie ' nieuwe groep ' gemarkeerd.":::
1. Zorg ervoor dat het groeps type *beveiliging* is geselecteerd en voer een groeps naam in, zoals *myJITGroup*. Onder *Azure AD-rollen kunnen worden toegewezen aan deze groep (preview)*. Selecteer *Ja*. Selecteer tot slot *maken*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Toont het nieuwe scherm voor het maken van een groep Azure Portal.":::
1. U gaat terug naar de pagina *groepen* . Selecteer de zojuist gemaakte groep en noteer de object-ID, waarnaar wordt verwezen voor de rest van deze instructies als `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Hiermee wordt het Azure Portal scherm voor de zojuist gemaakte groep weer gegeven, waarbij de object-id wordt gemarkeerd":::
1. Implementeer een AKS-cluster met door AKS beheerde Azure AD-integratie met behulp `<tenant-id>` `<object-id>` van de waarden en uit eerdere versies:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. In het Azure Portal, in het menu voor *activiteit* aan de linkerkant selecteert u *privileged Access (preview)* en selecteert u *bevoorrechte toegang inschakelen*.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="De pagina privileged Access (preview) van de Azure Portal wordt weer gegeven, waarbij ' geprivilegieerde toegang inschakelen ' is gemarkeerd":::
1. Selecteer *toewijzingen toevoegen* om toegang te verlenen.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Het scherm privileged Access (preview) van Azure Portal wordt weer gegeven nadat het inschakelen is ingeschakeld. De optie voor het toevoegen van toewijzingen is gemarkeerd.":::
1. Selecteer een rol van *lid* en selecteer de gebruikers en groepen waaraan u toegang tot het cluster wilt verlenen. Deze toewijzingen kunnen op elk gewenst moment worden gewijzigd door een groeps beheerder. Wanneer u klaar bent om door te gaan, selecteert u *volgende*.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Het scherm toewijzings leden toevoegen van de Azure Portal wordt weer gegeven, waarbij een voor beeld van een gebruiker is geselecteerd om te worden toegevoegd als een lid. De optie volgende is gemarkeerd.":::
1. Kies een toewijzings type *actief*, de gewenste duur en geef een reden op. Wanneer u klaar bent om door te gaan, selecteert u *toewijzen*. Zie [geschiktheid voor een bevoegde toegangs groep (preview) toewijzen in privileged Identity Management][aad-assignments]voor meer informatie over toewijzings typen.
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Het scherm instellingen voor toewijzingen toevoegen van de Azure Portal wordt weer gegeven. Een toewijzings type van ' actief ' is geselecteerd en er is een voor beeld van een uitvulling gegeven. De optie Assign is gemarkeerd.":::

Nadat de toewijzingen zijn gemaakt, controleert u of just-in-time toegang werkt door het cluster te openen. Bijvoorbeeld:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Volg de stappen om u aan te melden.

Gebruik de `kubectl get nodes` opdracht voor het weer geven van knoop punten in het cluster:

```azurecli-interactive
kubectl get nodes
```

Noteer de verificatie vereiste en volg de stappen om te verifiëren. Als dit lukt, ziet u uitvoer die lijkt op het volgende:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Problemen oplossen

Als er `kubectl get nodes` een fout wordt geretourneerd die lijkt op het volgende:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Zorg ervoor dat de beheerder van de beveiligings groep heeft opgegeven dat uw account een *actieve* toewijzing heeft.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure RBAC-integratie voor Kubernetes-autorisatie][azure-rbac-integration]
* Meer informatie over [Azure AD-integratie met KUBERNETES RBAC][azure-ad-rbac].
* Gebruik [kubelogin](https://github.com/Azure/kubelogin) om toegang te krijgen tot functies voor Azure-verificatie die niet beschikbaar zijn in kubectl.
* Meer informatie over [AKS-en Kubernetes-identiteits concepten][aks-concepts-identity].
* Gebruik [Azure Resource Manager (arm)-Sjablonen ][aks-arm-template] voor het maken van met AKS beheerde Azure AD-clusters.

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
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
