---
title: Een Java-toepassing implementeren met Open Dient/WebSphereSphere op een Azure Red Hat OpenShift 4-cluster
description: Implementeer een Java-toepassing met Open Dient/WebSphereSphere op een Azure Red Hat OpenShift 4-cluster.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, hadee, javaee, microprofile, open-wilt, websphere-usb, aro, openshift, red hat
ms.openlocfilehash: 2a308c7de754f395a3ef8a1bd97ed2441d27d21d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783571"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Een Java-toepassing implementeren met Open Dient/WebSphereSphere op een Azure Red Hat OpenShift 4-cluster

In deze handleiding wordt gedemonstreerd hoe u uw Java-, Java [EE-, Pagina EE-](https://jakarta.ee/)of [MicroProfile-toepassing](https://microprofile.io/) kunt uitvoeren in de runtime Open Wilt/WebSphereSphere En vervolgens de containertoepassing implementeert in een Azure Red Hat OpenShift (ARO) 4-cluster met behulp van de Open Hebto Operator. In dit artikel wordt beschreven hoe u een Toepassingstoepassing voorbereidt, de Docker-toepassingsafbeelding bouwt en de in een container geplaatste toepassing op een ARO 4-cluster wordt uitgevoerd.  Zie de projectpagina Openen voor meer informatie [over Open Had.](https://openliberty.io/) Zie de productpagina van [WebSphereSphere](https://www.ibm.com/cloud/websphere-liberty)voor meer informatie over IBM WebSphere Page.

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende vereisten om deze handleiding te kunnen doorlopen.

> [!NOTE]
> Azure Red Hat OpenShift vereist minimaal 40 kernen om een OpenShift-cluster te maken en uit te voeren. Het standaardquotum voor Azure-resources voor een nieuw Azure-abonnement voldoet niet aan deze vereiste. Als u een verhoging van de resourcelimiet wilt aanvragen, raadpleegt u [Standaardquotum: limieten verhogen per VM-reeks](../azure-portal/supportability/per-vm-quota-requests.md). Houd er rekening mee dat het gratis proefabonnement niet in aanmerking komt voor een quotumverhoging. [](../cost-management-billing/manage/upgrade-azure-subscription.md) Upgrade naar een abonnement met betalen per uur voordat u een quotumverhoging aanvraagt.

1. Bereid een lokale computer voor met een Unix-achtige besturingssysteem geïnstalleerd (bijvoorbeeld Ubuntu, macOS).
1. Installeer een Java SE-implementatie (bijvoorbeeld [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9).](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)
1. Installeer [Maven](https://maven.apache.org/download.cgi) 3.5.0 of hoger.
1. Installeer [Docker voor](https://docs.docker.com/get-docker/) uw besturingssysteem.
1. Installeer [Azure CLI](/cli/azure/install-azure-cli) 2.0.75 of hoger.
1. Controleer en installeer [`envsubst`](https://command-not-found.com/envsubst) deze als deze niet vooraf is geïnstalleerd in uw besturingssysteem.
1. Kloon de code voor dit voorbeeld op uw lokale systeem. Het voorbeeld staat op [GitHub.](https://github.com/Azure-Samples/open-liberty-on-aro)
1. Volg de instructies in [Een Azure Red Hat OpenShift 4-cluster maken.](./tutorial-create-cluster.md)

   Hoewel de stap 'Een Pull-geheim van Red Hat krijgen' is gelabeld als optioneel, **is dit vereist voor dit artikel.**  Met het pull-geheim kan Azure Red Hat OpenShift cluster de Open Operator vinden.

   Als u van plan bent om geheugenintensieve toepassingen op het cluster uit te voeren, geeft u de juiste grootte van de virtuele machine voor de werkknooppunten op met behulp van de `--worker-vm-size` parameter . Is bijvoorbeeld `Standard_E4s_v3` de minimale grootte van de virtuele machine om de Elasticsearch Operator op een cluster te installeren. Zie voor meer informatie:

   * [Azure CLI voor het maken van een cluster](/cli/azure/aro#az_aro_create)
   * [Ondersteunde grootten van virtuele machines voor geoptimaliseerd geheugen](./support-policies-v4.md#memory-optimized)
   * [Vereisten voor het installeren van de Elasticsearch Operator](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Maak verbinding met het cluster door de stappen in [Verbinding maken met een Azure Red Hat OpenShift 4-cluster te volgen.](./tutorial-connect-cluster.md)
   * Volg de stappen in De OpenShift CLI installeren, omdat we de opdracht `oc` later in dit artikel gebruiken.
   * Noteren van de url van de clusterconsole die eruitziet als `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Noteer de `kubeadmin` referenties.

1. Controleer of u zich kunt aanmelden bij de OpenShift CLI met het token voor gebruiker `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Het ingebouwde containerregister inschakelen voor OpenShift

Met de stappen in deze zelfstudie maakt u een Docker-afbeelding die naar een containerregister moet worden pushen dat toegankelijk is voor OpenShift. De eenvoudigste optie is om het ingebouwde register van OpenShift te gebruiken. Als u het ingebouwde containerregister wilt inschakelen, volgt u de stappen [in Ingebouwde containerregister configureren voor Azure Red Hat OpenShift 4.](built-in-container-registry.md) In dit artikel worden drie items uit deze stappen gebruikt.

* De gebruikersnaam en het wachtwoord van de Azure AD-gebruiker voor aanmelding bij de OpenShift-webconsole.
* De uitvoer van `oc whoami` na het volgen van de stappen voor het aanmelden bij de OpenShift CLI.  Deze waarde wordt **aad-user genoemd voor** discussie.
* De URL van het containerregister.

Noteer deze items wanneer u de stappen voor het inschakelen van het ingebouwde containerregister voltooit.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Een OpenShift-naamruimte maken voor de Java-app

1. Meld u in uw browser aan bij de OpenShift-webconsole met behulp van `kubeadmin` de referenties.
2. **Navigeer naar**  >  **Beheernaamruimten**  >  **Naamruimte maken.**
3. Vul in `open-liberty-demo` bij **Naam** en selecteer **Maken,** zoals hierna wordt weergegeven.

   ![naamruimte maken](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Een beheerder maken voor het demoproject

Naast het beheer van afbeeldingen krijgt **de aad-gebruiker** ook beheerdersmachtigingen voor het beheren van resources in het demoproject van het ARO 4-cluster.  Meld u aan bij de OpenShift CLI en verleen **de aad-gebruiker** de benodigde bevoegdheden door deze stappen te volgen.

1. Meld u in uw browser aan bij de OpenShift-webconsole met behulp van `kubeadmin` de referenties.
1. Vouw rechtsboven in de webconsole het contextmenu van de aangemelde gebruiker uit en selecteer **Vervolgens Aanmeldingsopdracht kopiëren.**
1. Meld u indien nodig aan bij een nieuw tabbladvenster met dezelfde gebruiker.
1. Selecteer **Token weergeven.**
1. Kopieer de waarde die hieronder wordt weergegeven **Aanmelden met dit token** naar het klembord en voer deze uit in een shell, zoals hier wordt weergegeven.
1. Voer de volgende opdrachten uit om de `admin` rol toe te staan aan de **aad-gebruiker** in de naamruimte `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>De OpenShift OpenShift-operator installeren

Nadat u het cluster heeft aanmaken en er verbinding mee maakt, installeert u de Operator Open Operator.  De belangrijkste startpagina voor de Operator Voor openen is op [GitHub.](https://github.com/OpenLiberty/open-liberty-operator)

1. Meld u in uw browser aan bij de OpenShift-webconsole met behulp van `kubeadmin` de referenties.
2. **Navigeer naar Operators**  >  **OperatorHub** en zoek naar Open Operator **.**
3. Selecteer **Operator openen in** de zoekresultaten.
4. Selecteer **Installeren**.
5. Controleer in het pop-upabonnement **Operator** maken alle naamruimten in het cluster (standaard) voor Installatiemodus,  **bèta** voor **Updatekanaal** en Automatisch **voor** **goedkeuringsstrategie:** 

   ![operatorabonnement maken voor Open Operator](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Selecteer **Abonneren** en wacht een paar minuten totdat de Operator openen wordt weergegeven.
7. Bekijk de Operator Openen met de status Geslaagd.  Als u dit niet hebt gedaan, kunt u het probleem vaststellen en oplossen voordat u doorgaat.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Geïnstalleerde operators met Open Operators is geïnstalleerd.":::

## <a name="prepare-the-liberty-application"></a>De Toepassing Voormaken

In deze handleiding gebruiken we een Java EE 8-toepassing als voorbeeld. Open Hebt is een server met volledig profiel voor [Java EE 8,](https://javaee.github.io/javaee-spec/javadocs/) zodat de toepassing eenvoudig kan worden uitgevoerd.  Open Zou is ook [geschikt voor het volledige profiel van De eE 8.](https://jakarta.ee/specifications/platform/8/apidocs/)

### <a name="run-the-application-on-open-liberty"></a>De toepassing uitvoeren op Open Keer

Als u de toepassing wilt uitvoeren op OpenLiggende, moet u een OpenHost-serverconfiguratiebestand maken, zodat de Invoegtoepassing Voor implementatie van [De Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) de toepassing kan verpakken. De Invoegtoepassing Voor Maven is niet vereist om de toepassing te implementeren in OpenShift.  In dit voorbeeld gebruiken we deze echter met de ontwikkelaarsmodus van Open Developer (dev).  Met de ontwikkelaarsmodus kunt u de toepassing eenvoudig lokaal uitvoeren. Voltooi de volgende stappen op uw lokale computer.

1. Kopieer `2-simple/src/main/liberty/config/server.xml` naar `1-start/src/main/liberty/config` en overschrijf het bestaande bestand met de lengte van nul. Hiermee `server.xml` configureert u de Open Server met Java EE-functies.
1. Kopieer `2-simple/pom.xml` naar `1-start/pom.xml` .  Met deze stap wordt de `liberty-maven-plugin` toegevoegd aan de POM.
1. Wijzig de map in `1-start` van uw lokale kloon.
1. Voer `mvn clean package` uit in een console om een war-pakket te genereren in de map `javaee-cafe.war` `./target` .
1. Voer `mvn liberty:dev` uit om Open Gaan in de dev-modus te starten.
1. Wacht totdat de server wordt gestart. De console-uitvoer moet eindigen met het volgende bericht:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Open `http://localhost:9080/` in uw browser om naar de startpagina van de toepassing te gaan. De toepassing ziet er ongeveer uit als in de volgende afbeelding:

   ![Webinterface van JavaEE](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Druk **op Control-C** om de toepassing en De Server openen te stoppen.

In de `2-simple` map van uw lokale kloon wordt het Maven-project met de bovenstaande wijzigingen al toegepast.

## <a name="prepare-the-application-image"></a>De toepassingsafbeelding voorbereiden

Als u uw App App Wilt implementeren en uitvoeren op een ARO 4-cluster, containeriseert u uw toepassing als een Docker-afbeelding met behulp van [Open Hebto-containerafbeeldingen](https://github.com/OpenLiberty/ci.docker) of [WebSphere Image-containerafbeeldingen.](https://github.com/WASdev/ci.docker)

### <a name="build-application-image"></a>Toepassingsafbeelding bouwen

Voltooi de volgende stappen om de toepassingsafbeelding te bouwen:

1. Wijzig de map in `2-simple` van uw lokale kloon.
2. Voer uit `mvn clean package` om de toepassing te verpakken.
3. Voer een van de volgende opdrachten uit om de toepassingsafbeelding te bouwen.
   * Bouwen met een Open Kans-basisafbeelding:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Bouw met de Basisafbeelding van WebSphere:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>De toepassing lokaal uitvoeren met Docker

Voordat u de containertoepassing implementeert in een extern cluster, moet u uitvoeren met uw lokale Docker om te controleren of deze werkt:

1. Voer `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` uit in uw console.
2. Wacht tot De Server is begonnen en de toepassing is geïmplementeerd.
3. Open `http://localhost:9080/` in uw browser om de startpagina van de toepassing te bezoeken.
4. Druk **op Control-C** om de toepassing en De Server van Te stoppen.

### <a name="push-the-image-to-the-container-image-registry"></a>De afbeelding naar het register van de containerafbeelding pushen

Wanneer u tevreden bent met de status van de toepassing, pusht u deze naar het ingebouwde register met containerafbeeldingen door de onderstaande instructies te volgen.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Meld u als Azure AD-gebruiker aan bij de OpenShift CLI

1. Meld u in uw browser aan bij de OpenShift-webconsole met de referenties van een Azure AD-gebruiker.

   1. Gebruik een InPrivate-, Incognito- of andere equivalente browservensterfunctie om u aan te melden bij de console.
   1. Openid **selecteren**

   > [!NOTE]
   > Noteer de gebruikersnaam en het wachtwoord die u gebruikt om u hier aan te melden. Deze gebruikersnaam en dit wachtwoord werken als beheerder voor andere acties in deze en andere artikelen.
1. Meld u aan met de OpenShift CLI door de volgende stappen uit te voeren.  Dit proces wordt ook wel `oc login` genoemd.
   1. Vouw rechtsboven in de webconsole het contextmenu van de aangemelde gebruiker uit en selecteer **Vervolgens Aanmeldingsopdracht kopiëren.**
   1. Meld u indien nodig aan bij een nieuw tabbladvenster met dezelfde gebruiker.
   1. Selecteer **Token weergeven.**
   1. Kopieer de waarde die hieronder wordt weergegeven **Aanmelden met dit token** naar het klembord en voer deze uit in een shell, zoals hier wordt weergegeven.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>De container-afbeelding naar het containerregister voor OpenShift pushen

Voer deze opdrachten uit om de afbeelding naar het containerregister voor OpenShift te pushen.

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

Push de afbeelding naar het ingebouwde register met containerafbeeldingen met de volgende opdracht.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Toepassing implementeren in het ARO 4-cluster

U kunt de voorbeeldtoepassing Nu implementeren in het Azure Red Hat OpenShift 4-cluster dat u eerder hebt gemaakt bij het werken aan de vereisten.

### <a name="deploy-the-application-from-the-web-console"></a>De toepassing implementeren vanuit de webconsole

Omdat we de Operator Open Operator gebruiken voor het beheren van Deen toepassingen, moeten we een exemplaar maken van de aangepaste resourcedefinitie , van het type 'OpenLibertyApplication'. De operator zorgt vervolgens voor alle aspecten van het beheren van de OpenShift-resources die vereist zijn voor implementatie.

1. Meld u in uw browser aan bij de OpenShift-webconsole met de referenties van de Azure AD-gebruiker.
1. Vouw **Start** uit, **selecteer Projecten**  >  **openen-nu-demo.**
1. Navigeer naar **Operators**  >  **geïnstalleerde operators**.
1. Selecteer in het midden van de pagina **Operator openen.**
1. Selecteer in het midden van de pagina **De toepassing Openen.**  De navigatie van items in de gebruikersinterface weerspiegelt de daadwerkelijke insluitingshiërarchie van technologieën die worden gebruikt.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![ARO Java Containment](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Selecteer **OpenLibertyApplication maken**
1. Vervang de gegenereerde yaml door de uwe, die zich bevindt in `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Selecteer **Maken**. U keert terug naar de lijst met OpenLibertyApplications.
1. Selecteer **javaee-café-simple.**
1. Selecteer resources in het midden van de **pagina.**
1. Selecteer in de tabel de koppeling **voor javaee-café-simple** met **het Soort** **route**.
1. Selecteer op de pagina die wordt geopend de koppeling onder **Locatie**.

De startpagina van de toepassing wordt geopend in de browser.

### <a name="delete-the-application-from-the-web-console"></a>De toepassing verwijderen uit de webconsole

Wanneer u klaar bent met de toepassing, volgt u deze stappen om de toepassing te verwijderen uit Open Shift.

1. Vouw in het linkernavigatiedeelvenster de vermelding voor **Operators uit.**
1. Selecteer **Geïnstalleerde operators.**
1. Selecteer **Operator Voor openen.**
1. Selecteer in het midden van de pagina **De Toepassing openen.**
1. Selecteer het verticale beletselteken (drie verticale puntjes) en selecteer **vervolgens OpenLiberty-toepassing verwijderen.**

### <a name="deploy-the-application-from-cli"></a>De toepassing implementeren vanuit CLI

In plaats van de gui van de webconsole te gebruiken, kunt u de toepassing implementeren vanuit de CLI. Als u dit nog niet hebt gedaan, downloadt en installeert u het opdrachtregelprogramma door de Red Hat-documentatie te volgen Aan de slag `oc` [met de CLI.](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)

1. Meld u in uw browser aan bij de OpenShift-webconsole met de referenties van de Azure AD-gebruiker.
2. Meld u aan bij de OpenShift CLI met het token voor de Azure AD-gebruiker.
3. Wijzig de map in van uw lokale kloon en voer de volgende opdrachten uit om uw Toepassing Te implementeren `2-simple` in het ARO 4-cluster.  Opdrachtuitvoer wordt ook inline weergegeven.

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

4. Controleer het onder `1/1` de kolom voordat u `READY` doorgaat.  Zo niet, onderzoek het probleem en los het op voordat u doorgaat.
5. Ontdek de host van de route naar de toepassing met de `oc get route` opdracht , zoals hier wordt weergegeven.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Zodra de Toepassing van Wordt uitgevoerd is, opent u de uitvoer van **Route Host** in uw browser om naar de startpagina van de toepassing te gaan.

### <a name="delete-the-application-from-cli"></a>De toepassing verwijderen uit CLI

Verwijder de toepassing uit de CLI door deze opdracht uit te voeren.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het ARO-cluster door de stappen te volgen in [Zelfstudie: Een](./tutorial-delete-cluster.md) cluster Azure Red Hat OpenShift 4 verwijderen

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u het volgende geleerd:
> [!div class="checklist"]
>
> * De Toepassing Voormaken
> * De toepassingsafbeelding bouwen
> * De containertoepassing uitvoeren op een ARO 4-cluster met behulp van de GUI en de CLI

Meer informatie vindt u in de verwijzingen die in deze handleiding worden gebruikt:

* [Open Door](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Operator Voor openen](https://github.com/OpenLiberty/open-liberty-operator)
* [Serverconfiguratie openen](https://openliberty.io/docs/ref/config/)
* [De Maven-invoeg-app Voor het maken van een app](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Containerafbeeldingen openen in Tern](https://github.com/OpenLiberty/ci.docker)
* [Afbeeldingen van WebSphere-container](https://github.com/WASdev/ci.docker)