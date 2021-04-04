---
title: Routerings methode voor prestatie verkeer configureren met behulp van Azure Traffic Manager | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Traffic Manager configureert om verkeer te routeren naar het eind punt met de laagste latentie
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994907"
---
# <a name="configure-the-performance-traffic-routing-method"></a>De routerings methode voor prestatie verkeer configureren

Met de methode voor het routerings verkeer van prestaties kunt u verkeer naar het eind punt sturen met de laagste latentie van het netwerk van de client. Normaal gesp roken is het Data Center met de laagste latentie het dichtst bij de geografische afstand. Met deze routerings methode voor verkeer kan geen realtime-wijzigingen in de netwerk configuratie of het laden worden verwerkt.

##  <a name="to-configure-performance-routing-method"></a>Routerings methode voor prestaties configureren

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com). Als u nog geen account hebt, kunt u zich registreren voor een [gratis proefversie van één maand](https://azure.microsoft.com/free/). 
2. Zoek in de zoekbalk van de portal naar de **Traffic Manager-profielen** en klik vervolgens op de profielnaam waarvoor u de routeringsmethode wilt configureren.
3. Verifieer in de blade van het **Traffic Manager-profiel** of zowel de cloudservices als websites die u in uw configuratie wilt opnemen aanwezig zijn.
4. Klik in de sectie **Instellingen** op **Configuratie** en voer in de blade **Configuratie** de volgende configuratie uit:
    1. Voor de **instellingen voor verkeers routerings methode** voor **routerings methode** selecteert u **prestaties**.
    2. Stel de **Controle-instellingen voor het eindpunt** voor alle eindpunten in dit profiel hetzelfde in en doe dit als volgt:
        1. Selecteer het juiste **Protocol** en geef het **Poortnummer** op. 
        2. Typ bij **Pad** een slash */* . Als u eindpunten wilt controleren, moet u een pad en bestandsnaam opgeven. Een slash (/) is een geldige vermelding voor het relatieve pad en impliceert dat het bestand zich in de hoofdmap bevindt (standaard).
        3. Klik bovenaan de pagina op **Opslaan**.
5.  Test de wijzigingen in uw configuratie als volgt:
    1.  Zoek in de zoekbalk van de portal naar de naam van het Traffic Manager-profiel en klik in de weergegeven resultaten op het Traffic Manager-profiel.
    2.  Klik in de blade met het **Traffic Manager**-profiel op **Overzicht**.
    3.  De blade **Traffic Manager-profiel** geeft de DNS-naam weer van het Traffic Manager-profiel dat u zojuist hebt gemaakt. Dit kan door clients worden gebruikt (bijvoorbeeld door ernaar te navigeren met een webbrowser) om naar het juiste eindpunt te gaan, zoals wordt bepaald door het routeringstype. In dit geval worden alle aanvragen doorgestuurd naar het eind punt met de laagste latentie van het netwerk van de client.
6. Als uw Traffic Manager-profiel werkt, kunt u de DNS-record op uw gezaghebbende DNS-server zo bewerken dat de domeinnaam van uw bedrijf verwijst naar de Traffic Manager-domeinnaam.

![Routerings methode voor prestatie verkeer configureren met Traffic Manager][1]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gewogen verkeersrouteringsmethode](traffic-manager-configure-weighted-routing-method.md).
- Meer informatie over [de routeringsmethode met prioriteit](traffic-manager-configure-priority-routing-method.md).
- Meer informatie over [geografische verkeersrouteringsmethode](traffic-manager-configure-geographic-routing-method.md).
- Meer informatie over het [testen van Traffic Manager-instellingen](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png