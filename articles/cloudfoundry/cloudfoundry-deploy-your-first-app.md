---
title: Uw eerste app implementeren in Cloud Foundry in Microsoft Azure
description: Een toepassing implementeren op Cloud Foundry in azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 037ac972dca49484f7b8c0ad8eed6942c901b997
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562925"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Uw eerste app implementeren in Cloud Foundry in Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) is een populair open-source toepassings platform dat beschikbaar is op Microsoft Azure. In dit artikel laten we zien hoe u een toepassing kunt implementeren en beheren op Cloud Foundry in een Azure-omgeving.

## <a name="create-a-cloud-foundry-environment"></a>Een Cloud Foundry omgeving maken

Er zijn verschillende opties voor het maken van een Cloud Foundry omgeving in Azure:

- Gebruik de [Cloud Foundry aanbieding voor de draai tabel][pcf-azuremarketplace] in azure Marketplace om een standaard omgeving te maken met PCF OPS Manager en Azure Service Broker. U vindt [volledige instructies][pcf-azuremarketplace-pivotaldocs] voor het implementeren van de Marketplace-aanbieding in de Pivot-documentatie.
- Maak een aangepaste omgeving door de [Cloud Foundry hand matig te implementeren][pcf-custom].
- [Implementeer de open-source Cloud Foundry pakketten rechtstreeks][oss-cf-bosh] door een [BOSH](https://bosh.io) -directeur in te stellen, een VM die de implementatie van de Cloud Foundry omgeving coördineert.

> [!IMPORTANT] 
> Als u PCF implementeert vanuit Azure Marketplace, noteert u de SYSTEMDOMAINURL en de beheerders referenties die zijn vereist voor toegang tot de Pivot-apps Manager, die beide zijn beschreven in de implementatie handleiding voor Marketplace. Ze zijn nodig om deze zelf studie te volt ooien. Voor Marketplace-implementaties is de SYSTEMDOMAINURL in het formulier `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Verbinding maken met de Cloud controller

De Cloud controller is het primaire toegangs punt voor een Cloud Foundry omgeving voor het implementeren en beheren van toepassingen. De belangrijkste Cloud controller-API (CCAPI) is een REST API, maar is toegankelijk via verschillende hulpprogram ma's. In dit geval communiceren we met de [Cloud FOUNDRY cli][cf-cli]. U kunt de CLI installeren in Linux, macOS of Windows, maar als u deze liever niet wilt installeren, is deze vooraf geïnstalleerd in de [Azure Cloud shell][cloudshell-docs].

Als u zich wilt aanmelden, laten voorafgaan door u `api` met de SYSTEMDOMAINURL die u hebt verkregen via Marketplace-implementatie. Aangezien de standaard implementatie gebruikmaakt van een zelfondertekend certificaat, moet u ook de `skip-ssl-validation` Schakel optie toevoegen.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

U wordt gevraagd om u aan te melden bij de Cloud controller. Gebruik de referenties van het beheerders account die u hebt verkregen via de implementatie stappen voor Marketplace.

Cloud Foundry voorziet in *organisaties* en *ruimten* als naam ruimten voor het isoleren van de teams en omgevingen binnen een gedeelde implementatie. De implementatie van de PCF Marketplace omvat de standaard *systeem* org en een set met ruimten die zijn gemaakt met de basis onderdelen, zoals de service voor automatisch schalen en de Service Broker van Azure. Kies voor nu de *systeem* ruimte.


## <a name="create-an-org-and-space"></a>Een organisatie en ruimte maken

Als u typt `cf apps` , ziet u een set systeem toepassingen die zijn geïmplementeerd in de systeem ruimte binnen de organisatie van het systeem. 

U moet ervoor zorgen dat de *systeem* organisatie voor systeem toepassingen is gereserveerd. Maak dus een organisatie en ruimte voor onze voorbeeld toepassing.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Gebruik de doel opdracht om over te scha kelen naar de nieuwe organisatie en ruimte:

```bash
cf target -o testorg -s dev
```

Wanneer u nu een toepassing implementeert, wordt deze automatisch gemaakt in de nieuwe organisatie en ruimte. Als u wilt bevestigen dat er momenteel geen apps in de nieuwe organisatie of ruimte zijn, typt u `cf apps` opnieuw.

> [!NOTE] 
> Zie de [documentatie van Cloud Foundry][cf-orgs-spaces-docs]voor meer informatie over organisaties en Spaces en hoe ze kunnen worden gebruikt voor Cloud Foundry op rollen gebaseerd toegangs beheer (Cloud Foundry RBAC).

## <a name="deploy-an-application"></a>Een app implementeren

We gaan een voor beeld gebruiken van een Cloud Foundry toepassing Hello lente, die is geschreven in Java en op basis van het [lente-Framework](https://spring.io) en [Spring boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>Het Hello lente-Cloud archief klonen

De voor beeld-Cloud toepassing Hello lente is beschikbaar op GitHub. Kloon het naar uw omgeving en wijzig de nieuwe map:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>De toepassing bouwen

Bouw de app met [Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>De toepassing implementeren met CF push

U kunt de meeste toepassingen implementeren op Cloud Foundry met behulp van de `push` opdracht:

```bash
cf push
```

Wanneer u een toepassing *pusht* , detecteert Cloud Foundry het type toepassing (in dit geval een Java-app) en identificeert het de afhankelijkheden (in dit geval het lente-Framework). Vervolgens worden alle items die nodig zijn om uw code uit te voeren, verpakt in een zelfstandige container installatie kopie, ook wel een *droplet* genoemd. Ten slotte Cloud Foundry plant u de toepassing op een van de beschik bare computers in uw omgeving en maakt u een URL waar u deze kunt bereiken. deze is beschikbaar in de uitvoer van de opdracht.

![Uitvoer van de CF-push opdracht][cf-push-output]

Als u de Cloud toepassing wilt bekijken, opent u de gegeven URL in uw browser:

![Standaard-UI voor Hello lente-Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> `cf push`Zie [hoe toepassingen worden klaargezet][cf-push-docs] in de Cloud Foundry documentatie voor meer informatie over wat er gebeurt tijdens.

## <a name="view-application-logs"></a>Toepassings logboeken weer geven

U kunt de Cloud Foundry CLI gebruiken om logboeken voor een toepassing weer te geven op basis van de naam:

```bash
cf logs hello-spring-cloud
```

De logboeken opdracht maakt standaard gebruik van *staart*, waarin nieuwe logboeken worden weer gegeven wanneer ze zijn geschreven. Als u nieuwe logboeken wilt weer geven, vernieuwt u de Hello-lente-Cloud-app in de browser.

Als u logboeken wilt weer geven die al zijn geschreven, voegt u de `recent` switch toe:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>De toepassing schalen

Standaard `cf push` maakt slechts één exemplaar van uw toepassing. Om hoge Beschik baarheid te garanderen en uit te schalen voor een hogere door Voer, wilt u meestal meer dan één exemplaar van uw toepassingen uitvoeren. U kunt al geïmplementeerde toepassingen eenvoudig uitschalen met behulp van de `scale` opdracht:

```bash
cf scale -i 2 hello-spring-cloud
```

Als `cf app` u de opdracht uitvoert op de toepassing, ziet u dat Cloud Foundry een andere instantie van de toepassing maakt. Zodra de toepassing is gestart, wordt door Cloud Foundry automatisch taakverdelings verkeer gestart.


## <a name="next-steps"></a>Volgende stappen

- [Raadpleeg de documentatie van Cloud Foundry][cloudfoundry-docs]
- [De Azure DevOps Services-invoeg toepassing voor Cloud Foundry instellen][vsts-plugin]
- [De micro soft Log Analytics-spuit voor Cloud Foundry configureren][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png