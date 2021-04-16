---
title: Uw eerste app implementeren in Cloud Foundry in Microsoft Azure
description: Een toepassing implementeren voor Cloud Foundry in Azure
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 1cffc36cbd4f24bbcbb5996a323ffa963e311693
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530957"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Uw eerste app implementeren in Cloud Foundry in Microsoft Azure

[Cloud Foundry](https://cloudfoundry.org) is een populair opensource-toepassingsplatform dat beschikbaar is op Microsoft Azure. In dit artikel laten we zien hoe u een toepassing implementeert en beheert op Cloud Foundry in een Azure-omgeving.

## <a name="create-a-cloud-foundry-environment"></a>Een Cloud Foundry maken

Er zijn verschillende opties voor het maken van een Cloud Foundry in Azure:

- Gebruik de [Pivotal Cloud Foundry-aanbieding][pcf-azuremarketplace] in de Azure Marketplace om een standaardomgeving te maken die PCF Ops Manager en de Azure Service Broker. U vindt de [volledige instructies voor het][pcf-azuremarketplace-pivotaldocs] implementeren van de Marketplace-aanbieding in de Pivotal-documentatie.
- Maak een aangepaste omgeving door [Pivotal Cloud Foundry implementeren.][pcf-custom]
- [Implementeer de opensource-Cloud Foundry][oss-cf-bosh] door een [BOSH-directeur](https://bosh.io) in te stellen, een VM die de implementatie van de Cloud Foundry coördineert.

> [!IMPORTANT] 
> Als u PCF implementeert vanuit de Azure Marketplace, noteert u de SYSTEMDOMAINURL en de beheerdersreferenties die zijn vereist voor toegang tot Pivotal Apps Manager. Beide worden beschreven in de marketplace-implementatiehandleiding. Ze zijn nodig om deze zelfstudie te voltooien. Voor Marketplace-implementaties heeft SYSTEMDOMAINURL de vorm `https://system.*ip-address*.cf.pcfazure.com` .

## <a name="connect-to-the-cloud-controller"></a>Verbinding maken met de cloudcontroller

De cloudcontroller is het primaire toegangspunt voor een Cloud Foundry voor het implementeren en beheren van toepassingen. De core Cloud Controller API ( PIPPI) is een REST API, maar is toegankelijk via verschillende hulpprogramma's. In dit geval werken we hiermee via de [Cloud Foundry CLI][cf-cli]. U kunt de CLI installeren in Linux, macOS of Windows, maar als u deze liever helemaal niet installeert, is deze vooraf geïnstalleerd in [de Azure Cloud Shell.][cloudshell-docs]

Als u zich wilt aanmelden, gaat u `api` naar de SYSTEMDOMAINURL die u hebt verkregen via de Marketplace-implementatie. Omdat voor de standaardimplementatie een zelfonder ondertekend certificaat wordt gebruikt, moet u ook de `skip-ssl-validation` switch opnemen.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

U wordt gevraagd u aan te melden bij de cloudcontroller. Gebruik de beheerdersaccountreferenties die u hebt verkregen bij de implementatiestappen voor Marketplace.

Cloud Foundry biedt *organisaties en* *ruimten* als naamruimten om de teams en omgevingen binnen een gedeelde implementatie te isoleren. De PCF Marketplace-implementatie  bevat de standaardsysteem-organisatie en een set spaties die zijn gemaakt om de basisonderdelen te bevatten, zoals de service voor automatisch schalen en de Azure-servicebroker. Kies voor nu de *systeemruimte.*


## <a name="create-an-org-and-space"></a>Een organisatie en ruimte maken

Als u typt, ziet u een set systeemtoepassingen die zijn geïmplementeerd `cf apps` in de systeemruimte binnen de systeemgroep. 

Houd de *systeemorgatie* gereserveerd voor systeemtoepassingen, dus maak een organisatie en ruimte voor onze voorbeeldtoepassing.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

Gebruik de doelopdracht om over te schakelen naar de nieuwe organisatie en de nieuwe ruimte:

```bash
cf target -o testorg -s dev
```

Wanneer u nu een toepassing implementeert, wordt deze automatisch gemaakt in de nieuwe organisatie en ruimte. Typ opnieuw om te bevestigen dat er momenteel geen apps in de nieuwe organisatie/ruimte `cf apps` zijn.

> [!NOTE] 
> Zie de documentatie over Cloud Foundry voor meer informatie over organisaties en ruimten en hoe deze kunnen worden [][cf-orgs-spaces-docs]gebruikt voor Cloud Foundry op rollen gebaseerd toegangsbeheer (Cloud Foundry RBAC).

## <a name="deploy-an-application"></a>Een app implementeren

We gebruiken een voorbeeld van een Cloud Foundry toepassing met de naam Hello Spring Cloud, die is geschreven in Java en is gebaseerd op het [Spring Framework](https://spring.io) [en Spring Boot](https://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>De Hello Spring Cloud-opslagplaats klonen

De Hello Spring Cloud-voorbeeldtoepassing is beschikbaar op GitHub. Kloon deze naar uw omgeving en wijzig deze in de nieuwe map:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>De toepassing bouwen

Bouw de app met [behulp van Apache Maven](https://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>De toepassing implementeren met cf push

U kunt de meeste toepassingen implementeren in Cloud Foundry met behulp van de `push` opdracht :

```bash
cf push
```

Wanneer u *een* toepassing pusht, Cloud Foundry het type toepassing (in dit geval een Java-app) gedetecteerd en worden de afhankelijkheden geïdentificeerd (in dit geval het Spring-framework). Vervolgens wordt alles verpakt dat nodig is om uw code uit te voeren in een zelfstandige containerafbeelding, ook wel een *droplet genoemd.* Ten slotte Cloud Foundry de toepassing op een van de beschikbare machines in uw omgeving gepland en wordt er een URL gemaakt waar u deze kunt bereiken. Deze is beschikbaar in de uitvoer van de opdracht.

![Uitvoer van cf push-opdracht][cf-push-output]

Als u de hello-spring-cloud-toepassing wilt zien, opent u de opgegeven URL in uw browser:

![Standaard gebruikersinterface voor Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> Zie How Applications Are Staged (Hoe toepassingen worden gefaseerd) in de Cloud Foundry documentatie voor `cf push` meer informatie over wat er Cloud Foundry gebeurt. [][cf-push-docs]

## <a name="view-application-logs"></a>Toepassingslogboeken weergeven

U kunt de cli Cloud Foundry gebruiken om logboeken voor een toepassing weer te geven met de naam:

```bash
cf logs hello-spring-cloud
```

De logboeken opdracht maakt standaard gebruik *van tail*, waarin nieuwe logboeken worden weergegeven als ze worden geschreven. Als u wilt zien hoe nieuwe logboeken worden weergegeven, vernieuwt u de hello-spring-cloud-app in de browser.

Als u logboeken wilt weergeven die al zijn geschreven, voegt u de schakelknop `recent` toe:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>De toepassing schalen

Standaard wordt `cf push` slechts één exemplaar van uw toepassing gemaakt. Om hoge beschikbaarheid te garanderen en uit te schalen voor een hogere doorvoer, wilt u over het algemeen meer dan één exemplaar van uw toepassingen uitvoeren. Met de opdracht kunt u eenvoudig reeds geïmplementeerde toepassingen `scale` uitschalen:

```bash
cf scale -i 2 hello-spring-cloud
```

Als u `cf app` de opdracht in de toepassing Cloud Foundry ziet u dat er een ander exemplaar van de toepassing wordt aanmaken. Zodra de toepassing is gestart, wordt Cloud Foundry taakverdeling van verkeer naar de toepassing gestart.


## <a name="next-steps"></a>Volgende stappen

- [Lees de Cloud Foundry documentatie][cloudfoundry-docs]
- [De Azure DevOps Services-invoegservice instellen voor Cloud Foundry][vsts-plugin]
- [Microsoft Log Analytics Nozzle configureren voor Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/ops-manager/2-10/install/pcf_azure.html
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
