---
title: Overzicht van Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: In dit artikel wordt beschreven hoe u in Azure apparaten inricht met Device Provisioning Service (DPS) en IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 6b33b866a10ad4a44cef14f3c86d8ca1f40c4750
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965371"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Apparaten inrichten met Azure IoT Hub Device Provisioning Service
Microsoft Azure biedt een uitgebreide set geïntegreerde openbare cloudservices om tegemoet te komen aan al uw IoT-vragen. IoT Hub Device Provisioning Service (DPS) is een helper-service die zero-touch mogelijk maakt, het Just-In-Time inrichten naar de juiste IoT Hub zonder tussenkomst van de gebruiker. Met DPS kunnen miljoenen apparaten op een veilige en schaalbare manier worden ingericht.

## <a name="when-to-use-device-provisioning-service"></a>Wanneer Device Provisioning Service gebruiken
Er zijn veel scenario's waarin DPS een uitstekende keuze is voor het verbinden en configureren van apparaten met IoT Hub, zoals:

* Apparaat zonder tussenkomst van gebruiker inrichten voor een afzonderlijke IoT-oplossing zonder dat hiervoor in de fabriek (eerste installatie) hardcoding van verbindingsgegevens voor IoT-Hub nodig is
* Taakverdeling van apparaten over meerdere hubs
* Apparaten verbinden met de IoT-oplossing van de eigenaar op basis van verkooptransactiegegevens (multitenancy)
* Apparaten verbinden met een bepaalde IoT-oplossing, afhankelijk van het gebruiksscenario (isolatie van oplossing)
* Apparaat met de laagst mogelijke latentie (geo-sharding) verbinden met de IoT-hub
* Herinrichten op basis van een wijziging op het apparaat
* Aanpassen van de sleutels die door het apparaat worden gebruikt om verbinding te maken met IoT Hub (als er geen verbinding wordt gemaakt met behulp van X.509-certificaten)

## <a name="behind-the-scenes"></a>Achter de schermen
Alle scenario's die hierboven worden vermeld, kunnen met dezelfde werkstroom en zonder tussenkomst van de gebruiker worden uitgevoerd met behulp van DPS. Veel van de handmatige stappen die gebruikelijk zijn bij het inrichten van apparaten zijn geautomatiseerd met DPS om zo het implementeren van IoT-apparaten te versnellen en de kans op handmatige fouten te verkleinen. In de volgende sectie wordt beschreven wat er achter de schermen gebeurt om een apparaat in te richten. De eerste stap is handmatig, alle overige gebeuren automatisch.

![Basiswerkstroom voor inrichting van apparaat](./media/about-iot-dps/dps-provisioning-flow.png)

1. De apparaatfabrikant voegt de registratiegegevens van het apparaat toe aan de lijst met registraties in Azure Portal.
2. Het apparaat neemt contact op met het DPS-eindpunt dat in de fabriek is ingesteld. Het apparaat geeft de identificatiegegevens door aan DPS om de identiteit aan te tonen.
3. DPS valideert de identiteit van het apparaat door de registratie-id en -sleutel te vergelijken met de vermelding in de lijst met registraties. Dit gebeurt met een nonce-challenge ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) of standaard x.509-verificatie (X.509).
4. DPS registreert het apparaat bij een IoT-hub en vult de [gewenste dubbelstatus](../iot-hub/iot-hub-devguide-device-twins.md) van het apparaat in.
5. De IoT-hub retourneert de gegevens van de apparaat-id naar DPS.
6. DPS retourneert de verbindingsgegevens van de IoT-hub naar het apparaat. Het apparaat kan nu rechtstreeks gegevens gaan verzenden naar de IoT-hub.
7. Het apparaat maakt verbinding met IoT Hub.
8. Het apparaat krijgt de gewenste status van de apparaatdubbel in IoT Hub.

## <a name="provisioning-process"></a>Inrichtingsproces
Er zijn twee verschillende stappen in het implementatieproces van een apparaat waarbij DPS een rol speelt en die onafhankelijk kunnen worden uitgevoerd:

* De **productiestap** waarin het apparaat wordt gemaakt en voorbereid in de fabriek, en
* De **configuratiestap voor de cloud** waarin Device Provisioning Service wordt geconfigureerd voor automatisch inrichten.

Beide stappen sluiten naadloos aan op bestaande productie- en implementatieprocessen. DPS vereenvoudigt zelfs enkele implementatieprocessen die handmatig werk vereisen om verbindingsgegevens op het apparaat te krijgen.

### <a name="manufacturing-step"></a>Productiestap
Deze stap omvat alles wat te maken heeft met de productielijn. Voorbeelden van rollen die zijn betrokken bij deze stap zijn siliciumontwerper, siliciumfabrikant, integrator en/of de eindfabrikant van het apparaat. Deze stap gaat om het maken van de hardware zelf.

DPS introduceert geen nieuwe stap in het productieproces. De service wordt geïntegreerd in de bestaande stap waarmee de eerste versie van de software en (in het ideale geval) de HSM op het apparaat worden geïnstalleerd. In plaats van een apparaat-id te maken in deze stap, wordt het apparaat geprogrammeerd met de gegevens van de inrichtingsservice, waardoor het apparaat bij inschakeling de service kan aanroepen om de verbindingsgegevens/toewijzing van de IoT-oplossing op te vragen.

In deze stap verstrekt de fabrikant ook belangrijke identificatiegegevens aan de persoon die het apparaat implementeert/gebruikt (operator). Het verstrekken van die gegevens kan zo eenvoudig zijn als het bevestigen dat alle apparaten beschikken over een X.509-certificaat dat is gegenereerd op basis van een ondertekend certificaat dat is geleverd door de persoon die het apparaat implementeert/gebruikt, of zo ingewikkeld als het extraheren van het openbare deel van een TPM-goedkeuringssleutel van elk TPM-apparaat. Deze services worden vandaag de dag aangeboden door verschillende fabrikanten van silicium.

### <a name="cloud-setup-step"></a>Configuratiestap voor de cloud
Deze stap betreft het configureren van de cloud voor een juiste automatische inrichting. In het algemeen er zijn twee typen gebruikers betrokken bij deze stap: iemand die weet hoe apparaten in eerste instantie moeten worden geconfigureerd (een operator van apparaten) en iemand anders die weet hoe apparaten moeten worden verdeeld tussen de IoT-hubs (een operator van oplossingen).

De inrichting moet in eerste instantie eenmalig worden geconfigureerd, wat meestal wordt afgehandeld door de operator van de oplossing. Zodra de inrichtingsservice is geconfigureerd, hoeft deze niet meer te worden gewijzigd, tenzij het gebruiksscenario verandert.

Nadat de service is geconfigureerd voor automatische inrichting, moet de service worden voorbereid voor de registratie van apparaten. Deze stap wordt uitgevoerd door de operator van het apparaat, die weet wat de gewenste configuratie is van het apparaat of de apparaten en die ervoor moet zorgen dat de inrichtingsservice de identiteit van het apparaat goed kan bevestigen wanneer het apparaat op zoek gaat naar de juiste IoT-hub. De operator van het apparaat neemt de belangrijkste identificatiegegevens van de fabrikant en voegt deze toe aan de lijst met registraties. De lijst met registraties kan nog worden gewijzigd als er nieuwe items worden toegevoegd of bestaande vermeldingen worden bijgewerkt met de meest recente informatie over de apparaten.

## <a name="registration-and-provisioning"></a>Registratie en inrichting
*Inrichting* kan verschillende dingen betekenen, afhankelijk van de branche waarin de term wordt gebruikt. In de context van het inrichten van IoT-apparaten bij hun cloudoplossing bestaat het proces uit twee delen:

1. Het eerste deel omvat het tot stand brengen van de eerste verbinding tussen het apparaat en de IoT-oplossing door het apparaat te registreren.
2. Het tweede gedeelte bestaat uit het toepassen van de juiste configuratie op het apparaat op basis van de specifieke vereisten van de oplossing waarbij het apparaat is geregistreerd.

Als beide stappen zijn voltooid, kunnen we zeggen dat het apparaat volledig is ingericht. Sommige cloudservices bieden alleen de eerste stap van het inrichtingsproces, het registreren van apparaten bij het eindpunt van de IoT-oplossing, maar niet de eerste configuratie. DPS automatiseert beide stappen en biedt zo een naadloze ervaring voor de inrichting van het apparaat.

## <a name="features-of-the-device-provisioning-service"></a>Functies van Device Provisioning Service
DPS bevat allerlei functies, waardoor de service ideaal is voor het inrichten van apparaten.

* Ondersteuning voor **beveiligde attestation** voor identiteiten op basis van zowel X.509 als TPM.
* **Lijst van registraties** met een volledig overzicht van de apparaten/groepen apparaten die op enige moment kunnen worden geregistreerd. De registratielijst bevat informatie over de gewenste configuratie van het apparaat als dit wordt geregistreerd en de lijst kan op elk gewenst moment worden bijgewerkt.
* **Meerdere beleidsregels voor toewijzing** om te bepalen hoe apparaten door DPS aan IoT-hubs worden toegewezen ter ondersteuning van uw scenario's: Laagste latentie, gelijkmatig gewogen distributie (standaard) en statische configuratie via de registratielijst. De latentie wordt bepaald met dezelfde methode als voor [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#performance).
* **Controle en logboekregistratie van diagnostische gegevens** om ervoor te zorgen dat alles goed werkt.
* **Ondersteuning voor meerdere hubs** zorgt ervoor dat DPS apparaten kan toewijzen aan meer dan één IoT-hub. DPS kan communiceren met hubs binnen verschillende Azure-abonnementen.
* **Ondersteuning voor meerdere regio's** zorgt ervoor dat DPS apparaten kan toewijzen aan IoT-hubs in andere regio's.
* **Versleuteling voor data-at-rest** zorgt ervoor dat gegevens in DPS worden versleuteld en ontsleuteld met 256-bits AES-versleuteling, een zeer krachtige blokversleuteling die voldoet aan FIPS 140-2.


Meer informatie over de concepten en functies die bij het inrichten van apparaten betrokken zijn, kunt u vinden in het onderwerp [DPS-terminologie](concepts-service.md). In dezelfde sectie vindt u ook de andere conceptuele onderwerpen.

## <a name="cross-platform-support"></a>Ondersteuning voor meerdere platformen
DPS werkt, net als alle andere Azure IoT-services, platformoverschrijdende met tal van besturingssystemen. Azure biedt open source-SDK's in een veelheid aan [talen](https://github.com/Azure/azure-iot-sdks) ter bevordering van het verbinden van apparaten en het beheren van de service. DPS ondersteunt de volgende protocollen voor het verbinden van apparaten:

* HTTPS
* AMQP
* AMQP via WebSockets
* MQTT
* MQTT via WebSockets

DPS ondersteunt alleen HTTPS-verbindingen voor servicebewerkingen.

## <a name="regions"></a>Regio's
DPS is in diverse regio's beschikbaar. De bijgewerkte lijst met bestaande en nieuwe regio's voor alle services wordt aangekondigd op [Azure-regio's](https://azure.microsoft.com/regions/). U kunt de beschikbaarheid van Device Provisioning Service controleren op de pagina [Status van Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> DPS is globaal en niet gebonden aan een locatie. U moet echter een regio opgeven waarin de metagegevens worden opgeslagen die zijn gekoppeld aan uw DPS-profiel.

## <a name="availability"></a>Beschikbaarheid
Er wordt een Service Level Agreement van 99,9% voor DPS gehanteerd. U kunt [de SLA hier lezen](https://azure.microsoft.com/support/legal/sla/iot-hub/). In de volledige [Azure SLA](https://azure.microsoft.com/support/legal/sla/) wordt de gegarandeerde beschikbaarheid van Azure als geheel uitgelegd.

## <a name="quotas"></a>Quota
Voor elk Azure-abonnement gelden standaardquotalimieten. Deze limieten kunnen invloed hebben op het bereik van uw IoT-oplossing. De huidige limiet voor een abonnementsvariant is 10 Device Provisioning Services per abonnement.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Meer informatie over quotalimieten vindt u hier:
* [Azure-abonnement en servicelimieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Gerelateerde Azure-onderdelen
Met DPS kunnen apparaten met behulp van Azure IoT Hub automatisch worden ingericht. Lees hier meer informatie over [IoT Hub](../iot-hub/index.yml).

## <a name="next-steps"></a>Volgende stappen
U hebt nu een algemeen beeld van het inrichten van IoT-apparaten in Azure. De volgende stap is het uitproberen van een compleet IoT-scenario.

[IoT Hub Device Provisioning Service instellen met Azure Portal](quick-setup-auto-provision.md)

[Een gesimuleerd apparaat maken en inrichten](quick-create-simulated-device.md)

[Apparaat instellen voor inrichting](tutorial-set-up-device.md)