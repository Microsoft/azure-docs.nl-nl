---
title: Een Azure IoT Central-toepassing exporteren | Microsoft Docs
description: Als oplossings beheerder wilt u een toepassings sjabloon exporteren zodat deze opnieuw kan worden gebruikt.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: aec72644f708d6363a80da28c5e571d0165fcdfa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91651833"
---
# <a name="export-your-application"></a>Uw toepassing exporteren

In dit artikel wordt beschreven hoe, als oplossings beheerder, een IoT Central toepassing moet exporteren om deze opnieuw te kunnen gebruiken.

U hebt hiervoor twee opties:

- U kunt een kopie van uw toepassing maken als u alleen een duplicaat kopie van uw toepassing wilt maken.
- U kunt een toepassings sjabloon maken op basis van uw toepassing als u meerdere kopieën wilt maken.

## <a name="copy-your-application"></a>Uw toepassing kopiëren

U kunt een kopie maken van elke toepassing, min eventuele exemplaren van apparaten, geschiedenis van apparaatgegevens en gebruikers gegevens. De kopie gebruikt een Standard-prijs plan waarvoor u wordt gefactureerd. U kunt geen toepassing maken die gebruikmaakt van het gratis prijs plan door een toepassing te kopiëren.

Selecteer **Kopiëren**. Voer in het dialoog venster de details in voor de nieuwe toepassing. Selecteer vervolgens **kopiëren** om te bevestigen dat u wilt door gaan. Zie de Snelstartgids [een toepassing maken](quick-deploy-iot-central.md) voor meer informatie over de velden in het formulier.

![Scherm afbeelding met de instellingen pagina ' toepassing kopiëren '.](media/howto-use-app-templates/appcopy2.png)

Nadat de Kopieer bewerking van de app is voltooid, kunt u naar de nieuwe toepassing navigeren met behulp van de koppeling.

![Pagina Toepassings instellingen](media/howto-use-app-templates/appcopy3a.png)

Als u een toepassing kopieert, wordt ook de definitie van regels en e-mail acties gekopieerd. Sommige acties, zoals flow en Logic Apps, zijn gekoppeld aan specifieke regels via de regel-ID. Wanneer een regel naar een andere toepassing wordt gekopieerd, krijgt deze een eigen regel-ID. In dit geval moeten gebruikers een nieuwe actie maken en vervolgens de nieuwe regel koppelen. Over het algemeen is het een goed idee om de regels en acties te controleren om er zeker van te zijn dat ze up-to-date zijn in de nieuwe app.

> [!WARNING]
> Als een dash board tegels bevat waarin informatie over specifieke apparaten wordt weer gegeven, worden in die tegels **de aangevraagde bron niet** in de nieuwe toepassing gevonden. U moet deze tegels opnieuw configureren om informatie weer te geven over apparaten in de nieuwe toepassing.

## <a name="create-an-application-template"></a>Een toepassingssjabloon maken

Wanneer u een Azure IoT Central-toepassing maakt, hebt u de keuze uit ingebouwde voorbeeld sjablonen. U kunt ook uw eigen toepassings sjablonen maken op basis van bestaande IoT Central toepassingen. U kunt vervolgens uw eigen toepassings sjablonen gebruiken wanneer u nieuwe toepassingen maakt.

Wanneer u een toepassings sjabloon maakt, worden de volgende items uit uw bestaande toepassing opgenomen:

- Het standaard toepassings dashboard, met inbegrip van de indeling van het dash board en alle tegels die u hebt gedefinieerd.
- Apparaatinstellingen, waaronder metingen, instellingen, eigenschappen, opdrachten en dash board.
- Wetgeving. Alle regel definities zijn opgenomen. Acties, met uitzonde ring van e-mail acties, worden echter niet meegenomen.
- Apparaatsets, met inbegrip van de voor waarden en dash boards.

> [!WARNING]
> Als een dash board tegels bevat waarin informatie over specifieke apparaten wordt weer gegeven, worden in die tegels **de aangevraagde bron niet** in de nieuwe toepassing gevonden. U moet deze tegels opnieuw configureren om informatie weer te geven over apparaten in de nieuwe toepassing.

Wanneer u een toepassings sjabloon maakt, bevat deze geen de volgende items:

- Apparaten
- Gebruikers
- Definities voor continue gegevens export

Voeg deze items hand matig toe aan alle toepassingen die zijn gemaakt op basis van een toepassings sjabloon.

Een toepassings sjabloon maken op basis van een bestaande IoT Central-toepassing:

1. Ga naar de sectie **beheer** in uw toepassing.
1. Selecteer **toepassings sjabloon exporteren**.
1. Voer op de pagina **toepassings sjabloon exporteren** een naam en een beschrijving in voor uw sjabloon.
1. Selecteer de knop **exporteren** om de toepassings sjabloon te maken. U kunt nu de **deel bare koppeling** kopiëren waarmee iemand een nieuwe toepassing kan maken op basis van de sjabloon:

![Een toepassingssjabloon maken](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>Een toepassings sjabloon gebruiken

Als u een toepassings sjabloon wilt gebruiken om een nieuwe IoT Central-toepassing te maken, hebt u een eerder gemaakte **deel bare koppeling** nodig. Plak de **koppeling** die u wilt delen in de adres balk van uw browser. De pagina **een toepassing maken** wordt weer gegeven als u uw aangepaste toepassings sjabloon hebt geselecteerd:

![Een toepassing maken op basis van een sjabloon](media/howto-use-app-templates/create-app.png)

Selecteer uw prijs plan en vul de andere velden in het formulier in. Selecteer vervolgens **maken** om een nieuwe IOT Central-toepassing te maken op basis van de toepassings sjabloon.

### <a name="manage-application-templates"></a>Toepassings sjablonen beheren

Op de pagina **exporteren van toepassings sjablonen** kunt u de toepassings sjabloon verwijderen of bijwerken.

Als u een toepassings sjabloon verwijdert, kunt u de eerder gegenereerde koppeling niet meer gebruiken om nieuwe toepassingen te maken.

Als u uw toepassings sjabloon wilt bijwerken, wijzigt u de naam of beschrijving van de sjabloon op de pagina **export van toepassings sjabloon** . Selecteer vervolgens de knop **exporteren** opnieuw. Met deze actie wordt een nieuwe **deel bare koppeling** gegenereerd en wordt een vorige **deel bare koppelings** -URL ongeldig gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u toepassings sjablonen gebruikt, is de voorgestelde volgende stap informatie over [het bewaken van de algemene status van de apparaten die zijn verbonden met een IOT Central-toepassing](howto-monitor-application-health.md)
