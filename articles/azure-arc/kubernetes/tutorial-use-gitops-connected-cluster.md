---
title: 'Zelfstudie: Configuraties implementeren met behulp van GitOps op een Azure Arc Kubernetes-cluster'
description: In deze zelfstudie wordt gedemonstreerd hoe u configuraties kunt toepassen op Azure Arc Kubernetes-cluster. Zie het artikel Configurations and GitOps - Azure Arc enabled Kubernetes (Configuraties en GitOps - Azure Arc Kubernetes) voor een conceptueel concept.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 66d00ae738cc693d46f1df333ce64accea4b12a8
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883936"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Zelfstudie: Configuraties implementeren met behulp van GitOps op een Azure Arc Kubernetes-cluster 

In deze zelfstudie gaat u configuraties toepassen met behulp van GitOps op een kubernetes-cluster Azure Arc kubernetes-cluster. U leert het volgende:

> [!div class="checklist"]
> * Maak een configuratie op een kubernetes Azure Arc cluster met een voorbeeld van een Git-opslagplaats.
> * Controleer of de configuratie is gemaakt.
> * Pas configuratie toe vanuit een persoonlijke Git-opslagplaats.
> * Valideer de Kubernetes-configuratie.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een bestaand Azure Arc kubernetes-verbonden cluster.
    - Als u nog geen cluster hebt verbonden, doorloop dan onze quickstart Een [kubernetes-cluster Azure Arc kubernetes-cluster verbinden.](quickstart-connect-cluster.md)
- Kennis van de voordelen en architectuur van deze functie. Lees meer in [het artikel Configuraties en GitOps - Azure Arc Kubernetes ingeschakeld.](conceptual-configurations.md)
- Installeer de `k8s-configuration` Azure CLI-extensie van versie >= 1.0.0:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Als de `k8s-configuration` extensie al is geïnstalleerd, kunt u deze bijwerken naar de nieuwste versie met behulp van de volgende opdracht: `az extension update --name k8s-configuration`

## <a name="create-a-configuration"></a>Een configuratie maken

De [voorbeeldopslagplaats](https://github.com/Azure/arc-k8s-demo) die in dit artikel wordt gebruikt, is gestructureerd rond de persona van een clusteroperator. De manifesten in deze opslagplaats bieden enkele naamruimten, implementeren workloads en bieden een teamspecifieke configuratie. Als u deze opslagplaats gebruikt met GitOps, worden de volgende resources in uw cluster gemaakt:

* Naamruimten: `cluster-config` , `team-a` , `team-b`
* Implementatie: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

De `config-agent` pollt Azure naar nieuwe of bijgewerkte configuraties. Deze taak duurt maximaal 30 seconden.

Als u een privéopslagplaats aan de configuratie wilt koppelen, voltooit u de onderstaande stappen in [Configuratie toepassen vanuit een persoonlijke Git-opslagplaats.](#apply-configuration-from-a-private-git-repository)

## <a name="use-azure-cli"></a>Azure CLI gebruiken
Gebruik de Azure CLI-extensie voor om `k8s-configuration` een verbonden cluster te koppelen aan de [Git-voorbeeldopslagplaats](https://github.com/Azure/arc-k8s-demo). 
1. Noem deze configuratie `cluster-config` .
1. Instrueer de agent om de operator in de `cluster-config` naamruimte te implementeren.
1. Geef de operator `cluster-admin` machtigingen.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Een openbare Git-opslagplaats gebruiken

| Parameter | Indeling |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] of git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Een persoonlijke Git-opslagplaats gebruiken met door SSH en Flux gemaakte sleutels

Voeg de openbare sleutel die door Flux wordt gegenereerd toe aan het gebruikersaccount in uw Git-serviceprovider. Als de sleutel wordt toegevoegd aan de opslagplaats in plaats van het gebruikersaccount, gebruikt `git@` u in plaats van in de `user@` URL. 

Ga naar de [sectie Configuratie toepassen vanuit een persoonlijke Git-opslagplaats](#apply-configuration-from-a-private-git-repository) voor meer informatie.


| Parameter | Indeling | Notities
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] of user@server:repo [.git] | `git@` kan vervangen `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Een persoonlijke Git-opslagplaats gebruiken met SSH en door de gebruiker geleverde sleutels

Geef uw eigen persoonlijke sleutel rechtstreeks of in een bestand op. De sleutel moet de [PEM-indeling hebben](https://aka.ms/PEMformat) en eindigen met newline (\n). 

Voeg de bijbehorende openbare sleutel toe aan het gebruikersaccount in uw Git-serviceprovider. Als de sleutel wordt toegevoegd aan de opslagplaats in plaats van het gebruikersaccount, gebruikt `git@` u in plaats van `user@` . 

Ga naar de [sectie Configuratie toepassen vanuit een persoonlijke Git-opslagplaats](#apply-configuration-from-a-private-git-repository) voor meer informatie.  

| Parameter | Indeling | Notities |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] of user@server:repo [.git] | `git@` kan vervangen `user@` |
| `--ssh-private-key` | met base64 gecodeerde sleutel in [PEM-indeling](https://aka.ms/PEMformat) | Geef de sleutel rechtstreeks op |
| `--ssh-private-key-file` | volledig pad naar lokaal bestand | Geef het volledige pad op naar het lokale bestand met de PEM-indelingssleutel

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Een persoonlijke Git-host gebruiken met SSH en door de gebruiker opgegeven bekende hosts

De Flux-operator houdt een lijst bij met algemene Git-hosts in het bekende hosts-bestand om de Git-opslagplaats te verifiëren voordat de SSH-verbinding tot stand wordt brengen. Als u een  ongebruikelijke Git-opslagplaats of uw eigen Git-host gebruikt, kunt u de hostsleutel leveren zodat Flux uw opslagplaats kan identificeren. 

Net als persoonlijke sleutels kunt u uw persoonlijke known_hosts inhoud rechtstreeks of in een bestand. Gebruik bij het leveren van uw eigen [inhoud](https://aka.ms/KnownHostsFormat)known_hosts specificaties voor de inhoudsindeling, samen met een van de bovenstaande SSH-sleutelscenario's.

| Parameter | Indeling | Notities |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] of user@server:repo [.git] | `git@` kan vervangen `user@` |
| `--ssh-known-hosts` | base64-gecodeerd | Bekende hostinhoud rechtstreeks leveren |
| `--ssh-known-hosts-file` | volledig pad naar lokaal bestand | Bekende hostinhoud in een lokaal bestand verstrekken |

### <a name="use-a-private-git-repository-with-https"></a>Een persoonlijke Git-opslagplaats gebruiken met HTTPS

| Parameter | Indeling | Notities |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | HTTPS met basis auth |
| `--https-user` | raw of base64-encoded | HTTPS-gebruikersnaam |
| `--https-key` | raw of base64-encoded | Persoonlijk HTTPS-toegangs token of wachtwoord

>[!NOTE]
>* Helm-operatorgrafiekversie 1.2.0+ ondersteunt de privé-auth van de HTTPS Helm-release.
>* Https Helm-release wordt niet ondersteund voor door AKS beheerde clusters.
>* Als u Flux nodig hebt om toegang te krijgen tot de Git-opslagplaats via uw proxy, moet u de Azure Arc agents bijwerken met de proxyinstellingen. Zie Verbinding maken met een [uitgaande proxyserver voor meer informatie.](./quickstart-connect-cluster.md#connect-using-an-outbound-proxy-server)


## <a name="additional-parameters"></a>Aanvullende parameters

Pas de configuratie aan met de volgende optionele parameters:

| Parameter | Beschrijving |
| ------------- | ------------- |
| `--enable-helm-operator`| Schakel over om ondersteuning voor Helm-grafiekimplementaties in te schakelen. |
| `--helm-operator-params` | Grafiekwaarden voor Helm-operator (indien ingeschakeld). Bijvoorbeeld `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Grafiekversie voor Helm-operator (indien ingeschakeld). Gebruik versie 1.2.0+. Standaardinstelling: 1.2.0. |
| `--operator-namespace` | Naam voor de naamruimte van de operator. Standaardinstelling: 'standaard'. Maximaal: 23 tekens. |
| `--operator-params` | Parameters voor operator. Moet worden opgegeven tussen enkele aanhalingstekens. Bijvoorbeeld: ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opties die worden ondersteund in  `--operator-params` :

| Optie | Beschrijving |
| ------------- | ------------- |
| `--git-branch`  | Vertakking van Git-opslagplaats die moet worden gebruikt voor Kubernetes-manifesten. De standaardwaarde is 'master'. Nieuwere opslagplaatsen hebben een hoofdvertakking met de naam `main` . In dat geval moet u `--git-branch=main` instellen. |
| `--git-path`  | Relatief pad binnen de Git-opslagplaats voor Flux om Kubernetes-manifesten te vinden. |
| `--git-readonly` | Git-opslagplaats wordt beschouwd als alleen-lezen. Flux probeert er niet naar te schrijven. |
| `--manifest-generation`  | Als deze functie is ingeschakeld, zal Flux zoeken naar .flux.yaml en Kustomize of andere manifestgeneratoren uitvoeren. |
| `--git-poll-interval`  | Periode waarin de Git-opslagplaats moet worden gepeild voor nieuwe door commits. De standaardwaarde is `5m` (5 minuten). |
| `--sync-garbage-collection`  | Als deze functie is ingeschakeld, verwijdert Flux de resources die zijn gemaakt, maar zijn ze niet meer aanwezig in Git. |
| `--git-label`  | Label om de voortgang van de synchronisatie bij te houden. Wordt gebruikt om de Git-vertakking te taggen.  De standaardinstelling is `flux-sync`. |
| `--git-user`  | Gebruikersnaam voor Git-door commit. |
| `--git-email`  | E-mail die moet worden gebruikt voor Git-commit. 

Als u niet wilt dat Flux naar de opslagplaats schrijft en of niet is ingesteld, wordt `--git-user` `--git-email` dit automatisch `--git-readonly` ingesteld.

Zie de [Flux-documentatie voor meer informatie.](https://aka.ms/FluxcdReadme)

> [!TIP]
> U kunt een configuratie maken in de Azure Portal op het **tabblad GitOps** van Azure Arc Kubernetes-resource.

## <a name="validate-the-configuration"></a>De configuratie valideren

Gebruik de Azure CLI om te controleren of de configuratie is gemaakt.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

De configuratieresource wordt bijgewerkt met informatie over de nalevingsstatus, berichten en debuggen.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Wanneer een configuratie wordt gemaakt of bijgewerkt, gebeuren er een paar dingen:

1. De Azure Arc controleert Azure Resource Manager nieuwe of `config-agent` bijgewerkte configuraties ( ) en ziet de nieuwe `Microsoft.KubernetesConfiguration/sourceControlConfigurations` `Pending` configuratie.
1. De `config-agent` leest de configuratie-eigenschappen en maakt de doelnaamruimte.
1. De Azure Arc maakt een Kubernetes-serviceaccount en wijs dit toe aan `controller-manager` [ClusterRoleBinding of RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) voor de juiste machtigingen ( `cluster` of `namespace` scope). Vervolgens wordt een exemplaar van `flux` geïmplementeerd.
1. Als u de optie SSH gebruikt met door Flux gegenereerde sleutels, genereert een `flux` SSH-sleutel en registreert u de openbare sleutel.
1. De `config-agent` status van rapporten wordt terug naar de configuratieresource in Azure.

Tijdens het inrichtingsproces doorstaat de configuratieresource enkele statuswijzigingen. Controleer de voortgang met de `az k8s-configuration show ...` bovenstaande opdracht:

| Fasewijziging | Description |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Vertegenwoordigt de begin- en voortgangsfase. |
| `complianceStatus` -> `Installed`  | `config-agent` het cluster is geconfigureerd en zonder fouten `flux` is geïmplementeerd. |
| `complianceStatus` -> `Failed` | `config-agent` er is een fout opgetreden bij het implementeren `flux` van . Details worden gegeven in de `complianceStatus.message` antwoord-body. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Configuratie toepassen vanuit een persoonlijke Git-opslagplaats

Als u een persoonlijke Git-opslagplaats gebruikt, moet u de openbare SSH-sleutel in uw opslagplaats configureren. U levert of Flux genereert de openbare SSH-sleutel. U kunt de openbare sleutel configureren in de specifieke Git-opslagplaats of op de Git-gebruiker die toegang heeft tot de opslagplaats. 

### <a name="get-your-own-public-key"></a>Uw eigen openbare sleutel op halen

Als u uw eigen SSH-sleutels hebt gegenereerd, hebt u al de persoonlijke en openbare sleutels.

#### <a name="get-the-public-key-using-azure-cli"></a>De openbare sleutel op te halen met behulp van Azure CLI 

Gebruik het volgende in Azure CLI als Flux de sleutels genereert.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Haal de openbare sleutel op uit de Azure Portal

Doorloop het volgende in Azure Portal flux de sleutels genereert.

1. Navigeer in Azure Portal naar de verbonden clusterresource.
2. Selecteer op de resourcepagina 'GitOps' en bekijk de lijst met configuraties voor dit cluster.
3. Selecteer de configuratie die gebruikmaakt van de persoonlijke Git-opslagplaats.
4. Kopieer in het contextvenster dat wordt geopend onderaan het venster de openbare sleutel **van de opslagplaats.**

#### <a name="add-public-key-using-github"></a>Openbare sleutel toevoegen met GitHub

Gebruik een van de volgende opties:

* Optie 1: voeg de openbare sleutel toe aan uw gebruikersaccount (van toepassing op alle opslagplaatsen in uw account):  
    1. Open GitHub en klik op het profielpictogram in de rechterbovenhoek van de pagina.
    2. Klik op **Instellingen**.
    3. Klik op **SSH- en GPG-sleutels.**
    4. Klik op **Nieuwe SSH-sleutel.**
    5. Een titel op te leveren.
    6. Plak de openbare sleutel zonder omliggende aanhalingstekens.
    7. Klik op **SSH-sleutel toevoegen.**

* Optie 2: voeg de openbare sleutel toe als een implementatiesleutel aan de Git-opslagplaats (alleen van toepassing op deze opslagplaats):  
    1. Open GitHub en navigeer naar uw opslagplaats.
    1. Klik op **Instellingen**.
    1. Klik op **Sleutels implementeren.**
    1. Klik op **Implementatiesleutel toevoegen.**
    1. Een titel op te leveren.
    1. Controleer **Schrijftoegang toestaan.**
    1. Plak de openbare sleutel zonder omliggende aanhalingstekens.
    1. Klik op **Sleutel toevoegen.**

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Openbare sleutel toevoegen met behulp van een Azure DevOps-opslagplaats

Gebruik de volgende stappen om de sleutel toe te voegen aan uw SSH-sleutels:

1. Klik **onder Gebruikersinstellingen** rechtsboven (naast de profielafbeelding) op **Openbare SSH-sleutels.**
1. Selecteer **+ Nieuwe sleutel.**
1. Een naam op te geven.
1. Plak de openbare sleutel zonder omliggende aanhalingstekens.
1. Klik op **Add**.

## <a name="validate-the-kubernetes-configuration"></a>De Kubernetes-configuratie valideren

Nadat `config-agent` het exemplaar is `flux` geïnstalleerd, moeten resources in de Git-opslagplaats naar het cluster stromen. Controleer of de naamruimten, implementaties en resources zijn gemaakt met de volgende opdracht:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

We kunnen zien dat `team-a` `team-b` , , en `itops` `cluster-config` naamruimten zijn gemaakt.

De `flux` operator is geïmplementeerd in de `cluster-config` naamruimte, zoals aangegeven door de configuratieresource:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Verdere verkenning

U kunt de andere resources die zijn geïmplementeerd als onderdeel van de configuratieopslagplaats verkennen met behulp van:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Resources opschonen

Verwijder een configuratie met behulp van de Azure CLI of Azure Portal. Nadat u de opdracht delete hebt uitgevoerd, wordt de configuratieresource onmiddellijk verwijderd in Azure. De gekoppelde objecten uit het cluster moeten binnen tien minuten volledig worden verwijderd. Als de configuratie de status Mislukt heeft wanneer deze wordt verwijderd, kan de volledige verwijdering van gekoppelde objecten tot een uur duren.

Wanneer een configuratie met bereik wordt verwijderd, wordt de naamruimte niet verwijderd door Azure Arc om te voorkomen dat bestaande `namespace` workloads worden geschonden. Indien nodig kunt u deze naamruimte handmatig verwijderen met behulp van `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Eventuele wijzigingen in het cluster die het resultaat zijn van implementaties vanuit de bij te houden Git-opslagplaats, worden niet verwijderd wanneer de configuratie wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende zelfstudie voor informatie over het implementeren van CI/CD met GitOps.
> [!div class="nextstepaction"]
> [CI/CD implementeren met GitOps](./tutorial-gitops-ci-cd.md)
