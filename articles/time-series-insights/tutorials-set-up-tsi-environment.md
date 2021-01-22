---
title: 'Zelfstudie: Een Gen 2-omgeving instellen in Azure Time Series Insights Gen2 | Microsoft Docs'
description: 'Zelfstudie: Informatie over hoe u een omgeving instelt in Azure Time Series Insights Gen2.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 01/19/2021
ms.custom: seodec18
ms.openlocfilehash: 912dbf48254a8d68b9bf76db04734fd791a3edb9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677051"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Zelfstudie: Een Azure Time Series Insights Gen2-omgeving instellen

In deze zelfstudie wordt u begeleid bij het maken van een Azure Time Series Insights Gen2-omgeving op basis van *betalen per gebruik* (PAYG).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Azure Time Series Insights Gen2-omgeving maken.
> * De Azure Time Series Insights Gen2-omgeving verbinden met een IoT Hub.
> * Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Gen2-omgeving.
> * Voer een eenvoudige analyse van de gegevens uit.
> * Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.

>[!TIP]
> [IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators) bieden vooraf geconfigureerde oplossingen van ondernemingsklasse, waarmee u de ontwikkeling van aangepaste IoT-oplossingen kunt versnellen.

Neem een [gratis Azure-abonnement](https://azure.microsoft.com/free/) als u nog geen abonnement hebt.

## <a name="prerequisites"></a>Vereisten

* U moet minimaal de rol **Inzender** hebben voor het Azure-abonnement. Lees [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure Portal](../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="create-a-device-simulation"></a>Een apparaatsimulatie maken

In dit deel maakt u drie gesimuleerde apparaten die gegevens verzenden naar een Azure IoT Hub-exemplaar.

1. Ga naar [de pagina Azure IoT-oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators). Op de pagina worden enkele vooraf gedefinieerde voorbeelden weergegeven. Meld u aan met uw Azure-account. Selecteer vervolgens **Apparaatsimulatie**.

   [![De pagina Azure IoT-oplossingsverbeteringen.](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Voer het abonnement in waar u de apparaatsimulatie wilt maken.

   Parameter|Beschrijving
   ---|---
   **Naam van implementatie** | Deze unieke waarde wordt gebruikt om een nieuwe resourcegroep te maken. De vermelde Azure-resources worden gemaakt en toegewezen aan de resourcegroep.
   **Azure-abonnement** | Geef hetzelfde abonnement op dat is gebruikt voor het maken van uw Azure Time Series Insights Gen2-omgeving in de vorige sectie.
   **Implementatieopties** | Selecteer **Nieuwe IoT-hub inrichten** om een nieuwe IoT-hub te maken specifiek voor deze zelfstudie.
   **Azure-locatie** | Geef dezelfde regio op die is gebruikt voor het maken van uw Azure Time Series Insights Gen2-omgeving in de vorige sectie.

   Wanneer u klaar bent, selecteert u **Maken** om de Azure-resources van de oplossing in te richten. Het kan tot 20 minuten duren voordat dit proces is voltooid.

   [![De apparaatsimulatieoplossing inrichten.](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Nadat het inrichten is voltooid, worden er twee meldingen weergegeven waarin staat dat de implementatiestatus is gewijzigd van **Inrichten** naar **Gereed**.

   >[!IMPORTANT]
   > Open uw oplossingsverbetering nog niet. Laat deze webpagina geopend, u gaat deze later weer gebruiken.

   [![Het inrichten van de apparaatsimulatieoplossing is voltooid.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Inspecteer nu de zojuist gemaakte resources in de Azure-portal. Op de pagina **Resourcegroepen** van de portal ziet u dat er een nieuwe resourcegroep is gemaakt met behulp van de **Oplossingsnaam** in de vorige stap. Noteer de resources die zijn gemaakt voor de simulatie van het apparaat.

   [![Resources voor apparaatsimulatie.](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Een Azure Time Series Insights Gen2-omgeving maken

In deze sectie wordt beschreven hoe u een Azure Time Series Insights Gen2-omgeving maakt en deze verbindt met de IoT-hub die is gemaakt met de IoT-oplossingsverbetering via de [Azure-portal](https://portal.azure.com/).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-abonnementsaccount.
1. Selecteer linksboven **+ Een resource maken**.
1. Selecteer de categorie **Internet of Things** en vervolgens **Time Series Insights**.

   [![Selecteer de Time Series Insights-omgevingsresource.](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Stel in het deelvenster **Time Series Insights-omgeving maken** op het tabblad **Basisbeginselen** de volgende parameters in:

    | Parameter | Bewerking |
    | --- | ---|
    | **Omgevingsnaam** | Voer een unieke naam in voor de Azure Time Series Insights Gen2-omgeving. |
    | **Abonnement** | Voer het abonnement in waarin u uw Azure Time Series Insights Gen2-omgeving wilt maken. Het is een best practice om hetzelfde abonnement te gebruiken als voor de rest van de IoT-resources die door de apparaatsimulator worden gemaakt. |
    | **Resourcegroep** | Selecteer een bestaande resourcegroep voor de Azure Time Series Insights Gen2-omgevingsresource of maak een nieuwe. Een resourcegroep is een container voor Azure-resources. Het is een best practice om dezelfde resourcegroep te gebruiken als voor de andere IoT-resources die door de apparaatsimulator worden gemaakt. |
    | **Locatie** | Selecteer een datacentrumregio voor uw Azure Time Series Insights Gen2-omgeving. Om extra latentie te voorkomen, kunt u het beste uw Azure Time Series Insights Gen2-omgeving maken in dezelfde regio als uw IoT-hub die door de apparaatsimulator is gemaakt. |
    | **Laag** |  Selecteer **Gen2(L1)** . Dit is de SKU voor het Azure Time Series Insights Gen2-product. |
    | **Eigenschapsnaam van tijdreeks-id** | Voer een naam in van een eigenschap die waarden bevat waarmee uw tijdreeks-exemplaren uniek worden geïdentificeerd. De waarde die u invoert in het vak **Eigenschapsnaam** als tijdsreeks-id kan later niet worden gewijzigd. Voor deze zelfstudie gebruiken we **_iothub-connection-device-id_* _. Lees [Best practices voor het kiezen van een tijdreeks-id](./how-to-select-tsid.md) voor meer informatie over de Time Series-id, waaronder een samengestelde tijdsreeks-id. |
    | _ *Naam van opslagaccount** | Voer een wereldwijd unieke naam voor een nieuw opslagaccount in.|
    | **Opslagaccounttype** | Selecteer de opslagtype voor een nieuw opslagaccount. We raden StorageV2 aan|
    | **Replicatie van opslagaccount** | Selecteer de opslagtype voor een nieuw opslagaccount. Op basis van uw locatieselectie kunt u kiezen uit LRS, GRS en ZRS. Voor deze zelfstudie kunt u LRS selecteren|
    | **Hiërarchische naamruimte** |Deze optie kan worden geselecteerd wanneer u het opslagtype selecteert dat StorageV2 moet worden. Dit is standaard uitgeschakeld. Voor deze zelfstudie kunt u de standaardinstelling *uitgeschakeld* laten|
    |**Semi-dynamische opslag inschakelen**|Selecteer **Ja** om semi-dynamische opslag in te schakelen. Deze instelling kan worden uitgeschakeld en opnieuw worden ingeschakeld nadat de omgeving is gemaakt. |
    |**Gegevensretentie (in dagen)**|Kies de standaardoptie van 7 dagen. |

    [![Nieuwe configuratie van Time Series Insights-omgeving.](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. Selecteer **Volgende: Bron van gebeurtenis**.

   [![Configureer de Time Series-id voor de omgeving.](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Stel op het tabblad **Gebeurtenisbron** de volgende parameters in:

   | Parameter | Bewerking |
   | --- | --- |
   | **Een gebeurtenisbron maken** | Selecteer **Ja**.|
   | **Naam** | Voer een unieke waarde in voor de naam van de gebeurtenisbron. |
   | **Brontype** | Selecteer **IoT Hub**. |
   | **Een hub selecteren** | Kies **Bestaande selecteren**. |
   | **Abonnement** | Selecteer het abonnement dat u hebt gebruikt voor de apparaatsimulator. |
   | **Naam van de IoT Hub** | Selecteer de naam van de IoT-hub die u hebt gebruikt voor de apparaatsimulator. |
   | **IoT Hub-toegangsbeleid** | Selecteer **iothubowner**. |
   | **IoT Hub-consumentengroep** | Selecteer **Nieuw**, voer een unieke naam in en selecteer vervolgens **+ Toevoegen**. De consumentengroep moet een unieke waarde zijn in Azure Time Series Insights Gen2. |
   | **Timestamp-eigenschap** | Deze waarde wordt gebruikt om de eigenschap **Timestamp** te identificeren in uw binnenkomende telemetriegegevens. Laat dit vak leeg voor deze zelfstudie. In deze simulator wordt de binnenkomende timestamp van IoT Hub gebruikt waarop Azure Time Series Insights Gen2 standaard is ingesteld. |

1. Selecteer **Controleren + maken**.

   [![Configureer de gemaakte IoT-hub als een gebeurtenisbron.](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Selecteer **Maken**.

    [![De pagina Controleren + maken met de knop Maken.](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    U kunt de status van uw implementatie bekijken:

    [![Melding dat de implementatie is voltooid.](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. U hebt standaard toegang tot uw Azure Time Series Insights Gen2-omgeving als u eigenaar bent van het Azure-abonnement. Controleer of u toegang hebt:

   1. Zoek uw resourcegroep en selecteer uw net gemaakte Azure Time Series Insights Gen2-omgeving.

      [![Selecteer en bekijk uw omgeving.](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Selecteer op de pagina Azure Time Series Insights Gen2 de optie **Beleid voor gegevenstoegang**:

      [![Controleer het beleid voor gegevenstoegang.](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Controleer of uw referenties worden vermeld:

      Als uw referenties niet worden weergegeven, moet u uzelf toestemming geven voor toegang tot de omgeving door Toevoegen te selecteren en te zoeken naar uw referenties. Raadpleeg [Gegevenstoegang verlenen](./concepts-access-policies.md) voor meer informatie over het instellen van machtigingen.

## <a name="stream-data"></a>Gegevens streamen

Nu u uw Azure Time Series Insights Gen2-omgeving hebt geïmplementeerd, begint u met gegevens streamen voor analyse.

1. Ga terug naar het dashboard [Oplossingsverbeteringen](https://www.azureiotsolutions.com/Accelerators#dashboard). Meld u indien nodig opnieuw aan met hetzelfde Azure-account dat u in deze zelfstudie hebt gebruikt. Selecteer uw apparaatoplossing en vervolgens **Naar uw oplossingsverbetering gaan** om uw geïmplementeerde oplossing te starten.

   [![Dashboard Oplossingsverbeteringen.](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. De web-app voor apparaatsimulatie begint met de vraag of u de webtoepassing de machtiging **Aanmelden en uw profiel lezen** wilt verlenen. Met deze machtiging kan de toepassing de gegevens van het gebruikersprofiel ophalen die nodig zijn om de werking van de toepassing te ondersteunen.

   [![Toestemming voor webtoepassing voor apparaatsimulatie.](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Selecteer **+ Nieuwe simulatie**.

    1. Nadat de pagina **Simulatie-instellingen** is geladen, voert u de vereiste parameters in.

        | Parameter | Bewerking |
        | --- | --- |
        | **Naam** | Voer een unieke naam voor een simulator in. |
        | **Beschrijving** | Voer een definitie in. |
        | **Simulatieduur** | Stel de simulatieduur in op **Voor onbepaalde tijd uitvoeren**. |
        | **Apparaatmodel** | Klik op + **Een apparaattype toevoegen** <br />**Naam**: Voer **Lift** in. <br />**Aantal**: Voer **3** in. <br /> Behoud de overige standaardwaarden |
        | **Doel-IoT-hub** | Stel **Vooraf ingerichte IoT-hub gebruiken** in. |

        [![Parameters configureren en starten.](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    1. Selecteer **Simulatie starten**. In het dashboard van de apparaatsimulatie worden **Actieve apparaten** en **Totaal berichten** weergegeven.

        [![Dashboard van Azure IoT-simulatie.](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Gegevens analyseren

In deze sectie voert u een eenvoudige analyse uit op uw tijdreeksgegevens met de [verkenner van Azure Time Series Insights Gen2](./concepts-ux-panels.md).

1. Ga naar de verkenner van Azure Time Series Insights Gen2 door de URL te selecteren op de resourcepagina in [Azure Portal](https://portal.azure.com/).

    [![De verkenner van Azure Time Series Insights Gen2-URL.](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. In de verkenner van Azure Time Series Insights Gen2 wordt een balk weergegeven die de bovenkant van het scherm beslaat. Dit is de beschikbaarheidskiezer. Zorg ervoor dat er ten minste twee zijn geselecteerd en vouw, indien nodig, het tijdsbestek uit door de kiezeruiteinden naar links en rechts te slepen.

1. **Time Series-exemplaren** worden aan de linkerkant weergegeven.

    [![Lijst van niet-bovenliggende exemplaren.](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Selecteer het eerste tijdreeksexemplaar. Selecteer vervolgens **Temperatuur weergeven**.

    [![Geselecteerd tijdreeksexemplaar met menuopdracht om de gemiddelde temperatuur weer te geven.](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Er wordt een tijdreeksgrafiek weergegeven. Wijzig het **Interval** in **30 s**.

1. Herhaal de vorige stap met de andere twee tijdreeksexemplaren, zodat alle drie worden weergegeven, zoals in deze grafiek:

    [![Grafiek met alle tijdreeksen.](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Selecteer de tijdsduurkiezer in de rechterbovenhoek. Hier kunt u specifieke begin- en eindtijden tot op de milliseconde selecteren of kiezen uit vooraf geconfigureerde opties als **Afgelopen 30 minuten**. U kunt ook de standaard tijdzone wijzigen.

    [![Stel het tijdsbereik in op de afgelopen 30 minuten.](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    De voortgang van de oplossingsverbetering voor de **Afgelopen 30 minuten** wordt nu weergegeven in de verkenner van Azure Time Series Insights Gen2.

## <a name="define-and-apply-a-model"></a>Een model definiëren en toepassen

In dit gedeelte past u een model toe om uw gegevens te structureren. U definieert typen, hiërarchieën en exemplaren om het model te voltooien. Lees [Time Series-model](./concepts-model-overview.md) voor meer informatie over gegevensmodellen.

1. Selecteer het tabblad **Model** in de verkenner:

   [![Het tabblad Model in de verkenner weergeven.](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Selecteer **+ Toevoegen** op het tabblad **Typen**.

1. Voer de volgende parameters in:

    | Parameter | Bewerking |
    | --- | ---|
    | **Naam** | Voer **Lift** in |
    | **Beschrijving** | Voer **Dit is een typedefinitie van Lift** in |

1. Selecteer vervolgens het tabblad **Variabelen**.

    1. Selecteer **+ Variabele toevoegen** en vul de volgende waarden in voor de eerste variabele van het type Lift. U maakt drie variabelen in totaal.

        | Parameter | Bewerking |
        | --- | --- |
        | **Naam** | Voer **Gemiddelde temperatuur** in. |
        | **Soort** | Selecteer **Numeriek** |
        | **Waarde** | Selecteer uit de voorinstellingen: Selecteer **temperatuur (dubbel)** . <br /> Opmerking: Het kan een paar minuten duren voordat **Waarde** wordt ingevuld nadat Azure Time Series Insights Gen2 gebeurtenissen begint te ontvangen.|
        | **Aggregatiebewerking** | Vouw **Geavanceerde opties** uit. <br /> Selecteer **GEMIDDELDE**. |

    1. Selecteer **Toepassen**. Selecteer daarna opnieuw **+ Variabele toevoegen** en stel de volgende waarden in:

        | Parameter | Bewerking |
        | --- | --- |
        | **Naam** | Voer **Gem. trillingen** in. |
        | **Soort** | Selecteer **Numeriek** |
        | **Waarde** | Selecteer uit de voorinstellingen: Selecteer **trillingen (dubbel)** . <br /> Opmerking: Het kan een paar minuten duren voordat **Waarde** wordt ingevuld nadat Azure Time Series Insights Gen2 gebeurtenissen begint te ontvangen.|
        | **Aggregatiebewerking** | Vouw **Geavanceerde opties** uit. <br /> Selecteer **GEMIDDELDE**. |

    1. Selecteer **Toepassen**. Selecteer daarna opnieuw **+ Variabele toevoegen** en stel de volgende waarden in voor de derde en de laatste variabele:

        | Parameter | Bewerking |
        | --- | --- |
        | **Naam** | Voer **Etage** in. |
        | **Soort** | Selecteer **Categorisch** |
        | **Waarde** | Selecteer uit de voorinstellingen: Selecteer **Etage (dubbel)** . <br /> Opmerking: Het kan een paar minuten duren voordat **Waarde** wordt ingevuld nadat Azure Time Series Insights Gen2 gebeurtenissen begint te ontvangen.|
        | **Categorieën** | <span style="text-decoration: underline">Label </span>  - <span style="text-decoration: underline">Waarden</span> <br /> Onderste: 1,2,3,4 <br /> Middelste: 5,6,7,8,9 <br /> Bovenste: 10,11,12,13,14,15 |
        | **Standaard categorie** | Voer **Onbekend** in |

        [![Voeg typevariabelen toe.](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    1. Selecteer **Toepassen**. 
    1. Selecteer **Opslaan**. Er worden drie variabelen gemaakt en weergegeven.

        [![Nadat u het type hebt toegevoegd, bekijkt u het in de weergave Model.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Selecteer het tabblad **Hiërarchieën**. Selecteer vervolgens **+ Toevoegen**.

   1. Stel in het deelvenster **Hiërarchie bewerken** de volgende parameters in:

        | Parameter | Bewerking |
        | --- | ---|
        | **Naam** | Voer **Locatiehiërarchie** in. |
        |**Niveaus**| Voer **Land** in als de naam van het eerste niveau <br /> Selecteer **+ Niveau toevoegen** <br /> Voer **Plaats** in voor het tweede niveau en selecteer vervolgens **+ Niveau toevoegen** <br /> Voer **Gebouw** in als de naam van het derde en laatste niveau |

   1. Selecteer **Opslaan**.

        [![Geef uw nieuwe hiërarchie weer in de weergave Model.](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigeer naar **Exemplaren**.

    1. Selecteer onder **Acties** helemaal rechts het potloodpictogram om het eerste exemplaar te bewerken met de volgende waarden:

        | Parameter | Bewerking |
        | --- | --- |
        | **Type** | Selecteer **Lift**. |
        | **Naam** | Voer **Lift 1** in|
        | **Beschrijving** | Voer **Exemplaar voor Lift 1** in |

    1. Navigeer naar de **Exemplaar-velden** en voer de volgende waarden in:

        | Parameter | Bewerking |
        | --- | --- |
        | **Hiërarchieën** | Selecteer **Locatiehiërarchie** |
        | **Land** | Voer **USA** in |
        | **Plaats** | Voer **Seattle** in |
        | **Bouwen** | Voer **Space Needle** in |

    1. Selecteer **Opslaan**.

1. Herhaal de vorige stap met de andere twee exemplaren terwijl u de volgende waarden gebruikt:

    **Voor Lift 2:**

    | Parameter | Bewerking |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Voer **Lift 2** in|
    | **Beschrijving** | Voer **Exemplaar voor Lift 2** in |
    | **Hiërarchieën** | Selecteer **Locatiehiërarchie** |
    | **Land** | Voer **USA** in |
    | **Plaats** | Voer **Seattle** in |
    | **Bouwen** | Voer **Pacific Science Center** in |

    **Voor Lift 3:**

    | Parameter | Bewerking |
    | --- | --- |
    | **Type** | Selecteer **Lift**. |
    | **Naam** | Voer **Lift 3** in|
    | **Beschrijving** | Voer **Exemplaar voor Lift 3** in |
    | **Hiërarchieën** | Selecteer **Locatiehiërarchie** |
    | **Land** | Voer **USA** in |
    | **Plaats** | Voer **New York** in |
    | **Bouwen** | Voer **Empire State Building** in |

    [![De bijgewerkte exemplaren weergeven.](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Ga terug naar het tabblad **Analyseren** om het deelvenster voor de grafiek weer te geven. Vouw onder **Locatiehiërarchie** alle hiërarchieniveaus uit om de tijdreeksexemplaren weer te geven:

    [![Alle hiërarchieën weergeven in de grafiekweergave.](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Onder **Pacific Science Center** selecteert u het tijdreeksexemplaar **Lift 2** en selecteert u vervolgens **Gemiddelde temperatuur weergeven**.

1. Selecteer voor hetzelfde exemplaar, **Lift 2**, de optie **Etage weergeven**.

    Met uw categorische variabele kunt u bepalen hoeveel tijd de lift op de bovenste, onderste en middelste etages was.

    [![Lift 2 visualiseren met hiërarchie en gegevens.](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Resources opschonen

Nu u de zelfstudie hebt voltooid, kunt u de resources die u hebt gemaakt opschonen:

1. Selecteer in het linkermenu in de [Azure-portal](https://portal.azure.com) de optie **Alle resources** en zoek uw Azure Time Series Insights Gen2-resourcegroep.
1. U kunt de hele resourcegroep (en alle resources erin) verwijderen door **Verwijderen** te selecteren of elke resource afzonderlijk verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:  

* Een verbetering voor apparaatsimulatie maken en gebruiken.
* Een Azure Time Series Insights Gen2 PAYG-omgeving maken.
* De Azure Time Series Insights Gen2-omgeving verbinden met een IoT Hub.
* Een voorbeeldoplossingsverbetering uitvoeren om gegevens te streamen naar de Azure Time Series Insights Gen2-omgeving.
* Een eenvoudige analyse uitvoeren van de gegevens.
* Een type Time Series Model en een hiërarchie definiëren en deze koppelen aan uw exemplaren.

Nu u weet hoe u een eigen Azure Time Series Gen2-omgeving maakt, is het tijd om meer te leren over de belangrijkste concepten in Azure Time Series Insights Gen2.

Meer informatie over Azure Time Series Insights Gen2-gegevensopname:

> [!div class="nextstepaction"]
> [Overzicht van Azure Time Series Insights Gen2-gegevensopname](./concepts-ingestion-overview.md)

Meer informatie over Azure Time Series Insights Gen2-opslag:

> [!div class="nextstepaction"]
> [Zelfstudie Azure Time Series Insights Gen2-gegevensopslag](./concepts-storage.md)

Meer informatie over Time Series-modellen:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2-gegevensmodellering](./concepts-model-overview.md)

Meer informatie over het verbinden van uw omgeving met Power BI:

> [!div class="nextstepaction"]
> [Gegevens uit Azure Time Series Insights Gen2 visualiseren in Power BI](./how-to-connect-power-bi.md)
