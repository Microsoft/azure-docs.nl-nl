---
title: Functies van klant gegevens aanvragen voor Azure DPS-apparaten
description: Voor apparaten die worden beheerd in de Azure Device Provisioning Service (DPS) die persoonlijk zijn, toont dit artikel beheerders hoe persoonlijke gegevens worden geëxporteerd of verwijderd.
author: dominicbetts
ms.author: dobett
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 75a2762a0937efbceaa168f8a2d6409e2e3a7ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967224"
---
# <a name="summary-of-customer-data-request-features"></a>Samenvatting van functies voor gegevensaanvragen van klanten

Azure IoT Hub Device Provisioning Service is een op REST API gebaseerde Cloud service gericht op zakelijke klanten die naadloze, automatische Zero-Touch inrichting van apparaten aan Azure IoT Hub mogelijk maken met beveiliging die op het apparaat begint en eindigt met de Cloud.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Aan afzonderlijke apparaten wordt een registratie-ID en apparaat-ID toegewezen door een Tenant beheerder. Gegevens van en over deze apparaten zijn gebaseerd op deze Id's. Micro soft onderhoudt geen informatie en heeft geen toegang tot gegevens die de correlatie van deze apparaten met een individu zouden toestaan.

Veel van de apparaten die in Device Provisioning Service worden beheerd, zijn geen persoonlijke apparaten, bijvoorbeeld een Office-Thermo staat of een Factory-robot. Klanten kunnen er echter voor zorgen dat sommige apparaten persoonlijk worden geïdentificeerd en kunnen hun eigen activa of inventaris tracking methoden voor het koppelen van apparaten aan individuen beschouwen. Met Device Provisioning Service worden alle gegevens die zijn gekoppeld aan apparaten beheerd en opgeslagen alsof het persoons gegevens zijn.

Tenant beheerders kunnen de Azure Portal of de REST-Api's van de service gebruiken voor het uitvoeren van informatie aanvragen door gegevens die zijn gekoppeld aan een apparaat-ID of registratie-ID te exporteren of te verwijderen.

> [!NOTE]
> Voor apparaten die zijn ingericht in azure IoT Hub via Device Provisioning Service, zijn extra gegevens opgeslagen in de Azure IoT Hub-service. Raadpleeg de [documentatie van Azure IOT hub Reference](../iot-hub/iot-hub-customer-data-requests.md) om een volledige aanvraag voor een bepaald apparaat te volt ooien.

## <a name="deleting-customer-data"></a>Klant gegevens verwijderen

Met Device Provisioning Service worden inschrijvingen en registratie records opgeslagen. Inschrijvingen bevatten informatie over apparaten die mogen worden ingericht, en registratie records laten zien welke apparaten het inrichtings proces al hebben door lopen.

Tenant beheerders kunnen inschrijvingen van de Azure Portal verwijderen en Hiermee worden alle bijbehorende registratie records ook verwijderd.

Zie [registratie van apparaten beheren](how-to-manage-enrollments.md)voor meer informatie.

Het is ook mogelijk om delete-bewerkingen uit te voeren voor inschrijvingen en registratie records met behulp van REST-Api's:

* Als u registratie gegevens voor één apparaat wilt verwijderen, kunt u [apparaatregistratie gebruiken-verwijderen](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment).
* Als u registratie gegevens voor een groep apparaten wilt verwijderen, kunt u de groep registratie van apparaten gebruiken [-verwijderen](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup).
* Om informatie over apparaten die zijn ingericht te verwijderen, kunt u de registratie [status gebruiken-registratie status verwijderen](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate).

## <a name="exporting-customer-data"></a>Klant gegevens exporteren

Met Device Provisioning Service worden inschrijvingen en registratie records opgeslagen. Inschrijvingen bevatten informatie over apparaten die mogen worden ingericht, en registratie records laten zien welke apparaten het inrichtings proces al hebben door lopen.

Tenant beheerders kunnen inschrijvingen en registratie records via de Azure Portal weer geven en exporteren met behulp van kopiëren en plakken.

Zie [registratie van apparaten beheren](how-to-manage-enrollments.md)voor meer informatie over het beheren van inschrijvingen.

Het is ook mogelijk om export bewerkingen uit te voeren voor inschrijvingen en registratie records met behulp van REST-Api's:

* Als u registratie gegevens voor één apparaat wilt exporteren, kunt u [apparaatregistratie gebruiken-ophalen](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment).
* Als u registratie-informatie voor een groep apparaten wilt exporteren, kunt u de groep registratie van apparaten gebruiken [-ophalen](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup).
* Als u informatie wilt exporteren over apparaten die al zijn ingericht, kunt u de [registratie status gebruiken-registratie status ophalen](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate).

> [!NOTE]
> Wanneer u de Enter prise-services van micro soft gebruikt, genereert micro soft bepaalde informatie, ook wel door het systeem gegenereerde logboeken. Sommige door het systeem gegenereerde Device Provisioning Service-logboeken zijn niet toegankelijk of kunnen niet worden geëxporteerd door Tenant beheerders. Deze logboeken vormen feitelijke acties die zijn uitgevoerd in de service en diagnostische gegevens met betrekking tot afzonderlijke apparaten.

## <a name="links-to-additional-documentation"></a>Koppelingen naar aanvullende documentatie

Volledige documentatie voor Device Provisioning Service-Api's bevindt zich op [https://docs.microsoft.com/rest/api/iot-dps](/rest/api/iot-dps) .

Azure IoT Hub- [functies voor gegevens aanvragen](../iot-hub/iot-hub-customer-data-requests.md)van de klant.