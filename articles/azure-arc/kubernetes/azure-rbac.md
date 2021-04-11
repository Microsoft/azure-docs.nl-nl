---
title: Azure RBAC voor Azure Arc enabled Kubernetes-clusters
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Gebruik Azure RBAC voor autorisatie controles op Azure Arc enabled Kubernetes-clusters
ms.openlocfilehash: bd8029cb2772a6f6bd9821abe6acf69c9c08599d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451015"
---
# <a name="azure-rbac-for-azure-arc-enabled-kubernetes-clusters"></a>Azure RBAC voor Azure Arc enabled Kubernetes-clusters

Kubernetes [ClusterRoleBinding en RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) object types helpen u om autorisatie in Kubernetes systeem eigen te definiëren. Met Azure RBAC kunt u Azure Active Directory en roltoewijzingen in azure gebruiken om verificatie controles op het cluster te beheren. Dit betekent dat u nu Azure-roltoewijzingen kunt gebruiken om te bepalen wie uw Kubernetes-objecten, zoals implementatie, Pod en service, kunnen lezen, schrijven, verwijderen.

Een conceptueel overzicht van deze functie is beschikbaar in het [Kubernetes-artikel van Azure RBAC-Azure Arc enabled](conceptual-azure-rbac.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Vereisten

- [Azure cli installeren of upgraden](https://docs.microsoft.com/cli/azure/install-azure-cli) naar versie >= 2.16.0

- Installeer de `connectedk8s` Azure cli-extensie van versie >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Als de `connectedk8s` uitbrei ding al is geïnstalleerd, kunt u deze bijwerken naar de nieuwste versie met behulp van de volgende opdracht: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Er is een bestaand Kubernetes-verbonden Azure-Arc-cluster ingeschakeld.
    - Als u nog geen cluster hebt verbonden, gebruikt u onze [Quick](quickstart-connect-cluster.md)start.
    - [Werk uw agents](agent-upgrade.md#manually-upgrade-agents) bij naar versie >= 1.1.0.

> [!NOTE]
> Deze functie kan niet worden ingesteld voor beheerde Kubernetes-aanbiedingen van cloud providers als elastische Kubernetes-service of Google Kubernetes-engine waarbij de gebruiker geen toegang heeft tot `apiserver` het cluster. Voor Azure Kubernetes service (AKS)-clusters [is deze functie systeem eigen beschikbaar](../../aks/manage-azure-rbac.md) en hoeft het AKS-cluster niet te worden verbonden met Azure Arc.

## <a name="set-up-azure-ad-applications"></a>Azure AD-toepassingen instellen

### <a name="create-server-application"></a>Server toepassing maken

1. Maak een nieuwe Azure AD-toepassing en ontvang de `appId` waarde, die in latere stappen wordt gebruikt als `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. De claims voor de lidmaatschaps groep van toepassingen bijwerken:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Een service-principal maken en de `password` veld waarde ophalen, die later vereist is `serverApplicationSecret` voor het inschakelen van deze functie op het cluster:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Verleen de API-machtigingen voor de toepassing:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Deze stap moet worden uitgevoerd door een beheerder van een Azure-Tenant.
    > * Voor het gebruik van deze functie in productie is het raadzaam om voor elk cluster een andere server toepassing te maken.

### <a name="create-client-application"></a>Client toepassing maken

1. Maak een nieuwe Azure AD-toepassing en haal de waarde ' appId ' op, die in latere stappen wordt gebruikt als `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Een service-principal maken voor deze client toepassing:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. De `oAuthPermissionId` voor de server toepassing ophalen:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. De vereiste machtigingen verlenen voor de client toepassing:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Een roltoewijzing voor de server toepassing maken

De server toepassing heeft de `Microsoft.Authorization/*/read` machtigingen nodig om te controleren of de gebruiker die de aanvraag maakt, is gemachtigd voor de Kubernetes-objecten die deel uitmaken van de aanvraag.

1. Maak een bestand met de naam accessCheck.jsop met de volgende inhoud:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Vervang de `<subscription-id>` door de daad werkelijke abonnements-id.

2. Voer de volgende opdracht uit om de nieuwe aangepaste rol te maken:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. Sla de waarde van het veld op in de bovenstaande uitvoer van de opdracht `id` . dit wordt in latere stappen als beschreven `roleId` .

4. Maak op de server toepassing een roltoewijzing met behulp van de hierboven gemaakte rol:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Azure RBAC inschakelen op het cluster

1. Schakel Azure RBAC in op uw Kubernetes-cluster met Arc-functionaliteit door de volgende opdracht uit te voeren:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Voordat u de bovenstaande opdracht uitvoert, moet u ervoor zorgen dat het `kubeconfig` bestand op de computer verwijst naar het cluster waarop de functie van Azure RBAC moet worden ingeschakeld.
    > 2. Gebruik `--skip-azure-rbac-list` met de bovenstaande opdracht voor een door komma's gescheiden lijst met gebruikers namen/e-mail berichten/OID verificatie controles met behulp van Kubernetes native ClusterRoleBinding-en RoleBinding-objecten in plaats van met Azure RBAC.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>Voor een Gene riek cluster waarvoor geen reconciliatie wordt uitgevoerd op apiserver-specificatie:

1. SSH in elk hoofd knooppunt van het cluster en voer de volgende stappen uit:

    1. `apiserver`Manifest openen in bewerkings modus:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Voeg de volgende specificatie toe onder `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Voeg de volgende specificatie toe onder `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Voeg de volgende `apiserver` argumenten toe:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Als het Kubernetes-cluster van versie >= 1.19.0 is, `apiserver argument` moet u ook het volgende instellen:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Sla de editor op en sluit deze om de pod bij te werken `apiserver` .


### <a name="for-a-cluster-created-using-cluster-api"></a>Voor een cluster dat is gemaakt met behulp van cluster-API

1. Kopieer het beveiligde geheim met verificatie-en autorisatie-webhook-configuratie bestanden `from the workload cluster` op de computer:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Wijzig het `namespace` veld in het `azure-arc-guard-manifests.yaml` bestand in de naam ruimte binnen het beheer cluster waarop u de aangepaste resources toepast voor het maken van werkbelasting clusters.

1. Dit manifest Toep assen:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Bewerk het `KubeadmControlPlane` object door het uit te voeren `kubectl edit kcp <clustername>-control-plane` :
    
    1. Voeg het volgende code fragment toe onder `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Voeg het volgende code fragment toe onder `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Voeg het volgende code fragment toe onder `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Sla het object op en sluit het af `KubeadmControlPlane` . Wacht totdat de wijzigingen zijn doorgevoerd op het werkbelasting cluster.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Roltoewijzingen voor gebruikers maken voor toegang tot het cluster

Eigen aren van de Azure Arc enabled Kubernetes-resource kunnen ingebouwde rollen of aangepaste rollen gebruiken om andere gebruikers toegang te verlenen tot het Kubernetes-cluster.

### <a name="built-in-roles"></a>Ingebouwde rollen

| Rol | Beschrijving |
|---|---|
| Viewer voor Azure Arc Kubernetes | Hiermee staat u alleen-lezen toegang toe om de meeste objecten in een naam ruimte weer te geven. Met deze rol mogen geen geheimen worden weer gegeven. Dit komt doordat `read` de machtiging voor geheimen toegang zou bieden tot `ServiceAccount` referenties in de naam ruimte, waardoor API-toegang wordt toegestaan via die `ServiceAccount` (een vorm van bevoegdheden escalation). |
| Kubernetes schrijver van Azure Arc | Hiermee wordt lees-/schrijftoegang tot de meeste objecten in een naam ruimte toegestaan. Deze rol staat het weer geven of wijzigen van rollen of rollen bindingen niet toe. Met deze rol kunt u echter ook toegang krijgen tot geheimen en een Peul uitvoeren als `ServiceAccount` in de naam ruimte, zodat het kan worden gebruikt om de API-toegangs niveaus van elk `ServiceAccount` in de naam ruimte te verkrijgen. |
| Azure Arc Kubernetes-beheerder | Hiermee staat u beheerders toegang toe. Bedoeld om te worden verleend binnen een naam ruimte met behulp van een RoleBinding. Bij gebruik in een RoleBinding staat lees-/schrijftoegang tot de meeste resources in een naam ruimte toe, inclusief de mogelijkheid om rollen en rollen bindingen te maken binnen de naam ruimte. Deze rol staat geen schrijf toegang tot resource quota of de naam ruimte zelf toe. |
| Azure Arc Kubernetes-Cluster beheer | Hiermee kan toegang van Super gebruikers elke actie uitvoeren op elke resource. Bij gebruik in een ClusterRoleBinding krijgt u volledige controle over elke resource in het cluster en in alle naam ruimten. Bij gebruik in een RoleBinding krijgt u volledige controle over elke resource in de naam ruimte van de roltype, met inbegrip van de naam ruimte zelf.|

U kunt roltoewijzingen maken binnen het bereik van het Kubernetes-cluster dat is ingeschakeld op de `Access Control (IAM)` Blade van de cluster resource op Azure Portal. U kunt ook Azure CLI-opdrachten gebruiken, zoals hieronder wordt weer gegeven:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

Dit `AZURE-AD-ENTITY-ID` kan een gebruikers naam (bijvoorbeeld testuser@mytenant.onmicrosoft.com ) of zelfs de `appId` van een Service-Principal zijn.

Hier volgt nog een voor beeld van het maken van een roltoewijzing in een specifieke naam ruimte binnen het cluster.

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Terwijl roltoewijzingen binnen het bereik van het cluster kunnen worden gemaakt met behulp van de Azure Portal of CLI, kunnen roltoewijzingen met een naam ruimte alleen worden gemaakt met de CLI.

### <a name="custom-roles"></a>Aangepaste rollen

U kunt ervoor kiezen om uw eigen functie definitie te maken voor gebruik in roltoewijzingen.

Door loop het onderstaande voor beeld van een roldefinitie waarmee een gebruiker alleen implementaties kan lezen. Zie [de volledige lijst met gegevens acties die u kunt gebruiken voor het maken van een roldefinitie](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)voor meer informatie.

Kopieer het onderstaande JSON-object naar een bestand met de naam custom-role.jsop. Vervang de `<subscription-id>` tijdelijke aanduiding door de daad werkelijke abonnements-id. De onderstaande aangepaste functie maakt gebruik van een van de gegevens acties en stelt u in staat om alle implementaties in het bereik (cluster/naam ruimte) te bekijken waar de roltoewijzing is gemaakt.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Maak de roldefinitie door de onderstaande opdracht uit te voeren vanuit de map waarin u hebt opgeslagen `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Een roltoewijzing maken met de definitie van deze aangepaste rol:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Kubectl configureren met gebruikers referenties

Er zijn twee manieren om `kubeconfig` een bestand te verkrijgen dat nodig is voor toegang tot het cluster:
1. Gebruik de functie [cluster Connect](cluster-connect.md) ( `az connectedk8s proxy` ) van het Azure Arc enabled Kubernetes-cluster.
1. Cluster beheer deelt het `kubeconfig` bestand met elke andere gebruiker.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Als u cluster met cluster Connect-functie opent

Voer de volgende opdracht uit om het proxy proces te starten:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Nadat het proxy proces is uitgevoerd, kunt u nog een tabblad openen in uw console om [te beginnen met het verzenden van uw aanvragen naar het cluster](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Als de Cluster beheerder het `kubeconfig` bestand met u heeft gedeeld 

1. Voer de volgende opdracht uit om referenties in te stellen voor de gebruiker:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Open het `kubeconfig` bestand dat u eerder hebt gemaakt. `contexts`Controleer onder de context van cluster verwijst naar de gebruikers referenties die u in de vorige stap hebt gemaakt.

1. **Configuratie-** instelling toevoegen onder gebruikers configuratie:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Aanvragen verzenden naar cluster

1. Voer een wille keurige `kubectl` opdracht uit. Bijvoorbeeld:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Als u om een verificatie op basis van een browser wordt gevraagd, kopieert u de aanmeldings-URL van het apparaat `https://microsoft.com/devicelogin` en opent u het in uw webbrowser.

1. Voer de code in die in de console wordt afgedrukt, kopieer en plak de code op uw terminal in de invoer prompt voor de verificatie van het apparaat.

1. Voer de gebruikers naam ( testuser@mytenant.onmicrosoft.com ) en het bijbehorende wacht woord in.

1. Als er een fout bericht als dit wordt weer gegeven, betekent dit dat u niet gemachtigd bent om toegang te krijgen tot de aangevraagde resource:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Een beheerder moet een nieuwe roltoewijzing maken die deze gebruiker machtigt om toegang te krijgen tot de resource.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> Veilig verbinding maken met het cluster met [cluster Connect](cluster-connect.md)