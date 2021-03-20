---
title: De Azure Portal gebruiken om een IoT Hub te maken | Microsoft Docs
description: Azure IoT hubs maken, beheren en verwijderen via de Azure Portal. Bevat informatie over prijs categorieën, schalen, beveiliging en configuratie van berichten.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 0a54d0c85902915d2ee62acd8a1d38b8db8b221c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92536042"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Een IoT-hub maken met behulp van Azure Portal

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

In dit artikel wordt beschreven hoe u IoT-hubs maakt en beheert met behulp van de [Azure Portal](https://portal.azure.com).

Als u de stappen in deze zelf studie wilt gebruiken, hebt u een Azure-abonnement nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>De instellingen van de IoT-hub wijzigen

U kunt de instellingen van een bestaande IoT-hub wijzigen nadat deze is gemaakt in het deel venster IoT Hub.

![Scherm afbeelding met de instellingen voor de IoT-hub](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

Hier volgen enkele eigenschappen die u kunt instellen voor een IoT-hub:

**Prijzen en schaal**: u kunt deze eigenschap gebruiken om naar een andere laag te migreren of het aantal IOT hub-eenheden in te stellen. 

**Bewaking van bewerkingen**: Schakel de verschillende bewakings categorieën in of uit, zoals logboek registratie voor gebeurtenissen met betrekking tot apparaat-naar-Cloud-berichten of Cloud-naar-apparaat-berichten.

**IP-filter**: Geef een bereik van IP-adressen op die worden geaccepteerd of geweigerd door de IOT-hub.

**Eigenschappen**: bevat de lijst met eigenschappen die u kunt kopiëren en gebruiken, zoals de resource-id, resource groep, locatie, enzovoort.

### <a name="shared-access-policies"></a>Gedeeld toegangsbeleid

U kunt ook de lijst met gedeelde toegangs beleid weer geven of wijzigen door te klikken op **beleid voor gedeelde toegang** in de sectie **instellingen** . Met deze beleids regels definieert u de machtigingen voor apparaten en services om verbinding te maken met IoT Hub. 

Klik op **toevoegen** om de Blade **gedeelde-toegangs beleid toevoegen** te openen.  U kunt de nieuwe beleids naam en de machtigingen invoeren die u aan dit beleid wilt koppelen, zoals wordt weer gegeven in de volgende afbeelding:

![Scherm opname van het toevoegen van een beleid voor gedeelde toegang](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* Het **REGI ster lezen** en **REGI ster schrijven** beleid verlenen Lees-en schrijf rechten voor het identiteits register. Deze machtigingen worden gebruikt door de Cloud Services van de back-end om apparaat-id's te beheren. Als u de schrijf optie kiest, wordt automatisch de optie lezen gekozen.

* Het **service Connect** -beleid verleent machtigingen voor toegang tot service-eind punten. Deze machtiging wordt gebruikt door de Cloud Services van de back-end om berichten van apparaten te verzenden en te ontvangen en om dubbele gegevens van het apparaat bij te werken en te lezen.

* Het beleid voor het **verbinden van apparaten** verleent machtigingen voor het verzenden en ontvangen van berichten met behulp van de eind punten van de IOT Hub apparaten. Deze machtiging wordt door apparaten gebruikt voor het verzenden en ontvangen van berichten van een IoT-hub, het bijwerken en lezen van het apparaat en de opslag van de module dubbele gegevens en het uitvoeren van uploads van bestanden.

Klik op **maken** om dit zojuist gemaakte beleid toe te voegen aan de bestaande lijst.

Zie [IOT hub-machtigingen](./iot-hub-devguide-security.md#iot-hub-permissions)voor meer gedetailleerde informatie over de toegang die wordt verleend door specifieke machtigingen.

## <a name="register-a-new-device-in-the-iot-hub"></a>Een nieuw apparaat registreren in de IoT-hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="message-routing-for-an-iot-hub"></a>Bericht routering voor een IoT-hub

Klik op **bericht routering** onder **Messa ging** om het deel venster bericht routering te bekijken, waarin u routes en aangepaste eind punten voor de hub definieert. Met [bericht routering](iot-hub-devguide-messages-d2c.md) kunt u beheren hoe gegevens van uw apparaten naar uw eind punten worden verzonden. De eerste stap is het toevoegen van een nieuwe route. Vervolgens kunt u een bestaand eind punt toevoegen aan de route of een nieuw type maken dat wordt ondersteund, zoals Blob Storage. 

![Deel venster bericht routering](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>Routes

Routes is het eerste tabblad in het deel venster bericht routering. Klik op +**toevoegen** om een nieuwe route toe te voegen. Het volgende scherm wordt weer gegeven. 

![Scherm opname van het toevoegen van een nieuwe route](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

Geef uw route een naam. De route naam moet uniek zijn in de lijst met routes voor die hub. 

Voor **eind punt** kunt u er een selecteren in de vervolg keuzelijst of een nieuw item toevoegen. In dit voor beeld zijn er al een opslag account en container beschikbaar. Als u deze wilt toevoegen als een eind punt, klikt u op +**toevoegen** naast de vervolg keuzelijst eind punt en selecteert u **Blob Storage**. In het volgende scherm ziet u waar het opslag account en de container zijn opgegeven.

![Scherm opname van het toevoegen van een opslag eindpunt voor de routerings regel](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

Klik op **Kies een container** om het opslag account en de container te selecteren. Wanneer u deze velden hebt geselecteerd, wordt er een weer gegeven in het deel venster eindpunt. Gebruik de standaard waarden voor de rest van de velden en **Maak** het eind punt voor het opslag account en voeg het toe aan de routerings regels.

Voor **gegevens bron** selecteert u telemetrie-berichten van apparaten. 

Voeg vervolgens een routerings query toe. In dit voor beeld worden de berichten met een toepassings eigenschap `level` met de naam een waarde die gelijk is aan, `critical` doorgestuurd naar het opslag account.

![Scherm opname van het opslaan van een nieuwe routerings regel](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

Klik op **Opslaan** om de routerings regel op te slaan. U keert terug naar het deel venster bericht Routering en uw nieuwe routerings regel wordt weer gegeven.

### <a name="custom-endpoints"></a>Aangepaste eindpunten

Klik op het tabblad **aangepaste eind punten** . U ziet alle aangepaste eind punten die al zijn gemaakt. Hier kunt u nieuwe eind punten toevoegen of bestaande eind punten verwijderen. 

> [!NOTE]
> Als u een route verwijdert, worden de eind punten die aan die route zijn toegewezen, niet verwijderd. Als u een eind punt wilt verwijderen, klikt u op het tabblad Aangepaste eind punten, selecteert u het eind punt dat u wilt verwijderen en klikt u op verwijderen.
>

Meer informatie over aangepaste eind punten vindt u in de [eind punten van de referentie-IOT-hub](iot-hub-devguide-endpoints.md).

U kunt Maxi maal 10 aangepaste eind punten definiëren voor een IoT-hub. 

Zie [bericht routering met IOT hub](tutorial-routing.md)voor een volledig voor beeld van het gebruik van aangepaste eind punten met route ring.

## <a name="find-a-specific-iot-hub"></a>Een specifieke IoT-hub zoeken

Hier volgen twee manieren om een specifieke IoT-hub te vinden in uw abonnement:

1. Als u de resource groep kent waarvan de IoT-hub deel uitmaakt, klikt u op **resource groepen** en selecteert u vervolgens de resource groep in de lijst. In het scherm voor de resource groep worden alle resources in die groep weer gegeven, met inbegrip van de IoT-hubs. Klik op de hub die u wilt bekijken.

2. Klik op **Alle resources**. In het deel venster **alle resources** ziet u een vervolg keuzelijst met de standaard waarde `All types` . Klik op de vervolg keuzelijst en schakel het selectie vakje uit `Select all` . Zoek `IoT Hub` en controleer deze. Klik op de vervolg keuzelijst om de keuze lijst te sluiten en de items worden gefilterd, zodat alleen uw IoT-hubs worden weer gegeven.

## <a name="delete-the-iot-hub"></a>De IoT-hub verwijderen

Als u een IOT-hub wilt verwijderen, gaat u naar de IoT-hub die u wilt verwijderen en klikt u vervolgens op de knop **verwijderen** onder de naam van de IOT-hub.

## <a name="next-steps"></a>Volgende stappen

Volg deze koppelingen voor meer informatie over het beheren van Azure IoT Hub:

* [Bericht routering met IoT Hub](tutorial-routing.md)
* [Uw IoT-hub bewaken](monitor-iot-hub.md)
