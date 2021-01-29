---
title: Een faserings omgeving instellen in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het gebruik van een Blue-groene implementatie met Azure veer Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053060"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Een faserings omgeving instellen in azure lente-Cloud

**Dit artikel is van toepassing op:** ✔️ Java

In dit artikel wordt beschreven hoe u een faserings implementatie instelt met behulp van het patroon voor blauw-groen-implementatie in azure lente-Cloud. Blauw/groen-implementatie is een Azure DevOps-patroon voor continue levering dat erop vertrouwt een bestaande (blauwe) versie live te houden terwijl een nieuwe (groene) wordt geïmplementeerd. In dit artikel wordt beschreven hoe u die fase ring implementeert in productie zonder de productie-implementatie rechtstreeks te wijzigen.

## <a name="prerequisites"></a>Vereisten

* Een actieve toepassing.  Zie [Quick Start: uw eerste Azure lente-Cloud toepassing implementeren](spring-cloud-quickstart.md).
* Azure CLI [ASC-extensie](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Als u een andere toepassing voor dit voor beeld wilt gebruiken, moet u een eenvoudige wijziging aanbrengen in een openbaar gedeelte van de toepassing.  Deze wijziging is van invloed op uw staging-implementatie van productie.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u kunt gebruiken om de instructies in dit artikel uit te voeren.  Het heeft gemeen schappelijke, vooraf geïnstalleerde Azure-hulpprogram ma's, waaronder de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com).  Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md)voor meer informatie.

Volg de instructies in de volgende secties om een faserings omgeving in te stellen in azure lente Cloud.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Apps en implementaties weer geven

Geïmplementeerde apps weer geven met behulp van de volgende procedures.

1. Ga naar uw Azure veer Cloud-exemplaar in het Azure Portal.

1. Open **implementaties** in het navigatie deel venster links.

    [![Implementatie-afschaffen](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Open de Blade apps om apps voor uw service-exemplaar weer te geven.

    [![Apps-dash board](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. U kunt op een app klikken en details weer geven.

    [![Apps-overzicht](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Open de Blade **implementaties** om alle implementaties van de app weer te geven. In het implementatie raster ziet u of de implementatie productie of fase ring is.

    [![Implementaties dashboard](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. U kunt op de naam van de implementatie klikken om het implementatie overzicht weer te geven. In dit geval is de enige implementatie de *standaard* naam.

    [![Overzicht van implementaties](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Een faserings implementatie maken

1. Maak in uw lokale ontwikkel omgeving een kleine wijziging in uw toepassing. Zo kunt u de twee implementaties eenvoudig onderscheiden. Voer de volgende opdracht uit om het jar-pakket te bouwen: 

    ```console
    mvn clean package -DskipTests
    ```

1. Maak in de Azure CLI een nieuwe implementatie en geef deze de naam ' groen ' van de faserings implementatie.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. Nadat de CLI-implementatie is voltooid, opent u de app-pagina vanuit het **toepassings dashboard** en bekijkt u alle exemplaren op het tabblad **implementaties** aan de linkerkant.

   [![Implementaties dash board na groen geïmplementeerd](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> De detectie status is *OUT_OF_SERVICE* zodat verkeer naar deze implementatie niet wordt doorgestuurd voordat de verificatie is voltooid.

## <a name="verify-the-staging-deployment"></a>De faserings implementatie verifiëren

Controleren of de ontwikkeling van de groene fase werkt:
1. Ga naar **implementaties** en klik op de `green` **faserings implementatie**.
1. Klik op de pagina **overzicht** op het **eind punt** van de test.
1. Hiermee opent u de faserings build waarin uw wijzigingen worden weer gegeven.

>[!TIP]
> * Controleer of het eind punt van de test eindigt met een slash (/) om ervoor te zorgen dat het CSS-bestand correct wordt geladen.  
> * Als uw browser vereist dat u aanmeldings referenties opgeeft om de pagina weer te geven, gebruikt u de [URL decoderen](https://www.urldecoder.org/) om uw test eindpunt te decoderen. Het decoderen van de URL retourneert een URL in de vorm "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green".  Gebruik dit formulier om toegang te krijgen tot uw eind punt.

>[!NOTE]    
> Instellingen voor de configuratie server zijn van toepassing op zowel uw faserings omgeving als productie. Als u bijvoorbeeld het context pad ( `server.servlet.context-path` ) voor uw app-gateway in de configuratie server instelt als *somepath*, wordt het pad naar uw groene implementatie gewijzigd in ' https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green/somepath/... '.
 
 Als u op dit punt naar de open bare app-gateway gaat, ziet u de oude pagina zonder uw nieuwe wijziging.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>De groene implementatie instellen als de productie omgeving

1. Nadat u uw wijziging in uw faserings omgeving hebt gecontroleerd, kunt u deze pushen naar productie. Ga terug naar **implementatie beheer** en selecteer de toepassing die momenteel in wordt weer gegeven `Production` .

1. Klik op de weglatings tekens na de **registratie status** en stel de productie-build in op `staging` .

   [![Implementaties stellen faserings implementatie in](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Ga terug naar de pagina **implementatie beheer** .  De implementatie `green` status *van* uw distributie moet worden weer gegeven. Dit is nu de actieve productie-build.

   [Implementatie van het implementatie ![ resultaat instellen fase ring](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Kopieer de URL en plak deze in een nieuw browser venster. de nieuwe toepassings pagina moet worden weer gegeven met uw wijzigingen.

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

* [CI/CD voor Azure lente-Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)