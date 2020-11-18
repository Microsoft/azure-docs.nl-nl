---
title: Configuraties implementeren met behulp van GitOps op het Kubernetes-cluster waarvoor Arc is ingeschakeld (preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps gebruiken voor een Azure Arc-cluster configuratie (preview-versie)
keywords: GitOps, Kubernetes, K8s, azure, Arc, Azure Kubernetes service, containers
ms.openlocfilehash: ce6c754c308d2979db9b1b8eb36e7858e8a91c3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659791"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Configuraties implementeren met behulp van GitOps op het Kubernetes-cluster waarvoor Arc is ingeschakeld (preview)

GitOps is de praktijk van het declareren van de gewenste status van de Kubernetes-configuratie (implementaties, naam ruimten, enzovoort) in een Git-opslag plaats, gevolgd door een polling en pull gebaseerde implementatie van deze configuraties naar het cluster met behulp van een operator. Dit document bevat informatie over de installatie van dergelijke werk stromen op Azure Arc enabled Kubernetes-clusters.

De verbinding tussen uw cluster en een of meer Git-opslag plaatsen wordt bijgehouden in Azure Resource Manager als een `sourceControlConfiguration` uitbreidings resource. De `sourceControlConfiguration` resource-eigenschappen geven aan waar en hoe Kubernetes resources van Git naar uw cluster moeten stromen. De `sourceControlConfiguration` gegevens worden opgeslagen versleuteld op rest in een Azure Cosmos DB-Data Base om de vertrouwelijkheid van gegevens te garanderen.

De `config-agent` uitvoering in uw cluster is verantwoordelijk voor het volgen van nieuwe of bijgewerkte `sourceControlConfiguration` uitbreidings resources op de Azure-Kubernetes-resource, het implementeren van een stroom operator voor het bekijken van de Git-opslag plaats en het door geven van updates die zijn aangebracht in de `sourceControlConfiguration` . Het is zelfs mogelijk om meerdere `sourceControlConfiguration` bronnen met `namespace` een bereik te maken op hetzelfde Azure Arc-Kubernetes-cluster om multitenancy te bereiken. In dat geval kan elke operator alleen configuraties implementeren naar de betreffende naam ruimte.

De Git-opslag plaats kan alle geldige Kubernetes-resources bevatten, waaronder naam ruimten, ConfigMaps, implementaties, DaemonSets, enzovoort.  Het kan ook helm-grafieken bevatten voor het implementeren van toepassingen. Een gemeen schappelijke reeks scenario's is het definiëren van een basislijn configuratie voor uw organisatie, zoals algemene Azure-rollen en-bindingen, bewakings-of logboek registratie agenten of cluster-brede Services.

Hetzelfde patroon kan worden gebruikt om een grotere verzameling clusters te beheren, die kan worden geïmplementeerd in heterogene omgevingen. U hebt bijvoorbeeld één opslag plaats die de basislijn configuratie voor uw organisatie definieert en die in één keer van toepassing is op tien tallen Kubernetes-clusters. Met [Azure Policy kan](use-azure-policy.md) het maken van een `sourceControlConfiguration` met een specifieke set para meters worden geautomatiseerd op alle Kubernetes-resources van Azure-Arc onder een bereik (abonnement of resource groep).

In deze hand leiding vindt u instructies voor het Toep assen van een set configuraties met het bereik Cluster beheer.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand Kubernetes-verbonden Azure-Arc-cluster. Als u een verbonden cluster nodig hebt, raadpleegt u de [Snelstartgids een cluster verbinden](./connect-cluster.md).

## <a name="create-a-configuration"></a>Een configuratie maken

De [voor beeld-opslag plaats](https://github.com/Azure/arc-k8s-demo) die in dit document wordt gebruikt, is gestructureerd rond de persoon van een cluster operator die een paar naam ruimten wil inrichten, een gemeen schappelijke werk belasting moet implementeren en een bepaalde specifieke configuratie voor het team moet bieden. Met deze opslag plaats worden de volgende resources op het cluster gemaakt:

**Naam ruimten:** `cluster-config` , `team-a` , `team-b` 
 **implementatie:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

De `config-agent` polleert Azure gedurende een nieuwe of bijgewerkte `sourceControlConfiguration` periode van 30 seconden. Dit is de maximale hoeveelheid tijd die nodig is `config-agent` om een nieuwe of bijgewerkte configuratie op te halen.
Als u een persoonlijke opslag plaats koppelt aan de `sourceControlConfiguration` , moet u de stappen in [configuratie Toep assen vanuit een persoonlijke Git-opslag plaats](#apply-configuration-from-a-private-git-repository)ook volt ooien.

### <a name="using-azure-cli"></a>Azure CLI gebruiken

Met de Azure CLI-extensie voor kunt `k8sconfiguration` u ons verbonden cluster koppelen aan een [voor beeld van een Git-opslag plaats](https://github.com/Azure/arc-k8s-demo). We zullen deze configuratie een naam geven `cluster-config` , de agent instrueren de operator in de `cluster-config` naam ruimte te implementeren en de operator `cluster-admin` machtigingen geven.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Uitvoer**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>opslagplaats-URL-para meter

Dit zijn de ondersteunde scenario's voor de waarde van de para meter---Repository-URL.

| Scenario | Indeling | Beschrijving |
| ------------- | ------------- | ------------- |
| Openbaar Git-opslag plaats | http [s]://Server/repo.git of git://server/repo.git   | Openbaar Git-opslag plaats  |
| Privé Git opslag plaats – SSH-sleutels-gemaakt op basis van stromen | SSH://[user@] Server/opslag plaats. git of [user@] server: opslag plaats. git | De open bare sleutel die wordt gegenereerd door stroom moet worden toegevoegd aan het gebruikers account in uw Git-service provider. Als de sleutel Deploy wordt toegevoegd aan opslag plaats in plaats van met een gebruikers account, gebruikt u `git@` in plaats van `user@` . [Hier](#apply-configuration-from-a-private-git-repository) vindt u meer informatie |

Deze scenario's worden ondersteund door stroom, maar nog niet door sourceControlConfiguration.

| Scenario | Indeling | Beschrijving |
| ------------- | ------------- | ------------- |
| Privé Git opslag plaats-HTTPS | https://server/repo.git | Binnenkort beschikbaar (biedt ondersteuning voor gebruikers naam/wacht woord, gebruikers naam/token, certificaat) |
| Privé Git opslag plaats-SSH: door de gebruiker verschafte sleutels | SSH://[user@] Server/opslag plaats. git of [user@] server: opslag plaats. git | Binnenkort beschikbaar |
| Privé Git-host – SSH – aangepaste known_hosts | SSH://[user@] Server/opslag plaats. git of [user@] server: opslag plaats. git | Binnenkort beschikbaar |

#### <a name="additional-parameters"></a>Aanvullende para meters

Hier volgen enkele aanvullende para meters om het maken van de configuratie aan te passen:

`--enable-helm-operator` : *Optionele* Schakel optie voor het inschakelen van ondersteuning voor helm-grafiek implementaties.

`--helm-operator-chart-values` : *Optionele* grafiek waarden voor de operator helm (indien ingeschakeld).  Bijvoorbeeld: '--set helm. Verses = v3 '.

`--helm-operator-chart-version` : *Optionele* grafiek versie voor de helm-operator (indien ingeschakeld). Standaard: ' 0.6.0 '.

`--operator-namespace` : *Optionele* naam voor de operator naam ruimte. Standaard: ' default '

`--operator-params` : *Optionele* para meters voor de operator. Moet binnen enkele aanhalings tekens worden opgegeven. Bijvoorbeeld: ```--operator-params='--git-readonly --git-path=releases' ```

Opties die worden ondersteund in--operator-params

| Optie | Beschrijving |
| ------------- | ------------- |
| --Git-Branch  | Vertakking van Git opslag plaats die moet worden gebruikt voor Kubernetes-manifesten. De standaard waarde is ' Master '. |
| --Git-pad  | Relatief pad binnen de Git-opslag plaats voor stroom om Kubernetes-manifesten te vinden. |
| --Git-ReadOnly | Git-opslag plaats worden als alleen-lezen beschouwd. Er wordt geen poging gedaan om te schrijven naar de stroom. |
| --Manifest-generatie  | Als deze functie is ingeschakeld, zoekt stroom. stroom. yaml en voert u Kustomize of andere manifest generatoren uit. |
| --Git-poll-interval  | Periode waarvoor Git-opslag plaats moet worden gecontroleerd op nieuwe door voeringen. De standaard waarde is 5 min. (5 minuten). |
| --garbagecollection-verzameling  | Als deze functie is ingeschakeld, worden door stroom gemaakte resources verwijderd, maar zijn ze niet meer aanwezig in Git. |
| --Git-label  | Label om de voortgang van de synchronisatie bij te houden, die wordt gebruikt om de Git-vertakking te labelen.  De standaard waarde is ' stroom-Sync '. |
| --Git-gebruiker  | Gebruikers naam voor git-door voer. |
| --Git-e-mail  | E-mail die moet worden gebruikt voor git-door voer. |

* Als '--Git-gebruiker ' of '--git-e-mail ' niet is ingesteld (wat betekent dat u niet wilt dat stroom wordt geschreven naar de opslag plaats), wordt--Git-ReadOnly automatisch ingesteld (als u dit nog niet hebt ingesteld).

* Als enableHelmOperator is ingesteld op True, kunnen operatorInstanceName + operatorNamespace-teken reeksen niet langer zijn dan 47 tekens.  Als u niet aan deze limiet voldoet, krijgt u de volgende fout:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Zie [vloei documentatie](https://aka.ms/FluxcdReadme)voor meer informatie.

> [!TIP]
> Het is mogelijk om een sourceControlConfiguration te maken op de Azure Portal en ook op het tabblad **configuraties** van de Blade Kubernetes van Azure-Arc ingeschakeld.

## <a name="validate-the-sourcecontrolconfiguration"></a>De sourceControlConfiguration valideren

Als u de Azure CLI gebruikt, controleert u of deze `sourceControlConfiguration` is gemaakt.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Houd er rekening mee dat de `sourceControlConfiguration` resource wordt bijgewerkt met de nalevings status, berichten en informatie over fout opsporing.

**Uitvoer**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Wanneer de `sourceControlConfiguration` is gemaakt, zijn er enkele dingen die zich op de schermen voordoen:

1. De Azure-Arc `config-agent` bewaakt Azure Resource Manager voor nieuwe of bijgewerkte configuraties ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration` )
1. `config-agent`de nieuwe configuratie wordt gekennisgevingd `Pending`
1. `config-agent` de configuratie-eigenschappen worden gelezen en voor bereidingen voor het implementeren van een beheerd exemplaar van `flux`
    * `config-agent` maakt de doel naam ruimte
    * `config-agent` bereidt een Kubernetes-service account voor met de juiste machtiging ( `cluster` of `namespace` bereik)
    * `config-agent` implementeert een exemplaar van `flux`
    * `flux` Hiermee wordt een SSH-sleutel gegenereerd en wordt de open bare sleutel geregistreerd
1. `config-agent` rapporteert de status terug naar de `sourceControlConfiguration`

Terwijl het inrichtings proces plaatsvindt, `sourceControlConfiguration` wordt de status van een aantal wijzigingen gewijzigd. Bewaak de voortgang met de `az k8sconfiguration show ...` bovenstaande opdracht:

1. `complianceStatus` -> `Pending`: Hiermee worden de initiële statussen en in uitvoeringen
1. `complianceStatus` -> `Installed`: `config-agent` het cluster kan worden geconfigureerd en `flux` zonder fouten worden geïmplementeerd
1. `complianceStatus` -> `Failed`: er `config-agent` is een fout opgetreden bij het implementeren `flux` ; de gegevens moeten beschikbaar zijn in de `complianceStatus.message` antwoord tekst

## <a name="apply-configuration-from-a-private-git-repository"></a>Configuratie Toep assen vanuit een persoonlijke Git-opslag plaats

Als u een privé Git-opslag plaats gebruikt, moet u één taak uitvoeren om de lus te sluiten: Voeg de open bare sleutel toe die is gegenereerd door `flux` als een **implementatie sleutel** in de opslag plaats.

**De open bare sleutel ophalen met behulp van Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**De open bare sleutel ophalen uit de Azure Portal**

1. Navigeer in het Azure Portal naar de verbonden cluster bron.
2. Selecteer op de pagina resource de optie configuraties en Bekijk de lijst met configuraties voor dit cluster.
3. Selecteer de configuratie die gebruikmaakt van de persoonlijke Git-opslag plaats.
4. Kopieer in het context venster dat wordt geopend onder aan het venster de **open bare sleutel van de opslag plaats**.

Als u GitHub gebruikt, kunt u een van de volgende twee opties gebruiken:

**Optie 1: de open bare sleutel toevoegen aan uw gebruikers account**

1. Open GitHub en klik op uw profiel pictogram in de rechter bovenhoek van de pagina.
2. Klik op **instellingen**
3. Klik op **SSH-en GPG-sleutels**
4. Klik op **nieuwe SSH-sleutel**
5. Een titel opgeven
6. Plak de open bare sleutel (min eventuele omringende aanhalings tekens)
7. Klik op **SSH-sleutel toevoegen**

**Optie 2: de open bare sleutel toevoegen als een Deploy-sleutel voor de Git-opslag plaats**

1. Open GitHub, navigeer naar uw opslag plaats, naar **instellingen** en **Implementeer sleutels**
2. Klik op **Deploy-sleutel toevoegen**
3. Een titel opgeven
4. **Schrijf toegang toestaan** inschakelen
5. Plak de open bare sleutel (min eventuele omringende aanhalings tekens)
6. Klik op **sleutel toevoegen**

**Als u een Azure DevOps-opslag plaats gebruikt, voegt u de sleutel toe aan uw SSH-sleutels**

1. Klik onder **gebruikers instellingen** in de rechter bovenhoek (naast de profiel afbeelding) op **open bare SSH-sleutels**
1. Selecteer  **+ nieuwe sleutel**
1. Een naam opgeven
1. De open bare sleutel zonder omringende aanhalings tekens plakken
1. Klik op **Toevoegen**

## <a name="validate-the-kubernetes-configuration"></a>De Kubernetes-configuratie valideren

Nadat `config-agent` het exemplaar is geïnstalleerd `flux` , moeten de resources in de Git-opslag plaats naar het cluster gaan. Controleer of de naam ruimten, implementaties en resources zijn gemaakt:

```console
kubectl get ns --show-labels
```

**Uitvoer**

```console
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

We kunnen zien dat de,, en `team-a` `team-b` `itops` `cluster-config` naam ruimten zijn gemaakt.

De `flux` operator is geïmplementeerd in de `cluster-config` naam ruimte, zoals is gericht op onze `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Uitvoer**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Verdere verkenning

U kunt de andere resources die zijn geïmplementeerd als onderdeel van de configuratie opslagplaats verkennen:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Een configuratie verwijderen

Een `sourceControlConfiguration` gebruiken van Azure CLI of Azure Portal verwijderen.  Nadat u de opdracht verwijderen initieert, `sourceControlConfiguration` wordt de resource direct verwijderd in azure, maar het kan Maxi maal één uur duren voordat de gekoppelde objecten van het cluster volledig worden verwijderd (er is een achterstands item om deze vertraging te verminderen).

> [!NOTE]
> Nadat een sourceControlConfiguration met het bereik van de naam ruimte is gemaakt, is het mogelijk dat gebruikers met een rol die is gekoppeld aan `edit` de naam ruimte, werk belastingen implementeren op deze naam ruimte. Wanneer `sourceControlConfiguration` het bereik van de naam ruimte wordt verwijderd, blijft de naam ruimte intact en wordt deze niet verwijderd om te voor komen dat deze andere workloads worden opgesplitst.
> Wijzigingen in het cluster die het resultaat zijn van implementaties van de getraceerde Git-opslag plaats, worden niet verwijderd wanneer het `sourceControlConfiguration` wordt verwijderd.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Uitvoer**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Volgende stappen

- [Helm gebruiken met broncode beheer configuratie](./use-gitops-with-helm.md)
- [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)
