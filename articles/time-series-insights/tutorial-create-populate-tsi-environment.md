---
title: 'Zelfstudie: Een omgeving maken - Azure Time Series Insights | Microsoft Docs'
description: Leer hoe u een Azure Time Series Insights-omgeving kunt maken die wordt gevuld met gegevens van gesimuleerde apparaten.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 982ca04237d16979d65260a040f5aed783822ac7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023221"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Zelfstudie: Een Azure Time Series Insights Gen1-omgeving maken

> [!CAUTION]
> Dit is een Gen1-artikel.

Deze zelfstudie begeleidt u bij het maken van een Azure Time Series Insights-omgeving, die wordt gevuld met gegevens van gesimuleerde apparaten. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Azure Time Series Insights-omgeving maken.
> * Een apparaatsimulatieoplossing met een IoT-hub maken.
> * De Azure Time Series Insights-omgeving verbinden met de IoT-hub.
> * Een apparaatsimulatie uitvoeren om gegevens te streamen naar de Azure Time Series Insights-omgeving.
> * De gesimuleerde telemetriegegevens verifiëren.

> [!IMPORTANT]
> Neem een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u nog geen abonnement hebt.

## <a name="prerequisites"></a>Vereisten

* Uw Azure-aanmeldingsaccount moet ook lid zijn van de rol **Eigenaar** van het abonnement. Lees [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure Portal](../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="review-video"></a>Video bekijken

Leer hoe u een Azure IoT-oplossingsverbetering kunt gebruiken om gegevens te genereren en aan de slag te gaan met Azure Time Series Insights.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Overzicht

In de Azure Time Series Insights-omgeving worden apparaatgegevens verzameld en opgeslagen. Eenmaal opgeslagen kunt u [Azure Time Series Insights Explorer](time-series-quickstart.md) en [Azure Time Series Insights Query API](/rest/api/time-series-insights/gen1-query-api) gebruiken om query's op de gegevens uit te voeren en de gegevens te analyseren.

Azure IoT Hub is de gebeurtenisbron die door alle apparaten (gesimuleerd of fysiek) in de zelfstudie wordt gebruikt om veilig verbinding te maken en gegevens naar de Azure-cloud te verzenden.

Deze zelfstudie maakt ook gebruik van een [IoT-oplossingsverbetering](https://www.azureiotsolutions.com) om voorbeeldtelemetriegegevens te genereren en te streamen naar IoT Hub.

>[!TIP]
> [IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com) bieden vooraf geconfigureerde oplossingen van ondernemingsklasse, waarmee u de ontwikkeling van aangepaste IoT-oplossingen kunt versnellen.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

Maak eerst de oplossing voor apparaatsimulatie, waarmee testgegevens worden gegenereerd om uw Azure Time Series Insights-omgeving te vullen.

1. Ga in een apart venster of tabblad naar [azureiotsolutions.com](https://www.azureiotsolutions.com). Log in met hetzelfde Azure-abonnementsaccount en selecteer de oplosssingsverbetering **Apparaatsimulatie**.

   [![De oplossingsverbetering Apparaatsimulatie uitvoeren](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Selecteer **Nu uitproberen**. Voer de vereiste parameters in op de pagina **Oplossing voor apparaatsimulatie maken**.

   Parameter|Beschrijving
   ---|---
   **Naam van implementatie** | Deze unieke waarde wordt gebruikt om een nieuwe resourcegroep te maken. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep.
   **Azure-abonnement** | Geef hetzelfde abonnement op dat is gebruikt voor het maken van uw Azure Time Series Insights-omgeving in de vorige sectie.
   **Implementatieopties** | Selecteer **Nieuwe IoT-hub inrichten** om een nieuwe IoT-hub te maken specifiek voor deze zelfstudie.
   **Azure-locatie** | Geef dezelfde regio op die is gebruikt voor het maken van uw Azure Time Series Insights-omgeving in de vorige sectie.

   Wanneer u klaar bent, selecteert u **Maken** om de Azure-resources van de oplossing in te richten. Het kan tot 20 minuten duren voordat dit proces is voltooid.

   [![De apparaatsimulatieoplossing inrichten](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Nadat het inrichten is voltooid, worden er twee meldingen weergegeven waarin staat dat de implementatiestatus is gewijzigd van **Inrichten** in **Gereed**.

   >[!IMPORTANT]
   > Open uw oplossingsverbetering nog niet. Laat deze webpagina geopend, u gaat deze later weer gebruiken.

   [![Het inrichten van de apparaatsimulatieoplossing is voltooid](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Inspecteer nu de zojuist gemaakte resources in de Azure-portal. Op de pagina **Resourcegroepen** van de portal ziet u dat er een nieuwe resourcegroep is gemaakt met behulp van de **Oplossingsnaam** in de vorige stap. Noteer de resources die zijn gemaakt voor de simulatie van het apparaat.

   [![Resources voor apparaatsimulatie](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Een omgeving maken

Maak vervolgens een Azure Time Series Insights omgeving in uw Azure-abonnement.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-abonnementsaccount.
1. Selecteer linksboven **+ Een resource maken**.
1. Selecteer de categorie **Internet of Things** en vervolgens **Time Series Insights**.

   [![Selecteer de Azure Time Series Insights-omgevingsresource](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Vul de vereiste parameters in op de pagina **Time Series Insights-omgeving**.

   Parameter|Beschrijving
   ---|---
   **Omgevingsnaam** | Kies een unieke naam voor de Azure Time Series Insights-omgeving. De namen worden gebruikt door de Azure Time Series Insights Explorer en de [query-API's](/rest/api/time-series-insights/gen1-query).
   **Abonnement** | Abonnementen zijn containers voor Azure-resources. Kies een abonnement voor het maken van de Azure Time Series Insights-omgeving.
   **Resourcegroep** | Een resourcegroep is een container voor Azure-resources. Kies een bestaande resourcegroep voor de Azure Time Series Insights-omgevingsresource of maak een nieuwe.
   **Locatie** | Kies een datacentrumregio voor uw Azure Time Series Insights-omgeving. Als u extra latentie wilt voorkomen, maakt u de Azure Time Series Insights-omgeving in dezelfde regio als andere IoT-resources.
   **Laag** | Kies de benodigde doorvoer. Selecteer **S1**.
   **Capaciteit** | Capaciteit is de vermenigvuldiger die wordt toegepast op de invoersnelheid en opslagcapaciteit die aan de geselecteerde SKU zijn verbonden. U kunt de capaciteit wijzigen nadat deze is gemaakt. Selecteer een capaciteit van **1**.

   Wanneer u klaar bent, selecteert u **Volgende: Gebeurtenisbron** om naar de volgende stap te gaan.

   [![Een Azure Time Series Insights-omgevingsresource maken](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Verbind nu de Azure Time Series Insights-omgeving met de IoT-hub die is gemaakt met de oplossingsverbetering. Stel **Een hub selecteren** in op `Select existing`. Kies vervolgens de IoT-hub die is gemaakt met de oplossingsverbetering bij het instellen van **Naam van IoT-hub**.

   [![De Azure Time Series Insights-omgeving verbinden met de gemaakte IoT-hub](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Selecteer ten slotte **Beoordelen en maken**.

1. Controleer het venster **Meldingen** om de voltooiing van de implementatie te bewaken.

   [![De implementatie van de Azure Time Series Insights-omgeving is voltooid](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Apparaatsimulatie uitvoeren

Nu de implementatie en de initiële configuratie zijn voltooid, vult u de Azure Time Series Insights-omgeving met voorbeeldgegevens van [gesimuleerde apparaten die zijn gemaakt door de oplossingsverbetering](#create-a-device-simulation).

Samen met de IoT-hub is een Azure App Service-webtoepassing gegenereerd om telemetrie voor gesimuleerde apparaten te maken en te verzenden.

1. Ga terug naar het dashboard [Oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Meld u indien nodig opnieuw aan met hetzelfde Azure-account dat u in deze zelfstudie hebt gebruikt. Selecteer uw apparaatoplossing en vervolgens **Naar uw oplossingsverbetering gaan** om uw geïmplementeerde oplossing te starten.

   [![Dashboard Oplossingsverbeteringen](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. De web-app voor apparaatsimulatie begint met de vraag of u de webtoepassing de machtiging **Aanmelden en uw profiel lezen** wilt verlenen. Met deze machtiging kan de toepassing de gegevens van het gebruikersprofiel ophalen die nodig zijn om de werking van de toepassing te ondersteunen.

   [![Toestemming voor webtoepassing voor apparaatsimulatie](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Selecteer **+ Nieuwe simulatie**. Nadat de pagina **Simulatie-instellingen** is geladen, voert u de vereiste parameters in.

   Parameter|Beschrijving
   ---|---
   **Doel-IoT-hub** | Selecteer **Vooraf ingerichte IoT-hub gebruiken**.
   **Apparaatmodel** | Selecteer **Koelunit**.
   **Aantal apparaten**  | Voer `10` in onder **Aantal**.
   **Telemetriefrequentie** | Voer `10` seconden in.
   **Simulatieduur** | Selecteer **Eindigen over:** en voer `5` minuten in.

   Wanneer u klaar bent, selecteert u **Simulatie starten**. De simulatie wordt in totaal 5 minuten uitgevoerd. Er worden om de 10 seconden gegevens van 1000 gesimuleerde apparaten gegenereerd.

   [![Apparaatsimulatie instellen](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Terwijl de simulatie wordt uitgevoerd, ziet u dat de velden **Totaal berichten** en **Berichten per seconde** ongeveer om de 10 seconden worden bijgewerkt. De simulatie eindigt na ongeveer 5 minuten en keert terug naar **Simulatie-instellingen**.

   [![Apparaatsimulatie wordt uitgevoerd](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>De telemetriegegevens verifiëren

In dit laatste gedeelte verifieert u dat de telemetriegegevens zijn gegenereerd en opgeslagen in de Azure Time Series Insights-omgeving. Voor het verifiëren van de gegevens gebruikt u de Azure Time Series Insights Explorer, die wordt gebruikt voor het opvragen en analyseren van telemetriegegevens.

1. Ga terug naar de pagina **Overzicht** van de resourcegroep van de Azure Time Series Insights-omgeving. Selecteer de Azure Time Series Insights-omgeving.

   [![De resourcegroep van de Azure Time Series Insights-omgeving en de omgeving](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Selecteer op de pagina **Overzicht** van de Azure Time Series Insights-omgeving de **URL van Time Series Insights Explorer** om Azure Time Series Insights Explorer te openen.

   [![Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Azure Time Series Insights Explorer wordt geladen en geverifieerd met behulp van uw Azure-portalaccount. In eerste instantie wordt het grafiekgebied waarmee de Azure Time Series Insights-omgeving is gevuld samen met de gesimuleerde telemetriegegevens weergegeven. Als u een kleiner tijdsbereik wilt filteren, selecteert u de vervolgkeuzelijst in de linkerbovenhoek. Voer een tijdsbereik in dat groot genoeg is om de duur van de apparaatsimulatie te overbruggen. Selecteer vervolgens het vergrootglas om te zoeken.

   [![Tijdsbereikfilter van Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Door het tijdsbereik te toe te spitsen kan de grafiek inzoomen op de afzonderlijke bursts van gegevensoverdracht naar de IoT-hub en de Azure Time Series Insights-omgeving. Let ook op de tekst **Streaming voltooid** in de rechterbovenhoek, waar het totale aantal gevonden gebeurtenissen wordt weergegeven. U kunt ook de schuifregelaar **Intervalgrootte** slepen om de granulariteit van de grafiek te bepalen.

   [![Gefilterde weergave van Azure Time Series Insights Explorer-tijdsbereik](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Ten slotte kunt u ook met de linkermuisknop op een regio klikken om een bereik te filteren. Klik vervolgens met de rechtermuisknop en gebruik **Gebeurtenissen verkennen** om gebeurtenisdetails weer te geven in de tabelweergave **Gebeurtenissen**.

   [![Gefilterde weergave en gebeurtenissen van Azure Time Series Insights Explorer-tijdsbereik](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie worden verschillende werkende Azure-services gemaakt om de Azure Time Series Insights-omgeving en de apparaatsimulatie-oplossing te ondersteunen. Ga terug naar de Azure-portal als u deze wilt verwijderen.

In het menu aan de linkerkant van de Azure-portal doet u het volgende:

1. Selecteer het pictogram **Resourcegroepen**. Selecteer vervolgens de resourcegroep die u hebt gemaakt voor de Azure Time Series Insights-omgeving. Selecteer bovenaan de pagina **Resourcegroep verwijderen**, voer de naam van de resourcegroep in en selecteer **Verwijderen**.

1. Selecteer het pictogram **Resourcegroepen**. Selecteer vervolgens de resourcegroep die is gemaakt met de oplossingsverbetering voor apparaatsimulatie. Selecteer bovenaan de pagina **Resourcegroep verwijderen**, voer de naam van de resourcegroep in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Een Azure Time Series Insights-omgeving maken.
> * Een apparaatsimulatieoplossing met een IoT-hub maken.
> * De Azure Time Series Insights-omgeving verbinden met de IoT-hub.
> * Een apparaatsimulatie uitvoeren om gegevens te streamen naar de Azure Time Series Insights-omgeving.
> * De gesimuleerde telemetriegegevens verifiëren.

U weet nu hoe u uw eigen Azure Time Series Insights-omgeving maakt. De volgende stap is het bouwen van een webtoepassing die gegevens uit die Azure Time Series Insights-omgeving gebruikt:

> [!div class="nextstepaction"]
> [Voorbeelden van het lezen van visualisaties via een gehoste client-SDK](https://tsiclientsample.azurewebsites.net/)