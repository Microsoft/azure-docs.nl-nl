---
title: Virtuele Azure-hubs beveiligen met Check Point Cloudguard Connect
description: Meer informatie over Check Point CloudGuard verbinding maken met beveiligde Azure Virtual hubs
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: victorh
ms.openlocfilehash: 3c61dc689d19e1a7d6f9b6dbefae846e9458d750
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146855"
---
# <a name="secure-virtual-hubs-using-check-point-cloudguard-connect"></a>Virtuele hubs beveiligen met Check Point Cloudguard Connect

Check Point CloudGuard Connect is een vertrouwde beveiligings partner in Azure Firewall Manager. Het beveiligt wereld wijd gedistribueerd filiaal op internet (B2I) of virtuele netwerk verbindingen (V2I) met geavanceerde preventie van bedreigingen. 

Met een eenvoudige configuratie in Azure Firewall Manager kunt u vertakkings-hub en virtuele netwerk verbindingen naar Internet routeren via de CloudGuard Connect Security as a Service (SECaaS). Verkeer wordt door Voer van uw hub naar de Cloud Service Check Point in IPsec VPN-tunnels beveiligd.

Wanneer u automatische synchronisatie in de Check Point-Portal inschakelt, wordt elke bron die als *beveiligd* is gemarkeerd in de Azure Portal automatisch beveiligd. U hoeft uw assets niet twee maal te beheren. U kunt ze slechts eenmaal in de Azure Portal beveiligen.

Het controle punt verenigt meerdere beveiligings Services onder één paraplu. Geïntegreerd beveiligings verkeer wordt eenmaal ontsleuteld en in één keer geïnspecteerd. Toepassings beheer, URL-filtering en inhouds bewustzijn (DLP) voor het afdwingen van veilig gebruik van Internet en het beveiligen van uw gegevens. IP'S en anti virus beschermen gebruikers tegen bekende netwerk aanvallen. Anti-bot blokkeert verbindingen met opdracht-en controle servers en waarschuwt u als een host is geïnfecteerd.

Met bedreigings emulatie (Sandboxing) worden gebruikers beschermd tegen onbekende en Zero-Day bedreigingen. Check Point Sand blast Zero-Day Protection is een in de Cloud gehoste zand boxing-technologie waarbij bestanden snel in quarantaine worden geplaatst en geïnspecteerd. Deze wordt uitgevoerd in een virtuele sandbox om schadelijk gedrag te ontdekken voordat het in uw netwerk binnenkomt. Zo wordt voor komen dat er bedreigingen worden gemaakt voordat de schade wordt bespaard op het besparen van mede werkers tijd. 

## <a name="deployment-example"></a>Voor beeld van implementatie

Bekijk de volgende video om te zien hoe u Check Point CloudGuard Connect als een vertrouwde Azure-beveiligings partner implementeert.

> [!VIDEO https://www.youtube.com/embed/C8AuN76DEmU]

## <a name="next-steps"></a>Volgende stappen

- [Een beveiligingspartnerprovider implementeren](deploy-trusted-security-partner.md)