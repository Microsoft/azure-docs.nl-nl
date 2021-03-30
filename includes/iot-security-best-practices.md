---
title: bestand opnemen
description: bestand opnemen
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "72793139"
---
Voor het beveiligen van een Internet of Things IoT-infra structuur is een strenge, diep gaande strategie vereist. Deze strategie vereist dat u gegevens in de Cloud beveiligt, gegevens integriteit beschermt tijdens de overdracht via het open bare Internet en apparaten veilig inricht. Elke laag bouwt over hogere beveiligings garanties in de algehele infra structuur.

## <a name="secure-an-iot-infrastructure"></a>Een IoT-infra structuur beveiligen

Deze diep gaande strategie voor beveiliging kan worden ontwikkeld en uitgevoerd met actieve deelname van verschillende spelers die betrokken zijn bij de productie, ontwikkeling en implementatie van IoT-apparaten en-infra structuur. Hier volgt een beschrijving van deze spelers op hoog niveau.

* **IOT hardware fabrikant/integrator**: deze spelers zijn doorgaans de fabrikanten van IOT-hardware die wordt geïmplementeerd, integrators die hardware van verschillende fabrikanten assembleren, of leveranciers die hardware bieden voor een IOT-implementatie die is gefabriceerd of geïntegreerd door andere leveranciers.

* **IOT Solution Developer**: de ontwikkeling van een IOT-oplossing wordt doorgaans uitgevoerd door een oplossings ontwikkelaar. Deze ontwikkelaar kan deel uitmaken van een intern team of een System Integrator (SI) die is gespecialiseerd in deze activiteit. De ontwikkel aars van IoT-oplossingen kunnen verschillende onderdelen van de IoT-oplossing ontwikkelen, verschillende niet-beproefde of open-source onderdelen integreren, of oplossings Accelerators met een kleine aanpassing.

* **IOT-oplossings implementatie**: nadat een IOT-oplossing is ontwikkeld, moet deze in het veld worden geïmplementeerd. Dit proces omvat het implementeren van hardware, interconnectie van apparaten en de implementatie van oplossingen op hardwareapparaten of in de Cloud.

* **IOT-oplossings operator**: nadat de IOT-oplossing is geïmplementeerd, zijn langlopende bewerkingen, bewaking, upgrades en onderhoud vereist. Deze taken kunnen worden uitgevoerd door een intern team dat informatie technologie specialisten, hardware-bewerkingen en onderhouds teams bevat, en domein specialisten die het juiste gedrag van de algehele IoT-infra structuur controleren.

De volgende secties bieden aanbevolen procedures voor elk van deze spelers om een veilige IoT-infra structuur te ontwikkelen, implementeren en gebruiken.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-hardwarefabrikant/integrator

Hier volgen de aanbevolen procedures voor IoT-hardwarefabrikanten en hardware integrators.

* **Hardware beperken tot minimale vereisten**: het hardware-ontwerp moet de minimale functies bevatten die nodig zijn voor de werking van de hardware en niets meer. Een voor beeld is het zo dat USB-poorten alleen worden toegevoegd als dit nodig is voor de werking van het apparaat. Met deze aanvullende functies opent u het apparaat voor ongewenste aanvals vectoren die moeten worden vermeden.

* **Laat de hardware geknoeiend testen**: ontwikkel mechanismen voor het detecteren van fysieke knoeien, zoals het openen van het apparaat of het verwijderen van een deel van het apparaat. Deze knoei signalen kunnen deel uitmaken van de gegevens stroom die is geüpload naar de Cloud, waardoor Opera tors van deze gebeurtenissen kunnen worden gewaarschuwd.

* **Bouwen rond beveiligde hardware**: als KPV toestemming geeft, bouwt u beveiligings functies zoals beveiligde en versleutelde opslag of opstart functionaliteit op basis van trusted platform module (TPM). Deze functies zorgen ervoor dat apparaten beter worden beveiligd en de algehele IoT-infra structuur helpen beveiligen.

* **Maak upgrades veilig**: firmware-upgrades tijdens de levens duur van het apparaat zijn onvermijdelijk. Door apparaten te bouwen met beveiligde paden voor upgrades en de cryptografische garantie van firmware versies, kan het apparaat veilig zijn tijdens en na de upgrade.

## <a name="iot-solution-developer"></a>Ontwikkel aars van IoT-oplossingen

Hier volgen de aanbevolen procedures voor de ontwikkel aars van IoT-oplossingen:

* **Volg de veilige methodologie** voor het ontwikkelen van software: het ontwikkelen van beveiligde software vereist dat u op de hoogte bent van de beveiliging, van het begin van het project tot de implementatie, testen en implementatie. De keuze van platformen, talen en hulpprogram ma's is van invloed op deze methodologie. De levens cyclus van micro soft voor beveiligings ontwikkeling biedt een stapsgewijze benadering van het bouwen van beveiligde software.

* **Kies open source software with Care**: open source software biedt u de mogelijkheid om snel oplossingen te ontwikkelen. Houd bij het kiezen van open-source software rekening met het activiteiten niveau van de community voor elk open source-onderdeel. Een actieve community zorgt ervoor dat de software wordt ondersteund en dat er problemen worden gedetecteerd en opgelost. Het is ook mogelijk dat een onzichtbaar en inactief open-source software project niet wordt ondersteund en dat er waarschijnlijk geen problemen worden gedetecteerd.

* **Integreer met zorg**: veel software beveiligings fouten bestaan op de grens van bibliotheken en api's. De functionaliteit die mogelijk niet vereist is voor de huidige implementatie, kan nog steeds beschikbaar zijn via een API-laag. Zorg ervoor dat u alle interfaces van onderdelen die zijn geïntegreerd voor beveiligings fouten controleert om te zorgen voor een volledige beveiliging.

## <a name="iot-solution-deployer"></a>IoT-oplossings implementatie

Hieronder volgen de aanbevolen procedures voor de implementaties van IoT-oplossingen:

* **Hardware veilig implementeren**: voor IOT-implementaties is het vereist dat hardware moet worden geïmplementeerd op onveilige locaties, zoals in open bare ruimten of land instellingen zonder toezicht. In dergelijke situaties moet u ervoor zorgen dat de implementatie van de hardware onrecht matig wordt geknoeid. Als er USB-of andere poorten beschikbaar zijn op de hardware, moet u ervoor zorgen dat ze veilig worden gedekt. Veel aanvals vectoren kunnen deze gebruiken als ingangs punten.

* **Verificatie sleutels veilig houden**: tijdens de implementatie moeten voor elk apparaat apparaat-id's en bijbehorende verificatie sleutels worden gegenereerd door de Cloud service. Bewaar deze sleutels fysiek veilig, zelfs na de implementatie. Alle versleutelde sleutels kunnen worden gebruikt door een schadelijk apparaat om als een bestaand apparaat te maskeren.

## <a name="iot-solution-operator"></a>IoT-oplossings operator

Hier volgen de aanbevolen procedures voor IoT-oplossings operators:

* **Het systeem up-to-date houden**: Zorg ervoor dat de besturings systemen van het apparaat en alle apparaatstuurprogramma's worden bijgewerkt naar de nieuwste versie. Als u automatische updates in Windows 10 (IoT of andere Sku's) inschakelt, houdt micro soft deze up-to-date, waarmee een veilig besturings systeem wordt geboden voor IoT-apparaten. Door andere besturings systemen (zoals Linux) up-to-date te houden, zorgt u ervoor dat ze ook worden beschermd tegen schadelijke aanvallen.

* **Beveiligen tegen schadelijke activiteiten**: als het besturings systeem toestaat, installeert u de nieuwste anti virus-en antimalware-mogelijkheden op elk besturings systeem van het apparaat. Met deze procedure kunt u de meeste externe dreigingen oplossen. U kunt de meeste moderne besturings systemen tegen bedreigingen beveiligen door de volgende stappen uit te voeren.

* **Regel matig controleren**: de IOT-infra structuur voor beveiligings problemen controleren is belang rijk bij het reageren op beveiligings incidenten. De meeste besturings systemen bieden ingebouwde gebeurtenis logboek registratie die regel matig moet worden gecontroleerd om ervoor te zorgen dat er geen inbreuk op de beveiliging heeft plaatsgevonden. Controle gegevens kunnen worden verzonden als een afzonderlijke telemetrische stroom naar de Cloud service waar ze kunnen worden geanalyseerd.

* **De IOT-infra structuur fysiek beschermen**: de ergste beveiligings aanvallen op IOT-infra structuur worden gestart met fysieke toegang tot apparaten. Een belang rijke veiligheids procedure is om te beschermen tegen kwaad aardig gebruik van USB-poorten en andere fysieke toegang. Een sleutel voor het opsporen van inbreuken die zich kunnen voordoen, is het vastleggen van fysieke toegang, zoals het gebruik van USB-poort. Opnieuw, Windows 10 (IoT en andere Sku's) biedt gedetailleerde logboek registratie van deze gebeurtenissen.

* **Cloud referenties beveiligen**: Cloud authenticatie referenties die worden gebruikt voor het configureren en uitvoeren van een IOT-implementatie, zijn mogelijk de eenvoudigste manier om toegang te krijgen tot een IOT-systeem. Beveilig de referenties door het wacht woord regel matig te wijzigen en het gebruik van deze referenties op open bare computers te onthouden.

De mogelijkheden van verschillende IoT-apparaten variëren. Sommige apparaten zijn mogelijk computers waarop common desktop besturingssystemen worden uitgevoerd, en op sommige apparaten wordt mogelijk een zeer licht gewicht besturings systeem uitgevoerd. De aanbevolen beveiligings procedures die eerder zijn beschreven, zijn mogelijk op verschillende niveaus van toepassing op deze apparaten. Indien dit wordt gegeven, moeten aanvullende aanbevolen procedures voor beveiliging en implementatie van de fabrikant van deze apparaten worden gevolgd.

Sommige verouderde en beperkte apparaten zijn mogelijk niet specifiek ontworpen voor IoT-implementatie. Deze apparaten hebben mogelijk niet de mogelijkheid om gegevens te versleutelen, verbinding te maken met internet of geavanceerde controle mogelijkheden te bieden. In dergelijke gevallen kan een moderne en beveiligde veld Gateway gegevens verzamelen van verouderde apparaten en de beveiliging bieden die nodig is om deze apparaten via internet te verbinden. Veld gateways kunnen beveiligde authenticatie, onderhandeling over versleutelde sessies, de ontvangst van opdrachten uit de Cloud en vele andere beveiligings functies bieden.