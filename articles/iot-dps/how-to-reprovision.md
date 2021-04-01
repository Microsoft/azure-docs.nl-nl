---
title: Apparaten opnieuw inrichten in azure IoT Hub Device Provisioning Service
description: Meer informatie over het opnieuw inrichten van apparaten met het DPS-exemplaar (Device Provisioning Service) en waarom u dit mogelijk moet doen.
author: wesmc7777
ms.author: wesmc
ms.date: 01/25/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d704e8f9687f3987d80018d84b41c0fd519da172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791893"
---
# <a name="how-to-reprovision-devices"></a>Apparaten opnieuw inrichten

Tijdens de levens cyclus van een IoT-oplossing is het gebruikelijk om apparaten te verplaatsen tussen IoT-hubs. In dit onderwerp vindt u hulp bij het configureren van beleids regels voor het opnieuw inrichten van oplossingen.

Zie IoT Hub voor het opnieuw inrichten van [apparaten](concepts-device-reprovision.md)voor meer gedetailleerde informatie over het opnieuw inrichten van scenario's.


## <a name="configure-the-enrollment-allocation-policy"></a>Het toewijzings beleid voor inschrijving configureren

Het toewijzings beleid bepaalt hoe de apparaten die aan de inschrijving zijn gekoppeld, worden toegewezen aan een IoT-hub zodra deze opnieuw is ingericht.

Met de volgende stappen configureert u het toewijzings beleid voor de inschrijving van een apparaat:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Device Provisioning service-exemplaar.

2. Klik op **inschrijvingen beheren** en klik op de registratie groep of de individuele inschrijving die u wilt configureren voor opnieuw inrichten. 

3. Selecteer onder **Selecteer hoe u apparaten aan hubs wilt toewijzen**, een van de volgende toewijzings beleidsregels:

    * **Laagste latentie**: met dit beleid worden apparaten toegewezen aan de gekoppelde IOT hub die leiden tot de laagste latentie communicatie tussen het apparaat en IOT hub. Met deze optie kan het apparaat communiceren met de dichtstbijzijnde IoT-hub op basis van locatie. 
    
    * **Evenredig gewogen distributie**: dit beleid distribueert apparaten over de gekoppelde IOT-hubs op basis van het toewijzings gewicht dat is toegewezen aan elke gekoppelde IOT-hub. Met dit beleid kunt u taken verdelen over een groep gekoppelde hubs op basis van de toewijzings gewichten die zijn ingesteld op die hubs. Als u apparaten op slechts één IoT Hub inrichten, raden we u aan deze instelling in te stellen. Dit is de standaardinstelling. 
    
    * **Statische configuratie**: dit beleid vereist dat een gewenste IOT hub worden weer gegeven in de inschrijvings vermelding voor een apparaat dat moet worden ingericht. Met dit beleid kunt u een enkele specifieke IoT-hub aanwijzen waaraan u apparaten wilt toewijzen.

4. Onder **Selecteer de IOT-hubs waaraan deze groep kan worden toegewezen**, selecteert u de gekoppelde IOT-hubs die u wilt opnemen in uw toewijzings beleid. Voeg eventueel een nieuwe gekoppelde IOT-hub toe met behulp van de knop **een nieuwe IOT hub koppelen** .

    Met het **laagste latentie** toewijzings beleid worden de hubs die u selecteert in de latentie-evaluatie opgenomen om de dichtstbijzijnde hub voor apparaattoewijzing te bepalen.

    Met het **evenredige gewogen distributie** toewijzings beleid worden apparaten gelijkmatig verdeeld over de hubs die u selecteert op basis van hun geconfigureerde toewijzings gewichten en de huidige belasting van het apparaat.

    Selecteer met het **statische configuratie** toewijzings beleid de IOT-hub waaraan apparaten moeten worden toegewezen.

4. Klik op **Opslaan** of ga door naar de volgende sectie om het beleid voor opnieuw inrichten in te stellen.

    ![Toewijzings beleid voor inschrijving selecteren](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>Het beleid voor opnieuw inrichten instellen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Device Provisioning service-exemplaar.

2. Klik op **inschrijvingen beheren** en klik op de registratie groep of de individuele inschrijving die u wilt configureren voor opnieuw inrichten.

3. Onder **Selecteer hoe u wilt dat apparaatgegevens worden verwerkt bij het opnieuw inrichten naar een andere IOT-hub**, kiest u een van de volgende beleids regels voor opnieuw inrichten:

    * **Gegevens opnieuw inrichten en migreren**: dit beleid onderneemt actie wanneer apparaten die zijn gekoppeld aan de inschrijvings vermelding een nieuwe inrichtings aanvraag indienen. Afhankelijk van de configuratie van de registratie vermelding, kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT hubs wijzigt, wordt de registratie van het apparaat met de eerste IoT-hub verwijderd. Alle status gegevens van de apparaten van die eerste IoT-hub worden gemigreerd naar de nieuwe IoT hub. Tijdens de migratie wordt de status van het apparaat gerapporteerd als **toewijzing**

    * **Opnieuw inrichten en opnieuw instellen op de eerste configuratie**: dit beleid voert een actie uit wanneer de apparaten die zijn gekoppeld aan de inschrijvings vermelding een nieuwe inrichtings aanvraag indienen. Afhankelijk van de configuratie van de registratie vermelding, kan het apparaat opnieuw worden toegewezen aan een andere IoT-hub. Als het apparaat IoT hubs wijzigt, wordt de registratie van het apparaat met de eerste IoT-hub verwijderd. De initiële configuratie gegevens waarvan het exemplaar van de inrichtings service dat is ontvangen toen het apparaat werd ingericht, worden geleverd aan de nieuwe IoT hub. Tijdens de migratie wordt de status van het apparaat gerapporteerd als **toewijzing**.

4. Klik op **Opslaan** om het opnieuw inrichten van het apparaat in te scha kelen op basis van uw wijzigingen.

    ![Scherm opname van de wijzigingen die u hebt aangebracht en de knop Opslaan.](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>Een inrichtings aanvraag verzenden vanaf het apparaat

Als u wilt dat apparaten opnieuw worden ingericht op basis van de configuratie wijzigingen die in de voor gaande secties zijn aangebracht, moeten deze apparaten opnieuw worden ingericht. 

Hoe vaak een apparaat een inrichtings aanvraag indient, is afhankelijk van het scenario. Het wordt echter aanbevolen om uw apparaten te Program meren om een inrichtings aanvraag te verzenden naar een inrichtings service-exemplaar bij het opnieuw opstarten, en biedt ondersteuning voor een [methode](../iot-hub/iot-hub-devguide-direct-methods.md) om het inrichten op aanvraag hand matig te activeren. Het inrichten kan ook worden geactiveerd door een [gewenste eigenschap](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example)in te stellen. 

Het beleid voor opnieuw inrichten voor een inschrijvings vermelding bepaalt hoe het Device Provisioning service-exemplaar deze inrichtings aanvragen verwerkt, en als de status gegevens van het apparaat tijdens het opnieuw inrichten moeten worden gemigreerd. Dezelfde beleids regels zijn beschikbaar voor individuele inschrijvingen en inschrijvings groepen:

Zie voor beelden van code voor het verzenden van inrichtings aanvragen van een apparaat tijdens een opstart reeks, [het automatisch inrichten van een gesimuleerd apparaat](quick-create-simulated-device.md).


## <a name="next-steps"></a>Volgende stappen

- Zie [IOT hub-concepten](concepts-device-reprovision.md) voor het opnieuw inrichten van apparaten voor meer informatie. 
- Zie voor meer informatie over het ongedaan maken van de inrichting van [apparaten die eerder automatisch zijn ingericht](how-to-unprovision-devices.md) , ongedaan maken 











