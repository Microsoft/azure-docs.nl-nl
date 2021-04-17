---
title: Een toepassing implementeren van bron naar Azure Red Hat OpenShift
description: Meer informatie over het implementeren van een Azure Red Hat OpenShift van broncode met behulp van de buildstrategie Source-to-Image (S2I)
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, bron naar afbeelding
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559025"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Een toepassing implementeren van bron naar Azure Red Hat OpenShift

In dit artikel implementeert u een toepassing in een Azure Red Hat OpenShift cluster vanuit broncode met behulp van een bron-naar-afbeelding-build (S2I). [Source-to-Image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) is een bouwproces voor het bouwen van reproduceerbare containerafbeeldingen op basis van broncode. S2I produceert kant-en-klaar-afbeeldingen door broncode in een containerafbeelding te injecteren en de container die broncode voor te bereiden voor uitvoering. U kunt OpenShift een toepassing laten bouwen vanaf de bron om deze te implementeren, zodat u niet bij elke wijziging een container hoeft te maken. OpenShift kan vervolgens automatisch nieuwe versies bouwen en implementeren wanneer u op de hoogte wordt gesteld van wijzigingen in de broncode.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Een project maken

Voer de opdracht uit om een nieuw project met `demoproject` de naam te maken:

```azurecli-interactive
oc new-project demoproject
```

De uitvoer ziet er ongeveer als het volgende uit:

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>De webconsole starten

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

Schakel over naar *het perspectief* Ontwikkelaar in plaats van het perspectief *Beheerder* in het menu aan de linkerkant en selecteer in de lijst `demoproject` met projecten. U moet dan op de *pagina Topologie* voor het project zijn.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Azure Red Hat OpenShift projecttopologie":::

Omdat het project leeg is, hoeft u geen workloads te vinden en krijgt u verschillende opties te zien voor het implementeren van een toepassing.

## <a name="deploying-using-the-web-console"></a>Implementeren met behulp van de webconsole

Selecteer Van Git in de opties die worden weergegeven voor het implementeren *van een toepassing.* Hiermee komt u terecht op de *pagina Importeren vanuit Git.* Gebruik `https://github.com/sclorg/django-ex.git` als de URL van de **Git-repo.** De voorbeeldwebtoepassing wordt geïmplementeerd met behulp van de programmeertaal Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Azure Red Hat OpenShift project van Git":::

> [!NOTE]
> OpenShift detecteert dat dit een Python-project is en selecteert de juiste builder-afbeelding.

Schuif omlaag naar *Geavanceerde opties* en zorg ervoor dat Het maken van een route naar **de toepassing** is ingeschakeld. Met deze actie maakt u een OpenShift-route, een manier om een service weer te geven door deze een extern bereikbare hostnaam te geven.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Azure Red Hat OpenShift project van Git - Route-installatie":::

Wanneer u klaar bent, klikt u onder aan de pagina op **Maken.** Hiermee maakt u resources voor het beheren van de build en implementatie van de toepassing. U wordt vervolgens omgeleid naar het overzicht van de topologie voor het project.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Azure Red Hat OpenShift project van Git - Topologie":::

Het overzicht van de topologie biedt een visuele weergave van de toepassing die u hebt geïmplementeerd. In deze weergave ziet u de algehele toepassingsstructuur.

U kunt op het Git-pictogram klikken om naar de Git-opslagplaats te gaan van waaruit de broncode voor de toepassing is gemaakt. Het pictogram linksonder toont de buildstatus van de toepassing. Als u op dit pictogram klikt, gaat u naar de sectie met details van de build. Als de toepassing routes heeft weergegeven, kan op het pictogram rechtsboven worden geklikt om de URL te openen voor de toepassingsroute die is gemaakt.

Terwijl de toepassing omhoog of omlaag schaalt, implementaties start en pods opnieuw maakt, wordt de weergave van de toepassing in de topologieweergave geanimeerd om u een realtime beeld te geven van wat er gebeurt.

Als u op het toepassingspictogram klikt, worden er meer details weergegeven, zoals hieronder wordt weergegeven.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Azure Red Hat OpenShift project van Git - Details":::

## <a name="viewing-the-builder-logs"></a>De opbouwlogboeken weergeven

Zodra de build is gestart, klikt u op de *koppeling Logboeken* weergeven die wordt weergegeven in het *deelvenster* Resources.

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Azure Red Hat OpenShift project van Git - Build-logboeken":::

Hiermee kunt u de voortgang van de build controleren terwijl deze wordt uitgevoerd. De builder-afbeelding, in dit geval Python, injecteert de broncode van de toepassing in de uiteindelijke afbeelding voordat deze naar het interne OpenShift-register met afbeeldingen wordt pusht. De build is voltooid wanneer u het laatste bericht 'Push successful' ziet.

## <a name="accessing-the-application"></a>Toegang tot de toepassing

Zodra de build van de toepassingsafbeelding is voltooid, wordt deze geïmplementeerd.

Klik op *Topologie* in de menubalk aan de linkerkant om terug te keren naar de topologieweergave voor het project. Wanneer u de toepassing hebt gemaakt met behulp van de webconsole, is er automatisch een *route* gemaakt voor de toepassing en wordt deze buiten het cluster beschikbaar gemaakt. De URL die kan worden gebruikt voor toegang tot de toepassing vanuit een webbrowser was zichtbaar op het *tabblad Resources* voor de toepassing die u eerder hebt bekeken.

In de topologieweergave kunt u de URL voor de geïmplementeerde toepassing openen door op het pictogram rechtsboven in de ring te klikken. Wanneer de implementatie is voltooid, klikt u op het pictogram en ziet u de toepassing die u hebt geïmplementeerd.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Azure Red Hat OpenShift project van Git - Bladeren in app":::

## <a name="deploying-using-the-command-line"></a>Implementeren met behulp van de opdrachtregel

U hebt geleerd hoe u een toepassing implementeert met behulp van de webconsole. U kunt nu dezelfde webtoepassing implementeren, maar deze keer met behulp van het `oc` opdrachtregelprogramma.

Voer de volgende opdracht uit om het project te verwijderen en opnieuw te beginnen:

```azurecli-interactive
oc delete project demoproject
```

U ontvangt een bevestigingsbericht dat `demoproject` de is verwijderd.

```output
project.project.openshift.io "demoproject" deleted
```

Maak de `demoproject` opnieuw door het volgende uit te uitvoeren:

```azurecli-interactive
oc new-project demoproject
```

Maak in het project een nieuwe toepassing vanuit de bron op GitHub en geef de S2I Builder op voor de nieuwste versie van Python.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

De uitvoer moet er ongeveer als de volgende uit zien:

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift gebruikt de naam van de Git-opslagplaats als de naam voor de toepassing. Controleer de status van de build en implementatie door het volgende uit te doen:

```azurecli-interactive
oc status
```

Wanneer de build en implementatie zijn voltooid, ziet u een vergelijkbare uitvoer als .

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Als u de toepassing buiten het OpenShift-cluster wilt blootstellen, moet u een route maken door het volgende uit te stellen:

```azurecli-interactive
oc expose service/django-ex
```

U zou een bevestiging moeten krijgen.

```output
route.route.openshift.io/django-ex exposed
```

Haal de URL op door het volgende uit te stellen:

```azurecli-interactive
oc get route django-ex
```

U moet de hostnaam terug krijgen die is toegewezen aan de route die u kunt gebruiken om naar de geïmplementeerde toepassing te bladeren.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Een nieuwe binaire build activeren

Wanneer u aan de toepassing werkt, wilt u waarschijnlijk wijzigingen aanbrengen en deze geïmplementeerd zien. U kunt eenvoudig een webhook instellen die een nieuwe build en implementatie activeert bij elke code-commit. Dit is echter mogelijk niet wenselijk, omdat u soms de wijzigingen wilt zien zonder dat u elke codewijziging naar de opslagplaats moet pushen.

In gevallen waarin u wijzigingen snel doorneemt, kunt u een zogenaamde binaire build gebruiken. Ter demonstratie van dit scenario kloont u de Git-opslagplaats voor de toepassing lokaal door het volgende uit te stellen:

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Hiermee maakt u een submap `django-ex` met de broncode voor de toepassing:

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Wijzig in de submap:

```azurecli-interactive
cd django-ex
```

Open de geïntegreerde Azure Cloud Shell-editor:

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Schuif omlaag en wijzig de regel met de `Welcome to your Django application on OpenShift` tekst `Welcome to Azure Red Hat OpenShift` . Sla het bestand op en sluit de editor via `...` het menu rechtsboven.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Azure Red Hat OpenShift project from Git - Edit application in Azure Cloud Shell editor":::

Start een nieuwe build door de opdracht uit te voeren:

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Door de vlag door te geven, uploadt de OpenShift-opdrachtregel de broncode uit de opgegeven map en start vervolgens `--from-dir=.` het build- en implementatieproces. Als het goed is, krijgt u uitvoer die vergelijkbaar is met de onderstaande uitvoer en na een paar minuten moet de build zijn voltooid.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Als u de browser vernieuwt met de toepassing, ziet u de bijgewerkte titel.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Azure Red Hat OpenShift project van Git - Door bijgewerkte app bladeren":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de toepassing, kunt u de volgende opdracht uitvoeren om het project te verwijderen:

```azurecli-interactive
oc delete project demoproject
```

U kunt het cluster ook verwijderen door de instructies te volgen in [Zelfstudie: Een cluster Azure Red Hat OpenShift 4 verwijderen.](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u het volgende geleerd:
> [!div class="checklist"]
>
> * Een project maken
> * Een toepassing implementeren vanuit broncode met behulp van de webconsole
> * Een toepassing implementeren vanuit broncode met behulp van de OpenShift-opdrachtregel
> * Een binaire build activeren met behulp van de OpenShift-opdrachtregel

Meer informatie over het bouwen en implementeren van toepassingen met behulp van bron-naar-afbeelding en [andere buildstrategieën.](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html)
