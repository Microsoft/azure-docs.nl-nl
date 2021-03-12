---
title: Een faserings omgeving instellen in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het gebruik van een Blue-groene implementatie met Azure veer Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016573"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Een faserings omgeving instellen in azure lente-Cloud

**Dit artikel is van toepassing op:** ✔️ Java

In dit artikel wordt uitgelegd hoe u een faserings implementatie instelt met behulp van het patroon voor blauw-groen-implementatie in azure lente-Cloud. De implementatie van blauw en groen is een continu bezorgings patroon van Azure DevOps dat afhankelijk is van het bewaren van een bestaande (Blue) versie Live terwijl er een nieuw (groen) bericht wordt geïmplementeerd. In dit artikel wordt beschreven hoe u die fase ring implementeert in productie zonder de productie-implementatie te wijzigen.

## <a name="prerequisites"></a>Vereisten

* Azure lente-Cloud exemplaar op een Standard-prijs categorie
* [Azure lente-Cloud extensie](/cli/azure/azure-cli-extensions-overview) voor Azure cli

In dit artikel wordt een toepassing gebruikt die is gebouwd op basis van de lente-initialisatie functie. Als u een andere toepassing voor dit voor beeld wilt gebruiken, moet u een eenvoudige wijziging aanbrengen in een openbaar gedeelte van de toepassing om uw staging-implementatie te onderscheiden van productie.

>[!TIP]
> [Azure Cloud shell](https://shell.azure.com) is een gratis interactieve shell die u kunt gebruiken om de instructies in dit artikel uit te voeren.  Het heeft gemeen schappelijke, vooraf geïnstalleerde Azure-hulpprogram ma's, waaronder de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw Cloud Shell-exemplaar. Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md)voor meer informatie.

Volg de instructies in de volgende secties om de implementatie van blauw groen in te stellen in azure lente-Cloud.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>De app en implementaties voorbereiden
Voer de volgende stappen uit om de toepassing te bouwen:

1. Genereer de code voor de voor beeld-app met lente initialisatie met [deze configuratie](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Down load de code.
3. Voeg het volgende HelloController. java-bron bestand toe aan de map `\src\main\java\com\example\hellospring\` :

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

Geïmplementeerde apps weer geven met behulp van de volgende procedure:

1. Ga naar uw Azure veer Cloud-exemplaar in het Azure Portal.

1. Open in het linkerdeel venster het deel venster **apps** om apps voor uw service-exemplaar weer te geven.

   ![Scherm opname van het deel venster apps openen.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. U kunt een app selecteren en details weer geven.

   ![Scherm afbeelding van Details voor een app.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Open **implementaties** om alle implementaties van de app te bekijken. In het raster worden productie-en faserings implementaties weer gegeven.

   ![Scherm opname van de weer gegeven app-implementaties.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Selecteer de URL om de momenteel geïmplementeerde toepassing te openen.
    
   ![Scherm opname waarin de U R L voor de geïmplementeerde toepassing wordt weer gegeven.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Selecteer **productie** in de kolom **status** om de standaard-app weer te geven.
    
   ![Scherm opname waarin de U R L voor de standaard-app wordt weer gegeven.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Selecteer **fase ring** in de kolom **status** om de faserings-app weer te geven.
    
   ![Scherm opname van de U R L voor de faserings-app.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Controleer of het eind punt van de test eindigt met een slash (/) om ervoor te zorgen dat het CSS-bestand correct wordt geladen.  
> * Als uw browser vereist dat u aanmeldings referenties opgeeft om de pagina weer te geven, gebruikt u de [URL decoderen](https://www.urldecoder.org/) om uw test eindpunt te decoderen. Het decoderen van de URL retourneert een URL in de indeling *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green*. Gebruik deze indeling om toegang te krijgen tot uw eind punt.

>[!NOTE]    
> Configuratie server instellingen zijn van toepassing op zowel uw faserings omgeving als uw productie omgeving. Als u bijvoorbeeld het pad naar de context (*server. servlet. context-path*) instelt voor uw app-gateway in de configuratie server als *somepath*, wordt het pad naar uw groene implementatie gewijzigd naar *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green/somepath/...*.
 
Als u op dit punt naar de open bare app-gateway gaat, ziet u de oude pagina zonder uw nieuwe wijziging.

## <a name="set-the-green-deployment-as-the-production-environment"></a>De groene implementatie instellen als de productie omgeving

1. Nadat u uw wijziging in uw faserings omgeving hebt gecontroleerd, kunt u deze pushen naar productie. Selecteer op de  >  pagina **implementaties** van apps de toepassing die momenteel in **productie** is.

1. Selecteer de weglatings tekens na **registratie status** van de groene implementatie en selecteer vervolgens **instellen als productie**. 

   ![Scherm opname van de selecties voor het instellen van de faserings opbouw voor productie.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Controleer of de URL van de app uw wijzigingen weergeeft.

   ![Scherm opname waarin de U R L van de app nu in productie wordt weer gegeven.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Nadat u de groene implementatie hebt ingesteld als de productie omgeving, wordt de vorige implementatie de faserings implementatie.

## <a name="modify-the-staging-deployment"></a>De faserings implementatie wijzigen

Als u niet tevreden bent met uw wijziging, kunt u de toepassings code wijzigen, een nieuw jar-pakket bouwen en het uploaden naar uw groene implementatie met behulp van de Azure CLI:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>De faserings implementatie verwijderen

Als u uw staging-implementatie wilt verwijderen uit de Azure Portal, gaat u naar de pagina voor uw staging-implementatie en selecteert u de knop **verwijderen** .

U kunt ook uw staging-implementatie verwijderen uit de Azure CLI door de volgende opdracht uit te voeren:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Volgende stappen

* [CI/CD voor Azure lente-Cloud](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)