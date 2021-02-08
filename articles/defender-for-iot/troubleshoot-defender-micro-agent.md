---
title: Oplossen van problemen met micro agent voor Defender IoT (preview-versie)
titleSuffix: Azure Defender for IoT
description: Meer informatie over het afhandelen van onverwachte of niet-verklarende fouten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/24/2021
ms.topic: reference
ms.service: azure
ms.openlocfilehash: 07198a5d0ef5d0a6c9eed97523c61826e451b7f5
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809748"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Oplossen van problemen met micro agent voor Defender IoT (preview-versie)

In het geval van onverwachte of onuitgelegde fouten gebruikt u de volgende probleemoplossings methoden om te proberen uw problemen op te lossen. U kunt ook het Azure Defender voor IoT-product team bereiken voor hulp als dat nodig is.   

## <a name="service-status"></a>Status van services 

De status van de service weer geven: 

1. Voer de volgende opdracht uit

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Controleer of de service stabiel is door er zeker van te zijn dat deze is `active` en dat de uptime in het proces geschikt is.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Controleer of uw service stabiel is door te controleren of deze actief is en of de uptime het meest geschikt is.":::

Als de service wordt weer gegeven als `inactive` , gebruikt u de volgende opdracht om de service te starten:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

U weet dat de service vastloopt als de uptime van het proces te kort is. U kunt dit probleem oplossen door de logboeken te bekijken.

## <a name="review-logs"></a>Logboeken controleren 

Gebruik de volgende opdracht om te controleren of de Defender IoT micro Agent-service wordt uitgevoerd met hoofd bevoegdheden.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Controleer of de Defender voor IoT micro Agent-service wordt uitgevoerd met hoofd bevoegdheden.":::

Als u de logboeken wilt weer geven, gebruikt u de volgende opdracht:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Als u de service opnieuw wilt starten, gebruikt u de volgende opdracht: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de [functie ondersteuning en het buiten](edge-security-module-deprecation.md)gebruik stellen.
