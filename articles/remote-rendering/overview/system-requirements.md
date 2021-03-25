---
title: Systeemvereisten
description: Een lijst met de systeem vereisten voor de externe rendering van Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.custom: references_regions
ms.openlocfilehash: 789233ce1ede751276f965143716694c6feca3ca
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032785"
---
# <a name="system-requirements"></a>Systeemvereisten

Dit hoofd stuk bevat de minimale systeem vereisten voor het werken met *Azure remote rendering* (arr).

## <a name="development-pc"></a>Ontwikkel computer

* Windows 10 versie 1903 of hoger.
* Up-to-date grafische Stuur Programma's.
* Optioneel: [H265 hardware-video decoder](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)als u de lokale preview van extern gerenderde inhoud wilt gebruiken (bijvoorbeeld eenheids Unit).

> [!IMPORTANT]
> Windows Update levert niet altijd de meest recente GPU-Stuur Programma's. Raadpleeg de website van uw GPU-fabrikant voor de meest recente Stuur Programma's:
>
> * [AMD-Stuur Programma's](https://www.amd.com/en/support)
> * [Intel-Stuur Programma's](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-Stuur Programma's](https://www.nvidia.com/Download/index.aspx)

In de volgende tabel ziet u welke Gpu's H265 hardware-video-decodering ondersteunt.

| GPU-fabrikant | Ondersteunde modellen |
|-----------|:-----------|
| GRAFISCHE | Controleer de **ondersteunings matrix van NVDEC** [onder aan deze pagina](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Uw GPU heeft ja nodig in de kolom **H. 265 4:2:0 8-bits** . |
| POWERNOW | Gpu's met ten minste versie 6 van de [gecombineerde video decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)van AMD. |
| Intel | Skylake en nieuwere Cpu's |

Hoewel de juiste H265-codec kan worden geïnstalleerd, kunnen de beveiligings eigenschappen van de codec-Dll's ertoe leiden dat er codec-initialisatie fouten optreden. In de [hand leiding](../resources/troubleshoot.md#h265-codec-not-available) voor het oplossen van problemen worden stappen beschreven voor het oplossen van dit probleem. Het DLL-probleem kan zich alleen voordoen wanneer u de service in een bureaublad toepassing gebruikt, bijvoorbeeld eenheid.

## <a name="devices"></a>Apparaten

De externe rendering van Azure biedt momenteel alleen ondersteuning voor **HoloLens 2** en Windows Desktop als doel apparaat. Zie de sectie [platform beperkingen](../reference/limits.md#platform-limitations) .

Het is belang rijk dat u de nieuwste HEVC-codec gebruikt, omdat nieuwere versies aanzienlijke verbeteringen in de latentie hebben. Controleren welke versie op het apparaat is geïnstalleerd:

1. Start de **Microsoft Store**.
1. Klik op de knop **'... '** in de rechter bovenhoek.
1. Selecteer **down loads en updates**.
1. Zoek in de lijst naar **HEVC-video-uitbrei dingen van de fabrikant van het apparaat**. Als dit item niet onder updates wordt weer gegeven, is de meest recente versie al geïnstalleerd.
1. Zorg ervoor dat de vermelde codec ten minste versie **1.0.21821.0**.
1. Klik op de knop **updates ophalen** en wacht totdat deze is geïnstalleerd.

## <a name="network"></a>Netwerk

Een stabiele netwerk verbinding met lage latentie is essentieel voor een goede gebruikers ervaring.

Zie speciaal hoofd stuk voor [netwerk vereisten](../reference/network-requirements.md).

Raadpleeg de [hand leiding](../resources/troubleshoot.md#unstable-holograms)voor het oplossen van problemen met netwerk problemen.

### <a name="network-firewall"></a>Netwerkfirewall

### <a name="sdk-version--0176"></a>SDK-versie >= 0.1.76

Externe rendering van virtuele machines gebruiken gedeelde IP-adressen uit de volgende IP-adresbereiken:

| Name             | Region         | IP-voor voegsel         |
|------------------|:---------------|:------------------|
| Australië - oost   | australiaeast  | 20.53.44.240/28   |
| VS - oost          | eastus         | 20.62.129.224/28  |
| VS - oost 2        | eastus2        | 20.49.103.240/28  |
| Japan - oost       | japaneast      | 20.191.165.112/28 |
| Europa - noord     | northeurope    | 52.146.133.64/28  |
| VS - zuid-centraal | southcentralus | 20.65.132.80/28   |
| Azië - zuidoost   | southeastasia  | 20.195.64.224/28  |
| Verenigd Koninkrijk Zuid         | uksouth        | 51.143.209.144/28 |
| Europa -west      | westeurope     | 20.61.99.112/28   |
| VS - west 2        | westus2        | 20.51.9.64/28     |

Zorg ervoor dat de firewalls (op apparaat, binnen routers, enzovoort) deze IP-bereiken en de volgende poortbereiken niet blok keren:

| Poort              | Protocol  | Toestaan    |
|-------------------|---------- |----------|
| 49152-65534       | TCP/UDP | Verzendt |

#### <a name="sdk-version--0176"></a>SDK-versie < 0.1.76

Zorg ervoor dat de firewalls (op het apparaat, binnen routers, enzovoort) niet de volgende poorten blok keren:

| Poort              | Protocol | Toestaan    | Beschrijving |
|-------------------|----------|----------|-------------|
| 50051             | TCP      | Verzendt | Eerste verbinding (HTTP-Handshake) |
| 8266              | UDP      | Verzendt | Gegevensoverdracht |
| 5000, 5433, 8443  | TCP      | Verzendt | Vereist voor het [hulp programma ArrInspector](../resources/tools/arr-inspector.md)|


## <a name="software"></a>Software

De volgende software moet zijn geïnstalleerd:

* De nieuwste versie van **Visual Studio 2019** [(down load)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools voor Mixed Reality](/windows/mixed-reality/install-the-tools). Met name de volgende *workload*-installaties zijn verplicht:
  * **Desktopontwikkeling met C++**
  * **Universal Windows Platform (UWP)-ontwikkeling**
* **Windows SDK 10.0.18362.0** [(down load)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(down load)](https://git-scm.com/downloads)
* Optioneel: als u de video stroom van de server op een desktop computer wilt weer geven, hebt u de **HEVC-video-extensie** [(Microsoft Store link)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)nodig. Controleer of de meest recente versie is geïnstalleerd door te controleren op updates in de Store.

## <a name="unity"></a>Unity

Voor ontwikkeling met Unit installeert u een huidige versie van Unity 2019,3 of 2019,4 LTS [(down load)](https://unity3d.com/get-unity/download). U kunt het beste Unity hub gebruiken voor het beheren van installaties.
Zorg ervoor dat u de volgende modules opneemt in uw Unity-installatie:
* **UWP**: ondersteuning voor UWP-builds (Universeel Windows-platform)
* **IL2CPP**: ondersteuning voor Windows-builds (IL2CPP)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een model weergeven met Unity](../quickstarts/render-model.md)