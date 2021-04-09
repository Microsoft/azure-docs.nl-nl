---
title: Micro soft Connected cache configureren voor het bijwerken van apparaten voor Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Overzicht van met micro soft verbonden cache voor het bijwerken van apparaten voor Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 2903407f88b57a7be948cdeb0610e6d65df975b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662469"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Micro soft Connected cache configureren voor het bijwerken van apparaten voor Azure IoT Hub

Micro soft Connected cache is geïmplementeerd op Azure IoT Edge gateways als een Azure IoT Edge-module. Net als andere Azure IoT Edge modules worden MCC module-implementatie omgevings variabelen en opties voor het maken van containers gebruikt voor het configureren van micro soft Connected cache modules.  In deze sectie worden de omgevings variabelen en de opties voor het maken van de container gedefinieerd die vereist zijn voor een klant om de micro soft Connected cache module te implementeren voor gebruik door update van het apparaat voor Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Implementatie Details van door micro soft verbonden cache Azure IoT Edge module

De naam van de door micro soft verbonden cache module is de beheerder. Er zijn geen andere module-of service-interacties die afhankelijk zijn van de naam van de module voor communicatie. Daarnaast is de bovenliggende onderliggende relatie van de door micro soft verbonden cache servers niet afhankelijk van deze module naam, maar in plaats daarvan de FQDN of het IP-adres van de Azure IoT Edge-gateway die is geconfigureerd zoals eerder is beschreven.

Micro soft Connected cache Azure IoT Edge module omgevings variabelen worden gebruikt voor het door geven van basis module-id-gegevens en functionele module-instellingen aan de container.

| Naam variabele                 | Waarde-indeling                           | Vereist/optioneel | Functionaliteit                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID van Azure-abonnement-ID             | Vereist          | Dit is de sleutel van de klant, die beveiligde<br>verificatie van het cache knooppunt voor het leveren van optimalisatie<br>Onderzoeksservices. Vereist om de module te laten functioneren. |
| CACHE_NODE_ID                 | GUID van cache knooppunt-ID                     | Vereist          | Unieke identificatie van de door micro soft verbonden cache<br>knoop punt voor Delivery Optimization Services. Vereist in volg orde<br> voor het functioneren van de module. |
| CUSTOMER_KEY                  | GUID van de klant sleutel                     | Vereist          | Dit is de sleutel van de klant, die beveiligde<br>verificatie van het cache knooppunt voor het leveren van Optimalisatie Services.<br>Vereist om de module te laten functioneren.|
| STORAGE_ *N* _SIZE_GB           | Waarbij N het aantal GB is dat vereist is   | Vereist          | Geef Maxi maal negen schijven op voor de cache-inhoud en geef<br>de maximale ruimte in gigabytes die moet worden toegewezen voor inhoud op elke cache station. Voorbeelden:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Het nummer van het station moet overeenkomen met de opgegeven bindings waarden voor cache stations<br>in de container Create Option MicrosoftConnectedCache *N* waarde|
| UPSTREAM_HOST                 | FQDN/IP                                | Optioneel          | Met deze waarde kan een upstream worden opgegeven die micro soft heeft verbonden<br>Cache knooppunt dat als proxy fungeert als het verbonden cache knooppunt<br> de verbinding met internet is verbroken. Deze instelling wordt gebruikt ter ondersteuning van<br> het geneste IoT-scenario.<br>**Opmerking:** Micro soft Connected cache luistert op http-standaard poort 80.|
| UPSTREAM_PROXY                | FQDN/IP: POORT                           | Optioneel          | De uitgaande internet proxy.<br>Dit kan ook de DMZ-proxy zijn als u een ISA 95-netwerk hebt. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP-ADRES<br>FQDN                        | Optioneel          | Vereist ter ondersteuning van aangepaste pakket opslagplaatsen.<br>Opslag plaatsen kunnen lokaal of op internet worden gehost.<br>Er is geen limiet voor het aantal aangepaste hosts dat kan worden geconfigureerd.<br><br>Voorbeelden:<br>Name = CACHEABLE_CUSTOM_1_HOST waarde = packages.foo.com<br> Name = CACHEABLE_CUSTOM_2_HOST waarde = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Optioneel          | Vereist ter ondersteuning van aangepaste pakket opslagplaatsen.<br>Deze waarde kan worden gebruikt als een alias en wordt gebruikt door de cache server waarnaar moet worden verwezen<br>verschillende DNS-namen. Bijvoorbeeld, de hostnaam van de opslagplaats inhoud kan packages.foo.com zijn,<br>voor verschillende regio's kan er echter een extra voor voegsel zijn dat wordt toegevoegd aan de hostnaam<br>zoals westuscdn.packages.foo.com en eastuscdn.packages.foo.com.<br>Door de canonieke alias in te stellen, zorgt u ervoor dat de inhoud niet wordt gedupliceerd<br>voor inhoud die afkomstig is van dezelfde host, maar met verschillende CDN-bronnen.<br>De notatie van de canonieke waarde is niet belang rijk, maar moet uniek zijn voor de host.<br>Het kan handig zijn om de waarde zo in te stellen dat deze overeenkomt met de waarde van de host.<br><br>Voor beelden op basis van voor beelden van aangepaste host:<br>Name = CACHEABLE_CUSTOM_1_CANONICAL waarde = foopackages<br> Name = CACHEABLE_CUSTOM_2_CANONICAL waarde = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Waar of onwaar                          | Optioneel          | Hiermee wordt het overzichts rapport weer gegeven op het lokale netwerk of Internet.<br>Het gebruik van een API-sleutel (later beschreven) is vereist om het samenvattings rapport weer te geven indien ingesteld op waar. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Waar of onwaar                          | Optioneel          | Hiermee wordt het weer geven van een samenvattings rapport op het lokale netwerk of Internet mogelijk zonder<br>het gebruik van een API-sleutel van elk apparaat in het netwerk. Gebruik dit als u de toegang niet wilt vergren delen<br>om samenvattings gegevens van de cache server via de browser weer te geven. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opties voor het maken van een micro soft Connected cache Azure IoT Edge-module container

De opties voor het maken van een container voor de implementatie van de MCC-module bieden controle over de instellingen met betrekking tot opslag en poorten die worden gebruikt door de MCC-module. Dit is de lijst met vereiste container gemaakte variabelen voor het implementeren van MCC.

### <a name="container-to-host-os-drive-mappings"></a>Container voor het hosten van OS-stationstoewijzingen

Vereist om de opslag locatie van de container toe te wijzen aan de opslag locatie op de schijf. < kunnen Maxi maal negen locaties worden opgegeven.

>[!Note]
>Het nummer van het station moet overeenkomen met de bindings waarden voor cache stations die zijn opgegeven in de omgevings variabele STORAGE_ *N* _SIZE_GB waarde, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Container om TCP-poort toewijzingen te hosten

Met deze optie geeft u de HTTP-poort van de externe computer op waarop MCC luistert naar inhouds aanvragen. De standaard HostPort is poort 80 en andere poorten worden op dit moment niet ondersteund omdat de ADU-client nu aanvragen op poort 80 maakt. TCP-poort 8081 is de interne container poort waarop de MCC luistert en kan niet worden gewijzigd.

```markdown
8081/tcp": [
   {
       "HostPort": "80"
   }
]
```

### <a name="container-service-tcp-port-mappings"></a>TCP-poort toewijzingen container service

Micro soft Connected cache module heeft een .NET core-service die wordt gebruikt door de cache-engine voor verschillende functies.

>[!Note]
>Ter ondersteuning van Azure IoT geneste Edge moet de HostPort niet worden ingesteld op 5000, omdat de register proxy module al luistert op poort 5000 van de host.

```markdown
5000/tcp": [
   {
       "HostPort": "5001"
   }
]
```

## <a name="microsoft-connected-cache-summary-report"></a>Samenvattings rapport van micro soft Connected cache

Het overzichts rapport is momenteel de enige manier waarop een klant cache gegevens kan weer geven voor de micro soft Connected cache-exemplaren die zijn geïmplementeerd op Azure IoT Edge gateways. Het rapport wordt gegenereerd met intervallen van 15 seconden en bevat de gemiddelde statistieken voor de periode, evenals geaggregeerde statistieken voor de levens duur van de module. De belangrijkste statistieken waarmee klanten geïnteresseerd zijn:

* **hitBytes** : dit is de som van de geleverde bytes die rechtstreeks afkomstig zijn uit de cache.
* **missBytes** : dit is de som van de bytes die door micro soft verbonden cache moeten worden gedownload van CDN om de cache te kunnen zien.
* **eggressBytes** : dit is de som van HitBytes en missBytes en het totale aantal bytes dat aan clients wordt geleverd.
* **hitRatioBytes** : dit is de verhouding tussen HitBytes en egressBytes.  Als 100% van de eggressBytes die in een periode zijn geleverd, gelijk zijn aan de hitBytes is dit bijvoorbeeld 1.

Het overzichts rapport is beschikbaar op `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` (Zie omgevings variabele details hieronder voor informatie over de zicht baarheid van dit rapport).
