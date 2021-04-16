---
title: Een toepassing implementeren in Azure Red Hat OpenShift met OpenShift Serverless
description: Meer informatie over het implementeren van een toepassing Azure Red Hat OpenShift met behulp van OpenShift Serverless
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, serverloos
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532834"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Een toepassing implementeren in Azure Red Hat OpenShift met OpenShift Serverless

In dit artikel implementeert u een toepassing in een Azure Red Hat OpenShift cluster met [behulp van OpenShift Serverless](https://www.openshift.com/learn/topics/serverless). OpenShift Serverless helpt ontwikkelaars bij het implementeren en uitvoeren van toepassingen die op aanvraag omhoog worden geschaald of geschaald, waardoor resourceverbruik wordt voorkomen wanneer deze niet in gebruik zijn.

Toepassingscode kan samen met de juiste runtimes worden verpakt in een container en de serverloze functionaliteit start de toepassingscontainers wanneer ze worden geactiveerd door een gebeurtenis. Toepassingen kunnen worden geactiveerd door verschillende gebeurtenisbronnen, zoals gebeurtenissen van uw eigen toepassingen, cloudservices van meerdere providers, SaaS-systemen (Software as a Service) en andere services.

Het beheren van alle aspecten van het implementeren van een container op een serverloze manier is rechtstreeks ingebouwd in de OpenShift-interface. Ontwikkelaars kunnen visueel identificeren welke gebeurtenissen de lancering van hun containertoepassingen aandrijden, met meerdere manieren om de gebeurtenisparameters te wijzigen. OpenShift Serverloze toepassingen kunnen worden geïntegreerd met andere OpenShift-services, zoals OpenShift Pipelines, Service Mesh en Monitoring, en bieden een volledige serverloze ervaring voor het ontwikkelen en implementeren van toepassingen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Installeer het Knative-opdrachtregelprogramma (kn)

U kunt de meest recente versie van de CLI downloaden die geschikt is voor uw computer <https://github.com/knative/client/releases/>

Als u de opdrachten op de Azure Cloud Shell, downloadt u de nieuwste Knative CLI voor Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>De webconsole starten

Zoek de URL van de webconsole van het cluster door het volgende uit te uitvoeren:

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

Als het goed is, krijgt u een URL die vergelijkbaar is met deze.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Start de console-URL in een browser en meld u aan met de referenties van `kubeadmin`.

:::image type="content" source="media/login.png" alt-text="Aanmeldingsscherm voor Azure Red Hat OpenShift":::

## <a name="install-the-openshift-serverless-operator"></a>De serverloze OpenShift-operator installeren

Wanneer u bent aangemeld bij de OpenShift-webconsole, moet u ervoor zorgen dat u zich in het perspectief *Beheerder* ziet. Open de *Operator Hub,* zoek de **serverloze OpenShift-operator** en selecteer deze.

![Zoek de serverloze OpenShift-operator](media/serverless/serverless-operatorhub.png)

Open vervolgens de installatiepagina van de operator door op **Installeren te klikken.**

![Klik op Installeren om de operator te installeren](media/serverless/serverless-clickinstall.png)

Kies het juiste *updatekanaal* voor de clusterversie van Azure Red Hat OpenShift en installeer de operator in `openshift-serverless` de naamruimte. Schuif omlaag en klik op **Installeren.**

![Installatiepagina operator](media/serverless/serverless-installpage.png)

Binnen een paar minuten moet op de statuspagina worden weergegeven dat de operator is geïnstalleerd en klaar is voor gebruik. Klik op de **knop Operator weergeven** om door te gaan.

![De operator is geïnstalleerd en is klaar voor gebruik](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Knative Serving installeren

De mogelijkheid om elke container serverloos op OpenShift Serverless uit te voeren, is mogelijk met behulp van upstream Knative. Knative breidt Kubernetes uit om een set onderdelen te bieden voor het implementeren, uitvoeren en beheren van moderne toepassingen met behulp van de serverloze methodologie.

### <a name="create-an-instance-of-the-knative-serving"></a>Een exemplaar van de Knative Serving maken

Schakel over naar de naamruimte door te klikken op de vervolgkeuzelijst project linksboven en klik vervolgens onder Opgegeven API's op Exemplaar maken in de `knative-serving` *kaart Knative Serving.*  

![Klik om een Knative Service-exemplaar te maken](media/serverless/serverless-createknativeserving.png)

Houd de standaardwaarden aan en schuif omlaag op de *pagina Knative serving* maken om op de knop **Maken te** klikken.

![Laat de standaardwaarden staan en klik op Maken](media/serverless/serverless-createknativeserving2.png)

Wacht totdat in *de kolom Status* De status **Gereed** wordt weer geven, waarna OpenShift Serverless is geïnstalleerd en u klaar bent om een Serverloos OpenShift-project te maken.

![Knative Serving Ready](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Een serverloos project maken

Voer de opdracht uit om een nieuw project met `demoserverless` de naam te maken:

```azurecli-interactive
oc new-project demoserverless
```

De uitvoer ziet er ongeveer als het volgende uit:

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Schakel over naar *het perspectief* Ontwikkelaar in plaats van het perspectief *Beheerder* in het menu aan de linkerkant en selecteer in de lijst `demoserverless` met projecten. U moet dan op de *pagina Topologie* voor het project zijn.

![Azure Red Hat OpenShift projecttopologie](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Implementeren met behulp van de webconsole

Selecteer Van Git in de opties die worden weergegeven voor het implementeren *van een toepassing.* Hiermee komt u terecht op de *pagina Importeren vanuit Git.* Gebruik `https://github.com/sclorg/django-ex.git` als de URL van de **Git-repo.** De voorbeeldwebtoepassing wordt geïmplementeerd met behulp van de programmeertaal Python.

![Azure Red Hat OpenShift project van Git](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift detecteert dat dit een Python-project is en selecteert de juiste opbouwafbeelding.

Schuif omlaag naar *Resources* en zorg ervoor dat **Knative Service** is geselecteerd als het resourcetype dat u wilt genereren. Met deze actie maakt u een Knative-service, een type implementatie waarmee OpenShift Serverloze schaalbaarheid naar nul wordt ingesteld wanneer deze niet actief is.

![Azure Red Hat OpenShift project - Knative](media/serverless/serverless-knative.png)


Wanneer u klaar bent, klikt u onder aan de pagina op **Maken.** Hiermee maakt u resources voor het beheren van de build en implementatie van de toepassing. U wordt vervolgens omgeleid naar het overzicht van de topologie voor het project.

Het overzicht van de topologie biedt een visuele weergave van de toepassing die u hebt geïmplementeerd. In deze weergave ziet u de algehele toepassingsstructuur.

Wacht tot de compilatie is voltooid. Dit kan enkele minuten duren. Nadat de build is voltooid, wordt er een groen vinkje weergegeven in de linkerbenedenhoek van de service.

![Azure Red Hat OpenShift projecttopologie - Bouwen geslaagd](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>De schaal van uw toepassing bekijken

Klik in *de lijst Weergaveopties* bovenaan de topologieweergave op *Aantal pods.* Wacht tot het aantal pods omlaag wordt geschaald naar nul Pods. Omlaag schalen kan enkele minuten duren.

![Azure Red Hat OpenShift projecttopologie - geschaald naar nul](media/serverless/serverless-scaledtozero.png)

Klik op *het pictogram URL* openen in de rechterbovenhoek van het deelvenster Knative Service. De toepassing wordt geopend op een nieuw tabblad. Sluit het nieuwe browsertabblad en ga terug naar de topologieweergave. In de topologieweergave ziet u dat uw toepassing is opgeschaald naar één pod om aan uw aanvraag te voldoen. Na een paar minuten schaalt uw toepassing weer omlaag naar nul Pods.

![Azure Red Hat OpenShift projecttopologie : geschaald naar één](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Een nieuwe revisie afdwingen en verkeersdistributie instellen

Bij elke update van de configuratie van een service wordt een nieuwe revisie voor de service gemaakt. De serviceroute wijst al het verkeer standaard naar de meest recente kant-en-klaarrevisie. U kunt dit gedrag wijzigen door te definiëren welke revisie een deel van het verkeer krijgt. Met knative services kunt u verkeer toewijzen, wat betekent dat revisies van een service kunnen worden toegewezen aan een toegewezen deel van het verkeer. Verkeerstoewijzing biedt ook een optie voor het maken van unieke URL's voor bepaalde revisies.

Klik in *de topologie* op de revisie in uw service om de details ervan weer te geven. De badges onder de Pod-ring en boven aan het detailvenster moeten `(REV)` zijn. Schuif in het zijpaneel op *het tabblad Resources* omlaag en klik op de configuratie die aan uw service is gekoppeld.

![Azure Red Hat OpenShift projecttopologie - Revisie](media/serverless/serverless-revdetails.png)

Forceer een configuratie-update door over te schakelen naar het *tabblad YAML* en omlaag te schuiven om de waarde van `timeoutSeconds` te bewerken als `301` . Klik op **Opslaan om** de bijgewerkte configuratie op te slaan. In een echt scenario kan een dergelijke configuratie-update ook worden geactiveerd door het bijwerken van de tag voor containerafbeeldingen.

![Een nieuwe revisie forceren door de configuratie bij te werken](media/serverless/serverless-confupdate.png)

Terug naar *de topologieweergave* gaat, ziet u dat er een nieuwe revisie is geïmplementeerd. Klik op de service die eindigt op de badge en klik op de knop Verkeersdistributie instellen. U zou nu het verkeer moeten kunnen verdelen tussen de verschillende `(KSVC)` revisies van de service. 

![Verkeersdistributie instellen](media/serverless/serverless-trafficdist.png)

In *de topologieweergave* ziet u nu hoe verkeer wordt verdeeld over de twee revisies.

![Verkeersdistributie controleren](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Het Knative-opdrachtregelprogramma (kn) gebruiken

In de vorige stappen hebt u de OpenShift-webconsole gebruikt om een toepassing te maken en te implementeren in OpenShift Serverless. Omdat op OpenShift Serverless Knative wordt uitgevoerd, kunt u ook het Knative-opdrachtregelprogramma (kn) gebruiken om Knative-services te maken.

> [!NOTE]
> Als u de CLI nog niet hebt geïnstalleerd, volgt u de stappen in de sectie `kn` Vereisten van dit artikel. Zorg er ook voor dat u bent aangemeld met het opdrachtregelprogramma `oc` OpenShift.

We gaan een containerafbeelding gebruiken die al is gebouwd op `quay.io/rhdevelopers/knative-tutorial-greeter` .

### <a name="deploy-a-service"></a>Een service implementeren

Voer de volgende opdracht uit om de service te implementeren:

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

U ziet een uitvoer die vergelijkbaar is met de onderstaande.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

U kunt een lijst met routes in het project ophalen door het volgende uit te doen:

```azurecli-interactive
kn route list
```

U krijgt een lijst met routes terug in de naamruimte. Open de URL in een browser om de geïmplementeerde service te zien.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Een nieuwe versie van de service implementeren

Implementeer een nieuwe versie van de toepassing door de onderstaande opdracht uit te voeren en de tag van de afbeelding en een `:latest` omgevingsvariabele door te `MESSAGE_PREFIX` geven:

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

U ontvangt een bevestiging dat er een nieuwe revisie `greeter-v2` is geïmplementeerd.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Als u een lijst met alle revisies en de distributie van het verkeer wilt weergeven, moet u de volgende opdracht uitvoeren:

```azurecli-interactive
kn revision list
```

U krijgt een lijst die vergelijkbaar is met de onderstaande lijst. Houd er rekening mee dat de nieuwe revisie 100% van het verkeer krijgt.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Blauw/groen en canary-implementaties

Wanneer een nieuwe revisie wordt geïmplementeerd, krijgt deze standaard 100% van het verkeer. Stel dat u een blauw/groen-implementatiestrategie wilt implementeren waarbij u snel kunt terugdraaien naar de oudere versie van de toepassing. Knative maakt dit eenvoudig.

Werk de service bij om drie verkeerstags te maken tijdens het verzenden van 100% van het verkeer naar de

- **current:** punten op de huidige geïmplementeerde versie
- **prev:** wijst naar de vorige versie
- **meest** recente : altijd wijst naar de nieuwste versie

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

U krijgt een bevestiging die vergelijkbaar is met de onderstaande.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Vermeld de routes met behulp van de onderstaande opdracht:

```azurecli-interactive
kn route describe greeter
```

U krijgt uitvoer met de URL's voor elk van de tags, samen met de verkeersdistributie.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Stel dat u snel wilt terugdraaien naar de vorige versie. U kunt de verkeersdistributie bijwerken om 100% van het verkeer naar de vorige tag te verzenden:

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Controleer het opnieuw door de routes weer te geven met behulp van de onderstaande opdracht:

```azurecli-interactive
kn route describe greeter
```

U krijgt uitvoer die laat zien dat 100% van de verkeersdistributie naar de vorige versie gaat.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Speel met de verkeersdistributie tijdens het vernieuwen van de hoofdroute `http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` (in dit geval) in uw browser.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de toepassing, kunt u de volgende opdracht uitvoeren om het project te verwijderen:

```azurecli-interactive
oc delete project demoserverless
```

U kunt het cluster ook verwijderen door de instructies te volgen in [Zelfstudie: Een cluster Azure Red Hat OpenShift 4 verwijderen.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u het volgende geleerd:
> [!div class="checklist"]
>
> * De OpenShift Serverless-operator en Knative Serving installeren
> * Een serverloos project implementeren met behulp van de webconsole
> * Een serverloos project implementeren met behulp van de Knative CLI (kn)
> * Blauwe/groene implementaties en Canary-implementaties configureren met behulp van de Knative CLI (kn)

Meer informatie over het bouwen en implementeren van serverloze, gebeurtenisgestuurde toepassingen op Azure Red Hat OpenShift met behulp van [OpenShift Serverless](https://www.openshift.com/learn/topics/serverless)volgt u de documentatie Aan de slag [met OpenShift Serverloze](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) toepassingen en de documentatie Serverloze toepassingen maken en [beheren.](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html)
