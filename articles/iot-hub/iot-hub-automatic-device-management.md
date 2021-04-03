---
title: Automatische Apparaatbeheer op schaal met Azure IoT Hub | Microsoft Docs
description: Automatische configuraties van Azure IoT Hub gebruiken om meerdere IoT-apparaten en-modules te beheren
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 0e017f4df413d6db528bb99756646859d9a74aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545392"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>Automatisch beheer van IoT-apparaat en module met Azure Portal

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Automatische Apparaatbeheer in azure IoT Hub automatiseert veel van de herhaalde en complexe taken voor het beheren van grote apparaat vloots. Met automatisch Apparaatbeheer kunt u een set apparaten op basis van hun eigenschappen richten, een gewenste configuratie definiëren en de apparaten vervolgens IoT Hub bijwerken wanneer ze binnen het bereik komen. Deze update wordt uitgevoerd met behulp van een _automatische apparaatconfiguratie_ of _automatische configuratie_ van een module, waarmee u de voltooiing en naleving kunt samen vatten, samen voegen en conflicten kunt afhandelen en configuraties in een gefaseerde benadering implementeert.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Automatische Apparaatbeheer werkt door het bijwerken van een set apparaatdubbels of module apparaatdubbels met gewenste eigenschappen en het rapporteren van een samen vatting dat is gebaseerd op twee gerapporteerde eigenschappen.  Er wordt een nieuwe klasse en een JSON-document geïntroduceerd, een *configuratie* met drie onderdelen:

* De **doel voorwaarde** definieert het bereik van de apparaatdubbels of module apparaatdubbels die moet worden bijgewerkt. De doel voorwaarde wordt opgegeven als een query op dubbele Tags en/of gerapporteerde eigenschappen.

* De **doel inhoud** definieert de gewenste eigenschappen die moeten worden toegevoegd of bijgewerkt in de apparaatdubbels of module apparaatdubbels van het doel apparaat. De inhoud bevat een pad naar de sectie met gewenste eigenschappen die moeten worden gewijzigd.

* De **metrische gegevens** bepalen het aantal samen vattingen van de verschillende configuratie statussen, zoals **geslaagd**, **in uitvoering** en **fout**. Aangepaste metrische gegevens worden opgegeven als query's op dubbele gerapporteerde eigenschappen.  Systeem metrieken zijn de standaard waarden die de dubbele update status meten, zoals het aantal apparaatdubbels dat is gericht en het aantal apparaatdubbels dat is bijgewerkt.

Automatische configuraties worden voor de eerste keer uitgevoerd, kort nadat de configuratie is gemaakt en vervolgens vijf minuten. Metrische query's worden uitgevoerd telkens wanneer de automatische configuratie wordt uitgevoerd.

## <a name="implement-twins"></a>Apparaatdubbels implementeren

Voor automatische apparaatconfiguratie is het gebruik van apparaatdubbels vereist om de status van de Cloud en de apparaten te synchroniseren.  Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](iot-hub-devguide-device-twins.md) voor meer informatie.

Voor automatische module configuraties moet module apparaatdubbels worden gebruikt om de status van de Cloud en modules te synchroniseren. Zie voor meer informatie de [module Apparaatdubbels begrijpen en gebruiken in IOT hub](iot-hub-devguide-module-twins.md).

## <a name="use-tags-to-target-twins"></a>Tags gebruiken om apparaatdubbels te richten

Voordat u een configuratie maakt, moet u opgeven welke apparaten of modules u wilt beïnvloeden. Met Azure IoT Hub worden apparaten geïdentificeerd en tags op het apparaat dubbele en worden modules geïdentificeerd met behulp van labels in de module. Elk apparaat of elke module kan meerdere labels hebben, en u kunt ze op elke manier definiëren die zinvol is voor uw oplossing. Als u bijvoorbeeld apparaten op verschillende locaties beheert, voegt u de volgende tags toe aan een apparaat:

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>Een configuratie maken

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub. 

2. Selecteer **IOT-apparaatconfiguratie**.

3. Selecteer **apparaatconfiguratie toevoegen** of **module configuratie toevoegen**.

   ![Apparaatconfiguratie of module configuratie toevoegen](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

Er zijn vijf stappen voor het maken van een configuratie. In de volgende secties wordt elke stap uitgelegd. 

### <a name="name-and-label"></a>Naam en label

1. Geef uw configuratie een unieke naam van Maxi maal 128 kleine letters. Vermijd spaties en de volgende ongeldige tekens: `& ^ [ ] { } \ | " < > /` .

2. Voeg labels toe om uw configuraties bij te houden. Labels zijn **naam**- **, waardeparen die** uw configuratie beschrijven. Bijvoorbeeld `HostPlatform, Linux` of `Version, 3.0.1`.

3. Selecteer **volgende** om naar de volgende stap te gaan. 

### <a name="specify-settings"></a>Instellingen opgeven

In deze sectie wordt de inhoud gedefinieerd die moet worden ingesteld in het doel apparaat of de module apparaatdubbels. Er zijn twee invoer waarden voor elke set instellingen. De eerste is het dubbele pad, het pad naar de JSON-sectie binnen de dubbele gewenste eigenschappen die worden ingesteld.  De tweede is de JSON-inhoud die in die sectie moet worden ingevoegd. 

U kunt bijvoorbeeld het dubbele pad instellen op `properties.desired.chiller-water` en vervolgens de volgende JSON-inhoud opgeven: 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![Het dubbele pad en de inhoud instellen](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


U kunt ook afzonderlijke instellingen instellen door het hele dubbele pad op te geven en de waarde zonder haken te geven. Stel bijvoorbeeld met het dubbele pad `properties.desired.chiller-water.temperature` de inhoud in op `66` . Maak vervolgens een nieuwe dubbele instelling voor de druk eigenschap. 

Als twee of meer configuraties gericht zijn op hetzelfde dubbele pad, wordt de inhoud van de configuratie met de hoogste prioriteit toegepast (prioriteit is gedefinieerd in stap 4).

Als u een bestaande eigenschap wilt verwijderen, geeft u de waarde van de eigenschap op `null` .

U kunt extra instellingen toevoegen door het selectie vakje **dubbele apparaat-instelling toevoegen** of **module dubbele instelling toevoegen** te selecteren.

### <a name="specify-metrics-optional"></a>Metrische gegevens opgeven (optioneel)

Metrische gegevens bieden een overzicht van de verschillende statussen die een apparaat of module kan melden na het Toep assen van configuratie-inhoud. U kunt bijvoorbeeld een metriek maken voor wijzigingen in de instellingen in behandeling, een metrische waarde voor fouten en een metrische waarde voor geslaagde instellingen wijzigen.

Elke configuratie kan Maxi maal vijf aangepaste metrische gegevens bevatten. 

1. Voer een naam in voor de naam van de **metrische gegevens**.

2. Voer een query in voor **metrische criteria**.  De query is gebaseerd op dubbele gerapporteerde eigenschappen van het apparaat.  De metriek vertegenwoordigt het aantal rijen dat door de query wordt geretourneerd.

Bijvoorbeeld:

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

U kunt een component toevoegen waarop de configuratie is toegepast, bijvoorbeeld: 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

Als u een metriek bouwt om te rapporteren over geconfigureerde modules, selecteert u `moduleId` uit `devices.modules` . Bijvoorbeeld:

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>Doelapparaten

Gebruik de eigenschap Tags van uw apparaatdubbels om te richten op de specifieke apparaten of modules die deze configuratie moeten ontvangen. U kunt ook op dubbele gerapporteerde eigenschappen richten.

Automatische configuraties van apparaten kunnen alleen dubbele Tags van het apparaat richten en automatische module configuraties kunnen alleen de doel module dubbele Tags hebben. 

Aangezien meerdere configuraties kunnen worden gericht op hetzelfde apparaat of dezelfde module, moet elke configuratie een prioriteits nummer hebben. Als er zich ooit een conflict voordoet, wordt de configuratie met de hoogste prioriteit wint. 

1. Voer een positief geheel getal in voor de configuratie **prioriteit**. De hoogste numerieke waarde wordt gezien als de hoogste prioriteit. Als twee configuraties hetzelfde prioriteits nummer hebben, is het het meest recent gemaakte WINS-account. 

2. Geef een **doel voorwaarde** op om te bepalen welke apparaten of modules met deze configuratie worden benaderd. De voor waarde is gebaseerd op dubbele Tags of dubbele gerapporteerde eigenschappen en moet overeenkomen met de indeling van de expressie. 

   Voor automatische apparaatconfiguratie kunt u alleen de tag of de gerapporteerde eigenschap op het doel opgeven. Bijvoorbeeld `tags.environment='test'` of `properties.reported.chillerProperties.model='4000x'`. U kunt opgeven `*` om alle apparaten te richten. 
   
   Gebruik voor automatische module configuratie een query om labels of gerapporteerde eigenschappen op te geven van de modules die zijn geregistreerd bij de IoT hub. Bijvoorbeeld `from devices.modules where tags.environment='test'` of `from devices.modules where properties.reported.chillerProperties.model='4000x'`. Het Joker teken kan niet worden gebruikt om alle modules te bereiken. 

3. Selecteer **volgende** om door te gaan naar de laatste stap.

### <a name="review-configuration"></a>Configuratie controleren

Controleer uw configuratie gegevens en selecteer vervolgens **verzenden**.

## <a name="monitor-a-configuration"></a>Een configuratie controleren

Voer de volgende stappen uit om de details van een configuratie te bekijken en de apparaten waarop deze wordt uitgevoerd, te controleren:

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub. 

2. Selecteer **IOT-apparaatconfiguratie**.

3. Inspecteer de configuratie lijst. Voor elke configuratie kunt u de volgende gegevens bekijken:

   * **Id** : de naam van de configuratie.

   * **Doel voorwaarde** : de query die wordt gebruikt voor het definiëren van de doel apparaten of-modules.

   * **Prioriteit** : het prioriteits nummer dat is toegewezen aan de configuratie.

   * **Aanmaak tijd** : de tijds tempel van het moment waarop de configuratie is gemaakt. Deze tijds tempel wordt gebruikt om bindingen te verstoren wanneer twee configuraties dezelfde prioriteit hebben. 

   * **Systeem metrieken** : meet waarden die worden berekend door IOT hub en kunnen niet worden aangepast door ontwikkel aars. Doel Hiermee geeft u het aantal apparaatdubbels dat overeenkomt met de doel voorwaarde. Is opgegeven het aantal apparaatdubbels dat door de configuratie is gewijzigd. Dit kan gedeeltelijke wijzigingen bevatten in het geval dat een afzonderlijke configuratie met een hogere prioriteit ook wijzigingen heeft doorgevoerd. 

   * **Aangepaste metrische gegevens** : meet waarden die door de ontwikkelaar zijn opgegeven als query's op dubbele gerapporteerde eigenschappen.  Er kunnen Maxi maal vijf aangepaste metrische gegevens per configuratie worden gedefinieerd. 
   
4. Selecteer de configuratie die u wilt bewaken.  

5. Controleer de configuratie gegevens. U kunt tabbladen gebruiken om specifieke details weer te geven over de apparaten die de configuratie hebben ontvangen.

   * **Doel voorwaarde** : de apparaten of modules die overeenkomen met de doel voorwaarde. 

   * **Metrische gegevens** : een lijst met metrische gegevens van het systeem en aangepaste metrische gegevens.  U kunt een lijst weer geven met apparaten of modules die voor elke metriek worden geteld door de metriek te selecteren in de vervolg keuzelijst en vervolgens **apparaten weer geven** of **modules weer geven** te selecteren.

   * **Dubbele** instellingen van het apparaat of de dubbele instellingen van de **module** -de dubbele instellingen die zijn ingesteld door de configuratie. 

   * **Configuratie labels** -paren sleutel waarden die worden gebruikt om een configuratie te beschrijven.  Labels hebben geen invloed op de functionaliteit. 

## <a name="modify-a-configuration"></a>Een configuratie wijzigen

Wanneer u een configuratie wijzigt, worden de wijzigingen onmiddellijk gerepliceerd naar alle doel apparaten of-modules. 

Als u de doel voorwaarde bijwerkt, worden de volgende updates uitgevoerd:

* Als een dubbele niet voldoet aan de oude doel voorwaarde, maar voldoet aan de nieuwe doel voorwaarde en deze configuratie de hoogste prioriteit voor die dubbele is, wordt deze configuratie toegepast. 

* Als een dubbele uitvoering van deze configuratie niet meer voldoet aan de doel voorwaarde, worden de instellingen van de configuratie verwijderd en wordt de dubbele wijziging door de configuratie van de volgende hoogste prioriteit gewijzigd. 

* Als een dubbele uitvoering van deze configuratie niet meer voldoet aan de doel voorwaarde en niet voldoet aan de doel voorwaarde van andere configuraties, worden de instellingen van de configuratie verwijderd en worden er geen andere wijzigingen doorgevoerd op de dubbele. 

Als u een configuratie wilt wijzigen, gebruikt u de volgende stappen: 

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub. 

2. Selecteer **IOT-apparaatconfiguratie**. 

3. Selecteer de configuratie die u wilt wijzigen. 

4. Wijzigingen aanbrengen in de volgende velden: 

   * Doel voorwaarde 
   * Labels 
   * Prioriteit 
   * Metrische gegevens

4. Selecteer **Opslaan**.

5. Volg de stappen in [een configuratie controleren](#monitor-a-configuration) om de wijzigingen door te voeren. 

## <a name="delete-a-configuration"></a>Een configuratie verwijderen

Wanneer u een configuratie verwijdert, nemen alle apparaatdubbels van het apparaat de volgende configuratie met de hoogste prioriteit. Als apparaatdubbels niet voldoet aan de doel voorwaarde van een andere configuratie, worden er geen andere instellingen toegepast. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub. 

2. Selecteer **IOT-apparaatconfiguratie**. 

3. Gebruik het selectie vakje om de configuratie te selecteren die u wilt verwijderen. 

4. Selecteer **Verwijderen**.

5. U wordt gevraagd om te bevestigen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u IoT-apparaten op schaal kunt configureren en controleren. Volg deze koppelingen voor meer informatie over het beheren van Azure IoT Hub:

* [De identiteiten van uw IoT Hub-apparaten bulksgewijs beheren](iot-hub-bulk-identity-mgmt.md)
* [Uw IoT-hub bewaken](monitor-iot-hub.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [Ontwikkelaars handleiding IoT Hub](iot-hub-devguide.md)
* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)

Zie voor meer informatie over het gebruik van de IoT Hub Device Provisioning Service om Zero-Touch, just-in-time inrichten in te scha kelen: 

* [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)