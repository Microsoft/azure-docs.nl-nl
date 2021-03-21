---
title: Aanbeveling voor CIS-benchmark tests onderzoeken
titleSuffix: Azure Defender for IoT
description: Voer basis-en geavanceerde onderzoeken uit op basis van aanbevelingen voor de basis van het besturings systeem.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809757"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Aanbeveling van de OS-basis lijn (gebaseerd op CIS-benchmark) onderzoeken 

Voer basis-en geavanceerde onderzoeken uit op basis van aanbevelingen voor de basis van het besturings systeem.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Basis onderzoek van het aanbevolen basis beveiligings aanbeveling van het besturings systeem  

U kunt aanbevelingen voor de OS-basis lijn onderzoeken door te navigeren naar uw Azure Defender voor IoT-Portal, onder de **IOT hub**. Zie [beveiligings aanbevelingen onderzoeken](quickstart-investigate-security-recommendations.md)voor meer informatie.

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Geavanceerd onderzoek van het Security aanbeveling van het basis besturingssysteem  

In deze sectie wordt beschreven hoe u beter inzicht krijgt in de test resultaten van de basis lijn van het besturings systeem en query's uitvoert op gebeurtenissen in azure Log Analytics.  

Het geavanceerde onderzoek van de Security aanbeveling van het basis besturingssysteem wordt alleen ondersteund met behulp van log Analytics. Verbind Defender voor IoT met een Log Analytics-werk ruimte voordat u doorgaat. Zie [Azure Defender voor IOT-agent configureren voor](how-to-configure-agent-based-solution.md)meer informatie over geavanceerde aanbevelingen voor beveiligings problemen met het besturings systeem.

Een query uitvoeren op uw IoT-beveiligings gebeurtenissen in Log Analytics voor waarschuwingen:

1. Navigeer naar de pagina **waarschuwingen** .

1. Selecteer **aanbevelingen onderzoeken in log Analytics werk ruimte**.

Een query uitvoeren op uw IoT-beveiligings gebeurtenissen in Log Analytics voor aanbevelingen:

1. Navigeer naar de pagina **aanbevelingen** .

1. Selecteer **aanbevelingen onderzoeken in log Analytics werk ruimte**.

1. Selecteer **Details van de basislijn regels voor het besturings systeem weer geven** op de pagina voor snelle weer gave van **aanbeveling Details** om de details van een specifiek apparaat weer te geven.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Bekijk de details van een specifiek apparaat."::: 

Uw IoT-beveiligings gebeurtenissen in Log Analytics werk ruimte rechtstreeks opvragen:

1. Navigeer naar de pagina **Logboeken** .

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Selecteer Logboeken in het deel venster aan de linkerkant.":::

1. Selecteer de **waarschuwingen onderzoeken** of selecteer de optie **waarschuwingen in log Analytics door onderzoek** van een beveiligings aanbeveling of waarschuwing.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Nuttige query's voor het onderzoeken van de basislijn resources van het besturings systeem: 

> [!Note]
> Zorg ervoor dat u vervangt door `<device-id>` de namen die u voor het apparaat hebt opgegeven in elk van de volgende query's. 


### <a name="retrieve-the-latest-information"></a>De meest recente gegevens ophalen

- **Probleem** met de inrichting van het apparaat: Voer de volgende query uit om de meest recente informatie op te halen over controles die zijn mislukt op de apparaat vloot: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Specifiek apparaat mislukt** : Voer de volgende query uit om de meest recente informatie op te halen over controles die zijn mislukt op een specifiek apparaat:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Specifieke apparaatfout** -Voer deze query uit om de meest recente informatie op te halen over controles die een fout op een specifiek apparaat hebben: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- De lijst met apparaten **bijwerken voor de inrichting van het apparaat waarvoor een specifieke controle is mislukt** : Voer deze query uit om een bijgewerkte lijst met apparaten (op de apparaat vloot) op te halen die een specifieke controle heeft mislukt:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Volgende stappen

[Beveiligings aanbevelingen onderzoeken](quickstart-investigate-security-recommendations.md).
 