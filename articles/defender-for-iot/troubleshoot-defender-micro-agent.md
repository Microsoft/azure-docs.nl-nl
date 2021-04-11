---
title: Oplossen van problemen met micro agent voor Defender IoT (preview-versie)
description: Meer informatie over het afhandelen van onverwachte of niet-verklarende fouten.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011415"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Oplossen van problemen met micro agent voor Defender IoT (preview-versie)

Als er een onverwachte fout optreedt, kunt u deze probleemoplossings methoden gebruiken in een poging om het probleem op te lossen. U kunt ook het Azure Defender voor IoT-product team bereiken voor hulp als dat nodig is.   

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

U weet dat de service vastloopt als de uptime van het proces minder dan 2 minuten is. U kunt dit probleem oplossen door [de logboeken te bekijken](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Hoofd bevoegdheden van micro agent valideren

Gebruik de volgende opdracht om te controleren of de Defender IoT micro Agent-service wordt uitgevoerd met hoofd bevoegdheden.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Controleer of de Defender voor IoT micro Agent-service wordt uitgevoerd met hoofd bevoegdheden.":::
## <a name="review-the-logs"></a>Raadpleeg de logboeken 

Gebruik de volgende opdracht om de logboeken te bekijken:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Snelle logboek controle

Als er een probleem optreedt tijdens het uitvoeren van de micro agent, kunt u de micro agent uitvoeren in een tijdelijke status, zodat u de logboeken weer geven met de volgende opdracht:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Start de service opnieuw op

Als u de service opnieuw wilt starten, gebruikt u de volgende opdracht: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Volgende stappen

Bekijk de [functie ondersteuning en het buiten](edge-security-module-deprecation.md)gebruik stellen.
