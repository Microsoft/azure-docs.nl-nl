---
title: Microsoft-Verbonden cache voor apparaatupdates configureren voor Azure IoT Hub | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Overzicht van Microsoft Verbonden cache voor apparaatupdates voor Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811825"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Microsoft-Verbonden cache voor apparaatupdates configureren voor Azure IoT Hub

Microsoft Verbonden cache wordt geïmplementeerd op Azure IoT Edge gateways als een Azure IoT Edge Module. Net als Azure IoT Edge modules worden omgevingsvariabelen voor MCC-module-implementatie en opties voor het maken van containers gebruikt om Microsoft-modules Verbonden cache configureren.  Deze sectie definieert de omgevingsvariabelen en opties voor het maken van containers die een klant nodig heeft om de Microsoft Verbonden cache-module te implementeren voor gebruik door Apparaatupdate voor Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Implementatiedetails Verbonden cache Azure IoT Edge Microsoft-module

De beheerder kan de Microsoft Verbonden cache-module een naam geven. Er zijn geen andere module- of service-interacties die afhankelijk zijn van de naam van de module voor communicatie. Daarnaast is de bovenliggende onderliggende relatie van de Microsoft Verbonden cache-servers niet afhankelijk van deze modulenaam, maar van de FQDN of het IP-adres van de Azure IoT Edge-gateway die is geconfigureerd zoals eerder besproken.

Microsoft Verbonden cache Azure IoT Edge Module Environment Variables worden gebruikt om basisinformatie over module-id's en functionele module-instellingen door te geven aan de container.

| Naam variabele                 | Waarde-indeling                           | Vereist/optioneel | Functionaliteit                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | GUID van Azure-abonnements-id             | Vereist          | Dit is de sleutel van de klant, die veilig is<br>verificatie van het cache-knooppunt voor Delivery Optimization<br>Diensten.<br>Vereist om de module te laten functioneren. |
| CACHE_NODE_ID                 | GUID van cache-knooppunt-id                     | Vereist          | De Microsoft-Verbonden cache<br>knooppunt voor Delivery Optimization Services.<br>Vereist in volgorde<br> om de module te laten functioneren. |
| CUSTOMER_KEY                  | GUID van klantsleutel                     | Vereist          | Dit is de sleutel van de klant, die veilig is<br>verificatie van het cache-knooppunt voor Delivery Optimization Services.<br>Vereist om de module te laten functioneren.|
| STORAGE_ *N* _SIZE_GB           | Waarbij N het cachestation is   | Vereist          | Geef maximaal 9 stations op om inhoud in de cache op te geven en geef de maximale ruimte in op<br>Gigabytes om toe te wijzen voor inhoud op elk cachestation. Voorbeelden:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>Het nummer van het station moet overeenkomen met de opgegeven bindingswaarden voor het cachestation<br>in de container maken optie MicrosoftConnectedCache *N waarde*<br>De minimale grootte van de cache is 10 GB.|
| UPSTREAM_HOST                 | FQDN/IP                                | Optioneel          | Met deze waarde kan een upstream van Microsoft Connected worden opgegeven<br>Cache-knooppunt dat als proxy fungeert als het Verbonden cache knooppunt<br> is niet verbonden met internet. Deze instelling wordt gebruikt ter ondersteuning van<br> het geneste IoT-scenario.<br>**Opmerking:** Microsoft Verbonden cache luistert op http-standaardpoort 80.|
| UPSTREAM_PROXY                | FQDN/IP:POORT                           | Optioneel          | De uitgaande internetproxy.<br>Dit kan ook de OT DMZ-proxy zijn als een ISA 95-netwerk. |
| CACHEABLE_CUSTOM_ *N* _HOST     | HOST/IP<br>FQDN                        | Optioneel          | Vereist voor de ondersteuning van aangepaste pakket-opslagplaatsen.<br>Opslagplaatsen kunnen lokaal of op internet worden gehost.<br>Er is geen limiet voor het aantal aangepaste hosts dat kan worden geconfigureerd.<br><br>Voorbeelden:<br>Name = CACHEABLE_CUSTOM_1_HOST Value = packages.foo.com<br> Name = CACHEABLE_CUSTOM_2_HOST Value = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Optioneel          | Vereist voor de ondersteuning van aangepaste pakket-opslagplaatsen.<br>Deze waarde kan worden gebruikt als alias en wordt door de cacheserver gebruikt om naar te verwijzen<br>verschillende DNS-namen. De hostnaam van de inhoud van de opslagplaats kan bijvoorbeeld packages.foo.com,<br>maar voor verschillende regio's kan er een extra voorvoegsel worden toegevoegd aan de hostnaam<br>zoals westuscdn.packages.foo.com en eastuscdn.packages.foo.com.<br>Door de canonieke alias in te stellen, zorgt u ervoor dat inhoud niet wordt gedupliceerd<br>voor inhoud die afkomstig is van dezelfde host, maar verschillende CDN-bronnen.<br>De indeling van de canonieke waarde is niet belangrijk, maar moet uniek zijn voor de host.<br>Het kan het gemakkelijkst zijn om de waarde in te stellen die overeen komt met de hostwaarde.<br><br>Voorbeelden op basis van voorbeelden van aangepaste host hierboven:<br>Name = CACHEABLE_CUSTOM_1_CANONICAL Value = foopackages<br> Name = CACHEABLE_CUSTOM_2_CANONICAL Value = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Waar of Onwaar                          | Optioneel          | Hiermee schakelt u weergave van het samenvattingsrapport op het lokale netwerk of internet in.<br>Het gebruik van een API-sleutel (later besproken) is vereist om het samenvattingsrapport weer te geven als dit is ingesteld op true. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Waar of Onwaar                          | Optioneel          | Hiermee schakelt u weergave van samenvattingsrapport op het lokale netwerk of internet zonder<br>het gebruik van een API-sleutel vanaf elk apparaat in het netwerk. Gebruik als u de toegang niet wilt vergrendelen<br>om samenvattingsgegevens van de cacheserver weer te geven via de browser. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Opties voor Verbonden cache Azure IoT Edge microsoft-modulecontainer maken

Opties voor het maken van containers voor de implementatie van MCC-modules bieden controle over de instellingen met betrekking tot opslag en poorten die worden gebruikt door de MCC-module. Dit is de lijst met vereiste variabelen die door de container zijn gemaakt voor het implementeren van MCC.

### <a name="container-to-host-os-drive-mappings"></a>Container voor het hosten van os-stationtoewijzingen

Vereist om de opslaglocatie van de container toe te wijs aan de opslaglocatie op de disk.< kunnen maximaal negen locaties worden opgegeven.

>[!Note]
>Het nummer van het station moet overeenkomen met de bindingswaarden voor het cachestation die zijn opgegeven in de omgevingsvariabele STORAGE_ *N* _SIZE_GB waarde, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Container voor het hosten van TCP-poorttoewijzingen

Met deze optie geeft u de http-poort van de externe machine op waar MCC naar inhoudsaanvragen luistert. De standaardhostport is poort 80 en andere poorten worden op dit moment niet ondersteund omdat de ADU-client momenteel aanvragen op poort 80 doet. TCP-poort 8081 is de interne containerpoort waar de MCC op luistert en die niet kan worden gewijzigd.

### <a name="container-service-tcp-port-mappings"></a>TCP-poorttoewijzingen voor containerservice

De Microsoft Verbonden cache-module heeft een .NET Core-service die door de caching-engine wordt gebruikt voor verschillende functies.

>[!Note]
>Ter ondersteuning van Azure IoT Nested Edge moet de HostPort niet worden ingesteld op 5000 omdat de registerproxymodule al luistert op hostpoort 5000.


Opties voor het maken van voorbeeldcontainers

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Microsoft Verbonden cache samenvattingsrapport

Het samenvattingsrapport is momenteel de enige manier voor een klant om cachinggegevens weer te geven voor de Microsoft Verbonden cache-exemplaren die zijn geïmplementeerd Azure IoT Edge gateways. Het rapport wordt gegenereerd met intervallen van 15 seconden en bevat gemiddelde statistieken voor de periode en geaggregeerde statistieken voor de levensduur van de module. De belangrijkste statistieken waarin klanten geïnteresseerd zijn, zijn:

* **hitBytes:** dit is de som van de bytes die rechtstreeks uit de cache zijn geleverd.
* **missBytes:** dit is de som van de bytes die Microsoft Verbonden cache moest downloaden van CDN om de cache te zien.
* **eggressBytes:** dit is de som van hitBytes en missBytes en is het totale aantal bytes dat aan clients wordt geleverd.
* **hitRatioBytes:** dit is de verhouding tussen hitBytes en egressBytes.  Als 100% van de geleverde eggressBytes in een periode gelijk is aan de hitBytes, is dit bijvoorbeeld 1.


Het overzichtsrapport is beschikbaar op `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` Vervangen \<Azure IoT Edge Gateway IP\> door het IP-adres of de hostnaam van IoT Edge gateway. (Zie details van omgevingsvariabele voor informatie over de zichtbaarheid van dit rapport).