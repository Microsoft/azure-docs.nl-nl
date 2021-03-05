---
title: Een Java-toepassing met open vrijheid/WebSphere vrijheid implementeren op een Azure Red Hat openshift 4-cluster
description: Implementeer een Java-toepassing met open vrijheid/WebSphere vrijheid op een Azure Red Hat openshift 4-cluster.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, javaee, microprofile, open-vrijheid, WebSphere-vrijheid, Aro, openshift, Red Hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212991"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Een Java-toepassing met open vrijheid/WebSphere vrijheid implementeren op een Azure Red Hat openshift 4-cluster

In deze hand leiding wordt gedemonstreerd hoe u uw Java-, Java EE-, [Jakarta ee](https://jakarta.ee/)-of [microprofile](https://microprofile.io/) -toepassing uitvoert op de open vrijheids-en WebSphere vrijheid-runtime en vervolgens de container toepassing implementeert in een Azure Red Hat open Shift (ARO) 4-cluster met behulp van de open vrijheids operator. In dit artikel wordt stapsgewijs uitgelegd hoe u een vrijheids toepassing voorbereidt, de Application docker-installatie kopie bouwt en de container toepassing uitvoert op een ARO 4-cluster.  Zie voor meer informatie over open vrijheid [de open vrijheid project-pagina](https://openliberty.io/). Zie [de product pagina van WebSphere vrijheid](https://www.ibm.com/cloud/websphere-liberty)voor meer informatie over IBM WebSphere vrijheid.

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Vereisten

Voer de volgende vereisten uit om deze hand leiding door te lopen.

> [!NOTE]
> Azure Red Hat OpenShift vereist minimaal 40 kernen om een OpenShift-cluster te maken en uit te voeren. Het standaardquotum voor Azure-resources voor een nieuw Azure-abonnement voldoet niet aan deze vereiste. Als u een verhoging van de resourcelimiet wilt aanvragen, raadpleegt u [Standaardquotum: limieten verhogen per VM-reeks](../azure-portal/supportability/per-vm-quota-requests.md). Houd er rekening mee dat het gratis proef abonnement niet in aanmerking komt voor een quota verhoging, [upgrade naar een abonnement voor betalen per gebruik voordat u](../cost-management-billing/manage/upgrade-azure-subscription.md) een quota verhoging aanvraagt.

1. Bereid een lokale computer voor met een UNIX-soortgelijk besturings systeem (bijvoorbeeld Ubuntu, macOS).
1. Installeer een Java-SE-implementatie (bijvoorbeeld [AdoptOpenJDK openjdk 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Installeer [maven](https://maven.apache.org/download.cgi) 3.5.0 of hoger.
1. Installeer [docker](https://docs.docker.com/get-docker/) voor uw besturings systeem.
1. Installeer [Azure cli](/cli/azure/install-azure-cli) 2.0.75 of hoger.
1. Controleren en installeren [`envsubst`](https://command-not-found.com/envsubst) als deze niet vooraf is geïnstalleerd in uw besturings systeem.
1. Kloon de code voor dit voor beeld op het lokale systeem. Het voor beeld bevindt zich op [github](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Volg de instructies in [een Azure Red Hat open Shift 4-cluster maken](./tutorial-create-cluster.md).

   Hoewel de stap ' een Red Hat pull-geheim ophalen ' is voorzien van het label optioneel, **is het vereist voor dit artikel**.  Met het pull-geheim kan uw Azure Red Hat open Shift-cluster de openstaande vrijheids operator vinden.

   Als u van plan bent geheugenintensieve toepassingen op het cluster uit te voeren, geeft u de juiste grootte van de virtuele machine voor de worker-knoop punten op met behulp van de `--worker-vm-size` para meter. `Standard_E4s_v3`Is bijvoorbeeld de minimale grootte van de virtuele machine om de Elasticsearch-operator in een cluster te installeren. Zie voor meer informatie:

   * [Azure CLI om een cluster te maken](/cli/azure/aro#az-aro-create)
   * [Ondersteunde grootten voor virtuele machines voor geoptimaliseerd geheugen](./support-policies-v4.md#memory-optimized)
   * [Vereisten voor het installeren van de Elasticsearch-operator](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Maak verbinding met het cluster door de stappen te volgen in [verbinding maken met een Azure Red Hat open Shift 4-cluster](./tutorial-connect-cluster.md).
   * Volg de stappen in ' de openhouds-CLI installeren ', omdat de `oc` opdracht later in dit artikel wordt gebruikt.
   * Noteer de URL van de cluster console, die er als volgt uitziet `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Noteer de `kubeadmin` referenties.

1. Controleer of u zich kunt aanmelden bij open Shift CLI met het token voor gebruiker `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Het ingebouwde container register voor open Shift inschakelen

Met de stappen in deze zelf studie maakt u een docker-installatie kopie die moet worden gepusht naar een container register dat toegankelijk is voor open SHIFT. De eenvoudigste optie is het gebruik van het ingebouwde REGI ster van open SHIFT. Als u het ingebouwde container register wilt inschakelen, volgt u de stappen in een [ingebouwd container register configureren voor Azure Red Hat open Shift 4](built-in-container-registry.md). In dit artikel worden drie items van deze stappen gebruikt.

* De gebruikers naam en het wacht woord van de Azure AD-gebruiker voor het aanmelden bij de open Shift-webconsole.
* De uitvoer van `oc whoami` na de volgende stappen voor het aanmelden bij de open Shift-cli.  Deze waarde wordt **Aad-gebruiker** voor de discussie genoemd.
* De container register-URL.

Houd rekening met deze items wanneer u de stappen hebt voltooid om het ingebouwde container register in te scha kelen.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Een open Shift-naam ruimte maken voor de Java-app

1. Meld u met de referenties aan bij de open Shift-webconsole vanuit uw browser `kubeadmin` .
2. Navigeer naar **beheer**  >  **naam** ruimten  >  **maken naam ruimte**.
3. Vul bij `open-liberty-demo` **naam** in en selecteer **maken**, zoals volgende wordt weer gegeven.

   ![naam ruimte maken](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Een beheerder maken voor het demo project

Naast Image Management worden aan de **Aad-gebruiker** ook beheerders machtigingen verleend voor het beheren van resources in het demo project van het Aro 4-cluster.  Meld u aan bij de open Shift CLI en wijs de **Aad-gebruiker** de benodigde bevoegdheden toe door de volgende stappen uit te voeren.

1. Meld u met de referenties aan bij de open Shift-webconsole vanuit uw browser `kubeadmin` .
1. Vouw in de rechter bovenhoek van de webconsole het context menu van de aangemelde gebruiker uit en selecteer vervolgens **aanmeldings opdracht kopiëren**.
1. Meld u indien nodig aan bij een nieuw tabblad venster met dezelfde gebruiker.
1. Selecteer **weergave token**.
1. Kopieer de onderstaande waarde voor **aanmelding met dit token** naar het klem bord en voer deze uit in een shell, zoals hier wordt weer gegeven.
1. Voer de volgende opdrachten uit om `admin` een rol toe te kennen aan de **Aad-gebruiker** in de naam ruimte `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>De openstaande vrijheids operator open Shift installeren

Nadat u een verbinding hebt gemaakt met het cluster, installeert u de open bare vrijheids operator.  De belangrijkste start pagina voor de open vrijheids operator bevindt zich op [github](https://github.com/OpenLiberty/open-liberty-operator).

1. Meld u met de referenties aan bij de open Shift-webconsole vanuit uw browser `kubeadmin` .
2. Navigeer naar **Opera tors**  >  **OperatorHub** en zoek naar een **open bare vrijheids operator**.
3. Selecteer **Open-vrijheids operator** in de zoek resultaten.
4. Selecteer **Installeren**.
5. Controleer in het pop- **Upabonnement operator maken** **alle naam ruimten op het cluster (standaard) voor de** **installatie modus**, het **bèta versie** - **Update kanaal** en de **automatische** **goedkeurings strategie**:

   ![operator abonnement maken voor open vrijheids operator](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Selecteer **Abonneren** en wacht enkele minuten of twee totdat de open vrijheids operator wordt weer gegeven.
7. Bekijk de open vrijheids operator met de status geslaagd.  Als u dit niet doet, moet u het probleem vaststellen en oplossen voordat u doorgaat.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Geïnstalleerde Opera tors met open vrijheid is geïnstalleerd.":::

## <a name="prepare-the-liberty-application"></a>De vrijheids toepassing voorbereiden

We gebruiken een Java EE 8-toepassing als het voor beeld in deze hand leiding. Open vrijheid is een [Java EE 8 Full profile](https://javaee.github.io/javaee-spec/javadocs/) compatibele server, zodat de toepassing eenvoudig kan worden uitgevoerd.  Open vrijheid is ook [Jakarta ee Full profile compatibel](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>De toepassing op open vrijheid uitvoeren

Als u de toepassing op open vrijheid wilt uitvoeren, moet u een open-vrijheids server configuratie bestand maken, zodat de [maven-invoeg](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) toepassing de app voor implementatie kan inpakken. De vrijheids maven-invoeg toepassing is niet vereist voor het implementeren van de toepassing naar open SHIFT.  We gebruiken dit echter in dit voor beeld met de ontwikkelaars modus (open vrijheid).  Met de ontwikkelaars modus kunt u de toepassing eenvoudig lokaal uitvoeren. Voer de volgende stappen uit op de lokale computer.

1. Kopiëren `2-simple/src/main/liberty/config/server.xml` naar `1-start/src/main/liberty/config` , waarbij het bestaande bestand met een lengte van nul wordt overschreven. Hiermee `server.xml` configureert u de open vrijheids server met Java EE-functies.
1. Kopiëren `2-simple/pom.xml` naar `1-start/pom.xml` .  Met deze stap voegt `liberty-maven-plugin` u de aan de pom toe.
1. Wijzig de map in `1-start` uw lokale kloon.
1. Voer `mvn clean package` uit in een-console om een War-pakket te genereren `javaee-cafe.war` in de Directory `./target` .
1. Voer uit `mvn liberty:dev` om vrijheid open te gaan in de ontwikkelings modus.
1. Wacht tot de server is gestart. De uitvoer van de console moet eindigen met het volgende bericht:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Open `http://localhost:9080/` in uw browser om de start pagina van de toepassing te bezoeken. De toepassing ziet er ongeveer als volgt uit:

   ![Webgebruikersinterface voor de Java-Cafe](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Druk op **Control-C** om de toepassing te stoppen en de vrijheids server te openen.

In de map `2-simple` van uw lokale kloon wordt het Maven-project weer gegeven met de bovenstaande wijzigingen die al zijn toegepast.

## <a name="prepare-the-application-image"></a>De installatie kopie van de toepassing voorbereiden

Als u uw vrijheids toepassing wilt implementeren en uitvoeren op een ARO 4-cluster, kunt u uw toepassing container plaatsen als docker-installatie kopie met behulp van [Open-vrijheids container installatie kopieën](https://github.com/OpenLiberty/ci.docker) of [WebSphere vrijheid container installatie kopieën](https://github.com/WASdev/ci.docker).

### <a name="build-application-image"></a>Toepassings installatie kopie maken

Voer de volgende stappen uit om de installatie kopie van de toepassing samen te stellen:

1. Wijzig de map in `2-simple` uw lokale kloon.
2. Voer uit `mvn clean package` om de toepassing te verpakken.
3. Voer een van de volgende opdrachten uit om de installatie kopie van de toepassing samen te stellen.
   * Bouwen met een open vrijheids basis installatie kopie:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Bouwen met WebSphere vrijheid-basis installatie kopie:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>De toepassing lokaal uitvoeren met docker

Voordat u de container toepassing implementeert op een extern cluster, voert u uit met uw lokale docker om te controleren of het werkt:

1. Voer `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` uit in uw-console.
2. Wacht tot de vrijheids server is gestart en de toepassing is geïmplementeerd.
3. Open `http://localhost:9080/` in uw browser om de start pagina van de toepassing te bezoeken.
4. Druk op **Control-C** om de toepassing en vrijheids server te stoppen.

### <a name="push-the-image-to-the-container-image-registry"></a>Push de installatie kopie naar het container installatie kopie register

Wanneer u tevreden bent met de status van de toepassing, pusht u deze naar het ingebouwde REGI ster van de container installatie kopie door de onderstaande instructies te volgen.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Meld u aan bij de open Shift-CLI als de Azure AD-gebruiker

1. Meld u met de referenties van een Azure AD-gebruiker aan bij de open Shift-webconsole vanuit uw browser.

   1. Gebruik een InPrivate-, Incognito-of andere vergelijk bare browser venster functie om u aan te melden bij de-console.
   1. **OpenID Connect** selecteren

   > [!NOTE]
   > Noteer de gebruikers naam en het wacht woord waarmee u zich aanmeldt. Deze gebruikers naam en dit wacht woord functioneren als beheerder voor andere acties in deze en andere artikelen.
1. Meld u aan met de open Shift CLI met behulp van de volgende stappen.  Voor discussies wordt dit proces aangeduid als `oc login` .
   1. Vouw in de rechter bovenhoek van de webconsole het context menu van de aangemelde gebruiker uit en selecteer vervolgens **aanmeldings opdracht kopiëren**.
   1. Meld u indien nodig aan bij een nieuw tabblad venster met dezelfde gebruiker.
   1. Selecteer **weergave token**.
   1. Kopieer de onderstaande waarde voor **aanmelding met dit token** naar het klem bord en voer deze uit in een shell, zoals hier wordt weer gegeven.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>De container installatie kopie naar het container register voor open Shift pushen

Voer deze opdrachten uit om de installatie kopie naar het container register voor open SHIFT te pushen.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Geslaagde uitvoer ziet er ongeveer als volgt uit.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Push de installatie kopie naar het ingebouwde container installatie kopie register met de volgende opdracht.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Toepassing implementeren in het ARO 4-cluster

U kunt nu de voor beeld-vrijheids toepassing implementeren naar het Azure Red Hat open Shift 4-cluster dat u eerder hebt gemaakt tijdens het door lopen van de vereisten.

### <a name="deploy-the-application-from-the-web-console"></a>De toepassing implementeren vanuit de webconsole

Omdat we de open bare vrijheids operator voor het beheren van vrijheids toepassingen gebruiken, moeten we een exemplaar van de *aangepaste resource definitie* maken van het type OpenLibertyApplication. De operator zorgt vervolgens voor alle aspecten van het beheren van de open Shift-resources die vereist zijn voor de implementatie.

1. Meld u met de referenties van de Azure AD-gebruiker aan bij de open Shift-webconsole vanuit uw browser.
1. Vouw **Start pagina** uit. Selecteer **projecten**  >  **Open-vrijheid-demo**.
1. Navigeer naar **Opera tors**  >  **geïnstalleerde Opera tors**.
1. Selecteer in het midden van de pagina **Open-vrijheids operator**.
1. Selecteer in het midden van de pagina **Open-vrijheids toepassing**.  De navigatie van items in de gebruikers interface komt overeen met de werkelijke containment-hiërarchie van technologieën die in gebruik zijn.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java containment](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Selecteer **OpenLibertyApplication maken**
1. Vervang de gegenereerde yaml met de uwe, die zich bevindt in `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Selecteer **Maken**. U keert terug naar de lijst met OpenLibertyApplications.
1. Selecteer **Java-café-simpel**.
1. Selecteer **resources** in het midden van de pagina.
1. Selecteer in de tabel de koppeling voor **Java-café-eenvoudig** met het **soort** **route**.
1. Op de pagina die wordt geopend, selecteert u de koppeling onder **locatie**.

U ziet de start pagina van de toepassing die in de browser wordt geopend.

### <a name="delete-the-application-from-the-web-console"></a>De toepassing verwijderen uit de webconsole

Wanneer u klaar bent met de toepassing, voert u de volgende stappen uit om de toepassing te verwijderen uit open SHIFT.

1. Vouw in het navigatie deel venster links de vermelding voor **Opera tors** uit.
1. Selecteer **geïnstalleerde Opera tors**.
1. Selecteer **Open-vrijheids operator**.
1. Selecteer in het midden van de pagina **open bare vrijheids toepassing**.
1. Selecteer het verticale weglatings teken (drie verticale puntjes) en selecteer vervolgens open- **vrijheid-toepassing verwijderen**.

### <a name="deploy-the-application-from-cli"></a>De toepassing implementeren vanuit CLI

In plaats van de gebruikers interface van de webconsole te gebruiken, kunt u de toepassing implementeren vanuit de CLI. Als u dit nog niet hebt gedaan, downloadt en installeert u het `oc` opdracht regel programma aan de hand van de Red Hat-documentatie [aan de slag met de CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Meld u met de referenties van de Azure AD-gebruiker aan bij de open Shift-webconsole vanuit uw browser.
2. Meld u aan bij de open Shift CLI met het token voor de Azure AD-gebruiker.
3. Wijzig de map in `2-simple` uw lokale kloon en voer de volgende opdrachten uit om uw vrijheids toepassing te implementeren in het Aro 4-cluster.  De uitvoer van de opdracht wordt ook inline weer gegeven.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. Controleer of er `1/1` onder de kolom wordt weer geven `READY` voordat u doorgaat.  Als dat niet het geval is, kunt u het probleem onderzoeken en oplossen voordat u doorgaat.
5. De host van de route naar de toepassing detecteren met de `oc get route` opdracht, zoals hier wordt weer gegeven.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Zodra de vrijheids toepassing actief is, opent u de uitvoer van de **route host** in uw browser om de start pagina van de toepassing te bezoeken.

### <a name="delete-the-application-from-cli"></a>De toepassing verwijderen uit CLI

Verwijder de toepassing uit de CLI door deze opdracht uit te voeren.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het ARO-cluster door de stappen in de [zelf studie te volgen: een Azure Red Hat open Shift 4-cluster verwijderen](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u het volgende geleerd:
> [!div class="checklist"]
>
> * De vrijheids toepassing voorbereiden
> * De installatie kopie van de toepassing bouwen
> * De container toepassing uitvoeren op een ARO 4-cluster met behulp van de GUI en de CLI

In deze hand leiding vindt u meer informatie over de referenties die worden gebruikt:

* [Open vrijheid](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open vrijheids operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Configuratie van vrijheids server openen](https://openliberty.io/docs/ref/config/)
* [Vrijheid maven-invoeg toepassing](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Installatie kopieën van vrijheids containers openen](https://github.com/OpenLiberty/ci.docker)
* [WebSphere vrijheid container-installatie kopieën](https://github.com/WASdev/ci.docker)