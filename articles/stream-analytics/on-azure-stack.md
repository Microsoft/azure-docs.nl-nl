---
title: Azure Stream Analytics uitvoeren op Azure Stack (preview-versie)
description: Maak een Azure Stream Analytics Edge-taak en implementeer deze via de IoT Edge runtime op Azure Stack hub.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 08/21/2020
ms.custom: seodec18
ms.openlocfilehash: 3463b3eae96c0a65206023ed0f21efe44294d4eb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020125"
---
# <a name="run-azure-stream-analytics-on-azure-stack-preview"></a>Azure Stream Analytics uitvoeren op Azure Stack (preview-versie)

> [!IMPORTANT]
> Deze functionaliteit is beschikbaar als preview-versie en wordt niet aanbevolen voor gebruik in de productie omgeving.

U kunt Azure Stream Analytics op Azure Stack hub uitvoeren als een IoT Edge-module. Er zijn configuratie-instellingen toegevoegd aan de IoT Edge-module, waardoor IT kan communiceren met Blob Storage, Event Hubs en IoT hubs die worden uitgevoerd in een Azure Stack hub-abonnement door aangepaste Url's te toestaan die in elke Azure Stack hub-werk nemer worden gevonden.

Met Stream Analytics op Azure Stack kunt u echte hybride architecturen maken voor de verwerking van streams in uw eigen persoonlijke, autonome Cloud. deze kan worden aangesloten of losgekoppeld met Cloud-native apps die gebruikmaken van consistente Azure-Services on-premises. 

Dit artikel laat u zien hoe u gegevens uit IoT Hub of event hub kunt streamen naar een andere Event hub of Blob Storage in een Azure Stack hub-abonnement en deze Stream Analytics verwerken.

## <a name="set-up-environments"></a>Omgevingen instellen

Azure Stream Analytics is een hybride service op Azure Stack hub. Het is een IoT Edge module die is geconfigureerd in azure, maar kan worden uitgevoerd op Azure Stack hub.  

Als u geen ervaring hebt met Azure Stack hub of IoT Edge, volgt u de onderstaande instructies om omgevingen in te stellen.

### <a name="prepare-the-azure-stack-hub-environment"></a>De Azure Stack hub-omgeving voorbereiden

Een Azure Stack hub-abonnement maken. Zie de [zelf studie voor het maken van een Azure stack hub-abonnement](/azure-stack/user/azure-stack-subscribe-services/) voor meer informatie.

Als u Azure Stack hub op uw eigen server wilt evalueren, kunt u de Azure Stack Development Kit (ASDK) gebruiken.  Zie het [overzicht van ASDK](/azure-stack/asdk/)voor meer informatie over de ASDK.

### <a name="install-the-iot-edge-runtime"></a>De IoT Edge-runtime installeren

Als u Azure Stream Analytics op Azure Stack hub wilt uitvoeren, moet uw apparaat over de IoT Edge runtime beschikken en moet het netwerk verbinding hebben met de Azure Stack hub of een VM zijn die wordt uitgevoerd op de Azure Stack hub. Met de IoT Edge runtime kunnen Stream Analytics Edge-taken worden geïntegreerd met Azure Storage en Azure-Event Hubs die worden uitgevoerd op uw Azure Stack hub. Zie [Azure stream Analytics op IOT Edge](stream-analytics-edge.md) voor meer informatie. 

Naast netwerk toegang tot de Azure Stack hub-resources, moet het IoT Edge apparaat (of virtuele machine) toegang hebben tot Azure IoT Hub in de open bare Azure-Cloud om de module Stream Analytics te configureren. 

In de volgende hand leidingen ziet u hoe u de IoT Edge runtime op uw apparaat of virtuele machine instelt:

* [De Azure IoT Edge-runtime op Windows installeren](../iot-edge/how-to-install-iot-edge.md)
* [Installeer de Azure IoT Edge runtime op op Debian gebaseerde Linux-systemen](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Een Azure Stream Analytics Edge-taak maken

ASA Edge-taken worden uitgevoerd in containers die zijn geïmplementeerd op Azure IoT Edge apparaten. Ze bestaan uit twee delen:
* Een Cloud onderdeel dat verantwoordelijk is voor de taak definitie: gebruikers definiëren de invoer, uitvoer, query en andere instellingen (op volg orde van gebeurtenissen, enzovoort) in de Cloud.
* Een module die wordt uitgevoerd op uw IoT-apparaten. Deze bevat de ASA-engine en ontvangt de taak definitie van de Cloud.

### <a name="create-a-storage-account"></a>Create a storage account

Wanneer u een Azure Stream Analytics-taak maakt die op een IoT Edge-apparaat moet worden uitgevoerd, moet deze worden opgeslagen op een manier die door het apparaat kan worden aangeroepen. U kunt een bestaand Azure Storage-account gebruiken of een nieuwe maken.
1. Ga in Azure Portal naar **een resource maken > opslag > opslag account-blob, bestand, tabel, wachtrij**.
2. Geef de volgende waarden op om uw opslagaccount te maken:

   | Veld | Waarde |
   | --- | --- |
   | Naam | Voer een unieke naam in voor het opslagaccount. |
   | Locatie | Kies een locatie dicht bij u in de buurt.|
   | Abonnement | Kies het hetzelfde abonnement als uw IoT-hub.|
   | Resourcegroep | U wordt aangeraden dezelfde resource groep te gebruiken voor alle test resources die u hebt gemaakt tijdens de [IOT Edge Snelstartgids](../iot-edge/quickstart.md) en zelf studies. zoals **IoTEdgeResources**. |

3. Houd de standaardwaarden voor de andere velden aan en selecteer **Maken**.


### <a name="create-a-new-job"></a>Een nieuwe taak maken

1. Ga in het Azure Portal naar **een resource maken > Internet of Things > stream Analytics taak**.
2. Geef de volgende waarden op om uw opslagaccount te maken:

   | Veld | Waarde |
   | --- | --- |
   | Taaknaam | Geef de taak een naam. Bijvoorbeeld **IoTEdgeJob**. |
   | Abonnement | Kies het hetzelfde abonnement als uw IoT-hub.|
   | Resourcegroep | U wordt aangeraden dezelfde resource groep te gebruiken voor alle test resources die u hebt gemaakt tijdens de [IOT Edge Snelstartgids](../iot-edge/quickstart.md) en zelf studies. zoals **IoTEdgeResources**. |
   | Locatie | Kies een locatie dicht bij u in de buurt. |
   | Hosting omgeving | Selecteer **Edge**. |

3. Selecteer **Maken**.

### <a name="configure-your-job"></a>Uw taak configureren

Zodra uw Stream Analytics-taak is gemaakt in de Azure-portal, kunt u deze configureren met invoer, uitvoer en een query die moeten worden uitgevoerd op de gegevens die worden doorgegeven. U kunt hand matig invoer opgeven van een IoT Hub of een event hub in een Azure Stack hub-abonnement.

1. Navigeer naar uw Stream Analytics-taak in de Azure-portal.
2. Selecteer bij **configureren** de **instellingen voor het opslag account** en kies het opslag account dat u in de vorige stap hebt gemaakt.
   > [!div class="mx-imgBorder"]
   > [![Account instelling ](media/on-azure-stack/storage-account-settings.png) voor taak opslag](media/on-azure-stack/storage-account-settings.png#lightbox)
3. Selecteer **invoer** onder **taak topologie** en **Voeg vervolgens stroom invoer toe.**
4. Kies **IOT hub**, **Event hub** of **Edge hub** in de vervolg keuzelijst. 
5. Als de invoer een event hub is of IoT Hub in een Azure Stack hub-abonnement, moet u de informatie hand matig opgeven, zoals hieronder wordt weer gegeven.

   #### <a name="event-hub"></a>Event Hub

   | Veld | Waarde |
   | --- | --- |
   | Invoeralias | Een beschrijvende naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen. |
   | Service Bus naam ruimte | De naam ruimte is een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub maakt, maakt u ook de naam ruimte. (Voor beeld: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Event Hub-naam | De naam van de Event Hub die moet worden gebruikt als invoer. |
   | Naam van het Event Hub-beleid | Het beleid voor gedeelde toegang dat toegang biedt tot de Event hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. Deze optie wordt automatisch ingevuld, tenzij u de optie selecteert om de Event hub-instellingen hand matig op te geven. |
   | Event hub-beleids sleutel | De gedeelde toegangs sleutel die wordt gebruikt om toegang te verlenen tot de Event hub. Deze optie wordt automatisch ingevuld tenzij u de optie selecteert om de Event hub-instellingen hand matig op te geven. U kunt deze vinden in de Event hub-instellingen. |
   | Event hub-consumenten groep (optioneel) | Het is raadzaam om voor elke Stream Analytics taak een afzonderlijke consumenten groep te gebruiken. Met deze teken reeks wordt de Consumer groep geïdentificeerd die moet worden gebruikt om gegevens op te nemen van de Event Hub. Als er geen consumenten groep is opgegeven, gebruikt de Stream Analytics-taak de $Default Consumer groep. |
   | Aantal partities | Aantal partitions is het aantal partities in een event hub. |

   > [!div class="mx-imgBorder"]
   > [![Event hub-invoer ](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT Hub

   | Veld | Waarde |
   | --- | --- |
   | Invoeralias | Een beschrijvende naam die u in de query van de taak gebruikt om naar deze invoer te verwijzen. |
   | IoT Hub | De naam van de IoT Hub die moet worden gebruikt als invoer. (Voor beeld:*<IoT Hub Name> . Shanghai.azurestack.Corp.Microsoft.com*) |
   | Naam van gedeeld toegangsbeleid | Het beleid voor gedeelde toegang dat toegang biedt tot de IoT Hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
   | Sleutel voor gedeeld toegangs beleid | De gedeelde toegangs sleutel die wordt gebruikt om toegang te verlenen tot de IoT Hub. Deze optie wordt automatisch ingevuld tenzij u de optie selecteert om de IOT hub-instellingen hand matig op te geven. |
   | Consumenten groep (optioneel) | Het wordt ten zeerste aangeraden om voor elke Stream Analytics taak een andere consumenten groep te gebruiken. De Consumer groep wordt gebruikt om gegevens op te nemen van de IoT Hub. Stream Analytics gebruikt de $Default-Consumer groep tenzij u anders opgeeft. |
   | Aantal partities | Aantal partitions is het aantal partities in een event hub. |

   > [!div class="mx-imgBorder"]
   > [![IOT hub invoer ](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

6. Behoud de standaardwaarden van de andere velden en selecteer Opslaan.
7. Open onder Taaktopologie de optie Uitvoer en selecteer vervolgens Toevoegen.
8. Kies Blob Storage, Event hub of Edge hub in de vervolg keuzelijst.
9. Als de uitvoer een event hub is of Blob Storage in een Azure Stack hub-abonnement, moet u de informatie hand matig opgeven, zoals hieronder wordt weer gegeven.

   #### <a name="event-hub"></a>Event Hub

   | Veld | Waarde |
   | --- | --- |
   | Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Event Hub te sturen. |
   | Service Bus naam ruimte | Een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub hebt gemaakt, hebt u ook een service bus-naam ruimte gemaakt. (Voor beeld: *SB:// <Event Hub Name> . eventhub.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Event Hub-naam | De naam van de Event Hub uitvoer. |
   | Naam van het Event Hub-beleid | Het beleid voor gedeelde toegang, dat u kunt maken op het tabblad configureren van de Event Hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
   | Event hub-beleids sleutel | De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Event Hub. |

   > [!div class="mx-imgBorder"]
   > [![Event hub-uitvoer ](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob Storage 

   | Veld | Waarde |
   | --- | --- |
   | Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Blob-opslag te sturen. |
   | Storage-account | De naam van het opslag account waarin u uw uitvoer wilt verzenden. (Voor beeld: *<Storage Account Name> . blob.Shanghai.azurestack.Corp.Microsoft.com*) |
   | Sleutel van het opslag account | De geheime sleutel die is gekoppeld aan het opslag account. Deze optie wordt automatisch ingevuld tenzij u de optie selecteert om de Blob Storage-instellingen hand matig op te geven. |

> [!NOTE]
> De Parquet-indeling wordt niet ondersteund voor Edge-taken op Azure Stack hub. Voor minimale rijen en maximum tijd, gebruikt u 0 of laat u deze leeg.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Stream Analytics implementeren op een virtuele machine of een apparaat dat is verbonden met Azure Stack

1. Open in de Azure Portal IoT Hub. Navigeer naar **IOT Edge** en klik op het apparaat (VM) dat u wilt instellen voor deze implementatie.
2. Selecteer **Modules instellen**. Selecteer vervolgens **+ toevoegen** en kies **Azure stream Analytics module**. 
3. Selecteer het abonnement en de stoom Analytics-taak die u hebt gemaakt. Klik op **Opslaan** en selecteer **volgende: routes**.

   > [!div class="mx-imgBorder"]
   > [![Modules ](media/on-azure-stack/edge-modules.png) toevoegen](media/on-azure-stack/edge-modules.png#lightbox)

4. Klik op **beoordeling + >maken**.
5. Selecteer in de stap **beoordeling en maken** de optie **maken**. 
   > [!div class="mx-imgBorder"]
   > [![Manifest ](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
6. Controleer of de module is toegevoegd aan de lijst.
   > [!div class="mx-imgBorder"]
   > [![Implementatie pagina ](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>Volgende stappen
- [Azure Stream Analytics op IoT Edge](./stream-analytics-edge.md)
- [Stream Analytics Edge-taken ontwikkelen](/stream-analytics-query/stream-analytics-query-language-reference)