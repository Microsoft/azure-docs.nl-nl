---
title: Metrische gegevens van Azure IoT Connector voor FHIR (preview) exporteren via diagnostische instellingen
description: In dit artikel wordt uitgelegd hoe u de metrische gegevens van Azure IoT connector voor FHIR (preview) exporteert via Diagnostische instellingen
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3b350de81e07f30f4f0f105c4e833a3b4450e558
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018428"
---
# <a name="export-azure-iot-connector-for-fhir-preview-metrics-through-diagnostic-settings"></a>Metrische gegevens van Azure IoT Connector voor FHIR (preview) exporteren via diagnostische instellingen

In dit artikel leert u hoe u Azure IoT connector exporteert voor snelle bronnen voor de gezondheids zorg (FHIR&#174;) * metrische Logboeken. De functie die logboek registratie van metrische gegevens mogelijk maakt, is de [**Diagnostische instellingen**](../../azure-monitor/essentials/diagnostic-settings.md) in de Azure Portal. 

> [!TIP]
> Volg de richt lijnen in [Diagnostische logboek registratie inschakelen in azure API voor FHIR en Azure IOT connector voor FHIR](enable-diagnostic-logging.md#enable-diagnostic-logging-in-azure-api-for-fhir) om controle logboek registratie in te stellen.

## <a name="enable-metrics-logging-for-the-azure-iot-connector-for-fhir-preview"></a>Logboek registratie van metrische gegevens inschakelen voor de Azure IoT-connector voor FHIR (preview-versie)
1. Als u de logboek registratie van metrische gegevens voor de Azure IoT-connector voor FHIR wilt inschakelen, selecteert u uw Azure API for FHIR-service in de Azure Portal 

2. Navigeren naar **Diagnostische instellingen** 

3. Selecteer **+ Diagnostische instelling toevoegen**

   :::image type="content" source="media/iot-metrics-export/diagnostic-settings-main.png" alt-text="IoT-Connector1" lightbox="media/iot-metrics-export/diagnostic-settings-main.png"::: 

4. Voer een naam in het dialoog venster **naam van diagnostische instelling** in.

5. Selecteer de methode die u wilt gebruiken voor toegang tot uw Diagnostische logboeken:

    1. **Archiveren naar een opslag account** voor controle of hand matige inspectie. Het opslag account dat u wilt gebruiken, moet al zijn gemaakt.
    2. **Stroom om te Event hub** voor opname door een service van derden of een aangepaste analytische oplossing. U moet een Event Hub-naam ruimte en Event Hub-beleid maken voordat u deze stap kunt configureren.
    3. **Streamen naar de log Analytics** -werk ruimte in azure monitor. U moet de werk ruimte voor logboek analyse maken voordat u deze optie kunt selecteren.

6. Selecteer **fouten, verkeer en latentie** voor de Azure IOT-connector voor FHIR.  Selecteer eventuele aanvullende metrische categorieën die u wilt vastleggen voor de Azure API voor FHIR.

7. Selecteer **Opslaan**.

   :::image type="content" source="media/iot-metrics-export/diagnostic-setting-add.png" alt-text="IoT-Connector2" lightbox="media/iot-metrics-export/diagnostic-setting-add.png":::

> [!Note] 
> Het kan tot vijf tien minuten duren voordat de eerste metrische Logboeken in de opslag plaats van uw keuze worden weer gegeven.  
 
Zie de [documentatie van Azure resource log](../../azure-monitor/essentials/platform-logs-overview.md) voor meer informatie over het werken met Diagnostische logboeken

## <a name="conclusion"></a>Conclusie 
Toegang tot metrische Logboeken is essentieel voor het controleren en oplossen van problemen.  Met Azure IoT connector voor FHIR kunt u deze acties uitvoeren via metrische Logboeken. 

## <a name="next-steps"></a>Volgende stappen

Bekijk veelgestelde vragen over de Azure IoT-connector voor FHIR.

>[!div class="nextstepaction"]
>[Veelgestelde vragen over Azure IoT connector voor FHIR](fhir-faq.md)

*In Azure Portal wordt Azure IoT Connector for FHIR aangeduid als IoT Connector (preview). FHIR is een gedeponeerd handelsmerk van HL7 en wordt gebruikt met de toestemming van HL7.