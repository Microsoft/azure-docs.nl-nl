---
title: Jenkins voor een Java-app voor Service Fabric in Azure configureren
description: Deze zelfstudie geeft u informatie over het instellen van continue integratie met Jenkins voor het implementeren van een Java Service Fabric-toepassing.
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531969"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>Zelfstudie: Een Jenkins-omgeving configureren om CI/CD in te schakelen voor een Java-toepassing voor Service Fabric

Deze zelfstudie is deel vijf van een serie. De zelfstudie laat zien hoe u Jenkins moet gebruiken voor het implementeren van upgrades voor uw toepassing. In deze zelfstudie wordt de Service Fabric Jenkins-invoegtoepassing gebruikt in combinatie met een GitHub-opslagplaats die als host fungeert voor de stemtoepassing, voor het implementeren van de toepassing naar een cluster.

In deel vijf van de serie leert u het volgende:
> [!div class="checklist"]
> * Service Fabric Jenkins-container implementeren op uw machine
> * Jenkins-omgeving voor implementatie naar Service Fabric instellen
> * Uw toepassing upgraden

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Java Service Fabric Reliable Services-toepassing maken](service-fabric-tutorial-create-java-app.md)
> * [De toepassing implementeren en er foutopsporing op toepassen in een lokaal cluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * [De toepassing implementeren in een Azure-cluster](service-fabric-tutorial-java-deploy-azure.md)
> * [Controle en diagnostische gegevens voor de toepassing instellen](service-fabric-tutorial-java-elk.md)
> * CI/CD instellen

## <a name="prerequisites"></a>Vereisten

* Installeer Git op uw lokale computer vanuit [de Git-pagina met downloads](https://git-scm.com/downloads). Lees voor meer informatie over Git de [Git-documentatie](https://git-scm.com/docs).
* Enige basiskennis van [Jenkins](https://jenkins.io/) is nodig.
* Maak een [GitHub](https://github.com/)-account en weet hoe u GitHub moet gebruiken.
* Installeer [Docker](https://www.docker.com/community-edition) op uw computer.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Haal de Service Fabric Jenkins container-installatiekopie op en implementeer deze

U kunt Jenkins instellen binnen of buiten een Service Fabric-cluster. De volgende instructies laten zien hoe dit buiten een cluster in te stellen met een opgegeven Docker-installatiekopie. Een vooraf geconfigureerde Jenkins build-omgeving kan echter ook worden gebruikt. De volgende afbeelding van de container is geïnstalleerd met de Service Fabric-invoegtoepassing en is onmiddellijk gereed voor gebruik met Service Fabric.

1. Haal de Service Fabric Jenkins container-installatiekopie op: ``docker pull rapatchi/jenkins:v10``. Deze installatiekopie wordt geleverd met de Service Fabric Jenkins-invoegtoepassing die vooraf is geïnstalleerd.

1. Voer de container-installatiekopie uit met de locatie waar uw Azure-certificaten zijn opgeslagen op uw gekoppelde lokale machine.

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. Haal de id op van het exemplaar van de installatiekopie van de container. U kunt een lijst van alle Docker-containers bekijken met de opdracht ``docker ps –a``

1. Het wachtwoord van uw instantie van Jenkins ophalen met de volgende opdracht:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Als de container-id 2d24a73b5964 is, gebruikt u 2d24.
    * Dit wachtwoord is vereist om u via de portal (``http://<HOST-IP>:8080``) aan te melden bij het Jenkins-dashboard
    * Nadat u zich voor de eerste keer hebt aangemeld, kunt u uw eigen gebruikersaccount maken of het beheerdersaccount gebruiken.

1. Stel GitHub in voor Jenkins door de stappen uit te voeren die genoemd zijn in [Een nieuwe SSH-sleutel genereren en toevoegen aan de SSH-agent](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/). Omdat de opdrachten worden uitgevoerd vanuit de Docker-container, volgt u de instructies voor de Linux-omgeving.
   * Gebruik de instructies van GitHub om de SSH-sleutel te genereren. Voeg vervolgens de SSH-sleutel aan het GitHub-account toe dat als host voor de opslagplaats fungeert.
   * Voer de opdrachten die in de vorige koppeling zijn genoemd, uit in de Jenkins Docker-shell (en niet op uw host).
   * Gebruik de volgende opdrachten om u vanaf uw host aan te melden bij de Jenkins-shell:

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Zorg ervoor dat het cluster waarin of de computer waarop de installatiekopie van de Jenkins-container wordt gehost, een openbaar IP-adres heeft. Met een openbaar IP-adres kan de instantie van Jenkins meldingen ontvangen uit GitHub.

## <a name="create-and-configure-a-jenkins-job"></a>Een Jenkins-taak maken en configureren

1. Als u geen opslagplaats hebt die u kunt gebruiken voor het hosten van het stemproject op GitHub, maakt u er eerst een. De opslagplaats wordt **dev_test** genoemd voor de rest van deze zelfstudie.

1. Maak een **nieuw item** op uw Jenkins-dashboard op ``http://<HOST-IP>:8080``,.

1. Voer een itemnaam in (bijvoorbeeld **MyJob**). Selecteer **free-style project** en klik op **OK**.

1. Ga naar de pagina van de taak en klik op **Configure**.

   a. Selecteer in het algemene gedeelte het selectievakje voor **GitHub project** en geef de URL van het GitHub-project. Dit is de URL waarop de Service Fabric Java-toepassing wordt gehost die u wilt integreren met de CI-/CD-stroom van Jenkins (Continue integratie, Continue implementatie), bijvoorbeeld ``https://github.com/testaccount/dev_test``.

   b. Selecteer onder de sectie **Broncodebeheer** de optie **Git**. Geef de opslagplaats-URL op waarop de Service Fabric Java-toepassing wordt gehost die u wilt integreren met de CI-/CD-stroom van Jenkins, (bijvoorbeeld *`https://github.com/testaccount/dev_test.git`* ). U kunt hier ook opgeven welke vertakking u wilt maken, bijvoorbeeld **/master**.

1. Configureer uw *GitHub* (waar de opslagplaats wordt gehost), zodat deze kan communiceren met Jenkins. Voer de volgende stappen uit:

   a. Ga naar de pagina van de GitHub-opslagplaats. Ga naar **Instellingen** > **Integraties en services**.

   b. Selecteer **Add Service**, typ **Jenkins** en selecteer de **Jenkins-GitHub-invoegtoepassing**.

   c. Geef de Jenkins-webhook-URL op (standaard is dit ``http://<PublicIPorFQDN>:8081/github-webhook/``). Klik op **add/update service**.

   d. Er wordt een testgebeurtenis verzonden naar uw Jenkins-exemplaar. Er komt een groen vinkje bij de webhook te staan in GitHub en uw project wordt gemaakt.

   ![Service Fabric Jenkins-configuratie](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. Selecteer in de sectie **Build Triggers** de gewenste buildoptie. Voor dit voorbeeld wilt u een build activeren telkens wanneer er een push naar de opslagplaats plaatsvindt. Selecteer daarom de optie **GitHub hook trigger for GITScm polling**.

1. Ga naar de sectie **Build** en selecteer in de vervolgkeuzelijst **Add build step** de optie **Invoke Gradle Script**. Geef in de volgende widget het pad op naar het **hoofdscript** van uw toepassing. Het haalt build.gradle op uit het opgegeven pad en werkt dienovereenkomstig.

    ![Service Fabric Jenkins Build action](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. Selecteer in de vervolgkeuzelijst **Post-Build Actions** de optie **Post-Build Actions**. Hier moet u clustergegevens opgeven, zoals waar de via Jenkins gecompileerde Service Fabric-toepassing wordt geïmplementeerd. Het pad naar het certificaat is waar het volume is gekoppeld (/tmp/myCerts).

    U kunt ook aanvullende toepassingsgegevens opgeven. Deze worden gebruikt om de toepassing te implementeren. Bekijk de volgende schermafbeelding voor een voorbeeld van de details van de toepassing:

    ![Dialoogvenster acties na compileren met aanvullende opties voor Service Fabric-projectimplementatie](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Het cluster dat u hier gebruikt, moet het cluster zijn waarin de Jenkins-containertoepassing wordt gehost als u Service Fabric gebruikt om de installatiekopie van de Jenkins-containerinstallatiekopie te implementeren.
    >

1. Klik op **Opslaan**.

## <a name="update-your-existing-application"></a>De bestaande toepassing bijwerken

1. Werk de titel bij van de HTML-code in het bestand *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* met **Service Fabric Voting Sample V2**.

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. Update de versie **ApplicationTypeVersion** en **ServiceManifestVersion** naar **2.0.0** in het bestand *Voting/VotingApplication / ApplicationManifest.xml*.

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. Update het **Versie**-veld in het **ServiceManifest** en het **Versie**-veld in de **CodePackage**-code in het bestand  *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* naar **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. Voor het initialiseren van een Jenkins-taak die een upgrade van de toepassing uitvoert, moet u uw nieuwe wijzigingen naar uw GitHub-opslagplaats forceren.

1. Klik in Service Fabric Explorer op de vervolgkeuzelijst **Toepassingen**. Klik op het tabblad **Upgrades in uitvoering** om de status van de upgrade te zien.

    ![Upgrade wordt uitgevoerd](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. Als u **http://\<Host-IP>:8080** bezoekt, is de stemtoepassing met volledige functionaliteit nu actief en werkend.

    ![Lokale stem-app](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Service Fabric Jenkins-container implementeren op uw machine
> * Jenkins-omgeving voor implementatie naar Service Fabric instellen
> * Uw toepassing upgraden

* Andere [Java-voorbeelden](https://github.com/Azure-Samples/service-fabric-java-getting-started) bekijken
