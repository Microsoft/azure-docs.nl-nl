---
title: Kennismaking met de gebruikersinterface van Azure IoT Central | Microsoft Docs
description: Vertrouwd raken met de belangrijkste gebieden van de Azure IoT Central gebruikersinterface die u gebruikt om uw IoT-oplossing te maken, beheren en gebruiken.
author: ankitscribbles
ms.author: ankitgup
ms.date: 02/09/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: bf60f512416007137e71119fa7474b1393099ebf
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718877"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Kennismaking met de gebruikersinterface van Azure IoT Central

In dit artikel maakt u kennis met Azure IoT Central gebruikersinterface. U kunt de gebruikersinterface gebruiken voor het maken, beheren en gebruiken van een IoT Central en de verbonden apparaten.

## <a name="iot-central-homepage"></a>Startpagina IoT Central

Op [IoT Central](https://aka.ms/iotcentral-get-started) startpagina vindt u meer informatie over het laatste nieuws en de functies die beschikbaar zijn op IoT Central, het maken van nieuwe toepassingen en het bekijken en starten van uw bestaande toepassingen.

:::image type="content" source="media/overview-iot-central-tour/iot-central-homepage.png" alt-text="Startpagina IoT Central":::

### <a name="create-an-application"></a>Een app maken

In de **sectie Bouwen** kunt u bladeren door de lijst met relevante IoT Central sjablonen, of u kunt volledig opnieuw beginnen met behulp van een aangepaste app-sjabloon.  

:::image type="content" source="media/overview-iot-central-tour/iot-central-build.png" alt-text="Ontwikkelpagina IoT Central":::

Raadpleeg het artikel [Een Azure IoT Central-toepassing maken](quick-deploy-iot-central.md) voor meer informatie.

### <a name="launch-your-application"></a>Uw toepassing starten

U start uw IoT Central door te navigeren naar de URL die u hebt gekozen tijdens het maken van de app. U kunt ook een lijst weergeven met alle toepassingen waartoe u toegang hebt tot het [IoT Central-app-beheer](https://aka.ms/iotcentral-apps).

:::image type="content" source="media/overview-iot-central-tour/app-manager.png" alt-text="App-beheer IoT Central":::

## <a name="navigate-your-application"></a>Door uw toepassing bladeren

Wanneer u zich in uw IoT-toepassing hebt, gebruikt u het linkerdeelvenster om toegang te krijgen tot verschillende functies. U kunt het linkerdeelvenster uitvouwen of samenvouwen door het pictogram met drie lijnen boven aan het deelvenster te selecteren:

> [!NOTE]
> De items die u in het linkerdeelvenster ziet, zijn afhankelijk van uw gebruikersrol. Meer informatie over het [beheren van gebruikers en rollen](howto-manage-users-roles.md). 

:::row:::
  :::column span="":::
      :::image type="content" source="media/overview-iot-central-tour/navigation-bar.png" alt-text="linkerdeelvenster":::

  :::column-end:::
  :::column span="2":::
     **Dashboard** geeft alle toepassings- en persoonlijke dashboards weer. 
     
     **Met** apparaten kunt u al uw apparaten beheren.

     **Met apparaatgroepen** kunt u verzamelingen apparaten weergeven en maken die zijn opgegeven door een query. Apparaatgroepen worden via de toepassing gebruikt om bulkbewerkingen uit te voeren.

     **Met** regels kunt u regels maken en bewerken om uw apparaten te bewaken. Regels worden geëvalueerd op basis van apparaatgegevens en activeren aanpasbare acties.

     **Analyse** biedt uitgebreide mogelijkheden voor het analyseren van historische trends en het correleren van verschillende telemetriegegevens van uw apparaten.

     **Met** taken kunt u uw apparaten op schaal beheren door bulkbewerkingen uit te voeren.

     **Met apparaatsjablonen** kunt u de kenmerken maken en beheren van apparaten die verbinding maken met uw toepassing.

     **Met gegevensexport** kunt u een continue export naar externe services configureren, zoals opslag en wachtrijen.

     **Met** beheer kunt u de instellingen, aanpassingen, facturering, gebruikers en rollen van uw toepassing beheren.

     **Mijn apps** kunt u teruggaan naar IoT Central app-manager.
     
   :::column-end:::
:::row-end:::

### <a name="search-help-theme-and-support"></a>Zoeken, hulp, thema en ondersteuning

Het bovenste menu wordt op elke pagina weergegeven:

:::image type="content" source="media/overview-iot-central-tour/toolbar.png" alt-text="IoT Central werkbalk":::

* Als u wilt zoeken naar apparaten, voert u een **zoekwaarde** in.
* Als u de taal of het thema van de gebruikersinterface wilt wijzigen, kiest u het pictogram **Instellingen**. Meer informatie over [het beheren van de voorkeuren van uw toepassing](howto-manage-preferences.md)
* Als u hulp en ondersteuning nodig hebt, kiest u het keuzemenu **Help** voor een lijst met bronnen. U kunt [informatie over uw toepassing](./howto-get-app-info.md) ophalen uit de koppeling **Over uw app**. In een toepassing met een gratis abonnement bevatten de ondersteuningsresources toegang tot [live chat](howto-show-hide-chat.md).
* Als u zich wilt afmelden bij de toepassing, kiest u het pictogram **Account**.

Voor de gebruikersinterface kunt u kiezen tussen een licht thema of een donker thema:

> [!NOTE]
> De optie om te kiezen tussen lichte en donkere thema's is niet beschikbaar als uw beheerder een aangepast thema voor de toepassing heeft geconfigureerd.

:::image type="content" source="media/overview-iot-central-tour/themes.png" alt-text="Schermopname van IoT Central Een thema kiezen.":::

### <a name="dashboard"></a>Dashboard

:::image type="content" source="Media/overview-iot-central-tour/dashboard.png" alt-text="Schermopname van IoT Central Dashboard.":::

* **Dashboard** is de eerste pagina die u ziet wanneer u zich bij uw IoT Central toepassing. U kunt meerdere toepassingsdashboards maken en aanpassen. Meer informatie over [tegels toevoegen aan uw dashboard](howto-add-tiles-to-your-dashboard.md)

* Persoonlijke dashboards kunnen ook worden gemaakt om te controleren wat u belangrijk vindt. Zie het artikel met instructies [Create Azure IoT Central personal dashboards](howto-create-personal-dashboards.md) (Azure IoT Central persoonlijke dashboards maken) voor meer informatie.

### <a name="devices"></a>Apparaten

:::image type="content" source="Media/overview-iot-central-tour/devices.png" alt-text="Schermopname van de pagina Apparaten.":::

Op deze pagina worden de apparaten in uw toepassing IoT Central gegroepeerd op _apparaatsjabloon_.

* Met een apparaatsjabloon wordt een type apparaat gedefinieerd dat met uw toepassing verbinding kan maken.
* Een apparaat vertegenwoordigt een echt of een gesimuleerd apparaat in uw toepassing.

Zie de quickstart [Uw apparaten bewaken](./quick-monitor-devices.md) voor meer informatie. 

### <a name="device-groups"></a>Apparaatgroepen

:::image type="content" source="Media/overview-iot-central-tour/device-groups.png" alt-text="Pagina Apparaatgroep":::

Op deze pagina kunt u apparaatgroepen maken en weergeven in uw IoT Central toepassing. U kunt apparaatgroepen gebruiken om bulkbewerkingen in uw toepassing uit te voeren of om gegevens te analyseren. Raadpleeg het artikel [Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing](tutorial-use-device-groups.md) voor meer informatie.

### <a name="rules"></a>Regels
:::image type="content" source="Media/overview-iot-central-tour/rules.png" alt-text="Schermopname van de pagina Regels.":::

Op deze pagina kunt u regels weergeven en maken op basis van apparaatgegevens. Wanneer een regel wordt activeert, kan deze een of meer acties activeren, zoals het verzenden van een e-mailbericht of het aanroepen van een webhook. Zie de zelfstudie Regels [configureren voor meer](tutorial-create-telemetry-rules.md) informatie.

### <a name="analytics"></a>Analyse

:::image type="content" source="Media/overview-iot-central-tour/analytics.png" alt-text="Schermopname van de pagina Analyse.":::

Analyse biedt uitgebreide mogelijkheden voor het analyseren van historische trends en het correleren van verschillende telemetriegegevens van uw apparaten. Raadpleeg het artikel [Analyses maken voor uw Azure IoT Central-toepassing](howto-create-analytics.md) voor meer informatie.

### <a name="jobs"></a>Taken

:::image type="content" source="Media/overview-iot-central-tour/jobs.png" alt-text="Pagina Taken":::

Op deze pagina kunt u taken weergeven en maken die kunnen worden gebruikt voor bulkbewerkingen voor apparaatbeheer op uw apparaten. U kunt eigenschappen en instellingen van apparaten bijwerken en opdrachten uitvoeren ten aanzien van apparaatgroepen. Zie voor meer informatie, het artikel [Een taak uitvoeren](howto-run-a-job.md).

### <a name="device-templates"></a>Apparaatsjablonen

:::image type="content" source="Media/overview-iot-central-tour/templates.png" alt-text="Schermopname van apparaatsjablonen.":::

Op de pagina apparaatsjablonen kunt u apparaatsjablonen weergeven en maken in de toepassing. Raadpleeg voor meer informatie de zelfstudie [Define a new device type in your Azure IoT Central application](howto-set-up-template.md) (Een nieuw apparaattype definiëren in uw Azure IoT Central-toepassing).

### <a name="data-export"></a>Gegevensexport

:::image type="content" source="Media/overview-iot-central-tour/export.png" alt-text="Gegevensexport":::

Met gegevensexport kunt u gegevensstromen naar externe systemen instellen. Zie het artikel [Exporteren van gegevens in Azure IoT Central](./howto-export-data.md) voor meer informatie.

### <a name="administration"></a>Beheer

:::image type="content" source="media/overview-iot-central-tour/administration.png" alt-text="Schermopname van IoT-beheer.":::

Op de pagina Beheer kunt u uw IoT Central-toepassing configureren en aanpassen. Hier kunt u de naam, de URL, de thema's van uw toepassing wijzigen, de gebruikers en rollen beheren, API-tokens maken en uw toepassing exporteren. Raadpleeg voor meer informatie het artikel [Administer your Azure IoT Central application](howto-administer.md) (Uw Azure IoT Central-toepassing beheren).

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht hebt van Azure IoT Central en bekend bent met de indeling van de gebruikersinterface, is de voorgestelde vervolgstap om de snelstart [Create an Azure IoT Central application](quick-deploy-iot-central.md) (Een Azure IoT Central-toepassing maken) te voltooien.
