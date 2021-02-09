---
title: Systeem vereisten voor Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Meer informatie over de software-en netwerk vereisten voor uw Azure Stack Edge Pro
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: c87c8cfd40903f6296f0a36f5c83c0b5c5cb7818
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831571"
---
# <a name="azure-stack-edge-pro-system-requirements"></a>Systeem vereisten voor Azure Stack Edge Pro

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Microsoft Azure Stack Edge Pro-oplossing en voor de clients die verbinding maken met Azure Stack Edge Pro. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Azure Stack Edge Pro implementeert. U kunt deze informatie naar behoefte terugsturen tijdens de implementatie en de volgende bewerking.

De systeem vereisten voor de Azure Stack Edge Pro zijn onder andere: 

- **Software vereisten voor hosts** : beschrijft de ondersteunde platforms, browsers voor de lokale configuratie-UI, SMB-clients en eventuele aanvullende vereisten voor de clients die toegang hebben tot het apparaat.
- **Netwerk vereisten voor het apparaat** : bevat informatie over eventuele netwerk vereisten voor de werking van het fysieke apparaat.

## <a name="supported-os-for-clients-connected-to-device"></a>Ondersteund besturings systeem voor clients die zijn verbonden met het apparaat

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Ondersteunde protocollen voor clients die toegang hebben tot het apparaat

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Ondersteunde browsers voor de lokale web-UI

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Vereisten voor netwerk poort

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Poort vereisten voor Azure Stack Edge Pro

De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om SMB-, Cloud-of beheer verkeer toe te staan. In deze tabel verwijst *naar* of *binnenkomend* naar de richting waarin de inkomende client toegang tot uw apparaat vraagt. *Out* of *uitgaand* verwijst naar de richting waarin uw Azure stack Edge Pro-apparaat gegevens extern verzendt, bijvoorbeeld uitgaand naar Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Poort vereisten voor IoT Edge

Azure IoT Edge staat uitgaande communicatie van een on-premises edge-apparaat naar Azure-Cloud toe met ondersteunde IoT Hub protocollen. Inkomende communicatie is alleen vereist voor specifieke scenario's waarbij Azure IoT Hub berichten naar het Azure IoT Edge apparaat moet pushen (bijvoorbeeld Cloud-naar-apparaat-berichten).

Gebruik de volgende tabel voor poort configuratie voor de servers die als host fungeren voor Azure IoT Edge runtime:

| Poort nummer | In of uit | Poort bereik | Vereist | Hulp |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Uit       | WAN        | Ja      | Uitgaand openen voor IoT Edge inrichting. Deze configuratie is vereist wanneer u hand matige scripts of een Azure IoT Device Provisioning Service (DPS) gebruikt.|

Voor volledige informatie gaat u naar de [firewall-en poort configuratie regels voor IOT Edge-implementatie](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewall regels

Netwerk beheerders kunnen regel matig geavanceerde firewall regels configureren op basis van de URL-patronen om het binnenkomende en uitgaande verkeer te filteren. Uw Azure Stack Edge Pro-apparaat en de service zijn afhankelijk van andere micro soft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslag accounts en Microsoft Update servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt voor het configureren van firewall regels. Het is belang rijk te weten dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gewijzigd. Deze wijzigingen vereisen dat de netwerk beheerder firewall regels voor uw Azure Stack Edge Pro kan controleren en bijwerken als dat nodig is.

We raden u aan de firewall regels voor uitgaand verkeer in te stellen, op basis van Azure Stack Edge Pro vaste IP-adressen in de meeste gevallen. U kunt echter de onderstaande informatie gebruiken om geavanceerde firewall regels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> - De IP-adressen van het apparaat moeten altijd worden ingesteld op alle netwerk interfaces die zijn ingeschakeld voor de Cloud.
> - De doel-Ip's moeten worden ingesteld op [Azure Data Center IP-bereiken](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>URL-patronen voor de gateway functie

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-patronen voor de functie compute

| URL-patroon                      | Onderdeel of functionaliteit                     |   
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.io | Micro soft container Registry (vereist)               |
| https:// \* . azurecr.io                     | Persoonlijke en container registers van derden (optioneel) | 
| https:// \* . Azure-devices.net              | IoT Hub toegang (vereist)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-patronen voor de gateway voor Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-patronen voor het berekenen van Azure Government

| URL-patroon                      | Onderdeel of functionaliteit                     |  
|----------------------------------|---------------------------------------------|
| https: \/ /MCR.Microsoft.com<br></br>https:// \* . CDN.mscr.com | Micro soft container Registry (vereist)               |
| https:// \* . Azure-devices.us              | IoT Hub toegang (vereist)           |
| https:// \* . azurecr.us                    | Persoonlijke en container registers van derden (optioneel) | 

## <a name="internet-bandwidth"></a>Internet bandbreedte

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Overwegingen bij reken grootte

Gebruik uw ervaring bij het ontwikkelen en testen van uw oplossing om ervoor te zorgen dat u over voldoende capaciteit beschikt op uw Azure Stack Edge Pro-apparaat en u beschikt over de optimale prestaties van uw apparaat.

Denk hierbij aan de volgende factoren:

- **Container Details** : denk aan het volgende.

    - Hoeveel containers bevinden zich in uw werk belasting? U kunt een groot aantal licht gewicht containers hebben in plaats van een aantal bronnen.
    - Wat zijn de resources die zijn toegewezen aan deze containers en wat zijn de resources die ze gebruiken?
    - Hoeveel lagen delen uw containers?
    - Zijn er ongebruikte containers? Een gestopt container neemt nog steeds schijf ruimte in beslag.
    - In welke taal zijn uw containers geschreven?
- **Grootte van de verwerkte gegevens** -hoeveel gegevens worden er door uw containers verwerkt? Nemen deze gegevens schijf ruimte in beslag of worden de gegevens in het geheugen verwerkt?
- **Verwachte prestaties** : wat zijn de gewenste prestatie kenmerken van uw oplossing? 

Als u de prestaties van uw oplossing wilt begrijpen en verfijnen, kunt u het volgende gebruiken:

- De metrische reken gegevens die beschikbaar zijn in de Azure Portal. Ga naar uw Azure Stack Edge-resource en ga vervolgens naar **bewaking > metrische gegevens**. Bekijk de **Edge Compute-Memory Usage** en **Edge Compute-percentage CPU** om inzicht te krijgen in de beschik bare bronnen en hoe de bronnen worden gebruikt.
- De beschik bare bewakings opdrachten via de Power shell-interface van het apparaat, zoals:

    - `dkr` statistieken voor het ophalen van een live stream van de container (s) voor het resource gebruik. De opdracht ondersteunt CPU, geheugen gebruik, geheugen limiet en metrische gegevens voor netwerk-i/o.
    - `dkr system df` om informatie te krijgen over de hoeveelheid schijf ruimte die wordt gebruikt. 
    - `dkr image [prune]` ongebruikte installatie kopieën opruimen en ruimte vrijmaken.
    - `dkr ps --size` de geschatte grootte van een actieve container weer geven. 

    Ga voor meer informatie over de beschik bare opdrachten naar [bewaken en problemen oplossen van Compute-modules](azure-stack-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Ten slotte moet u ervoor zorgen dat u uw oplossing valideert op uw gegevensset en de prestaties van Azure Stack Edge Pro te kwantificeren voordat u de implementatie in productie neemt.


## <a name="next-step"></a>Volgende stap

- [Uw Azure Stack Edge Pro implementeren](azure-stack-edge-deploy-prep.md)