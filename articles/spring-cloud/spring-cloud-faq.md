---
title: Veelgestelde vragen over Azure lente Cloud | Microsoft Docs
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure veer Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 50c8a36b943abb24f2c31bfc2bc33e646a74524a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596621"
---
# <a name="azure-spring-cloud-faq"></a>Veelgestelde vragen over Azure veer Cloud

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure veer Cloud.

## <a name="general"></a>Algemeen

### <a name="why-azure-spring-cloud"></a>Waarom Azure lente Cloud?

Azure lente Cloud biedt een platform als een service (PaaS) voor veer-Cloud ontwikkelaars. Azure lente-Cloud beheert uw toepassings infrastructuur zodat u zich kunt concentreren op toepassings code en bedrijfs logica. De belangrijkste functies die zijn ingebouwd in azure lente Cloud zijn onder andere Eureka, configuratie server, service register server, Pivotal build service, Blue-groen implementatie en meer. Met deze service kunnen ontwikkel aars ook hun toepassingen koppelen aan andere Azure-Services, zoals Azure Cosmos DB, Azure Database for MySQL en Azure cache voor redis.

Azure veer Cloud verbetert de diagnostische ervaring van toepassingen voor ontwikkel aars en Opera tors door Azure Monitor, Application Insights en Log Analytics te integreren.

### <a name="how-secure-is-azure-spring-cloud"></a>Hoe veilig is Azure lente-Cloud?

Beveiliging en privacy zijn een van de belangrijkste prioriteiten voor Azure-klanten en Azure lente-Cloud gebruikers. Azure zorgt ervoor dat alleen klanten toegang hebben tot toepassings gegevens,-Logboeken of-configuraties door al deze gegevens veilig te versleutelen. 

* De service-exemplaren in azure lente Cloud zijn van elkaar geïsoleerd.
* Azure lente Cloud biedt volledige TLS/SSL-en certificaat beheer.
* Essentiële beveiligings patches voor OpenJDK en lente-Cloud Runtimes worden zo snel mogelijk toegepast op Azure veer Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In welke regio's is Azure lente-Cloud beschikbaar?

VS-Oost, VS-West 2, VS-centraal, Zuid-Centraal VS, Noord-Centraal VS, VS-West, VS-West 2, Europa-west, Europa-noord, UK-zuid, Zuidoost-Azië, Australië-oost, Canada-centraal, UAE-noord, Centraal-India, Korea-centraal, Azië-oost en China-oost 2 (Moon cake). [Meer informatie](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>Worden klant gegevens buiten de opgegeven regio opgeslagen?

Azure lente Cloud is een regionale service. Alle klant gegevens in azure lente Cloud worden opgeslagen in meerdere regio's binnen hetzelfde geografische gebied van de opgegeven regio voor redundantie. Zie [Data locatie in azure](https://azure.microsoft.com/global-infrastructure/data-residency/)voor meer informatie over geo en regio.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Wat zijn de bekende beperkingen van Azure lente-Cloud?

Voor de Azure lente-Cloud gelden de volgende bekende beperkingen:
    
* `spring.application.name` wordt overschreven door de naam van de toepassing die wordt gebruikt voor het maken van elke toepassing.
* `server.port` wordt standaard ingesteld op poort 1025. Als een andere waarde wordt toegepast, wordt deze overschreven. U moet deze instelling ook respecteren en de server poort niet opgeven in uw code.
* De Azure Portal-en Azure Resource Manager-sjablonen bieden geen ondersteuning voor het uploaden van toepassings pakketten. U kunt toepassings pakketten alleen uploaden door de toepassing te implementeren via de Azure CLI.

### <a name="what-pricing-tiers-are-available"></a>Welke prijs categorieën zijn er beschikbaar? 
Welk item moet ik gebruiken en wat zijn de limieten binnen elke laag?
* Azure lente-Cloud biedt twee prijs Categorieën: Basic en Standard. De laag basis is gericht op dev/test en er wordt geprobeerd Azure lente-Cloud uit te voeren. De Standard-laag is geoptimaliseerd voor het uitvoeren van productie verkeer voor algemeen gebruik. Zie de [prijs informatie voor Azure lente Cloud](https://azure.microsoft.com/pricing/details/spring-cloud/) voor limieten en vergelijking van het functie niveau.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Hoe kan ik feedback geven en problemen melden?

Als u problemen ondervindt met Azure lente Cloud, maakt u een [ondersteunings aanvraag voor Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Als u een functie aanvraag wilt verzenden of feedback wilt geven, gaat u naar [Azure feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Ontwikkeling

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Ik ben een lente-Cloud ontwikkelaar, maar er is nog geen ervaring met Azure. Wat is de snelste manier om te leren hoe u een Azure lente-Cloud toepassing kunt ontwikkelen?

Voor de snelste manier om aan de slag te gaan met Azure lente Cloud, volgt u de instructies in [Quick Start: een Azure lente-Cloud toepassing starten met behulp van de Azure Portal](spring-cloud-quickstart.md).

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Wat Java runtime ondersteunt Azure lente-Cloud ondersteuning?

Azure lente-Cloud ondersteunt Java 8 en 11. Zie [Java runtime en versies van het besturings systeem](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>Is Spring boot 2.4. x ondersteund?
Er is een probleem vastgesteld met de Spring boot 2,4 en er wordt momenteel gewerkt aan de lente-Community om deze op te lossen. In de tussen tijd neemt u deze twee afhankelijkheden op om TLS-verificatie tussen uw apps en Eureka mogelijk te maken.

```xml
<dependency> 
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Waar kan ik de logboeken en metrische gegevens van mijn lente-Cloud weer geven?

Zoek metrische gegevens op het tabblad Overzicht van de app en het tabblad [Azure monitor](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer) .

Azure lente Cloud ondersteunt het exporteren van Lente-Cloud toepassings logboeken en-metrische gegevens naar Azure Storage, EventHub en [log Analytics](../azure-monitor/logs/data-platform-logs.md). De tabel naam in Log Analytics is *AppPlatformLogsforSpring*. Zie [diagnostische services](diagnostic-services.md)voor meer informatie over het inschakelen van deze functie.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Wordt gedistribueerde tracering door Azure lente Cloud ondersteund?

Ja. Zie [zelf studie: gedistribueerde tracering gebruiken met Azure lente-Cloud](spring-cloud-tutorial-distributed-tracing.md)voor meer informatie.

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Welke resource typen worden ondersteund door service binding?

Er worden momenteel drie services ondersteund:
* Azure Cosmos DB
* Azure Database for MySQL
* Azure-cache voor redis.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Kan ik permanente volumes weer geven, toevoegen of verplaatsen vanuit mijn toepassingen?

Ja.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Hoeveel uitgaande open bare IP-adressen heeft een Azure lente-Cloud instantie?

Het aantal uitgaande open bare IP-adressen kan variëren afhankelijk van de lagen en andere factoren. 

| Azure veer Cloud-exemplaar type | Standaard aantal uitgaande open bare IP-adressen |
| -------------------------------- | ---------------------------------------------- |
| Exemplaren van de Basic-laag             | 1                                              |
| Exemplaren van de Standard-laag          | 2                                              |
| VNet-injectie-instanties         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>Kan ik het aantal uitgaande open bare IP-adressen verhogen?

Ja, u kunt een [ondersteunings ticket](https://azure.microsoft.com/support/faq/)  openen om meer uitgaande open bare IP-adressen aan te vragen.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Wanneer ik een Azure lente-Cloud service-exemplaar Verwijder/Verplaats, worden ook de bijbehorende uitbreidings resources verwijderd/verplaatst?

Dit is afhankelijk van de logica van resource providers die eigenaar zijn van de uitbreidings resources. De extensie resources van een `Microsoft.AppPlatform` exemplaar horen niet bij dezelfde naam ruimte, waardoor het gedrag afhankelijk is van de resource provider. De bewerking verwijderen/verplaatsen getrapt bijvoorbeeld niet in de resources met **Diagnostische instellingen** . Als er een nieuw Azure-exemplaar voor de Cloud wordt ingericht met dezelfde resource-ID als de verwijderde, of als het vorige exemplaar van de Azure veer Cloud wordt teruggezet, worden de vorige bronnen voor **Diagnostische gegevens** nog verder uitgebreid.

U kunt de diagnostische instellingen van de lente-Cloud verwijderen met behulp van Azure CLI:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java runtime en besturingssysteem versies

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Welke versies van Java runtime worden ondersteund in azure lente-Cloud?

Azure lente Cloud ondersteunt Java LTS-versies met de meest recente builds, die momenteel 2020, Java 8 en Java 11 worden ondersteund. Zie [de JDK voor Azure en Azure stack installeren](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Wie heeft deze Java-Runtimes gemaakt?

Azul-systemen. De Azul Zulu for Azure - Enterprise Edition JDK-builds zijn een gratis, voor meerdere platforms geschikte en productieklare distributie van de OpenJDK voor Azure en Azure Stack ondersteund door Microsoft en Azul Systems. Ze bevatten alle onderdelen voor het maken en uitvoeren van Java SE-toepassingen.

### <a name="how-often-will-java-runtimes-get-updated"></a>Hoe vaak worden Java-Runtimes bijgewerkt?

LTS-en MTS JDK-releases hebben een driemaandelijkse beveiligings updates, probleem oplossingen en essentiële updates en patches voor buiten band, indien nodig. Deze ondersteuning omvat backports naar Java 7 en 8 van beveiligings updates en fout oplossingen die zijn gerapporteerd in nieuwere versies van Java, zoals Java 11.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Hoe lang worden de versies van Java 8 en Java 11 LTS ondersteund?

Zie [Java lange termijn ondersteuning voor Azure en Azure stack](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Java 8 LTS wordt ondersteund tot december 2030.
* Java 11 LTS wordt tot september 2027 ondersteund.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Hoe kan ik een ondersteunde Java-runtime voor lokale ontwikkeling downloaden?

Zie [de JDK voor Azure en Azure stack installeren](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Wat is het beleid voor het buiten gebruik stellen voor oudere Java-Runtimes?

De open bare kennisgeving wordt om de 12 maanden verzonden voordat een oude runtime versie buiten gebruik wordt gesteld. U moet 12 maanden naar een latere versie migreren.

* Abonnements beheerders ontvangen een e-mail melding wanneer we een Java-versie buiten gebruik worden gesteld.
* De informatie over buiten gebruik stellen wordt gepubliceerd in de documentatie.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Hoe kan ik ondersteuning krijgen voor problemen met het Java-runtime niveau?

U kunt een ondersteunings ticket openen met ondersteuning voor Azure.  Zie [een Azure-ondersteunings aanvraag maken](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Wat is het besturings systeem voor het uitvoeren van mijn apps?

De meest recente Ubuntu LTS-versie wordt gebruikt, momenteel [Ubuntu 20,04 LTS (brandpuntsafstand Fossa)](https://releases.ubuntu.com/focal/) is het standaard besturingssysteem.

### <a name="how-often-are-os-security-patches-applied"></a>Hoe vaak worden er beveiligings patches voor het besturings systeem toegepast?

Beveiligings patches die van toepassing zijn op Azure lente-Cloud, worden maandelijks geïmplementeerd op productie.
Essentiële beveiligings patches (CVE Score >= 9) die van toepassing zijn op een Azure lente-Cloud worden zo snel mogelijk uitgerold.
::: zone-end

## <a name="deployment"></a>Implementatie

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Biedt Azure lente Cloud ondersteuning voor blauw-groen-implementatie?
Ja. Zie [een faserings omgeving instellen](spring-cloud-howto-staging-environment.md)voor meer informatie.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>Kan ik Kubernetes gebruiken om mijn toepassings containers te bewerken?

Nee.  Azure lente-Cloud is een samen vatting van de ontwikkelaar van de onderliggende architectuur, zodat u zich kunt concentreren op toepassings code en bedrijfs logica.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Biedt Azure lente Cloud ondersteuning voor het bouwen van containers van de bron?

Ja. Zie [de lente Cloud toepassing vanuit de bron code starten](spring-cloud-quickstart.md)voor meer informatie.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Biedt Azure lente Cloud ondersteuning voor automatisch schalen in app-exemplaren?

Ja.  Zie [Setup automatisch schalen](spring-cloud-tutorial-setup-autoscale.md)voor meer informatie.

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Wat zijn de aanbevolen procedures voor het migreren van bestaande lente-Cloud micro Services naar Azure veer Cloud?

Wanneer u bestaande lente-Cloud micro Services migreert naar Azure lente-Cloud, is het een goed idee om de volgende best practices te bekijken:
* Alle toepassings afhankelijkheden moeten worden opgelost.
* Bereid uw configuratie vermeldingen, omgevings variabelen en JVM-para meters voor, zodat u ze kunt vergelijken met de implementatie in azure lente Cloud.
* Als u de service binding wilt gebruiken, gaat u door uw Azure-Services en zorgt u ervoor dat u de juiste toegangs machtigingen hebt ingesteld.
* U wordt aangeraden alle Inge sloten services te verwijderen of uit te scha kelen die mogelijk conflicteren met services die worden beheerd door Azure lente Cloud, zoals onze service discovery-service, configuratie server, enzovoort.
* U wordt aangeraden officiële, stabiele Pivot-veer bibliotheken te gebruiken. Niet-officiële, bèta-of gevorkeerde versies van Pivot-lente bibliotheken hebben geen SLA-ondersteuning (Service Level Agreement).

Controleer na de migratie de CPU/RAM-metrische gegevens en het netwerk verkeer om ervoor te zorgen dat de toepassings exemplaren op de juiste wijze worden geschaald.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core-versies

### <a name="which-net-core-versions-are-supported"></a>Welke .NET Core-versies worden ondersteund?

.NET Core 3,1 en hoger.

### <a name="how-long-will-net-core-31-be-supported"></a>Hoelang wordt .NET Core 3,1 ondersteund?

Tot 3 december 2022. Zie [.net core-ondersteunings beleid](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Problemen oplossen

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Wat zijn de gevolgen van het service register zelden niet beschikbaar?

In sommige gevallen is er zelden een fout opgetreden, zoals 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
vanuit uw logboeken van toepassingen. Dit probleem is geïntroduceerd in een lente-Framework met een zeer laag aantal als gevolg van onstabiele netwerken of andere netwerk problemen. 

Er mogen zich geen gevolgen voor de gebruikers ervaring voordoen, de Eureka-client beschikt over het beleid voor levens onderhoud en het opnieuw proberen. U kunt dit beschouwen als één tijdelijke fout en het veilig overs Laan.

We zullen dit onderdeel verbeteren en deze fout voor komen van toepassingen van gebruikers in korte toekomst.


## <a name="next-steps"></a>Volgende stappen

Als u meer vragen hebt, raadpleegt u de [Azure lente-probleemoplossings gids voor Clouds](spring-cloud-troubleshoot.md).
