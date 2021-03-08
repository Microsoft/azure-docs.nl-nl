---
title: Een faserings omgeving instellen in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het gebruik van een Blue-groene implementatie met Azure veer Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 403cfe72a4c5b6dbaea7e4eb93c37f687970443c
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451928"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Een faserings omgeving instellen in azure lente-Cloud

**Dit artikel is van toepassing op:** ✔️ Java

In dit artikel wordt uitgelegd hoe u een faserings implementatie instelt met behulp van het Blue-groen implementatie patroon in azure lente-Cloud. De implementatie van blauw en groen is een continu bezorgings patroon van Azure DevOps dat afhankelijk is van het bewaren van een bestaande (Blue) versie Live, terwijl er een nieuw (groen)-abonnement wordt geïmplementeerd. In dit artikel wordt beschreven hoe u die fase ring implementeert in productie zonder de productie-implementatie te wijzigen.

## <a name="prerequisites"></a>Vereisten

* Azure veer Cloud-instantie in de *Standard* - **prijs categorie**.
* Azure CLI [Azure lente-Cloud uitbreiding](/cli/azure/azure-cli-extensions-overview)

In dit artikel wordt gebruikgemaakt van een toepassing die is gebouwd op basis van de lente initialisatie functie. Als u een andere toepassing voor dit voor beeld wilt gebruiken, moet u een eenvoudige wijziging aanbrengen in een openbaar gedeelte van de toepassing om uw staging-implementatie te onderscheiden van productie.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u kunt gebruiken om de instructies in dit artikel uit te voeren.  Het heeft gemeen schappelijke, vooraf geïnstalleerde Azure-hulpprogram ma's, waaronder de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com).  Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md)voor meer informatie.

Volg de instructies in de volgende secties om blauw-groen implementaties in te stellen in azure lente-Cloud.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>App en implementaties voorbereiden
Voer de volgende stappen uit om de toepassing te bouwen:
1. Genereer de code voor de voor beeld-app met behulp van de lente initialisatie functie met [deze configuratie](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Down load de code.
3. Voeg het volgende bron bestand HelloController. java toe aan de map `\src\main\java\com\example\hellospring\` .
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud!"; 
  } 

} 
```
4. Bouw het jar-bestand:
```azurecli
mvn clean packge -DskipTests
```
5. Maak de app in uw Azure lente Cloud-exemplaar:
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
```
6. De app implementeren in azure lente Cloud:
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. Wijzig de code voor uw staging-implementatie:
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. Bouw het jar-bestand opnieuw op:
```azurecli
mvn clean packge -DskipTests
```
9. De groene implementatie maken: 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>Apps en implementaties weer geven

Geïmplementeerde apps weer geven met behulp van de volgende procedures.

1. Ga naar uw Azure veer Cloud-exemplaar in het Azure Portal.

1. Open in het linkernavigatievenster de Blade apps om apps voor uw service-exemplaar weer te geven.

    [![Apps-dash board](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. U kunt op een app klikken en details weer geven.

    [![Apps-overzicht](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Open **implementaties** om alle implementaties van de app te bekijken. In het raster worden productie-en faserings implementaties weer gegeven.

    [![Dash board app/implementaties](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Klik op de URL om de momenteel geïmplementeerde toepassing te openen.
    ![URL geïmplementeerd](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Klik op **productie** in de kolom **status** om de standaard-app weer te geven.
    ![Standaard actief](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Klik op **staging** in de kolom **status** om de faserings-app weer te geven.
    ![Fase ring wordt uitgevoerd](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Controleer of het eind punt van de test eindigt met een slash (/) om ervoor te zorgen dat het CSS-bestand correct wordt geladen.  
> * Als uw browser vereist dat u aanmeldings referenties opgeeft om de pagina weer te geven, gebruikt u de [URL decoderen](https://www.urldecoder.org/) om uw test eindpunt te decoderen. Het decoderen van de URL retourneert een URL in de vorm "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green".  Gebruik dit formulier om toegang te krijgen tot uw eind punt.

>[!NOTE]    
> Instellingen voor de configuratie server zijn van toepassing op zowel uw faserings omgeving als productie. Als u bijvoorbeeld het context pad ( `server.servlet.context-path` ) voor uw app-gateway in de configuratie server instelt als *somepath*, wordt het pad naar uw groene implementatie gewijzigd in ' https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green/somepath/... '.
 
 Als u op dit punt naar de open bare app-gateway gaat, ziet u de oude pagina zonder uw nieuwe wijziging.

## <a name="set-the-green-deployment-as-the-production-environment"></a>De groene implementatie instellen als de productie omgeving

1. Nadat u uw wijziging in uw faserings omgeving hebt gecontroleerd, kunt u deze pushen naar productie. Selecteer op de / pagina **implementaties** van apps de toepassing die momenteel in is `Production` .

1. Klik op de weglatings tekens na de **registratie status** van de groene implementatie en stel de fase ring in op productie. 

   [![Productie instellen op fase ring](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Nu moet de URL van de app uw wijzigingen weer geven.

   ![Nu faseren in implementatie](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Nadat u de groene implementatie hebt ingesteld als de productie omgeving, wordt de vorige implementatie de faserings implementatie.

## <a name="modify-the-staging-deployment"></a>De faserings implementatie wijzigen

Als u niet tevreden bent met uw wijziging, kunt u de toepassings code wijzigen, een nieuw jar-pakket bouwen en dit uploaden naar uw groene implementatie met behulp van de Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>De faserings implementatie verwijderen

Als u uw staging-implementatie wilt verwijderen uit de Azure-poort, gaat u naar de pagina faserings implementatie en selecteert u vervolgens de knop **verwijderen** .

U kunt ook uw staging-implementatie verwijderen uit de Azure CLI door de volgende opdracht uit te voeren:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Volgende stappen

* [CI/CD voor Azure lente-Cloud](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)