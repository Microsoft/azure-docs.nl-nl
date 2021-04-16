---
title: Quickstart - installatiekopieën van Java-containers ontwerpen en pushen naar Azure Container Registry met Maven en Jib
description: Ontwerp een in een container geplaatste Java-app en push deze naar Azure Container Registry met behulp van de Maven Jib-invoegtoepassing.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537293"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Quickstart: Containerinstallatiekopieën naar Azure Container Registry ontwerpen en pushen

In deze quickstart leert u om een in een container geplaatste Java-app te ontwerpen en deze naar Azure Container Registry te pushen met behulp van de Maven Jib-invoegtoepassing. Het gebruik van Maven en Jib is een voorbeeld van het gebruik van hulpprogramma's voor ontwikkelaars om te werken met een Azure Container Registry.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement; als u nog geen Azure-abonnement hebt, kunt u uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) activeren of u aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/pricing/free-trial).
* De [Azure-opdrachtregelinterface (CLI)](/cli/azure/overview).
* Een ondersteunde JDK (Java Development Kit). Zie <https://aka.ms/azure-jdks> voor meer informatie over de JDK's die kunnen worden gebruikt bij het ontwikkelen in Azure.
* Het build-hulpprogramma [Maven](http://maven.apache.org) van Apache (versie 3 of hoger).
* Een [Git](https://git-scm.com)-client.
* Een [Docker](https://www.docker.com)-client.
* De [ACR Docker-referentiehelper](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>De web-app Aan de slag met Spring Boot in Docker maken

De volgende stappen helpen u bij het bouwen van een Spring Boot-web-app en bij het lokaal testen van de web-app.

1. Gebruik in de opdrachtprompt de volgende opdracht om het voorbeeldproject [Aan de slag met Spring Boot in Docker](https://github.com/spring-guides/gs-spring-boot-docker) te klonen.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Ga naar de map met het voltooide project.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Gebruik Maven om de voorbeeld-app te bouwen en uit te voeren.

   ```bash
   mvn package spring-boot:run
   ```

1. Test de web-app door naar `http://localhost:8080` te bladeren of met de volgende opdracht `curl`:

   ```bash
   curl http://localhost:8080
   ```

Het volgende bericht wordt weergegeven: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Een Azure Container Registry maken met Azure CLI

Vervolgens maakt u een Azure-resourcegroep en uw ACR aan de hand van de volgende stappen:

1. Meld u aan bij uw Azure-account met behulp van de volgende opdracht:

   ```azurecli
   az login
   ```

1. Geef het te gebruiken Azure-abonnement op:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Maak een resourcegroep voor de Azure-resources die in deze zelfstudie worden gebruikt. Vervang in de volgende opdracht de tijdelijke aanduidingen door de naam van uw eigen resource en een locatie zoals `eastus`.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Maak een privé-Azure-containerregister in de resourcegroep met behulp van de volgende opdracht. Zorg dat u de plaatsaanduidingen vervangt door echt waarden. In de zelfstudie wordt in latere stappen de voorbeeld-app als een Docker-installatiekopie naar dit register gepusht.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Uw app via Jib naar het containerregister pushen

Ten slotte werkt u de projectconfiguratie bij en gebruikt u de opdrachtprompt om uw installatiekopie te ontwerpen en te implementeren.

> [!NOTE]
> Als u zich wilt aanmelden bij het Azure-containerregister dat u zojuist hebt gemaakt, dan moet u de Docker-daemon uitvoeren. [Hier is de officiële Docker-documentatie](https://docs.docker.com/install/) om Docker op uw computer te installeren.

1. Meld u aan bij uw Azure Container Registry vanaf de Azure CLI met behulp van de volgende opdracht. Vervang de tijdelijke aanduiding door de naam van uw eigen register.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Met de `az configure`-opdracht wordt de standaardnaam van het register ingesteld voor gebruik met `az acr`-opdrachten.

1. Navigeer naar de voltooide projectmap voor uw SpringBoot-app (bijvoorbeeld *C:\SpringBoot\gs-spring-boot-docker\complete* of */users/robert/SpringBoot/gs-spring-boot-docker/complete*) en open het bestand *pom.xml* met een teksteditor.

1. Werk de verzameling `<properties>` in het bestand *pom.xml* bij met de volgende XML. Vervang de tijdelijke aanduiding door de naam van uw register en voeg een `<jib-maven-plugin.version>`-eigenschap met de waarde `2.2.0` of een nieuwere versie van de [jib-maven-invoegtoepassing](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) toe.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Werk de verzameling `<plugins>` in het bestand *pom.xml* bij zodat het element `<plugin>` een vermelding bevat voor de `jib-maven-plugin`, zoals weergegeven in het volgende voorbeeld. U ziet dat we een basisinstallatiekopie uit het MCR (Microsoft-containerregister) gebruiken: `mcr.microsoft.com/java/jdk:8-zulu-alpine`. Deze bevat een officieel ondersteunde JDK voor Azure. Zie [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) en [Java SE JDK en Maven](https://hub.docker.com/_/microsoft-java-maven) voor andere MCR-basisinstallatiekopieën met officieel ondersteunde JDK’s.

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Ga naar de map met het voltooide project voor uw Spring Boot-toepassing en voer de volgende opdracht uit om de installatiekopie te bouwen en deze naar het register te pushen:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Uit veiligheidsoverwegingen is de referentie die is gemaakt door `az acr login` slechts één uur geldig. Als u een foutmelding *401 Niet geautoriseerd* krijgt, dan kunt u de opdracht `az acr login -n <your registry name>` uitvoeren om zich opnieuw te verifiëren.

## <a name="verify-your-container-image"></a>De containerinstallatiekopie verifiëren

Gefeliciteerd! U heeft u uw door een in een container geplaatste Java App-build in een door Azure ondersteunde JDK die gepusht is naar uw ACR. U kunt de installatiekopie nu testen door deze te implementeren in Azure App Service, of deze naar een lokale locatie te halen met de opdracht (tijdelijke aanduiding vervangen):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Volgende stappen

Zie voor andere versies van de officiële door Microsoft ondersteunde Java-basisinstallatiekopieën:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK en Maven](https://hub.docker.com/_/microsoft-java-maven)

Voor meer informatie over Spring en Azure gaat u door naar het documentatiecentrum van Spring op Azure.

> [!div class="nextstepaction"]
> [Spring in Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Aanvullende resources

Zie de volgende bronnen voor meer informatie:

* [Azure for Java Developers](/azure/java) (Azure voor Java-ontwikkelaars)
* [Werken met Azure DevOps en Java](/azure/devops/java)
* [Aan de slag met Spring Boot in Docker](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Een Spring Boot-toepassing implementeren in Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Een aangepaste Docker-installatiekopie gebruiken voor Azure-web-apps in Linux](../app-service/tutorial-custom-container.md)
