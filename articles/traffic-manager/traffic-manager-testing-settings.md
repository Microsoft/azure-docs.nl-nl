---
title: Azure Traffic Manager-instellingen controleren
description: In dit artikel leest u hoe u uw Traffic Manager instellingen kunt controleren en de methode voor verkeers routering kunt testen.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022323"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager-instellingen controleren

Als u uw Traffic Manager-instellingen wilt testen, moet u meerdere clients hebben op verschillende locaties van waaruit u uw tests kunt uitvoeren. Vervolgens kunt u de eind punten in uw Traffic Manager profiel een voor een weer geven.

* Stel de DNS TTL-waarde in op laag zodat wijzigingen snel worden door gegeven (bijvoorbeeld 30 seconden).
* Ken de IP-adressen van uw Azure-Cloud Services en-websites in het profiel dat u wilt testen.
* Gebruik hulpprogram ma's waarmee u een DNS-naam kunt omzetten in een IP-adres en dat adres kunt weer geven.

U controleert of de DNS-namen worden omgezet in IP-adressen van de eind punten in uw profiel. De namen moeten worden omgezet op een manier die overeenkomt met de routerings methode voor verkeer die is gedefinieerd in het Traffic Manager profiel. U kunt de hulpprogram ma's als **nslookup** of een **graaf** gebruiken om DNS-namen op te lossen.

De volgende voor beelden helpen u bij het testen van uw Traffic Manager-profiel.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Traffic Manager profiel controleren met behulp van Nslookup en ipconfig in Windows

1. Open een opdracht of Windows Power shell-prompt als beheerder.
2. Typ `ipconfig /flushdns` om de cache van de DNS-resolver leeg te laten.
3. Typ `nslookup <your Traffic Manager domain name>`. Met de volgende opdracht wordt bijvoorbeeld de domein naam met het voor voegsel *MyApp. contoso* gecontroleerd

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    Een typisch resultaat toont de volgende informatie:

    + De DNS-naam en het IP-adres van de DNS-server die wordt gebruikt om deze Traffic Manager domein naam op te lossen.
    + De Traffic Manager domein naam die u hebt getypt op de opdracht regel na ' nslookup ' en het IP-adres waarnaar het Traffic Manager domein wordt omgezet. Het tweede IP-adres is het belang dat u wilt controleren. Het moet overeenkomen met een openbaar VIP-adres (virtuele IP) voor een van de Cloud Services of websites in het Traffic Manager profiel dat u wilt testen.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>De routerings methode voor failover-verkeer testen

1. Alle eind punten laten staan.
2. Als u één client gebruikt, vraagt u de DNS-omzetting voor uw bedrijfs domein naam met behulp van nslookup of een vergelijkbaar hulp programma.
3. Zorg ervoor dat het omgezette IP-adres overeenkomt met het primaire eind punt.
4. Ga naar het primaire eind punt of verwijder het controle bestand zodat Traffic Manager denkt dat de toepassing niet actief is.
5. Wacht tot de TTL (time-to-Live) van de DNS-server van het Traffic Manager profiel plus een extra twee minuten. Als uw DNS-TTL bijvoorbeeld 300 seconden (5 minuten) is, moet u wachten op zeven minuten.
6. Flush uw DNS-client cache en vraag de DNS-omzetting aan met behulp van Nslookup. In Windows kunt u uw DNS-cache leegmaken met de opdracht ipconfig/flushdns.
7. Zorg ervoor dat het omgezette IP-adres overeenkomt met uw secundaire eind punt.
8. Herhaal het proces en zet elk eind punt neer. Controleer of de DNS het IP-adres van het volgende eind punt in de lijst retourneert. Wanneer alle eind punten niet beschikbaar zijn, moet u het IP-adres van het primaire eind punt opnieuw verkrijgen.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>De routerings methode voor gewogen verkeer testen

1. Alle eind punten laten staan.
2. Als u één client gebruikt, vraagt u de DNS-omzetting voor uw bedrijfs domein naam met behulp van nslookup of een vergelijkbaar hulp programma.
3. Zorg ervoor dat het omgezette IP-adres overeenkomt met een van de eind punten.
4. Flush uw DNS-client cache en herhaal stap 2 en 3 voor elk eind punt. U ziet dat er verschillende IP-adressen worden weer gegeven voor elk van uw eind punten.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>De routerings methode voor prestatie verkeer testen

Als u een routerings methode voor prestatie verkeer effectief wilt testen, moet u clients hebben die zich in verschillende delen van de wereld bevinden. U kunt clients maken in verschillende Azure-regio's die kunnen worden gebruikt voor het testen van uw services. Als u een wereld wijd netwerk hebt, kunt u zich op afstand aanmelden bij clients in andere delen van de wereld en uw tests vanaf daar uitvoeren.

U kunt ook gratis webgebaseerde DNS-Zoek-en ontwikkelings services beschikbaar hebben. Sommige van deze hulpprogram ma's bieden u de mogelijkheid om DNS-naam omzetting te controleren vanaf verschillende locaties over de hele wereld. Zoek op ' DNS lookup ' naar voor beelden. Services van derden zoals Gomez of speech kunnen worden gebruikt om te bevestigen dat uw profielen verkeer naar verwachting distribueren.

## <a name="next-steps"></a>Volgende stappen

* [Over Traffic Manager routerings methoden voor verkeer](traffic-manager-routing-methods.md)
* [Prestatieoverwegingen voor Traffic Manager](traffic-manager-performance-considerations.md)
* [Problemen met Traffic Manager in gedegradeerde status oplossen](traffic-manager-troubleshooting-degraded.md)
