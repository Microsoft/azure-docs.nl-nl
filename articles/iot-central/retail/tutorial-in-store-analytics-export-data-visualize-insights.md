---
title: 'Zelfstudie: gegevens exporteren en inzichten visualiseren in Azure IoT Central'
description: In deze zelfstudie leert u hoe u gegevens exporteert uit IoT Central en inzichten visualiseert in een Power BI-dashboard.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: f00448f19cc0a2118477a9527005548fea25537e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187269"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Zelfstudie: Gegevens exporteren uit Azure IoT Central en inzichten visualiseren in Power BI



In de twee vorige zelfstudies hebt u een IoT Central-toepassing gemaakt en aangepast met behulp van de toepassingssjabloon **In-Store Analytics: betaling**. In deze zelfstudie configureert u uw IoT Central-toepassing voor het exporteren van telemetriegegevens die van de apparaten zijn verzameld. Vervolgens gebruikt u Power BI om een aangepast dashboard te maken waarmee de winkelmanager de inzichten kan visualiseren die zijn afgeleid van de telemetrie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een IoT Central-toepassing configureren voor het exporteren van telemetrie naar een Event Hub.
> * Logic Apps gebruiken om gegevens te verzenden van een Event Hub naar een streaming-gegevensset van Power BI.
> * Een Power BI-dashboard maken om gegevens in de streaming-gegevensset te visualiseren.

## <a name="prerequisites"></a>Vereisten

Voor deze zelfstudie hebt u het volgende nodig:

* De twee vorige zelfstudies in de reeks moeten zijn voltooid, te weten [Een in-store analytics-toepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) en [Het operatordashboard aanpassen en apparaten beheren in Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* Een Power BI-account. Als u geen Power BI-account hebt, meldt u zich aan voor een [gratis Power BI Pro-proefabonnement](https://app.powerbi.com/signupredirect?pbi_source=web) voordat u begint.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een Event Hub en logische app maakt, moet u een resourcegroep maken om deze items te beheren. De resourcegroep moet zich op dezelfde locatie bevinden als de IoT Central-toepassing **In-Store Analytics: betaling**. Een resourcegroep maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Resourcegroepen** in het linkernavigatievenster. Selecteer vervolgens **Toevoegen**.
1. Selecteer bij **Abonnement** de naam van het Azure-abonnement waarin u de IoT Central-toepassing wilt maken.
1. Voer bij **Resourcegroep** de naam _retail-store-analysis_* in.
1. Selecteer bij **Regio** dezelfde regio die u hebt gekozen voor de IoT Central-toepassing.
1. Selecteer **Controleren + maken**.
1. Selecteer op de pagina **Controleren + maken** de optie **Maken**.

U hebt nu een resourcegroep met de naam **retail-store-analysis** in uw abonnement.

## <a name="create-an-event-hub"></a>Een Event Hub maken

Voordat u de toepassing voor het controleren van de winkel kunt configureren voor het exporteren van telemetrie, moet u een Event Hub maken om de geëxporteerde gegevens te ontvangen. In de volgende stappen wordt uitgelegd hoe u een Event Hub maakt:

1. Selecteer in Azure Portal **Een resource maken** linksboven in het scherm.
1. Typ _Event Hubs_ in het vak **Marketplace doorzoeken** en druk op **Enter**.
1. Selecteer **Maken** op de pagina **Event Hubs**.
1. Voer op de pagina **Naamruimte maken** de volgende stappen uit:
    * Voer een unieke naam in voor de naamruimte, zoals _uwnaam-retail-store-analysis_. Het systeem controleert of de naam beschikbaar is.
    * Kies de prijscategorie **Basis**.
    * Selecteer bij **Abonnement** het abonnement dat u hebt gebruikt om de IoT Central-toepassing te maken.
    * Selecteer de resourcegroep **retail-store-analysis**.
    * Selecteer de locatie die u ook hebt gebruikt voor de IoT Central-toepassing.
    * Selecteer **Maken**. U moet mogelijk een paar minuten wachten voordat het systeem de resources heeft ingericht.
1. Navigeer in de portal naar de resourcegroep **retail-store-analysis**. Wacht totdat de installatie is voltooid. Mogelijk moet u **Vernieuwen** selecteren om de implementatiestatus bij te werken. U kunt de status van het maken van de Event Hub-naamruimte ook controleren in **Meldingen**.
1. Selecteer in de resourcegroep **retail-store-analysis** de **Event Hubs-naamruimte**. U ziet de startpagina voor uw **Event Hubs-naamruimte** in de portal.

Nu u een **Event Hubs-naamruimte** hebt, kunt u een **Event Hub** maken voor gebruik met uw IoT Central-toepassing:

1. Selecteer op de startpagina voor uw **Event Hubs-naamruimte** in de portal de optie **+ Event Hub**.
1. Voer op de pagina **Event Hub maken** _store-telemetry_ in als de naam en selecteer vervolgens **Maken**.

U hebt nu een Event Hub die u kunt gebruiken bij het configureren van gegevensexport vanuit uw IoT Central-toepassing:

![Event Hub](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Gegevensexport configureren

Nu u een Event Hub hebt, kunt u uw toepassing **In-Store Analytics: betaling** configureren voor het exporteren van telemetrie van de verbonden apparaten. De volgende stappen laten zien hoe u dit doet:

1. Meld u aan bij de IoT Central-toepassing **In-Store Analytics: betaling**.
1. Selecteer **Gegevensexport** in het linkerdeelvenster.
1. Selecteer **Nieuw > Azure Event Hubs**.
1. Voer _Telemetry export_ in als de **weergavenaam**.
1. Selecteer uw **Event Hubs-naamruimte**.
1. Selecteer de Event Hub **store-telemetry**.
1. Schakel **Apparaten** en **Apparaatsjablonen** uit in het gedeelte **Te exporteren gegevens**.
1. Selecteer **Opslaan**.

Het kan enkele minuten duren voordat er met de gegevensexport telemetrie wordt verzonden naar uw Event Hub. U kunt de status van de export zien op de pagina **Gegevensexport**:

![Configuratie voor continue gegevensexport](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>De Power BI-gegevenssets maken

In het Power BI-dashboard worden gegevens weergegeven uit uw toepassing voor winkelbewaking. In deze oplossing gebruikt u streaming-gegevenssets van Power BI als de gegevensbron voor het Power BI-dashboard. In dit gedeelte definieert u het schema van de streaming-gegevenssets, zodat de logische app gegevens vanaf de Event Hub kan doorsturen. De volgende stappen laten zien hoe u twee streaming-gegevenssets maakt voor de omgevingssensoren plus één streaming-gegevensset voor de aanwezigheidssensor:

1. Meld u aan bij uw **Power BI**-account.
1. Selecteer **Werkruimten** en selecteer vervolgens **Een werkruimte maken**.
1. Geef op de pagina **Een werkruimte maken** _In-Store Analytics: betaling_ op voor **Werkruimtenaam**.
1. Ga naar onderen op de pagina **Welkom bij de werkruimte In-Store Analytics: betaling** en selecteer **Overslaan**.
1. Selecteer **Maken > Streaming-gegevensset** op de pagina van de werkruimte.
1. Kies op de pagina **Nieuwe streaming-gegevensset** **API** en selecteer vervolgens **Volgende**.
1. Voer _Zone 1 sensor_ in als de **naam van de gegevensset**.
1. Voer de drie **Waarden van de gegevensstroom** in de volgende tabel in:

    | Waardenaam  | Waardetype |
    | ----------- | ---------- |
    | Tijdstempel   | DateTime   |
    | Vochtigheid    | Aantal     |
    | Temperatuur | Aantal     |

1. Zet **Analyse van historische gegevens** op Aan.
1. Selecteer **Maken** en vervolgens **Gereed**.
1. Maak een andere streaming-gegevensset met de naam **Zone 2 sensor** met hetzelfde schema en dezelfde instellingen als de streaming-gegevensset **Zone 1 sensor**.

U hebt nu twee streaming-gegevenssets. De logische app stuurt telemetrie van de twee omgevingssensoren die zijn verbonden met uw toepassing **In-Store Analytics: betaling** naar deze twee gegevenssets:

![Zone-gegevenssets](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Deze oplossing gebruikt één streaming-gegevensset voor elke sensor omdat het niet mogelijk is om filters toe te passen op streaminggegevens in Power BI.

U hebt ook een streaming-gegevensset nodig voor de telemetrie van de aanwezigheid:

1. Selecteer **Maken > Streaming-gegevensset** op de pagina van de werkruimte.
1. Kies op de pagina **Nieuwe streaming-gegevensset** **API** en selecteer vervolgens **Volgende**.
1. Voer _Occupancy sensor_ in als de **naam van de gegevensset**.
1. Voer de vijf **Waarden van de gegevensstroom** in de volgende tabel in:

    | Waardenaam     | Waardetype |
    | -------------- | ---------- |
    | Tijdstempel      | DateTime   |
    | Queue Length 1 | Aantal     |
    | Queue Length 2 | Aantal     |
    | Dwell Time 1   | Aantal     |
    | Dwell Time 2   | Aantal     |

1. Zet **Analyse van historische gegevens** op Aan.
1. Selecteer **Maken** en vervolgens **Gereed**.

U hebt nu een derde streaming-gegevensset waarin waarden van de gesimuleerde aanwezigheidssensor worden opgeslagen. Deze sensor rapporteert de rijlengte voor de twee kassa's in de winkel en hoe lang klanten in deze rijen staan te wachten:

![Gegevensset over aanwezigheid](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Een logische app maken

In deze oplossing leest de logische app telemetrie van de Event Hub, parseert de gegevens en verzendt deze vervolgens naar de streaming-gegevenssets van Power BI die u hebt gemaakt.

Voordat u de logische app maakt, hebt u de apparaat-id's nodig van de twee RuuviTag-sensoren die u hebt verbonden met uw IoT Central-toepassing in de zelfstudie [Een in-store analytics-toepassing maken in Azure IoT Central](./tutorial-in-store-analytics-create-app.md):

1. Meld u aan bij de IoT Central-toepassing **In-Store Analytics: betaling**.
1. Selecteer **Apparaten** in het linkerdeelvenster. Selecteer vervolgens **RuuviTag**.
1. Noteer de **apparaat-id's**. In de volgende schermopname zijn de id's **f5dcf4ac32e8** en **e29ffc8d5326**:

    ![Apparaat-id's](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

In de volgende stappen ziet u hoe u de logische app maakt in Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Een resource maken** linksboven in het scherm.
1. Typ _Logic App_ in het vak **Marketplace doorzoeken** en druk op **Enter**.
1. Selecteer op de pagina **Logische app** de optie **Maken**.
1. Ga als volgt te werk op de pagina **Logische app**:
    * Voer een unieke naam in voor de logische app, zoals _uwnaam-retail-store-analysis_.
    * Selecteer bij **Abonnement** het abonnement dat u hebt gebruikt om de IoT Central-toepassing te maken.
    * Selecteer de resourcegroep **retail-store-analysis**.
    * Selecteer de locatie die u ook hebt gebruikt voor de IoT Central-toepassing.
    * Selecteer **Maken**. U moet mogelijk een paar minuten wachten voordat het systeem de resources heeft ingericht.
1. Navigeer in Azure Portal naar de nieuwe logische app.
1. Scrol omlaag op de pagina **Ontwerper van logische apps** en selecteer **Lege logische app**.
1. Typ _Event Hubs_ in **Connectors en triggers doorzoeken**.
1. Selecteer **Wanneer gebeurtenissen beschikbaar zijn in Event Hub** bij **Triggers**.
1. Voer _Store telemetry_ in bij **Verbindingsnaam** en selecteer uw **Event Hubs-naamruimte**.
1. Selecteer het beleid **RootManageSharedAccess** en selecteer **Maken**.
1. Ga als volgt te werk in de actie **Wanneer gebeurtenissen beschikbaar zijn in Event Hub**:
    * Selecteer **store-telemetry** bij **Event Hub-naam**.
    * Selecteer **application/json** bij **Inhoudstype**.
    * Stel **Interval** in op drie en **Frequentie** op seconden.
1. Selecteer **Opslaan** om uw logische app op te slaan.

Als u de logica wilt toevoegen aan het ontwerp van de logische app, selecteert u **Codeweergave**:

1. Vervang `"actions": {},` door de volgende JSON. Vervang de twee tijdelijke aanduidingen `[YOUR RUUVITAG DEVICE ID 1]` en `[YOUR RUUVITAG DEVICE ID 2]` door de id's die u hebt genoteerd voor de twee RuuviTag-apparaten:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Selecteer **Opslaan** en vervolgens **Ontwerper** om de visuele versie te zien van de logica die u hebt toegevoegd:

    ![Ontwerp van logische app](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Selecteer **Switch by DeviceID** om de actie uit te vouwen. Selecteer vervolgens **Zone 1 environment** en daarna **Een actie toevoegen**.
1. Typ **Power BI** in **Connectors en acties doorzoeken** en druk op **Enter**.
1. Selecteer de actie **Add rows to a dataset (preview)** .
1. Selecteer **Aanmelden** en volg de aanwijzingen om u aan te melden bij uw Power BI-account.
1. Als het aanmeldingsproces is voltooid, gaat u als volgt te werk in de actie **Add rows to a dataset**:
    * Selecteer **In-Store Analytics: betaling** als de werkruimte.
    * Selecteer **Zone 1 sensor** als de gegevensset.
    * Selecteer **RealTimeData** als de tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel**, **Vochtigheid** en **Temperatuur**.
    * Selecteer het veld **Tijdstempel** en selecteer vervolgens **x-opt-enqueuedtime** in de lijst **Dynamische inhoud**.
    * Selecteer het veld **Vochtigheid** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **Vochtigheid**.
    * Selecteer het veld **Temperatuur** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **Temperatuur**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De actie **Zone 1 environment** ziet eruit als in de volgende schermopname: ![Zone 1 environment](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Selecteer de actie **Zone 2 environment** en daarna **Een actie toevoegen**.
1. Typ **Power BI** in **Connectors en acties doorzoeken** en druk op **Enter**.
1. Selecteer de actie **Add rows to a dataset (preview)** .
1. Ga als volgt te werk in de actie **Add rows to a dataset 2**:
    * Selecteer **In-Store Analytics: betaling** als de werkruimte.
    * Selecteer **Zone 2 sensor** als de gegevensset.
    * Selecteer **RealTimeData** als de tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel**, **Vochtigheid** en **Temperatuur**.
    * Selecteer het veld **Tijdstempel** en selecteer vervolgens **x-opt-enqueuedtime** in de lijst **Dynamische inhoud**.
    * Selecteer het veld **Vochtigheid** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **Vochtigheid**.
    * Selecteer het veld **Temperatuur** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **Temperatuur**.
    Selecteer **Opslaan** om uw wijzigingen op te slaan.  De actie **Zone 2 environment** ziet eruit als in de volgende schermopname: ![Zone 2 environment](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Selecteer de actie **Occupancy** en selecteer vervolgens de actie **Switch by Interface ID**.
1. Selecteer de actie **Dwell Time interface** en selecteer **Een actie toevoegen**.
1. Typ **Power BI** in **Connectors en acties doorzoeken** en druk op **Enter**.
1. Selecteer de actie **Add rows to a dataset (preview)** .
1. Ga als volgt te werk in de actie **Add rows to a dataset**:
    * Selecteer **In-Store Analytics: betaling** als de werkruimte.
    * Selecteer **Occupancy Sensor** als de gegevensset.
    * Selecteer **RealTimeData** als de tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel**, **Dwell Time 1** en **Dwell Time 2**.
    * Selecteer het veld **Tijdstempel** en selecteer vervolgens **x-opt-enqueuedtime** in de lijst **Dynamische inhoud**.
    * Selecteer het veld **Dwell Time 1** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **DwellTime1**.
    * Selecteer het veld **Dwell Time 2** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **DwellTime2**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De actie **Dwell Time interface** ziet eruit als in de volgende schermopname: ![Schermopname met de actie "Dwell Time interface".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Selecteer de actie **People Count interface** en selecteer **Een actie toevoegen**.
1. Typ **Power BI** in **Connectors en acties doorzoeken** en druk op **Enter**.
1. Selecteer de actie **Add rows to a dataset (preview)** .
1. Ga als volgt te werk in de actie **Add rows to a dataset**:
    * Selecteer **In-Store Analytics: betaling** als de werkruimte.
    * Selecteer **Occupancy Sensor** als de gegevensset.
    * Selecteer **RealTimeData** als de tabel.
    * Selecteer **Nieuwe parameter toevoegen** en selecteer vervolgens de velden **Tijdstempel**, **Queue Length 1** en **Queue Length 2**.
    * Selecteer het veld **Tijdstempel** en selecteer vervolgens **x-opt-enqueuedtime** in de lijst **Dynamische inhoud**.
    * Selecteer het veld **Queue Length 1** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **count1**.
    * Selecteer het veld **Queue Length 2** en selecteer vervolgens **Meer weergeven** naast **Telemetrie parseren**. Selecteer vervolgens **count2**.
    * Selecteer **Opslaan** om uw wijzigingen op te slaan. De actie **People Count interface** ziet eruit als in de volgende schermopname: ![Actie Occupancy](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

De logische app wordt automatisch uitgevoerd. Als u de status van elke uitvoering wilt zien, gaat u naar de pagina **Overzicht** van de logische app in Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Een Power BI-dashboard maken

Er worden nu telemetriegegevens vanuit uw IoT Central-toepassing verzonden via uw Event Hub. Uw logische app parseert de Event Hub-berichten vervolgens en voegt deze toe aan een Power BI-streaming-gegevensset. U kunt nu een Power BI-dashboard gaan maken om de telemetrie te visualiseren:

1. Meld u aan bij uw **Power BI**-account.
1. Selecteer **Werkruimten > In-Store Analytics: betaling**.
1. Selecteer **Maken > Dashboard**.
1. Voer **Store analytics** in als de naam van het dashboard en selecteer **Maken**.

### <a name="add-line-charts"></a>Lijngrafieken toevoegen

Voeg vier titels voor lijngrafieken toe om de temperatuur en luchtvochtigheid van de twee omgevingssensoren weer te geven. Gebruik de gegevens in de volgende tabel om de tegels te maken: Als u een tegel wilt toevoegen, moet u eerst **...(Meer opties) > Tegel toevoegen** selecteren. Selecteer daarna **Aangepaste streaminggegevens** en **Volgende**:

| Instelling | Grafiek 1 | Grafiek 2 | Grafiek 3 | Grafiek 4 |
| ------- | -------- | -------- | -------- | -------- |
| Gegevensset | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| Visualisatietype | Lijndiagram | Lijndiagram | Lijndiagram | Lijndiagram |
| As | Tijdstempel | Tijdstempel | Tijdstempel | Tijdstempel |
| Waarden | Temperatuur | Vochtigheid | Temperatuur | Vochtigheid |
| Tijdvenster | 60 minuten | 60 minuten | 60 minuten | 60 minuten |
| Titel | Temperature (1 hour) | Humidity (1 hour) | Temperature (1 hour) | Humidity (1 hour) |
| Subtitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

In de volgende schermopname ziet u de instellingen voor de eerste grafiek:

![Lijndiagraminstellingen](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Kaarten toevoegen om omgevingsgegevens weer te geven

Voeg vier titels voor kaarten toe om de meest recente waarden voor de temperatuur en luchtvochtigheid van de twee omgevingssensoren weer te geven. Gebruik de gegevens in de volgende tabel om de tegels te maken: Als u een tegel wilt toevoegen, moet u eerst **...(Meer opties) > Tegel toevoegen** selecteren. Selecteer daarna **Aangepaste streaminggegevens** en **Volgende**:

| Instelling | Kaart 1 | Kaart 2 | Kaart 3 | Kaart 4 |
| ------- | ------- | ------- | ------- | ------- |
| Gegevensset | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| Visualisatietype | Kaart | Kaart | Kaart | Kaart |
| Velden | Temperatuur | Vochtigheid | Temperatuur | Vochtigheid |
| Titel | Temperature (F) | Vochtigheid (%) | Temperature (F) | Vochtigheid (%) |
| Subtitel | Zone 1 | Zone 1 | Zone 2 | Zone 2 |

In de volgende schermopname ziet u de instellingen voor de eerste kaart:

![Kaartinstellingen](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Tegels toevoegen voor het weergeven van aanwezigheidsgegevens bij het afrekenen

Voeg vier kaarttegels toe om de lengte van de rij aan te geven en de wachttijd voor de twee kassa's in de winkel. Gebruik de gegevens in de volgende tabel om de tegels te maken. Als u een tegel wilt toevoegen, moet u eerst **...(Meer opties) > Tegel toevoegen** selecteren. Selecteer daarna **Aangepaste streaminggegevens** en **Volgende**:

| Instelling | Kaart 1 | Kaart 2 | Kaart 3 | Kaart 4 |
| ------- | ------- | ------- | ------- | ------- |
| Gegevensset | Occupancy sensor | Occupancy sensor | Occupancy sensor | Occupancy sensor |
| Visualisatietype | Gegroepeerd kolomdiagram | Gegroepeerd kolomdiagram | Meter | Meter |
| As    | Tijdstempel | Tijdstempel | N.v.t. | N.v.t. |
| Waarde | Dwell Time 1 | Dwell Time 2 | Queue Length 1 | Queue Length 2 |
| Time window | 60 minuten | 60 minuten |  N.v.t. | N.v.t. |
| Titel | Dwell Time | Dwell Time | Wachtrijlengte | Wachtrijlengte |
| Subtitel | Checkout 1 | Checkout 2 | Checkout 1 | Checkout 2 |

Wijzig de grootte van de tegels in het dashboard en rangschik ze zodat ze eruitzien zoals in de volgende schermopname:

![Schermopname van het Power B I-dashboard met tegels waarvan het formaat en de positie is gewijzigd.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

U kunt een aantal grafische afbeeldingen toevoegen om het dashboard verder aan te passen:

![Power BI-dashboard](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met uw IoT Central-toepassing, kunt u deze verwijderen door u aan te melden bij de toepassing en naar de pagina **Toepassingsinstellingen** in het gedeelte **Beheer** te gaan.

Als u de toepassing wilt blijven gebruiken, maar de bijbehorende kosten wilt verlagen, schakelt u de gegevensexport uit waarmee telemetrie naar de Event Hub wordt verzonden.

U kunt de Event Hub en de logische app verwijderen in Azure Portal door de resourcegroep met de naam **retail-store-analysis** te verwijderen.

U kunt de Power BI-gegevenssets en het dashboard verwijderen door de werkruimte te verwijderen via de pagina Power BI-instellingen voor de werkruimte.

## <a name="next-steps"></a>Volgende stappen

In deze drie zelfstudies hebt u een end-to-end oplossing gezien die gebruikmaakt van de toepassingssjabloon **In-Store Analytics: betaling**. U hebt apparaten verbonden met de toepassing, IoT Central gebruikt om de apparaten te bewaken en Power BI gebruikt om een dashboard te maken voor het visualiseren van inzichten op basis van de telemetrie van de apparaten. Een aanbevolen volgende stap is om een van de andere toepassingssjablonen van IoT Central te verkennen:

> [!div class="nextstepaction"]
> * [Oplossingen voor de energiesector bouwen met IoT Central](../energy/overview-iot-central-energy.md)
> * [Oplossingen voor de overheid bouwen met IoT Central](../government/overview-iot-central-government.md)
> * [Oplossingen voor de gezondheidszorg bouwen met IoT Central](../healthcare/overview-iot-central-healthcare.md)
