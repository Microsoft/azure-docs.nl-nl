---
title: Java-en basis besturingssysteem voor Azure lente Cloud micro service-apps
description: Principes voor het onderhouden van een gezonde Java-en base-besturings systeem voor Azure lente Cloud micro service-apps
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c90062f1968cc7be5a742a67363f57b9632fdfa
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878125"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java- en Base-besturingssysteem voor Spring Microservice-apps

**Dit artikel is van toepassing op:** ✔️ Java

Hieronder vindt u de principes voor het onderhouden van een gezonde Java-en basis besturingssysteem voor lente-micro service-apps.
## <a name="principles-for-healthy-java-and-base-os"></a>Principes voor een gezonde Java-en basis besturingssysteem
* Is hetzelfde basis besturingssysteem voor alle lagen-basis | Standaard | Ultieme.
    * Momenteel gebruiken apps in azure lente-Cloud een combi natie van Debian 10 en Ubuntu 18,04.
    * VMware build service maakt gebruik van Ubuntu 18,04.
* Moeten hetzelfde basis besturingssysteem zijn, ongeacht de begin punten van de implementatie-bron | JAR
    * Momenteel gebruiken apps in azure lente-Cloud een combi natie van Debian 10 en Ubuntu 18,04.
* Het basis besturingssysteem is vrij van beveiligings problemen.
    * Debian 10 base Operating System heeft 147 open CVEs.
    * Ubuntu 18,04-basis besturings systeem heeft 132 open CVEs.
* Moet JRE-headless gebruiken.
    * Momenteel gebruiken apps in azure lente Cloud JDK. JRE-headless is een kleinere afbeelding.
* Maakt gebruik van de meest recente versies van Java.
    * Momenteel gebruiken apps in azure lente-Cloud Java 8 build 242. Dit is een verouderde build.
 
Azul-systemen scannen voortdurend op wijzigingen in de basis besturingssystemen en houden de laatste gebouwde installatie kopieën up-to-date. Azure lente-Cloud zoekt naar wijzigingen in installatie kopieën en werkt deze voortdurend bij in implementaties.
 
## <a name="faq-for-azure-spring-cloud"></a>Veelgestelde vragen over Azure Spring Cloud

* Welke versies van Java worden ondersteund? Primaire versie en buildnummer.
    * Ondersteuning voor LTS-versies-Java 8 en 11.
    * Maakt gebruik van de meest recente build: u kunt nu bijvoorbeeld het volgende doen: Java 8 build 252 en Java 11 build 7.
* Wie heeft deze Java-Runtimes gemaakt?
    * Azul-systemen.
* Wat is het basis besturingssysteem voor installatie kopieën?
    * Ubuntu 20,04 LTS (brand Fossa). Apps blijven blijven werken met de meest recente LTS-versie van Ubuntu.
    * Zie [Ubuntu 20,04 LTS (brandpuntsafstand Fossa)](http://releases.ubuntu.com/focal/)
* Hoe kan ik een ondersteunde Java-runtime voor lokale ontwikkel aars downloaden? 
    * Zie [de JDK voor Azure en Azure stack installeren](/azure/developer/java/fundamentals/java-jdk-install)
* Hoe kan ik ondersteuning krijgen voor problemen met het Java-runtime niveau?
    * Open een ondersteunings ticket met ondersteuning voor Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Standaard implementatie in azure lente-Cloud

> ![Standaard implementatie](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)
* [Java-ondersteuning op lange termijn voor Azure en Azure Stack](/azure/developer/java/fundamentals/java-jdk-long-term-support)