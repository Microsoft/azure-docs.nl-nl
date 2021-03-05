---
title: Problemen oplossen
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Meer informatie over het oplossen van veelvoorkomende problemen bij het inschakelen en gebruiken van Azure dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s '
ms.openlocfilehash: 14ced0c66b42b6f18c946d0c75091be1af5598f5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197789"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Problemen met Azure dev Spaces oplossen

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Deze hand leiding bevat informatie over veelvoorkomende problemen die zich kunnen voordoen bij het gebruik van Azure dev Spaces.

Als u een probleem ondervindt bij het gebruik van Azure dev Spaces, maakt u een [probleem in de Azure dev Spaces github-opslag plaats](https://github.com/Azure/dev-spaces/issues).

## <a name="before-you-begin"></a>Voordat u begint

Om problemen effectiever op te lossen, kan het helpen om meer gedetailleerde logboeken te maken voor beoordeling.

Voor Visual Studio stelt u de `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` omgevings variabele in op 1. Zorg ervoor dat u Visual Studio opnieuw start om de omgevings variabele van kracht te laten worden. Wanneer deze functie is ingeschakeld, worden gedetailleerde logboeken naar uw `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` Directory geschreven.

In de CLI kunt u meer informatie tijdens het uitvoeren van de opdracht uitvoeren met behulp van de `--verbose` Switch. U kunt ook meer gedetailleerde logboeken bekijken in `%TEMP%\Azure Dev Spaces` . Op een Mac kan de map *temp* worden gevonden door `echo $TMPDIR` vanuit een Terminal venster te worden uitgevoerd. Op een Linux-computer is de map *temp* doorgaans `/tmp` . Controleer bovendien of logboek registratie is ingeschakeld in uw [Azure cli-configuratie bestand](/cli/azure/azure-cli-configuration#cli-configuration-values-and-environment-variables).

Azure dev Spaces werkt ook het beste bij het opsporen van fouten in één exemplaar of pod. Het `azds.yaml` bestand bevat een instelling, *replicaCount*, die het aantal peulen aangeeft dat Kubernetes voor uw service wordt uitgevoerd. Als u de *replicaCount* wijzigt om uw toepassing te configureren voor het uitvoeren van meerdere peulen voor een bepaalde service, wordt het fout opsporingsprogramma gekoppeld aan de eerste Pod, wanneer deze alfabetisch wordt weer gegeven. Het fout opsporingsprogramma koppelt aan een andere pod wanneer de oorspronkelijke pod wordt gerecycled, mogelijk als gevolg van onverwacht gedrag.

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Veelvoorkomende problemen bij het inschakelen van Azure dev Spaces

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Fout: kan de Azure dev Space-controller niet maken

Mogelijk ziet u deze fout wanneer er iets mis gaat met het maken van de controller. Als het een tijdelijke fout is, verwijdert u de controller en maakt u deze opnieuw om het probleem op te lossen.

U kunt ook proberen de controller te verwijderen:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Gebruik de Azure dev Spaces CLI om een controller te verwijderen. Het is niet mogelijk om een controller uit Visual Studio te verwijderen. Het is ook niet mogelijk om de Azure dev Spaces CLI te installeren in de Azure Cloud Shell zodat u een controller niet uit de Azure Cloud Shell kunt verwijderen.

Als u de CLI van Azure dev Spaces niet hebt geïnstalleerd, kunt u deze eerst installeren met de volgende opdracht en vervolgens de controller verwijderen:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>De controller kan niet worden gemaakt vanwege een lengte van de naam van de controller

De naam van een controller van Azure dev Space mag niet langer zijn dan 31 tekens. Als de naam van de controller langer is dan 31 tekens wanneer u dev Spaces inschakelt in een AKS-cluster of een controller maakt, ontvangt u een fout melding. Bijvoorbeeld:

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

U kunt dit probleem oplossen door een controller met een andere naam te maken. Bijvoorbeeld:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Fout bij het inschakelen van ontwikkel ruimten wanneer Windows-knooppunt groepen worden toegevoegd aan een AKS-cluster

Momenteel is Azure dev Spaces alleen bedoeld om te worden uitgevoerd op Linux en alleen knoop punten. Wanneer u een AKS-cluster met een Windows-knooppunt groep hebt, moet u ervoor zorgen dat Azure dev Spaces alleen worden gepland voor Linux-knoop punten. Als er een Azure dev Space Pod is gepland om te worden uitgevoerd op een Windows-knoop punt, zal die pod niet starten en kunnen er geen dev-ruimten worden ingeschakeld.

U kunt dit probleem oplossen door [een Taint toe te voegen](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) aan uw AKS-cluster om ervoor te zorgen dat Linux peul niet is gepland om te worden uitgevoerd op een Windows-knoop punt.

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fout "er zijn geen untainted Linux-knoop punten gevonden in de status gereed op het cluster. Er moet ten minste één untainted Linux-knoop punt in de status gereed zijn om een Peul te implementeren in de azds-naam ruimte.

Azure dev Spaces kan geen controller maken op uw AKS-cluster omdat er geen untainted-knoop punt met de status *gereed* kan worden gevonden voor het plannen van een Peul op. Voor Azure-ontwikkel ruimten is ten minste één Linux-knoop punt met de status *gereed* die het plannen van peulen toestaat zonder dat er toleranties worden opgegeven.

U kunt dit probleem oplossen door [de Taint-configuratie](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) op uw AKS-cluster bij te werken om ervoor te zorgen dat er ten minste één Linux-knoop punt kan worden gepland, zonder dat u de toleranties opgeeft. Zorg er ook voor dat er ten minste één Linux-knoop punt is *dat het plannen* van Peul toestaat zonder dat u de toleranties kunt opgeven. Als het knoop punt lange tijd neemt om de status *gereed* te bereiken, kunt u het knoop punt opnieuw starten.

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fout ' Azure dev Spaces is niet correct geïnstalleerd ' bij het uitvoeren van AZ AKS use-dev-Spaces

Het installatiepad van een update voor de Azure dev Space CLI is gewijzigd. Als u een eerdere versie van Azure CLI dan 2.0.63 gebruikt, wordt deze fout weer geven. Als u uw versie van de Azure CLI wilt weer geven, gebruikt u `az --version` .

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

Ondanks het fout bericht bij het uitvoeren `az aks use-dev-spaces` van een versie van de Azure cli vóór 2.0.63, wordt de installatie voltooid. U kunt `azds` zonder problemen blijven gebruiken.

U kunt dit probleem oplossen door uw installatie van [Azure cli](/cli/azure/install-azure-cli) bij te werken naar 2.0.63 of hoger. Met deze update wordt het fout bericht opgelost dat wordt weer gegeven wanneer het wordt uitgevoerd `az aks use-dev-spaces` . U kunt ook uw huidige versie van de Azure CLI en de Azure dev Spaces CLI blijven gebruiken.

### <a name="error-unable-to-reach-kube-apiserver"></a>Fout ' kan uitvoeren-apiserver niet bereiken '

Mogelijk ziet u deze fout wanneer Azure dev Spaces geen verbinding kunnen maken met de API-server van uw AKS-cluster.

Als de toegang tot uw AKS-cluster-API-server is vergrendeld of als u de [API-server geautoriseerde IP-](../aks/api-server-authorized-ip-ranges.md) adresbereiken hebt ingeschakeld voor uw AKS-cluster, moet u ook uw cluster [maken](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled) of [bijwerken](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges) om [extra bereiken op basis van uw regio toe te staan](configure-networking.md#aks-cluster-network-requirements)

Zorg ervoor dat de API-server beschikbaar is door kubectl-opdrachten uit te voeren. Als de API-server niet beschikbaar is, neemt u contact op met de AKS-ondersteuning en probeert u het opnieuw wanneer de API-server werkt.

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Veelvoorkomende problemen bij het voorbereiden van het project voor Azure-ontwikkel ruimten

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Waarschuwing "Dockerfile kan niet worden gegenereerd als gevolg van niet-ondersteunde taal"
Azure dev Spaces biedt systeem eigen ondersteuning voor C# en Node.js. Wanneer u uitvoert `azds prep` in een map met code die is geschreven in een van deze talen, worden door Azure dev Spaces automatisch een geschikte Dockerfile voor u gemaakt.

U kunt nog steeds Azure dev Spaces gebruiken met code geschreven in andere talen, maar u moet de Dockerfile hand matig maken voordat u `azds up` voor de eerste keer wordt uitgevoerd.

Als uw toepassing is geschreven in een taal die door Azure dev Spaces niet systeem eigen ondersteuning biedt, moet u een geschikte Dockerfile opgeven voor het bouwen van een container installatie kopie die uw code uitvoert. Docker bevat een [lijst met aanbevolen procedures voor het schrijven van Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) en een [Dockerfile-verwijzing](https://docs.docker.com/engine/reference/builder/) die u kan helpen bij het schrijven van een Dockerfile die aansluit bij uw behoeften.

Zodra er een geschikte Dockerfile aanwezig is, voert u uit `azds up` om uw toepassing uit te voeren in azure dev Spaces.

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Veelvoorkomende problemen bij het starten of stoppen van services met Azure dev Spaces

### <a name="error-config-file-not-found"></a>Fout ' configuratie bestand niet gevonden: '

Wanneer `azds up` u deze uitvoert, wordt deze fout weer geven. Beide `azds up` en `azds prep` moeten worden uitgevoerd vanuit de hoofd directory van het project dat u wilt uitvoeren in uw ontwikkelaars ruimte.

Dit probleem oplossen:
1. Wijzig uw huidige map in de hoofdmap met uw service code. 
1. Als u geen _azds. yaml_ -bestand in de map code hebt, voert u uit `azds prep` om assets voor docker-, Kubernetes-en Azure-ontwikkel ruimten te genereren.

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Time-out bij wachten op build van container installatie kopie... stap met virtuele AKS-knoop punten

Deze time-out treedt op wanneer u probeert om dev Spaces te gebruiken om een service uit te voeren die is geconfigureerd om te worden uitgevoerd op een [virtueel AKS-knoop punt](../aks/virtual-nodes-portal.md). Ontwikkel ruimten biedt momenteel geen ondersteuning voor het bouwen of opsporen van fouten op virtuele knoop punten.

Als u `azds up` de switch uitvoert `--verbose` of uitgebreide logboek registratie inschakelt in Visual Studio, ziet u meer details:

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

De bovenstaande opdracht toont aan dat de pod van de service is toegewezen aan *virtueel-node-ACI-Linux*. Dit is een virtueel knoop punt.

Om dit probleem op te lossen, werkt u de helm-grafiek voor de service bij om de *nodeSelector* *-of de* toegestane waarden te verwijderen waarmee de service op een virtueel knoop punt kan worden uitgevoerd. Deze waarden worden meestal gedefinieerd in het bestand van de grafiek `values.yaml` .

U kunt nog steeds een AKS-cluster gebruiken waarvoor de functie virtuele knoop punten is ingeschakeld, als de service die u wilt bouwen of fouten wilt opsporen via dev Spaces, worden uitgevoerd op een VM-knoop punt. Het uitvoeren van een service met ontwikkel ruimten op een VM-knoop punt is de standaard configuratie.

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Fout "kan geen kant-en-klare Tiller-pod vinden" bij het starten van dev Spaces

Deze fout treedt op als de helm-client niet meer kan communiceren met de Tiller-pod die in het cluster wordt uitgevoerd.

U kunt dit probleem oplossen door de agent knooppunten in het cluster opnieuw op te starten.

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>Fout ' release azds- \<identifier\> - \<spacename\> - \<servicename\> failed: Services \<servicename\> bestaat al ' of ' pull-toegang geweigerd voor \<servicename\> , bestaat niet of ' docker aanmelding ' is vereist.

Deze fouten kunnen zich voordoen als u direct helm-opdrachten (zoals `helm install` , `helm upgrade` of `helm delete` ) combineert met de opdracht dev Spaces (zoals `azds up` en `azds down` ) binnen dezelfde ontwikkel ruimte. Dit gebeurt omdat ontwikkel ruimten een eigen Tiller-exemplaar hebben, wat een conflict veroorzaakt met uw eigen Tiller-exemplaar dat wordt uitgevoerd in dezelfde dev-ruimte.

Het is nauw keurig om zowel helm-opdrachten als dev Spaces-opdrachten te gebruiken voor hetzelfde AKS-cluster, maar elke naam ruimte die voor ontwikkel aars is ingeschakeld, moet beide gebruikmaken van een van beide.

Stel dat u een helm opdracht gebruikt om uw hele toepassing uit te voeren in een bovenliggende ontwikkel ruimte. U kunt onderliggende ontwikkel ruimten van die bovenliggende shape maken, ontwikkel ruimten gebruiken om afzonderlijke services uit te voeren binnen de onderliggende ontwikkel ruimten en de services tegelijk te testen. Wanneer u klaar bent om uw wijzigingen te controleren, gebruikt u een helm-opdracht om de bijgewerkte code te implementeren in de bovenliggende ontwikkel ruimte. Gebruik niet `azds up` voor het uitvoeren van de bijgewerkte service in de bovenliggende ontwikkel ruimte, omdat deze in eerste instantie een conflict veroorzaakt met de service die wordt uitgevoerd met behulp van helm.

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>Bestaande Dockerfile niet gebruikt voor het bouwen van een container

Azure dev Spaces kunnen worden geconfigureerd om te verwijzen naar een specifieke _Dockerfile_ in uw project. Als er Azure dev Spaces worden gebruikt, wordt de _Dockerfile_ die u verwacht om uw containers te bouwen, mogelijk expliciet duidelijk te maken met Azure dev Spaces die Dockerfile moeten worden gebruikt. 

Om dit probleem op te lossen, opent u het _azds. yaml_ -bestand dat Azure-ontwikkel ruimten die in uw project zijn gegenereerd. Update *configuraties: ontwikkelen: Build: dockerfile* om te verwijzen naar de dockerfile die u wilt gebruiken. Bijvoorbeeld:

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>Fout "niet toegestaan: authenticatie vereist" bij het gebruik van een docker-installatie kopie uit een persoonlijk REGI ster

U gebruikt een docker-installatie kopie uit een persoonlijk REGI ster waarvoor verificatie is vereist.

Om dit probleem op te lossen, kunt u ontwikkel ruimten toestaan om installatie kopieën uit dit privé-REGI ster te verifiëren en te halen met behulp van [imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets). Als u imagePullSecrets wilt gebruiken, [maakt u een Kubernetes-geheim](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) in de naam ruimte waarin u de installatie kopie gebruikt. Geef vervolgens het geheim op als een imagePullSecret in `azds.yaml` .

Hieronder ziet u een voor beeld van een opgegeven imagePullSecrets in `azds.yaml` .

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> Als u imagePullSecrets in instelt `azds.yaml` , worden imagePullSecrets die zijn opgegeven in de `values.yaml` .

### <a name="error-service-cannot-be-started"></a>Fout: de service kan niet worden gestart.

Mogelijk ziet u deze fout wanneer uw service code niet kan worden gestart. De oorzaak is vaak in gebruikers code. Voor meer informatie over diagnostische gegevens kunt u gedetailleerdere logboek registratie inschakelen bij het starten van de service.

Gebruik op de opdracht regel de `--verbose` om gedetailleerde logboek registratie in te scha kelen. U kunt ook een uitvoer indeling opgeven met `--output` . Bijvoorbeeld:

```cmd
azds up --verbose --output json
```

In Visual Studio:

1. Open de **Hulpprogram ma's > opties** en klik onder **projecten en oplossingen** op **bouwen en uitvoeren**.
2. Wijzig de instellingen voor de **uitgebreide uitvoer van MSBuild-project compilatie** in **gedetailleerde** of **diagnose**.

    ![Scherm afbeelding van opties dialoog venster](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>Een service opnieuw uitvoeren na het opnieuw maken van de controller

U ontvangt een fout bericht dat de *service niet kan worden gestart* wanneer u een service opnieuw probeert uit te voeren nadat u de Azure dev Space-controller die is gekoppeld aan dit cluster hebt verwijderd en opnieuw hebt gemaakt. In dit geval bevat de uitgebreide uitvoer de volgende tekst:

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Deze fout treedt op omdat het verwijderen van de controller van de dev Spaces geen Services verwijdert die eerder door die controller zijn geïnstalleerd. Het opnieuw maken van de controller en poging om de services uit te voeren met de nieuwe controller mislukt, omdat de oude Services nog steeds aanwezig zijn.

Om dit probleem op te lossen, gebruikt u de `kubectl delete` opdracht om de oude Services hand matig uit uw cluster te verwijderen en voert u vervolgens dev Spaces opnieuw uit om de nieuwe services te installeren.

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>Fout: de service kan niet worden gestart. bij gebruik van Dockerfiles met meerdere fasen

U ontvangt een fout melding dat een *service niet kan worden gestart* wanneer u een Dockerfile met meerdere fasen gebruikt. In dit geval bevat de uitgebreide uitvoer de volgende tekst:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Deze fout treedt op omdat Azure dev Spaces momenteel geen builds van meerdere fasen ondersteunen. Herschrijf uw Dockerfile om te voor komen dat meerdere fases worden gebouwd.

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>Netwerk verkeer wordt niet doorgestuurd naar uw AKS-cluster bij het verbinden van uw ontwikkel computer

Wanneer u [Azure dev Spaces gebruikt om uw AKS-cluster te verbinden met uw ontwikkel computer](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes), kan er een probleem optreden waarbij het netwerk verkeer niet wordt doorgestuurd tussen uw ontwikkel computer en het AKS-cluster.

Wanneer u uw ontwikkel machine verbindt met uw AKS-cluster, stuurt Azure dev Spaces het netwerk verkeer tussen uw AKS-cluster en uw ontwikkel computer door het bestand van uw ontwikkel machine te wijzigen `hosts` . Met Azure dev Spaces maakt u een vermelding in het `hosts` adres van de Kubernetes-service die u vervangt als een hostnaam. Dit item wordt gebruikt bij het door sturen van poorten om het netwerk verkeer tussen uw ontwikkel computer en het AKS-cluster te omleiden. Als een service op uw ontwikkel computer een conflict veroorzaakt met de poort van de Kubernetes-service die u vervangt, kunnen Azure-ontwikkel ruimten geen netwerk verkeer door sturen voor de Kubernetes-service. De *Windows BranchCache* -service is bijvoorbeeld doorgaans gebonden aan *0.0.0.0:80*, wat conflicten veroorzaakt voor poort 80 op alle lokale IP-adressen.

Om dit probleem op te lossen, moet u alle services of processen die conflicteren met de poort van de Kubernetes-service die u wilt vervangen, stoppen. U kunt hulpprogram ma's, zoals *netstat*, gebruiken om te controleren welke services of processen op uw ontwikkel computer conflicteren.

U kunt bijvoorbeeld de *Windows BranchCache* -service stoppen en uitschakelen:
* Voer `services.msc` uit vanaf de opdracht prompt.
* Klik met de rechter muisknop op *BranchCache* en selecteer *Eigenschappen*.
* Klik op *stoppen*.
* U kunt deze ook uitschakelen door *opstart type* in te stellen op *uitgeschakeld*.
* Klik op *OK*.

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>Fout ' geen AzureAssignedIdentity gevonden voor Pod: azds/azds-webhook-implementatie- \<id\> in toegewezen status '

Bij het uitvoeren van een service met Azure-ontwikkel ruimten in een AKS-cluster met een [beheerde identiteit](../aks/use-managed-identity.md) en [pod beheerde identiteiten](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) , reageert het proces mogelijk niet meer na de installatie stap van de *grafiek* . Als u de *azds-injectie-webhook* in de *azds* -naam ruimte inspecteert, ziet u deze fout mogelijk.

De services Azure dev Spaces worden uitgevoerd op uw cluster. Gebruik de beheerde identiteit van het cluster om te communiceren met de back-upservers van Azure dev buiten het cluster. Wanneer de beheerde identiteit Pod is geïnstalleerd, worden er netwerk regels geconfigureerd op de knoop punten van uw cluster om alle aanroepen voor beheerde identiteits referenties om te leiden naar een [NMI-daemon (node Managed Identity) die op het cluster is geïnstalleerd](https://github.com/Azure/aad-pod-identity#node-managed-identity). Deze NMI-Daemonset identificeert de aanroepende pod en zorgt ervoor dat Pod is gelabeld om toegang te krijgen tot de aangevraagde beheerde identiteit. Azure dev Spaces kunnen niet detecteren of pod beheerde identiteit is geïnstalleerd op een cluster, waardoor de benodigde configuratie niet kan worden uitgevoerd om Azure dev Spaces-Services toegang te geven tot de beheerde identiteit van het cluster. Omdat de Azure dev Spaces-Services niet zijn geconfigureerd voor toegang tot de beheerde identiteit van het cluster, staat de NMI-Daemonset niet toe dat ze een Azure AD-token voor de beheerde identiteit verkrijgen en niet kunnen communiceren met Azure dev Spaces back-upservices.

Om dit probleem op te lossen, past u een [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) toe voor de *azds-injecter-webhook* en updatet u het gehele instrument door Azure dev Spaces om toegang te krijgen tot de beheerde identiteit.

Maak een bestand met de naam *webhookException. yaml* en kopieer de volgende YAML-definitie:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

Met het bovenstaande bestand maakt u een *AzurePodIdentityException* -object voor de *azds-injecter-webhook*. Als u dit object wilt implementeren, gebruikt u `kubectl` :

```cmd
kubectl apply -f webhookException.yaml
```

Als u het gehele instrument door Azure dev Spaces wilt bijwerken om toegang te krijgen tot de beheerde identiteit, moet u de *naam ruimte* bijwerken in de onderstaande YAML-definitie en `kubectl` deze Toep assen op elke dev-ruimte.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

U kunt ook *identiteit* -en *AzureIdentityBinding* -objecten maken en de pod-labels voor werk belastingen die worden uitgevoerd in ruimten die worden gebruikt door Azure-ontwikkel ruimten, gebruiken om toegang te krijgen tot de beheerde identiteit die door het AKS-cluster is gemaakt.

Als u de details van de beheerde identiteit wilt weer geven, voert u de volgende opdracht uit voor uw AKS-cluster:

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

De bovenstaande opdracht voert de *clientId* en *resourceId* uit voor de beheerde identiteit. Bijvoorbeeld:

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

Als u een *identiteit* -object wilt maken, maakt u een bestand met de naam *clusteridentity. yaml* en gebruikt u de volgende YAML definitie bijgewerkt met de details van uw beheerde identiteit uit de vorige opdracht:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

Als u een *AzureIdentityBinding* -object wilt maken, maakt u een bestand met de naam *clusteridentitybinding. yaml* en gebruikt u de volgende YAML definitie:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

Gebruik de volgende opties om de *identiteit* -en *AzureIdentityBinding* -objecten te implementeren `kubectl` :

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

Nadat u de *identiteit* -en *AzureIdentityBinding* -objecten hebt geïmplementeerd, krijgt elke werk belasting met het label *aadpodidbinding: My-label-value* toegang tot de beheerde identiteit van het cluster. Voeg dit label toe en implementeer alle werk belastingen die in een wille keurige ontwikkel ruimte worden uitgevoerd. Bijvoorbeeld:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

### <a name="error-cannot-get-connection-details-for-azure-dev-spaces-controller-abc-because-it-is-in-the-failed-state-something-wrong-might-have-happened-with-your-controller"></a>Fout ' kan geen verbindings Details ophalen voor de controller ' ABC ' van Azure dev Spaces, omdat deze de status Mislukt heeft. Er is mogelijk een probleem opgetreden met de controller.

U kunt dit probleem oplossen door de Azure dev Spaces-controller uit het cluster te verwijderen en opnieuw te installeren:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Als Azure-ontwikkel ruimten buiten gebruik worden gesteld, kunt u ook [migreren naar Bridge naar Kubernetes](migrate-to-bridge-to-kubernetes.md) . Dit biedt een betere ervaring.

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Veelvoorkomende problemen met het gebruik van Visual Studio en Visual Studio code met Azure dev Spaces

### <a name="error-required-tools-and-configurations-are-missing"></a>Fout: de vereiste hulpprogram ma's en configuraties ontbreken

Deze fout kan optreden bij het starten van code: "[Azure dev Spaces] de vereiste hulpprogram ma's en configuraties voor Build en debug [project name] ontbreken."
De fout betekent dat azds.exe zich niet in de omgevings variabele PATH bevindt, zoals in VS code wordt weer gegeven.

Start VS code vanuit een opdracht prompt waarbij de omgevings variabele PATH correct is ingesteld.

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fout: de vereiste hulpprogram ma's voor het maken van de fout code ' ProjectName ' zijn verouderd. '

U ziet deze fout in Visual Studio code als u een nieuwere versie van de VS code-extensie voor Azure dev Spaces hebt, maar een oudere versie van de Azure dev Spaces-CLI.

Probeer de nieuwste versie van de Azure dev Spaces CLI te downloaden en te installeren:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>Fout: ' kan de uitbrei ding van het fout opsporingsprogramma niet vinden voor het type: coreclr '

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code. Mogelijk hebt u niet de VS code-extensie voor C# geïnstalleerd op uw ontwikkel computer. De C#-extensie bevat ondersteuning voor fout opsporing voor .NET core (CoreCLR).

U kunt dit probleem oplossen door de [VS code-extensie voor C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)te installeren.

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>Fout "het geconfigureerde type debug" coreclr "wordt niet ondersteund"

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code. Mogelijk beschikt u niet over de VS code-uitbrei ding voor Azure dev Spaces die zijn geïnstalleerd op uw ontwikkel computer.

U kunt dit probleem oplossen door de VS code-extensie voor Azure dev Spaces te installeren.

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Fout ' ongeldige ' CWD-waarde '/src '. Het systeem kan het opgegeven bestand niet vinden. " of ' starten: programma '/src/[pad naar project binary] ' bestaat niet '

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code. De VS code-uitbrei ding maakt standaard gebruik `src` van de werkmap voor het project in de container. Als u uw hebt bijgewerkt `Dockerfile` om een andere werkmap op te geven, wordt deze fout weer geven.

U kunt dit probleem oplossen door het `launch.json` bestand onder de submap van de projectmap bij te werken `.vscode` . Wijzig de `configurations->cwd` instructie zodat deze verwijst naar de map die `WORKDIR` is gedefinieerd in het project `Dockerfile` . Mogelijk moet u ook de `configurations->program` richt lijn bijwerken.

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fout: het pipe programma azds is onverwacht afgesloten met code 126. "

Deze fout wordt mogelijk weer gegeven bij het uitvoeren van het fout opsporingsprogramma voor Visual Studio-code.

U kunt dit probleem oplossen door Visual Studio code te sluiten en opnieuw te openen. Start het foutopsporingsprogramma opnieuw.

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Fout ' interne controle mislukt: Bekijk ENOSPC ' bij het toevoegen van fout opsporing aan een Node.js-toepassing

Deze fout treedt op wanneer het knoop punt waarop de Pod wordt uitgevoerd, met de Node.js toepassing waarmee u probeert te koppelen met een fout opsporingsprogramma, de *FS.inotify.max_user_watches* waarde overschrijdt. In sommige gevallen is [de standaard waarde van *FS.inotify.max_user_watches* mogelijk te klein om het toevoegen van een fout opsporingsprogramma rechtstreeks aan een pod toe te voegen](https://github.com/Azure/AKS/issues/772).

Een tijdelijke oplossing voor dit probleem is om de waarde van *FS.inotify.max_user_watches* op elk knoop punt in het cluster te verhogen en het knoop punt opnieuw op te starten om de wijzigingen van kracht te laten worden.

## <a name="other-common-issues"></a>Overige algemene problemen

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fout "azds" wordt niet herkend als een intern of extern opdracht, programma of batch bestand

Deze fout kan optreden als `azds.exe` niet op de juiste wijze is geïnstalleerd of geconfigureerd.

Dit probleem oplossen:

1. Controleer de locatie% Program Files%/Microsoft SDKs\Azure\Azure dev Spaces CLI for `azds.exe` . Als dat het geval is, voegt u die locatie toe aan de omgevings variabele PATH.
2. Als `azds.exe` niet is geïnstalleerd, voert u de volgende opdracht uit:

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>Autorisatie fout ' micro soft. DevSpaces/REGI ster/Action '

U hebt toegang tot de *eigenaar* of *Inzender* nodig in uw Azure-abonnement voor het beheren van Azure dev Spaces. Als u ontwikkelaars ruimten wilt beheren en u geen *eigenaar* of *Inzender* toegang hebt tot het gekoppelde Azure-abonnement, ziet u mogelijk een autorisatie fout. Bijvoorbeeld:

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

Als u dit probleem wilt oplossen, moet u de naam ruimte hand matig registreren met behulp van een account met *eigenaar* of *Inzender* toegang tot het Azure-abonnement `Microsoft.DevSpaces` :

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>Nieuwe Peul wordt niet gestart

De initialisatie functie Kubernetes kan de PodSpec niet Toep assen op nieuwe peulen vanwege Kubernetes RBAC-machtigings wijzigingen in de rol *cluster beheerder* in het cluster. De nieuwe pod kan ook een ongeldige PodSpec hebben, bijvoorbeeld het service account dat is gekoppeld aan het Pod bestaat niet meer. Gebruik de opdracht om de peulen te zien die de status in *behandeling* hebben vanwege het probleem van de initialisatie functie `kubectl get pods` :

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Dit probleem kan van invloed zijn op *alle naam ruimten* in het cluster, met inbegrip van naam ruimten waar Azure dev Spaces niet is ingeschakeld.

U kunt dit probleem oplossen door [de ontwikkel ruimten cli bij te werken naar de meest recente versie](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) en vervolgens de *azds InitializerConfiguration* te verwijderen uit de Azure dev Spaces-controller:

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Wanneer u de azds- *InitializerConfiguration* van de Azure dev Spaces-controller hebt verwijderd, gebruikt u voor het verwijderen van een `kubectl delete` wille keurig verschil met de status in *behandeling* . Nadat alle in behandeling zijnde peul zijn verwijderd, moet u uw peul opnieuw implementeren.

Als nieuwe peulen nog steeds zijn vastgelopen in de status *in behandeling* na een herimplementatie, gebruikt `kubectl delete` u om een wille keurig peul te verwijderen met de status in *behandeling* . Nadat alle in behandeling zijnde peul zijn verwijderd, verwijdert u de controller uit het cluster en installeert u deze opnieuw:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

Nadat de controller opnieuw is geïnstalleerd, implementeert u uw peul opnieuw.

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Onjuiste Azure RBAC-machtigingen voor het aanroepen van de controller en Api's van dev Spaces

De gebruiker die toegang probeert te krijgen tot de Azure dev Spaces-controller moet toegang hebben om de beheerder *kubeconfig* te lezen op het AKS-cluster. Deze machtiging is bijvoorbeeld beschikbaar in de [ingebouwde rol Azure Kubernetes service cluster admin](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). De gebruiker die de Azure dev Spaces-controller opent, moet ook de rol *Inzender* of *eigenaar* Azure hebben voor de controller. Meer informatie over het bijwerken van de machtigingen van een gebruiker voor een AKS-cluster is [hier](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)beschikbaar.

De Azure-rol van de gebruiker voor de controller bijwerken:

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar de resource groep die de controller bevat. Dit is meestal hetzelfde als uw AKS-cluster.
1. Schakel het selectie vakje *verborgen typen weer geven* in.
1. Klik op de controller.
1. Open het deel venster *Access Control (IAM)* .
1. Klik *op het tabblad roltoewijzingen.*
1. Klik op *toevoegen* en vervolgens op *functie toewijzing toevoegen*.
    * Selecteer voor *rol* de optie *Inzender* of *eigenaar*.
    * *Als u toegang wilt toewijzen*, selecteert u *Azure AD-gebruiker,-groep of Service-Principal*.
    * Zoek bij *selecteren* naar de gebruiker aan wie u machtigingen wilt verlenen.
1. Klik op *Opslaan*.

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Het omzetten van de DNS-naam voor een open bare URL die is gekoppeld aan een service dev Spaces is mislukt

U kunt een open bare URL-eind punt voor uw service configureren door de `--enable-ingress` Schakel optie voor de opdracht op te geven `azds prep` of door het `Publicly Accessible` selectie vakje in Visual Studio in te scha kelen. De open bare DNS-naam wordt automatisch geregistreerd wanneer u uw service in dev Spaces uitvoert. Als deze DNS-naam niet is geregistreerd, ziet u dat er *geen pagina kan worden weer gegeven* of dat de *site niet* in uw webbrowser kan worden bereikt wanneer u verbinding maakt met de open bare URL.

Dit probleem oplossen:

* Controleer de status van alle Url's die zijn gekoppeld aan uw ontwikkelaars Spaces-Services:

  ```console
  azds list-uris
  ```

* Als een URL de status in *behandeling* heeft, wordt er nog steeds ontwikkel ruimten gewacht totdat de DNS-registratie is voltooid. Soms duurt het enkele minuten voordat de registratie is voltooid. Met dev Spaces wordt ook een localhost-tunnel voor elke service geopend, die u kunt gebruiken tijdens het wachten op DNS-registratie.
* Als een URL langer dan 5 minuten in *behandeling* is, kan dit wijzen op een probleem met de externe DNS-pod die het open bare eind punt maakt of de pod van de nginx ingress die het open bare eind punt ophaalt. Gebruik de volgende opdrachten om deze velden te verwijderen en AKS toe te staan om ze automatisch opnieuw te maken:
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fout bericht: upstream Connect-fout of verbinding verbreken/opnieuw instellen vóór headers

Deze fout kan optreden wanneer u probeert toegang te krijgen tot uw service. Bijvoorbeeld, wanneer u naar de URL van de service in een browser gaat. Deze fout betekent dat de container poort niet beschikbaar is. Dit kan de volgende oorzaken hebben:

* De container wordt nog steeds gemaakt en geïmplementeerd. Dit probleem kan zich voordoen als u `azds up` het fout opsporingsprogramma uitvoert of start en vervolgens probeert toegang te krijgen tot de container voordat deze is geïmplementeerd.
* Poort configuratie is niet consistent in uw _Dockerfile_, helm-grafiek en server code waarmee een poort wordt geopend.

Dit probleem oplossen:

1. Als de container wordt gebouwd/geïmplementeerd, kunt u 2-3 seconden wachten en de service opnieuw proberen toegang te krijgen. 
1. Controleer de poort configuratie in de volgende assets:
    * **[Helm grafiek](https://docs.helm.sh):** Opgegeven door de `service.port` en `deployment.containerPort` in values. yaml met de `azds prep` opdracht.
    * Alle poorten die in de toepassings code worden geopend, bijvoorbeeld in Node.js: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>Het type of de naam van de naam ruimte ' MyLibrary ' is niet gevonden

Een bibliotheek project dat u gebruikt, is niet gevonden. Met dev Spaces is de build-context standaard op het niveau van project/service.  

Dit probleem oplossen:

1. Wijzig het `azds.yaml` bestand om de build-context in te stellen op het oplossings niveau.
2. Wijzig de `Dockerfile` en de `Dockerfile.develop` bestanden om te verwijzen naar de project bestanden, bijvoorbeeld `.csproj` correct ten opzichte van de nieuwe build-context.
3. Voeg een `.dockerignore` in dezelfde map als het `.sln` bestand toe.
4. Werk de `.dockerignore` met aanvullende vermeldingen naar behoefte bij.

[Hier](https://github.com/sgreenmsft/buildcontextsample)vindt u een voor beeld.

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>Horizon taal pod automatisch schalen werkt niet in een dev-ruimte

Wanneer u een service in een dev-ruimte uitvoert, wordt de pod van die service [geïnjecteerd met extra containers voor instrumentatie](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster) en moeten alle containers in een pod resource limieten en aanvragen instellen voor Horizon taal pod automatisch schalen.

Om dit probleem op te lossen, past u een resource aanvraag toe en beperkt u de containers voor de geïnjecteerde dev-ruimten. Resource aanvragen en-limieten kunnen worden toegepast voor de geïnjecteerde container (devspaces-proxy) door de `azds.io/proxy-resources` aantekening toe te voegen aan uw Pod spec. De waarde moet worden ingesteld op een JSON-object dat de sectie Resources vertegenwoordigt van de container specificatie van de proxy.

Hieronder ziet u een voor beeld van een aantekening voor proxy bronnen die moet worden toegepast op uw Pod-specificatie.
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>Azure dev Spaces inschakelen voor een bestaande naam ruimte met het uitvoeren van een Peul

Mogelijk hebt u een bestaand AKS-cluster en een bestaande naam ruimte met een Peul waarbij u Azure dev Spaces wilt inschakelen.

Als u Azure-ontwikkel ruimten wilt inschakelen voor een bestaande naam ruimte in een AKS-cluster, voert `use-dev-spaces` u uit en gebruikt `kubectl` u dit om alle peulen in die naam ruimte opnieuw te starten.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

Nadat uw peul opnieuw is opgestart, kunt u beginnen met het gebruik van uw bestaande naam ruimte met Azure dev Spaces.

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>Azure dev Spaces inschakelen op een AKS-cluster met beperkt uitgaand verkeer voor cluster knooppunten

Als u Azure-ontwikkel ruimten wilt inschakelen op een AKS-cluster waarvoor het uitgaande verkeer van cluster knooppunten is beperkt, moet u de volgende FQDN-namen toestaan:

| FQDN                                    | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Voor het ophalen van images voor Linux alpine en andere Azure dev Spaces |
| gcr.io | HTTP: 443 | Helm/Tiller-installatie kopieën ophalen|
| storage.googleapis.com | HTTP: 443 | Helm/Tiller-installatie kopieën ophalen|

Werk uw firewall of beveiligings configuratie bij om netwerk verkeer toe te staan van en naar de bovenstaande FQDN-namen en [Azure dev Spaces-infrastructuur services](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations).

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>Fout: kan het cluster niet vinden \<cluster\> in het abonnement \<subscriptionId\>

Deze fout kan optreden als uw kubeconfig-bestand is gericht op een ander cluster of abonnement dan u probeert te gebruiken met het Azure dev Spaces-programma aan de client zijde. Met het hulp programma voor het gebruik van Azure dev Spaces aan de client zijde wordt het gedrag van *kubectl* gerepliceerd, dat gebruikmaakt van [een of meer kubeconfig-bestanden](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/) om te selecteren en te communiceren met het cluster.

Dit probleem oplossen:

* Gebruiken `az aks use-dev-spaces -g <resource group name> -n <cluster name>` om de huidige context bij te werken. Met deze opdracht wordt ook Azure dev-ruimten op uw AKS-cluster ingeschakeld als dat nog niet is gebeurd. U kunt ook gebruiken `kubectl config use-context <cluster name>` om de huidige context bij te werken.
* Gebruik `az account show` om het huidige Azure-abonnement weer te geven dat u wilt richten en controleer of dit juist is. U kunt het abonnement dat u wilt richten wijzigen met `az account set` .

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>Fout bij het gebruik van dev Spaces na het roteren van AKS-certificaten

Na [het draaien van de certificaten in uw AKS-cluster](../aks/certificate-rotation.md), kunnen bepaalde bewerkingen, zoals `azds space list` en, `azds up` mislukken. U moet ook de certificaten op uw Azure dev Space-controller vernieuwen na het draaien van de certificaten in uw cluster.

U kunt dit probleem oplossen door te controleren of uw *kubeconfig* de bijgewerkte certificaten heeft met behulp van `az aks get-credentials` de `azds controller refresh-credentials` opdracht. Bijvoorbeeld:

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
