---
title: Aanbevolen procedures voor het configureren van de apparaatconfiguratie voor Azure IoT Hub | Microsoft Docs
description: Meer informatie over best practices voor het gebruik van automatische Apparaatbeheer om herhaalde en complexe taken te minimaliseren die betrokken zijn bij het beheer van IoT-apparaten op schaal.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96024126"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Aanbevolen procedures voor het configureren van apparaten binnen een IoT-oplossing

Automatische Apparaatbeheer in azure IoT Hub automatiseert veel herhaalde en complexe taken voor het beheren van grote apparaat vloots in de hele levens cyclus. In dit artikel worden veel van de aanbevolen procedures gedefinieerd voor de verschillende rollen die betrokken zijn bij het ontwikkelen en gebruiken van een IoT-oplossing.

* **IOT-hardwarefabrikant/integrator:** Fabrikanten van IoT-hardware, integrators die hardware van verschillende fabrikanten assembleren, of leveranciers die hardware leveren voor een IoT-implementatie die is gefabriceerd of geïntegreerd door andere leveranciers. Betrokken bij de ontwikkeling en integratie van firmware, Inge sloten besturings systemen en Inge sloten software.

* **Ontwikkel aars van IOT-oplossingen:** De ontwikkeling van een IoT-oplossing wordt doorgaans uitgevoerd door een ontwikkelaar van oplossingen. Deze ontwikkelaar kan deel uitmaken van een intern team of een systeem integrator die is gespecialiseerd in deze activiteit. De ontwikkel aars van IoT-oplossingen kunnen verschillende onderdelen van de IoT-oplossing volledig ontwikkelen, verschillende standaard-of open source-onderdelen integreren of een [IOT-oplossings versneller](../iot-accelerators/index.yml)aanpassen.

* **IOT-oplossings operator:** Nadat de IoT-oplossing is geïmplementeerd, zijn langlopende bewerkingen, bewaking, upgrades en onderhoud vereist. Deze taken kunnen worden uitgevoerd door een intern team dat bestaat uit informatie technologie specialisten, hardware-bewerkingen en onderhouds teams, en domein specialisten die het juiste gedrag van de algemene IoT-infra structuur controleren.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Automatische Apparaatbeheer begrijpen voor het configureren van IoT-apparaten op schaal

Automatische Apparaatbeheer omvat de vele voor delen van [apparaatdubbels](iot-hub-devguide-device-twins.md) en [module apparaatdubbels](iot-hub-devguide-module-twins.md) om de gewenste en gerapporteerde statussen tussen de Cloud en apparaten te synchroniseren. Met [automatische hardwareconfiguraties](./iot-hub-automatic-device-management.md) worden grote sets van apparaatdubbels automatisch bijgewerkt en wordt de voortgang en naleving samenvatten. De volgende stappen op hoog niveau beschrijven hoe automatische Apparaatbeheer wordt ontwikkeld en gebruikt:

* De **IOT hardware fabrikant/integrator** implementeert functies voor Apparaatbeheer binnen een Inge sloten toepassing met behulp van [apparaatdubbels](iot-hub-devguide-device-twins.md). Deze functies kunnen firmware-updates, software-installatie en-update en instellingen beheer omvatten.

* De **Software** van de IOT-oplossing implementeert de Management-laag van beheer bewerkingen voor apparaten met behulp van [apparaatdubbels](iot-hub-devguide-device-twins.md) en [automatische configuraties](./iot-hub-automatic-device-management.md)van apparaten. De oplossing moet een operator interface definiëren voor het uitvoeren van beheer taken voor het apparaat.

* De **oplossings operator IOT** maakt gebruik van de IOT-oplossing voor het uitvoeren van beheer taken voor het apparaat, met name om apparaten te groeperen, configuratie wijzigingen te initiëren, zoals firmware-updates, de voortgang te controleren en problemen op te lossen die zich voordoen.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-hardwarefabrikant/integrator

Hieronder vindt u aanbevolen procedures voor hardwarefabrikanten en integrators die gebruikmaken van Inge sloten software ontwikkeling:

* **[Apparaatdubbels](iot-hub-devguide-device-twins.md)implementeren:** Met apparaatdubbels kan de gewenste configuratie vanuit de cloud worden gesynchroniseerd en voor de rapportage van de huidige configuratie en de apparaateigenschappen. De beste manier om apparaatdubbels binnen Inge sloten toepassingen te implementeren, is via de [Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks). Apparaatdubbels van apparaten zijn het meest geschikt voor configuratie omdat:

    * Ondersteuning voor bidirectionele communicatie.
    * De status van apparaten die zijn verbonden met een apparaat zonder verbinding.
    * Volg het principe van uiteindelijke consistentie.
    * Zijn volledig apparaatmetagegevens in de Cloud.

* **Het apparaat onderstructuur voor Apparaatbeheer:** Het dubbele apparaat moet zodanig zijn gestructureerd dat de eigenschappen van Apparaatbeheer in secties logisch zijn gegroepeerd. Hierdoor kunnen configuratie wijzigingen worden geïsoleerd zonder dat dit van invloed is op andere onderdelen van de dubbele. U kunt bijvoorbeeld een sectie maken binnen de gewenste eigenschappen voor de firmware, een andere sectie voor software en een derde sectie voor netwerk instellingen. 

* **Kenmerken van apparaten rapporteren die nuttig zijn voor Apparaatbeheer:** Kenmerken zoals het merk en model van fysieke apparaten, firmware, besturings systeem, serie nummer en andere id's zijn handig voor rapportage en als para meters voor het instellen van configuratie wijzigingen.

* **Definieer de belangrijkste statussen voor rapportage status en voortgang:** Statussen op het hoogste niveau moeten worden geïnventariseerd zodat ze aan de operator kunnen worden gerapporteerd. Zo rapporteert een firmware-update status als actueel, downloaden, Toep assen, in uitvoering en fout. Aanvullende velden definiëren voor meer informatie over elke status.

## <a name="iot-solution-developer"></a>Ontwikkel aars van IoT-oplossingen

Hier volgen de aanbevolen procedures voor ontwikkel aars van IoT-oplossingen die systemen maken op basis van Azure:

* **[Apparaatdubbels](iot-hub-devguide-device-twins.md)implementeren:** Met apparaatdubbels kan de gewenste configuratie vanuit de cloud worden gesynchroniseerd en voor de rapportage van de huidige configuratie en de apparaateigenschappen. De beste manier om apparaatdubbels in Cloud Solutions-toepassingen te implementeren, is via de [Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks). Apparaatdubbels van apparaten zijn het meest geschikt voor configuratie omdat:

    * Ondersteuning voor bidirectionele communicatie.
    * De status van apparaten die zijn verbonden met een apparaat zonder verbinding.
    * Volg het principe van uiteindelijke consistentie.
    * Zijn volledig apparaatmetagegevens in de Cloud.

* **Apparaten ordenen met Device-dubbele Tags:** De oplossing moet de operator toestaan kwaliteits ringen of andere sets apparaten te definiëren op basis van verschillende implementatie strategieën zoals Canarische. De organisatie van een apparaat kan worden geïmplementeerd in uw oplossing met behulp van dubbele Tags en [query's](iot-hub-devguide-query-language.md)van het apparaat. De inrichting van het apparaat is nood zakelijk om configuratie-uitrollen veilig en nauw keurig mogelijk te maken.

* **[Automatische configuraties van apparaten](./iot-hub-automatic-device-management.md)implementeren:** Met automatische apparaatconfiguratie worden configuratie wijzigingen in grote sets van IoT-apparaten geïmplementeerd en gecontroleerd via apparaat apparaatdubbels.

   Automatische configuraties doel sets van apparaatdubbels via de **doel voorwaarde,** een query op apparaatspecifieke Tags of gerapporteerde eigenschappen van het apparaat. De **doel inhoud** is de set gewenste eigenschappen die worden ingesteld binnen de apparaatdubbels van het doel apparaat. De doel inhoud moet worden uitgelijnd met de dubbele structuur van het apparaat dat is gedefinieerd door de IoT hardware fabrikant/integrator. De **metrische gegevens** zijn query's op dubbele gerapporteerde eigenschappen van het apparaat en moeten ook worden uitgelijnd met de dubbele structuur van het apparaat dat is gedefinieerd door de IOT hardware fabrikant/integrator.

   Automatische apparaatconfiguratie wordt voor het eerst uitgevoerd, kort nadat de configuratie is gemaakt en vervolgens met een interval van vijf minuten. Ze profiteren ook van de IoT Hub het uitvoeren van drijvende-verwerkings apparaten met een snelheid die nooit groter is dan de [beperkings limieten](iot-hub-devguide-quotas-throttling.md) voor dubbele Lees bewerkingen en updates van het apparaat.

* **De [Device Provisioning Service](../iot-dps/how-to-manage-enrollments.md)gebruiken:** ontwikkel aars van oplossingen moeten de Device Provisioning Service gebruiken voor het toewijzen van apparaatbesturing-Tags aan nieuwe apparaten, zodat ze automatisch worden geconfigureerd door **automatische configuraties van apparaten** die zijn gericht op apparaatdubbels met die tag. 

## <a name="iot-solution-operator"></a>IoT-oplossings operator

Hieronder vindt u aanbevolen procedures voor IoT-oplossings operators die gebruikmaken van een IoT-oplossing die is gebouwd op Azure:

* **Apparaten ordenen voor beheer:** De IoT-oplossing moet bepalen of toestaan dat kwaliteits ringen of andere sets apparaten worden gemaakt op basis van verschillende implementatie strategieën zoals Canarische. De sets apparaten worden gebruikt voor het implementeren van configuratie wijzigingen en voor het uitvoeren van andere beheer bewerkingen voor Apparaatbeheer.

* **Configuratie wijzigingen uitvoeren met behulp van een gefaseerde implementatie:**  Een gefaseerde implementatie is een algemeen proces waarbij een operator wijzigingen implementeert in een uitgebreidere set IoT-apparaten. Het doel is om wijzigingen geleidelijk aan te brengen om het risico te verkleinen dat er grote veranderingen in de omvang ontstaan.  De operator moet de interface van de oplossing gebruiken voor het maken van een [automatische apparaatconfiguratie](./iot-hub-automatic-device-management.md) en de doel voorwaarde moet gericht zijn op een eerste set apparaten (zoals een Canarische groep). De operator moet vervolgens de configuratie wijziging valideren in de eerste set apparaten.

   Zodra de validatie is voltooid, wordt de automatische apparaatconfiguratie door de operator bijgewerkt zodat deze een grotere set apparaten bevat. De operator moet de prioriteit voor de configuratie ook instellen op hoger dan andere configuraties die momenteel zijn gericht op die apparaten. De implementatie kan worden bewaakt met behulp van de metrische gegevens die worden gerapporteerd door de automatische apparaatconfiguratie.

* **Terugdraai acties uitvoeren in geval van fouten of onjuiste configuraties:**  Een automatische apparaatconfiguratie die fouten of onjuiste configuraties veroorzaakt, kan worden teruggedraaid door de **doel voorwaarde** te wijzigen zodat de apparaten niet langer voldoen aan de doel voorwaarde. Zorg ervoor dat een andere automatische apparaatconfiguratie met een lagere prioriteit nog steeds is gericht op deze apparaten. Controleer of het terugdraaien is geslaagd door de metrische gegevens weer te geven: de gerollte back-upconfiguratie moet niet langer de status voor niet-doel apparaten weer geven en de metrische gegevens van de tweede configuratie moeten nu tellingen bevatten voor de apparaten die nog steeds zijn gericht.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van apparaatdubbels in de [apparaatdubbels in IOT hub](iot-hub-devguide-device-twins.md).

* Door loop de stappen om een automatische apparaatconfiguratie te maken, bij te werken of te verwijderen in [IOT-apparaten configureren en controleren op schaal](./iot-hub-automatic-device-management.md).

* Een firmware-update patroon implementeren met behulp van apparaat apparaatdubbels en automatische apparaatconfiguratie in [zelf studie: een update proces voor een firmware van een apparaat implementeren](tutorial-firmware-update.md).