---
title: Een Azure IoT hub klonen
description: Een Azure IoT hub klonen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: robinsh
ms.openlocfilehash: 370ea2f16632ae18142f0770742e5a52d3cabae0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92151649"
---
# <a name="how-to-clone-an-azure-iot-hub-to-another-region"></a>Een Azure IoT hub klonen naar een andere regio

In dit artikel worden manieren besproken om een IoT Hub te klonen en vindt u enkele vragen die u moet beantwoorden voordat u begint. Hier volgen enkele redenen waarom u een IoT-hub wilt klonen:
 
* U verplaatst uw bedrijf van de ene regio naar een andere, zoals van Europa naar Noord-Amerika (of andersom), en u wilt dat uw resources en gegevens geografisch dicht bij uw nieuwe locatie worden geplaatst, zodat u uw hub moet verplaatsen.

* U stelt een hub in voor een ontwikkeling versus productie omgeving.

* U wilt een aangepaste implementatie van hoge Beschik baarheid met meerdere hubs uitvoeren. Zie de [sectie over het behalen van een kruis regio ha van IOT hub hoge Beschik baarheid en herstel na nood gevallen](iot-hub-ha-dr.md#achieve-cross-region-ha)voor meer informatie.

* U wilt het aantal [partities](iot-hub-scaling.md#partitions) verhogen dat voor uw hub is geconfigureerd. Deze instelling wordt ingesteld wanneer u uw hub voor het eerst maakt en kan niet worden gewijzigd. U kunt de informatie in dit artikel gebruiken om uw hub te klonen en wanneer de kloon wordt gemaakt, het aantal partities verg Roten.

Als u een hub wilt klonen, moet u een abonnement hebben met beheerders toegang tot de oorspronkelijke hub. U kunt de nieuwe hub in een nieuwe resource groep en regio plaatsen, in hetzelfde abonnement als de oorspronkelijke hub, of zelfs in een nieuw abonnement. U hoeft niet dezelfde naam te gebruiken omdat de naam van de hub globaal uniek moet zijn.

> [!NOTE]
> Op dit moment is er geen functie beschikbaar om automatisch een IoT-hub te klonen. Het is hoofd zakelijk een hand matig proces en is dus tamelijk fout gevoelig. De complexiteit van het klonen van een hub ligt direct in verhouding tot de complexiteit van de hub. Het klonen van een IoT-hub zonder bericht routering is bijvoorbeeld redelijk eenvoudig. Als u bericht routering als één complexiteit toevoegt, wordt het klonen van de hub ten minste een orde van grootte ingewik kelder. Als u ook de resources verplaatst die worden gebruikt voor de route ring van eind punten, is het een andere volg orde van magniture ingewik kelder. 

## <a name="things-to-consider"></a>Aandachtspunten

Er zijn verschillende zaken waarmee u rekening moet houden voordat u een IoT-hub kloont.

* Zorg ervoor dat alle beschik bare functies op de oorspronkelijke locatie ook beschikbaar zijn op de nieuwe locatie. Sommige services zijn beschikbaar als preview-versie en niet alle functies zijn overal verkrijgbaar.

* Verwijder de oorspronkelijke resources niet voordat u de gekloonde versie maakt en verifieert. Als u een hub verwijdert, is deze niet langer beschikbaar en is er geen manier om deze te herstellen om de instellingen of gegevens te controleren om ervoor te zorgen dat de hub correct wordt gerepliceerd.

* Voor veel resources zijn wereld wijd unieke namen vereist. u moet dus verschillende namen gebruiken voor de gekloonde versies. U moet ook een andere naam gebruiken voor de resource groep waartoe de gekloonde hub behoort. 

* De gegevens voor de oorspronkelijke IoT-hub worden niet gemigreerd. Dit omvat telemetrie-berichten, Cloud-naar-apparaat-opdrachten (C2D) en taak gerelateerde informatie, zoals planningen en geschiedenis. Metrische gegevens en logboek registratie resultaten worden ook niet gemigreerd. 

* Voor gegevens of berichten die naar Azure Storage worden doorgestuurd, kunt u de gegevens in het oorspronkelijke opslag account laten staan, deze gegevens overdragen naar een nieuw opslag account in de nieuwe regio, of de oude gegevens op de bestaande plaats laten staan en een nieuw opslag account maken op de nieuwe locatie voor de nieuwe gegevens. Zie [aan de slag met AzCopy](../storage/common/storage-use-azcopy-v10.md)voor meer informatie over het verplaatsen van gegevens in Blob Storage.

* Gegevens voor Event Hubs en voor Service Bus onderwerpen en wacht rijen kunnen niet worden gemigreerd. Dit zijn tijdgebonden gegevens en worden niet opgeslagen na het verwerken van de berichten.

* U moet de downtime voor de migratie plannen. Het klonen van de apparaten naar de nieuwe hub kost tijd. Als u de import/export-methode gebruikt, heeft Bench Mark tests aangetoond dat het ongeveer twee uur kan duren om 500.000 apparaten te verplaatsen, en vier uur om een miljoen apparaten te verplaatsen. 

* U kunt de apparaten naar de nieuwe hub kopiëren zonder de apparaten af te sluiten of te wijzigen. 

    * Als de apparaten oorspronkelijk zijn ingericht met DPS, worden de verbindings gegevens die zijn opgeslagen op elk apparaat opnieuw ingericht. 
    
    * Anders moet u de import/export-methode gebruiken om de apparaten te verplaatsen, waarna de apparaten moeten worden gewijzigd om de nieuwe hub te gebruiken. U kunt bijvoorbeeld instellen dat uw apparaat de IoT Hub hostnaam gebruikt van de dubbele gewenste eigenschappen. Het apparaat neemt IoT Hub hostnaam, verbreekt het apparaat van de oude hub en verbindt het opnieuw met het nieuwe.
    
* U moet de certificaten bijwerken die u gebruikt, zodat u deze kunt gebruiken met de nieuwe resources. U hebt de hub waarschijnlijk ook in een DNS-tabel gedefinieerd, maar u moet deze DNS-informatie bijwerken.

## <a name="methodology"></a>Methodologie

Dit is de algemene methode die wordt aanbevolen voor het verplaatsen van een IoT-hub van de ene regio naar een andere. Voor bericht routering, wordt ervan uitgegaan dat de resources niet worden verplaatst naar de nieuwe regio. Zie de [sectie over bericht routering](#how-to-handle-message-routing)voor meer informatie.

   1. De hub en de instellingen exporteren naar een resource manager-sjabloon. 
   
   1. Breng de benodigde wijzigingen aan in de sjabloon, zoals het bijwerken van alle exemplaren van de naam en de locatie voor de gekloonde hub. Voor alle resources in de sjabloon die worden gebruikt voor eind punten van berichten routering, werkt u de sleutel in de sjabloon voor die resource bij.
   
   1. Importeer de sjabloon in een nieuwe resource groep op de nieuwe locatie. Hiermee maakt u de kloon.

   1. Fouten opsporen als dat nodig is. 
   
   1. Voeg iets toe dat niet naar de sjabloon is geëxporteerd. 
   
       Consumenten groepen worden bijvoorbeeld niet geëxporteerd naar de sjabloon. U moet de Consumer groepen hand matig toevoegen aan de sjabloon of de [Azure Portal](https://portal.azure.com) gebruiken nadat de hub is gemaakt. Er is een voor beeld van het toevoegen van één consumer groep aan een sjabloon in het artikel [een Azure Resource Manager sjabloon gebruiken om IOT hub bericht routering te configureren](tutorial-routing-config-message-routing-rm-template.md).
       
   1. Kopieer de apparaten van de oorspronkelijke hub naar de kloon. Dit wordt behandeld in de sectie [het beheren van de apparaten die zijn geregistreerd bij de IOT hub](#managing-the-devices-registered-to-the-iot-hub).

## <a name="how-to-handle-message-routing"></a>Bericht routering afhandelen

Als uw hub gebruikmaakt van [aangepaste route ring](iot-hub-devguide-messages-read-custom.md), is het exporteren van de sjabloon voor de hub de routerings configuratie, maar bevat het niet zelf de resources zelf. U moet kiezen of u de routerings resources wilt verplaatsen naar de nieuwe locatie of dat u ze wilt laten staan en ze wilt blijven gebruiken. 

Stel bijvoorbeeld dat u een hub in West US hebt die berichten routert naar een opslag account (ook in VS-West) en u de hub wilt verplaatsen naar VS-Oost. U kunt de hub verplaatsen en er nog steeds berichten naar het opslag account in VS-West sturen, of u kunt de hub verplaatsen en ook het opslag account verplaatsen. Het kan zijn dat er weinig prestaties zijn van het routeren van berichten naar eindpunt resources in een andere regio.

U kunt een hub die gebruikmaakt van bericht routering eenvoudig verplaatsen als u de resources die worden gebruikt voor de eind punten van de route ring niet ook verplaatst. 

Als de hub gebruikmaakt van bericht routering, hebt u twee opties. 

1. Verplaats de resources die worden gebruikt voor de Routing-eind punten naar de nieuwe locatie.

    * U moet de nieuwe resources zelf hand matig maken in het [Azure Portal](https://portal.azure.com) of via het gebruik van Resource Manager-sjablonen. 

    * U moet de naam van alle resources wijzigen wanneer u deze op de nieuwe locatie maakt, omdat deze globaal unieke namen hebben. 
     
    * Voordat u de nieuwe hub maakt, moet u de resource namen en bron sleutels bijwerken in de sjabloon van de nieuwe hub. De resources moeten aanwezig zijn wanneer de nieuwe hub wordt gemaakt.

1. Verplaats de resources die worden gebruikt voor de Routing-eind punten niet. Gebruik deze ' op locatie '.

   * In de stap waarin u de sjabloon bewerkt, moet u de sleutels voor elke routerings resource ophalen en in de sjabloon plaatsen voordat u de nieuwe hub maakt. 

   * De hub verwijst nog steeds naar de oorspronkelijke routerings resources en stuurt berichten naar deze bronnen zoals ze zijn geconfigureerd.

   * U krijgt een klein prestatie niveau omdat de hub en de Routing-eindpunt resources zich niet op dezelfde locatie bevinden.

## <a name="prepare-to-migrate-the-hub-to-another-region"></a>De migratie van de hub naar een andere regio voorbereiden

Deze sectie bevat specifieke instructies voor het migreren van de hub.

### <a name="find-the-original-hub-and-export-it-to-a-resource-template"></a>Zoek de oorspronkelijke hub en exporteer deze naar een resource sjabloon.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 

1. Ga naar **resource groepen** en selecteer de resource groep die de hub bevat die u wilt verplaatsen. U kunt ook naar **resources** gaan en de hub op die manier vinden. Selecteer de hub.

1. Selecteer **sjabloon exporteren** in de lijst met eigenschappen en instellingen voor de hub. 

   ![Scherm afbeelding met de opdracht voor het exporteren van de sjabloon voor de IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-export-template.png)

1. Selecteer **downloaden** om de sjabloon te downloaden. Sla het bestand ergens op om het opnieuw te vinden. 

   ![Scherm afbeelding met de opdracht voor het downloaden van de sjabloon voor de IoT Hub.](./media/iot-hub-how-to-clone/iot-hub-download-template.png)

### <a name="view-the-template"></a>De sjabloon weer geven 

1. Ga naar de map met Down loads (of naar de map die u hebt gebruikt tijdens het exporteren van de sjabloon) en zoek het zip-bestand. Open het zip-bestand en zoek het bestand met de naam `template.json` . Selecteer deze en selecteer vervolgens CTRL + C om de sjabloon te kopiëren. Ga naar een andere map die zich niet in het zip-bestand bevindt en plak het bestand (CTRL + V). Nu kunt u deze bewerken.
 
    Het volgende voor beeld is voor een generieke hub zonder routerings configuratie. Het is een hub met S1-laag (met 1 eenheid) met de naam **ContosoTestHub29358** in de regio **westus**. Dit is de geëxporteerde sjabloon.

    ``` json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Devices/IotHubs",
                "apiVersion": "2018-04-01",
                "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
                "location": "westus",
                "sku": {
                    "name": "S1",
                    "tier": "Standard",
                    "capacity": 1
                },
                "properties": {
                    "operationsMonitoringProperties": {
                        "events": {
                            "None": "None",
                            "Connections": "None",
                            "DeviceTelemetry": "None",
                            "C2DCommands": "None",
                            "DeviceIdentityOperations": "None",
                            "FileUploadOperations": "None",
                            "Routes": "None"
                        }
                    },
                    "ipFilterRules": [],
                    "eventHubEndpoints": {
                        "events": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        },
                        "operationsMonitoringEvents": {
                            "retentionTimeInDays": 1,
                            "partitionCount": 2,
                            "partitionIds": [
                                "0",
                                "1"
                            ],
                            "path": "contosotesthub29358-operationmonitoring",
                            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
                        }
                    },
                    "routing": {
                        "endpoints": {
                            "serviceBusQueues": [],
                            "serviceBusTopics": [],
                            "eventHubs": [],
                            "storageContainers": []
                        },
                        "routes": [],
                        "fallbackRoute": {
                            "name": "$fallback",
                            "source": "DeviceMessages",
                            "condition": "true",
                            "endpointNames": [
                                "events"
                            ],
                            "isEnabled": true
                        }
                    },
                    "storageEndpoints": {
                        "$default": {
                            "sasTtlAsIso8601": "PT1H",
                            "connectionString": "",
                            "containerName": ""
                        }
                    },
                    "messagingEndpoints": {
                        "fileNotifications": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "enableFileUploadNotifications": false,
                    "cloudToDevice": {
                        "maxDeliveryCount": 10,
                        "defaultTtlAsIso8601": "PT1H",
                        "feedback": {
                            "lockDurationAsIso8601": "PT1M",
                            "ttlAsIso8601": "PT1H",
                            "maxDeliveryCount": 10
                        }
                    },
                    "features": "None"
                }
            }
        ]
    }
    ```

### <a name="edit-the-template"></a>De sjabloon bewerken 

U moet wijzigingen aanbrengen voordat u de sjabloon kunt gebruiken om de nieuwe hub in de nieuwe regio te maken. Gebruik [VS code](https://code.visualstudio.com) of een tekst editor om de sjabloon te bewerken.

#### <a name="edit-the-hub-name-and-location"></a>De naam en locatie van de hub bewerken

1. Verwijder de sectie para meters bovenaan. het is veel eenvoudiger om alleen de naam van de hub te gebruiken omdat we niet meerdere para meters hebben. 

    ``` json
        "parameters": {
            "IotHubs_ContosoTestHub29358_name": {
                "defaultValue": "ContosoTestHub29358",
                "type": "String"
            }
        },
    ```

1. Wijzig de naam zodat deze de werkelijke (nieuwe) naam gebruikt in plaats van deze op te halen uit een para meter (die u in de vorige stap hebt verwijderd). 

    Voor de nieuwe hub gebruikt u de naam van de oorspronkelijke hub plus de teken reeks *kloon* om de nieuwe naam op te nemen. Begin door de naam en locatie van de hub op te schonen.
    
    Oude versie:

    ``` json 
    "name": "[parameters('IotHubs_ContosoTestHub29358_name')]",
    "location": "westus",
    ```
    
    Nieuwe versie: 

    ``` json 
    "name": "ContosoTestHub29358clone",
    "location": "eastus",
    ```

    Vervolgens ziet u dat de waarden voor het **pad** de oude naam van de hub bevatten. Wijzig deze voor gebruik van de nieuwe. Dit zijn de padgegevens onder **eventHubEndpoints** - **gebeurtenissen** en **OperationsMonitoringEvents**.

    Wanneer u klaar bent, ziet de sectie Event Hub endpoints er als volgt uit:

    ``` json
    "eventHubEndpoints": {
        "events": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        },
        "operationsMonitoringEvents": {
            "retentionTimeInDays": 1,
            "partitionCount": 2,
            "partitionIds": [
                "0",
                "1"
            ],
            "path": "contosotesthub29358clone-operationmonitoring",
            "endpoint": "sb://iothub-ns-contosotes-2227755-92aefc8b73.servicebus.windows.net/"
        }
    ```

#### <a name="update-the-keys-for-the-routing-resources-that-are-not-being-moved"></a>De sleutels bijwerken voor de routerings resources die niet worden verplaatst

Wanneer u de Resource Manager-sjabloon exporteert voor een hub waarvoor route ring is geconfigureerd, ziet u dat de sleutels voor deze resources niet zijn opgegeven in de geëxporteerde sjabloon. hun locatie wordt aangeduid met sterretjes. U moet deze invullen in door naar deze resources in de portal te gaan en de sleutels op te halen **voordat** u de sjabloon van de nieuwe hub importeert en de hub maakt. 

1. De vereiste sleutels voor een van de routerings resources ophalen en in de sjabloon opnemen. U kunt de sleutel (s) ophalen uit de bron in de [Azure Portal](https://portal.azure.com). 

   Als u bijvoorbeeld berichten naar een opslag container routert, zoek dan het opslag account in de portal. Selecteer in de sectie instellingen de optie **toegangs sleutels** en kopieer een van de sleutels. De sleutel ziet er als volgt uit wanneer u de sjabloon voor het eerst exporteert:

   ```json
   "connectionString": "DefaultEndpointsProtocol=https;
   AccountName=fabrikamstorage1234;AccountKey=****",
   "containerName": "fabrikamresults",
   ```

1. Nadat u de account sleutel voor het opslag account hebt opgehaald, plaatst u deze in de sjabloon in de-component in `AccountKey=****` de plaats van de sterretjes. 

1. Voor service bus-wacht rijen haalt u de gedeelde toegangs sleutel op die overeenkomt met de SharedAccessKeyName. Dit zijn de sleutel en `SharedAccessKeyName` in de JSON:

   ```json
   "connectionString": "Endpoint=sb://fabrikamsbnamespace1234.servicebus.windows.net:5671/;
   SharedAccessKeyName=iothubroutes_FabrikamResources;
   SharedAccessKey=****;
   EntityPath=fabrikamsbqueue1234",
   ```

1. Hetzelfde geldt voor de Service Bus-onderwerpen en Event hub-verbindingen.

#### <a name="create-the-new-routing-resources-in-the-new-location"></a>Nieuwe routerings resources maken op de nieuwe locatie

Deze sectie is alleen van toepassing als u de resources verplaatst die door de hub worden gebruikt voor de eind punten van de route ring.

Als u de routerings resources wilt verplaatsen, moet u de resources hand matig instellen op de nieuwe locatie. U kunt de routerings resources maken met behulp van de [Azure Portal](https://portal.azure.com), of door de Resource Manager-sjabloon te exporteren voor elk van de resources die worden gebruikt door het bericht routering, deze te bewerken en te importeren. Nadat de resources zijn ingesteld, kunt u de sjabloon van de hub (die de routerings configuratie bevat) importeren.

1. Maak elke resource die wordt gebruikt door het bewerkings plan. U kunt dit hand matig doen met behulp van de [Azure Portal](https://portal.azure.com)of de resources maken met Resource Manager-sjablonen. Als u sjablonen wilt gebruiken, volgt u de volgende stappen:

    1. Voor elke resource die wordt gebruikt door de route ring, exporteert u deze naar een resource manager-sjabloon.
    
    1. Werk de naam en de locatie van de resource bij. 

    1. Eventuele kruis verwijzingen tussen de resources bijwerken. Als u bijvoorbeeld een sjabloon voor een nieuw opslag account maakt, moet u de naam van het opslag account in die sjabloon en alle andere sjablonen die ernaar verwijzen, bijwerken. In de meeste gevallen is de sectie route ring in de sjabloon voor de hub de enige andere sjabloon die verwijst naar de bron. 

    1. Importeer elk van de sjablonen, waarmee elke resource wordt geïmplementeerd.

    Zodra de resources die door het bewerkings plan worden gebruikt, zijn ingesteld en worden uitgevoerd, kunt u door gaan.

1. Wijzig in de sjabloon voor de IoT-hub de naam van elk van de routerings bronnen in de nieuwe naam en werk de locatie zo nodig bij. 

U hebt nu een sjabloon waarmee een nieuwe hub wordt gemaakt die bijna precies zo lijkt op de oude hub, afhankelijk van hoe u de route ring wilt afhandelen.

## <a name="move----create-the-new-hub-in-the-new-region-by-loading-the-template"></a>Verplaatsen: Maak de nieuwe hub in de nieuwe regio door de sjabloon te laden

Maak de nieuwe hub op de nieuwe locatie met behulp van de sjabloon. Als u routerings resources hebt die u wilt verplaatsen, moeten de resources worden ingesteld op de nieuwe locatie en moeten de verwijzingen in de sjabloon worden bijgewerkt. Als u de routerings resources niet verplaatst, moeten ze zich in de sjabloon bevinden met de bijgewerkte sleutels.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Selecteer **Een resource maken**. 

1. In het zoekvak plaatst u ' sjabloon implementatie ' en selecteert u ENTER.

1. **Sjabloon implementatie selecteren (implementeren met aangepaste sjablonen)**. Hiermee gaat u naar een scherm voor de Sjabloonimlementatie. Selecteer **Maken**. U ziet dit scherm:

   ![Scherm afbeelding met de opdracht voor het maken van uw eigen sjabloon](./media/iot-hub-how-to-clone/iot-hub-custom-deployment.png)

1. Selecteer **uw eigen sjabloon bouwen in de editor**, waarmee u uw sjabloon kunt uploaden vanuit een bestand. 

1. Selecteer **bestand laden**. 

   ![Scherm opname van de opdracht voor het uploaden van een sjabloon bestand](./media/iot-hub-how-to-clone/iot-hub-upload-file.png)

1. Blader naar de nieuwe sjabloon die u hebt bewerkt en selecteer deze en selecteer vervolgens **openen**. De sjabloon wordt geladen in het bewerkings venster. Selecteer **Opslaan**. 

   ![Scherm opname van het laden van de sjabloon](./media/iot-hub-how-to-clone/iot-hub-loading-template.png)

1. Vul de volgende velden in.

   **Abonnement**: Selecteer het abonnement dat u wilt gebruiken.

   **Resource groep**: Maak een nieuwe resource groep op een nieuwe locatie. Als u al een nieuwe versie hebt ingesteld, kunt u deze selecteren in plaats van een nieuwe te maken.

   **Locatie**: als u een bestaande resource groep hebt geselecteerd, wordt deze ingevuld zodat u de locatie van de resource groep kunt vergelijken. Als u een nieuwe resource groep hebt gemaakt, is dit de locatie.

   **Ik ga akkoord met selectie vakjes**: Dit betekent dat u akkoord gaat om te betalen voor de resource (s) die u maakt.

1. Selecteer de knop **kopen** .

De portal valideert nu uw sjabloon en implementeert uw gekloonde hub. Als u routerings configuratie gegevens hebt, wordt deze opgenomen in de nieuwe hub, maar wordt de resource op de voor gaande locatie aangeleverd.

## <a name="managing-the-devices-registered-to-the-iot-hub"></a>De apparaten beheren die zijn geregistreerd bij de IoT hub

Nu u uw kloon actief hebt, moet u alle apparaten van de oorspronkelijke hub kopiëren naar de kloon. 

Er zijn meerdere manieren om dit te bereiken. U hebt de [Device Provisioning Service (DPS)](../iot-dps/about-iot-dps.md)al gebruikt om de apparaten in te richten, of u hebt er geen gedaan. Als u dit hebt gedaan, is dit niet lastig. Als dat niet het geval is, kan dit zeer gecompliceerd zijn. 

Als u DPS niet hebt gebruikt voor het inrichten van uw apparaten, kunt u de volgende sectie overs Laan en beginnen met het [gebruik van importeren/exporteren om de apparaten te verplaatsen naar de nieuwe hub](#using-import-export-to-move-the-devices-to-the-new-hub).

## <a name="using-dps-to-re-provision-the-devices-in-the-new-hub"></a>DPS gebruiken om de apparaten opnieuw in te richten in de nieuwe hub

Zie [apparaten opnieuw inrichten](../iot-dps/how-to-reprovision.md)als u DPS wilt gebruiken om de apparaten te verplaatsen naar de nieuwe locatie. Wanneer u klaar bent, kunt u de apparaten weer geven in de [Azure Portal](https://portal.azure.com) en controleren of ze zich op de nieuwe locatie bevinden.

Ga naar de nieuwe hub met behulp van de [Azure Portal](https://portal.azure.com). Selecteer uw hub en selecteer **IOT-apparaten**. U ziet de apparaten die opnieuw zijn ingericht voor de gekloonde hub. U kunt ook de eigenschappen voor de gekloonde hub weer geven. 

Als u route ring hebt geïmplementeerd, test u en controleert u of uw berichten correct naar de bronnen worden doorgestuurd.

### <a name="committing-the-changes-after-using-dps"></a>Het door voeren van de wijzigingen na het gebruik van DPS

Deze wijziging is doorgevoerd door de DPS-service.

### <a name="rolling-back-the-changes-after-using-dps"></a>De wijzigingen worden teruggedraaid na het gebruik van DPS. 

Als u de wijzigingen wilt terugdraaien, moet u de apparaten opnieuw inrichten van de nieuwe hub naar de oude.

U bent nu klaar met het migreren van uw hub en de bijbehorende apparaten. U kunt overs Laan om de [opschoon bewerking uit](#clean-up)te scha kelen.

## <a name="using-import-export-to-move-the-devices-to-the-new-hub"></a>Import-Export gebruiken om de apparaten naar de nieuwe hub te verplaatsen

De toepassing streeft naar .NET core, zodat u deze kunt uitvoeren op Windows of Linux. U kunt het voor beeld downloaden, de verbindings reeksen ophalen, de vlaggen instellen voor de bits die u wilt uitvoeren en deze uitvoeren. U kunt dit doen zonder de code te openen.

### <a name="downloading-the-sample"></a>Het voor beeld downloaden

1. Gebruik de IoT C#-voor beelden op deze pagina: [Azure IOT-voor beelden voor C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/). Down load het zip-bestand en pak het uit op uw computer. 

1. De relevante code bevindt zich in./iot-hub/Samples/service/ImportExportDevicesSample. U hoeft de code niet te bekijken of te bewerken om de toepassing te kunnen uitvoeren.

1. Als u de toepassing wilt uitvoeren, geeft u drie verbindings reeksen en vijf opties op. U geeft deze gegevens op als opdracht regel argumenten of u kunt een combi natie van beide gebruiken. De opties in als opdracht regel argumenten en de verbindings reeksen worden door gegeven als omgevings variabelen. 

   De reden hiervoor is dat de verbindings reeksen lang en ongewenst zijn en waarschijnlijk niet worden gewijzigd, maar dat u de opties wilt wijzigen en de toepassing meerdere keren kunt uitvoeren. Als u de waarde van een omgevings variabele wilt wijzigen, moet u het opdracht venster en Visual Studio of VS-code sluiten, al naar gelang u deze gebruikt. 

### <a name="options"></a>Opties

Hier volgen de vijf opties die u opgeeft wanneer u de toepassing uitvoert. We zetten deze in een minuut op de opdracht regel.

*   **addDevices** (argument 1): Stel dit in op True als u virtuele apparaten wilt toevoegen die voor u worden gegenereerd. Deze worden toegevoegd aan de bron-hub. Stel ook **numToAdd** (argument 2) in om op te geven hoeveel apparaten u wilt toevoegen. Het maximum aantal apparaten dat u kunt registreren op een hub is 1.000.000. Het doel van deze optie is voor testen: u kunt een specifiek aantal apparaten genereren en deze vervolgens naar een andere hub kopiëren.

*   **copyDevices** (argument 3): Stel dit in op True om de apparaten van de ene hub naar een andere te kopiëren. 

*   **deleteSourceDevices** (argument 4): Stel dit in op True om alle apparaten te verwijderen die zijn geregistreerd op de bron-hub. We raden u aan om te wachten totdat u zeker weet dat alle apparaten zijn overgedragen voordat u dit uitvoert. Wanneer u de apparaten verwijdert, kunt u ze niet meer ophalen.

*   **deleteDestDevices** (argument 5): Stel dit in op True om alle apparaten te verwijderen die zijn geregistreerd op de doel-hub (de kloon). U kunt dit doen als u de apparaten meer dan één keer wilt kopiëren. 

De Basic-opdracht is *DotNet run* : Hiermee wordt aan .net door gegeven dat het lokale csproj-bestand moet worden gemaakt en uitgevoerd. U voegt uw opdracht regel argumenten toe aan het einde voordat u deze uitvoert. 

De opdracht regel ziet er als volgt uit:

``` console 
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub, or delete them. 
    // The first argument is true, numToAdd is 50, and the other arguments are false.
    dotnet run true 1000 false false false 

    // Copy the devices you just added to the other hub; don't delete anything.
    // The first argument is false, numToAdd is 0, copy-devices is true, and the delete arguments are both false
    dotnet run false 0 true false false 
```

### <a name="using-environment-variables-for-the-connection-strings"></a>Omgevings variabelen gebruiken voor de verbindings reeksen

1. Als u het voor beeld wilt uitvoeren, hebt u de verbindings reeksen nodig voor de oude en nieuwe IoT-hubs en naar een opslag account dat u kunt gebruiken voor tijdelijke werk bestanden. De waarden voor deze worden opgeslagen in omgevings variabelen.

1. Als u de connection string waarden wilt ophalen, meldt u zich aan bij de [Azure Portal](https://portal.azure.com). 

1. Plaats de verbindings reeksen ergens anders kunt u deze ophalen, zoals Klad blok. Als u het volgende kopieert, kunt u de verbindings reeksen in direct plakken waar ze naartoe gaan. Voeg geen spaties toe rondom het gelijkteken of wijzigt de naam van de variabele. Bovendien hoeft u geen dubbele aanhalings tekens te plaatsen rond de verbindings reeksen. Als u een aanhalings tekens plaatst rondom het opslag account connection string, werkt het niet.

   Voor Windows stelt u de omgevings variabelen in:

   ``` console  
   SET IOTHUB_CONN_STRING=<put connection string to original IoT Hub here>
   SET DEST_IOTHUB_CONN_STRING=<put connection string to destination or clone IoT Hub here>
   SET STORAGE_ACCT_CONN_STRING=<put connection string to the storage account here>
   ```
 
   Voor Linux definieert u de omgevings variabelen:

   ``` console  
   export IOTHUB_CONN_STRING="<put connection string to original IoT Hub here>"
   export DEST_IOTHUB_CONN_STRING="<put connection string to destination or clone IoT Hub here>"
   export STORAGE_ACCT_CONN_STRING="<put connection string to the storage account here>"
   ```

1. Voor de IoT hub-verbindings reeksen, gaat u naar elke hub in de portal. U kunt zoeken in **resources** voor de hub. Als u de resource groep kent, gaat u naar **resource groepen**, selecteert u de resource groep en selecteert u vervolgens de hub in de lijst met assets in die resource groep. 

1. Selecteer **beleid voor gedeelde toegang** via de instellingen voor de hub en selecteer vervolgens **iothubowner** en kopieer een van de verbindings reeksen. Doe hetzelfde voor de doel-hub. Voeg deze toe aan de juiste SET-opdrachten.

1. Zoek voor het opslag account connection string het opslag account in **resources** of onder de bijbehorende **resource groep** en open het. 
   
1. Selecteer in de sectie instellingen de optie **toegangs sleutels** en kopieer een van de verbindings reeksen. Plaats de connection string in het tekst bestand voor de juiste SET-opdracht. 

Nu hebt u de omgevings variabelen in een bestand met de SET-opdrachten en weet u wat uw opdracht regel argumenten zijn. Laten we het voor beeld uitvoeren.

### <a name="running-the-sample-application-and-using-command-line-arguments"></a>De voorbeeld toepassing uitvoeren en opdracht regel argumenten gebruiken

1. Open een opdrachtpromptvenster. Selecteer Windows en typ in `command prompt` om het opdracht prompt venster te openen.

1. Kopieer de opdrachten die de omgevings variabelen in één keer instellen en plak ze in het opdracht prompt venster en selecteer ENTER. Wanneer u klaar bent, typt u `SET` in het opdracht prompt venster om de omgevings variabelen en de waarden ervan weer te geven. Nadat u deze hebt gekopieerd naar het opdracht prompt venster, hoeft u deze niet opnieuw te kopiëren, tenzij u een nieuw opdracht prompt venster opent.

1. Wijzig in het opdracht prompt venster de mappen totdat u zich in./ImportExportDevicesSample (waarbij het bestand ImportExportDevicesSample. csproj bestaat). Typ vervolgens het volgende en voeg uw opdracht regel argumenten toe.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
    dotnet run arg1 arg2 arg3 arg4 arg5
    ```

    De DotNet opdracht bouwt en voert de toepassing uit. Omdat u de opties doorgeeft tijdens het uitvoeren van de toepassing, kunt u de waarden ervan wijzigen telkens wanneer u de toepassing uitvoert. U kunt de app bijvoorbeeld één keer uitvoeren en nieuwe apparaten maken, deze opnieuw uitvoeren en deze apparaten naar een nieuwe hub kopiëren, enzovoort. U kunt ook alle stappen in dezelfde uitvoering uitvoeren, maar we raden u aan geen apparaten te verwijderen totdat u zeker weet dat u het klonen hebt voltooid. Hier volgt een voor beeld waarmee 1000-apparaten worden gemaakt en vervolgens naar de andere hub worden gekopieerd.

    ``` console
    // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices

    // Add 1000 devices, don't copy them to the other hub or delete them. 
    dotnet run true 1000 false false false 

    // Do not add any devices. Copy the ones you just created to the other hub; don't delete anything.
    dotnet run false 0 true false false 
    ```

    Nadat u hebt gecontroleerd of de apparaten zijn gekopieerd, kunt u de apparaten uit de bron-hub als volgt verwijderen:

   ``` console
   // Format: dotnet run add-devices num-to-add copy-devices delete-source-devices delete-destination-devices
   // Delete the devices from the source hub.
   dotnet run false 0 false true false 
   ```

### <a name="running-the-sample-application-using-visual-studio"></a>De voorbeeld toepassing uitvoeren met Visual Studio

1. Als u de toepassing in Visual Studio wilt uitvoeren, wijzigt u de huidige map in de map waarin het bestand IoTHubServiceSamples. SLN zich bevindt. Voer vervolgens deze opdracht uit in het opdracht prompt venster om de oplossing te openen in Visual Studio. U moet dit doen in hetzelfde opdracht venster waarin u de omgevings variabelen instelt, zodat deze variabelen bekend zijn.

   ``` console       
   IoTHubServiceSamples.sln
   ```
    
1. Klik met de rechter muisknop op het project *ImportExportDevicesSample* en selecteer **instellen als opstart project**.    
    
1. Stel de variabelen boven aan Program. cs in de map ImportExportDevicesSample voor de vijf opties in.

   ``` csharp
   // Add randomly created devices to the source hub.
   private static bool addDevices = true;
   //If you ask to add devices, this will be the number added.
   private static int numToAdd = 0; 
   // Copy the devices from the source hub to the destination hub.
   private static bool copyDevices = false;
   // Delete all of the devices from the source hub. (It uses the IoTHubConnectionString).
   private static bool deleteSourceDevices = false;
   // Delete all of the devices from the destination hub. (Uses the DestIotHubConnectionString).
   private static bool deleteDestDevices = false;
   ```

1. Selecteer F5 om de toepassing uit te voeren. Nadat de uitvoering is voltooid, kunt u de resultaten bekijken.

### <a name="view-the-results"></a>De resultaten bekijken 

U kunt de apparaten weer geven in de [Azure Portal](https://portal.azure.com) en controleren of ze zich op de nieuwe locatie bevinden.

1. Ga naar de nieuwe hub met behulp van de [Azure Portal](https://portal.azure.com). Selecteer uw hub en selecteer **IOT-apparaten**. U ziet de apparaten die u zojuist hebt gekopieerd van de oude hub naar de gekloonde hub. U kunt ook de eigenschappen voor de gekloonde hub weer geven. 

1. Controleer op import/export-fouten door naar het Azure-opslag account in de [Azure Portal](https://portal.azure.com) te gaan en te zoeken in de `devicefiles` container voor `ImportErrors.log` . Als dit bestand leeg is (de grootte is 0), zijn er geen fouten opgetreden. Als u hetzelfde apparaat meer dan één keer probeert te importeren, wordt het apparaat de tweede keer geweigerd en wordt er een fout bericht aan het logboek bestand toegevoegd.

### <a name="committing-the-changes"></a>De wijzigingen worden doorgevoerd 

U hebt op dit moment de hub naar de nieuwe locatie gekopieerd en de apparaten naar de nieuwe kloon gemigreerd. Nu moet u wijzigingen aanbrengen zodat de apparaten met de gekloonde hub werken.

Als u de wijzigingen wilt door voeren, volgt u de stappen die u moet uitvoeren: 

* Werk elk apparaat bij om de naam van de IoT Hub-host te wijzigen zodat de naam van de IoT Hub-host naar de nieuwe hub verwijst. U moet dit doen met dezelfde methode die u hebt gebruikt wanneer u het apparaat voor het eerst hebt ingericht.

* Wijzig alle toepassingen die naar de oude hub verwijzen om naar de nieuwe hub te verwijzen.

* Wanneer u klaar bent, moet de nieuwe hub actief zijn. De oude hub mag geen actieve apparaten hebben en de status niet verbonden hebben. 

### <a name="rolling-back-the-changes"></a>De wijzigingen worden teruggedraaid

Als u besluit de wijzigingen terug te draaien, kunt u de volgende stappen uitvoeren:

* Werk elk apparaat bij om de IoT Hub hostnaam te wijzigen zodat de IoT Hub hostnaam voor de oude hub wordt verwijzen. U moet dit doen met dezelfde methode die u hebt gebruikt wanneer u het apparaat voor het eerst hebt ingericht.

* Wijzig alle toepassingen die naar de nieuwe hub verwijzen om naar de oude hub te verwijzen. Als u bijvoorbeeld Azure Analytics gebruikt, moet u mogelijk uw [Azure stream Analytics-invoer](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)opnieuw configureren.

* Verwijder de nieuwe hub. 

* Als u routerings resources hebt, moet de configuratie op de oude hub nog steeds verwijzen naar de juiste routerings configuratie, en moet u met deze resources werken nadat de hub opnieuw is opgestart.

### <a name="checking-the-results"></a>De resultaten controleren 

Als u de resultaten wilt controleren, wijzigt u uw IoT-oplossing zodat deze naar uw hub verwijst op de nieuwe locatie en voert u deze uit. Met andere woorden, Voer dezelfde acties uit met de nieuwe hub die u hebt uitgevoerd met de vorige hub en controleer of ze goed werken. 

Als u route ring hebt geïmplementeerd, test u en controleert u of uw berichten correct naar de bronnen worden doorgestuurd.

## <a name="clean-up"></a>Opschonen

U kunt niet opschonen totdat u zeker weet dat de nieuwe hub actief is en de apparaten goed werken. Zorg er ook voor dat u de route ring test als u deze functie gebruikt. Wanneer u klaar bent, kunt u de oude bronnen opschonen door de volgende stappen uit te voeren:

* Verwijder de oude hub als u dat nog niet hebt gedaan. Hiermee verwijdert u alle actieve apparaten van de hub.

* Als u routerings resources hebt die u naar de nieuwe locatie hebt verplaatst, kunt u de oude routerings bronnen verwijderen.

## <a name="next-steps"></a>Volgende stappen

U hebt een IoT-hub gekloond in een nieuwe hub in een nieuwe regio, compleet met de apparaten. Zie [IOT hub apparaat-id's in bulk importeren en exporteren](iot-hub-bulk-identity-mgmt.md)voor meer informatie over het uitvoeren van bulk bewerkingen op basis van het id-REGI ster in een IOT hub.

Raadpleeg de volgende artikelen voor meer informatie over het IoT Hub en de ontwikkeling van de hub.

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)

* [Zelf studie over IoT Hub route ring](tutorial-routing.md)

* [Overzicht van IoT Hub Apparaatbeheer](iot-hub-device-management-overview.md)

* Als u de voorbeeld toepassing wilt implementeren, raadpleegt u de [.net core-toepassings implementatie](/dotnet/core/deploying/index).