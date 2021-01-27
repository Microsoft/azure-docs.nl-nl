---
title: Azure Security Center gebruiken om uw docker-hosts te beveiligen en de containers te beschermen
description: De docker-hosts beveiligen en controleren of ze compatibel zijn met de CIS docker Bench Mark
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916231"
---
# <a name="harden-your-docker-hosts"></a>Docker-hosts verharden

Azure Security Center identificeert niet-beheerde containers die worden gehost op IaaS Linux-VM's of andere Linux-machines waarop Docker-containers worden uitgevoerd. Security Center evalueert doorlopend de configuraties van deze containers. Vervolgens worden ze vergeleken met de [Docker-benchmark van het CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

Security Center bevat de volledige regelset van de CIS Docker-benchmark en waarschuwt u als uw containers niet voldoen aan een van de controles. Als er onjuiste configuraties worden gevonden, worden in Security Center aanbevelingen voor de beveiliging gegenereerd. U kunt de aanbevelingen bekijken en problemen herstellen met behulp van de **pagina met aanbevelingen van Security Center**.

Als er beveiligings problemen worden gevonden, worden deze gegroepeerd in één aanbeveling.

>[!NOTE]
> Deze CIS-benchmark controles worden niet uitgevoerd op door AKS beheerde instanties of door Databricks beheerde Vm's.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Vereiste rollen en machtigingen:|**Lezer** op de werk ruimte waarmee de host verbinding maakt|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Beveiligings problemen in uw docker-configuratie identificeren en oplossen

1. Open de pagina **aanbevelingen** vanuit het menu van Security Center.

1. Filter op de aanbevolen **beveiligings problemen in container beveiligings configuraties moet worden hersteld** en selecteer de aanbeveling.

    De pagina aanbeveling bevat de betrokken resources (docker-hosts). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Aanbeveling voor het oplossen van beveiligings problemen in container beveiligings configuraties ":::

1. Als u de CIS-besturings elementen wilt weer geven en herstellen waarvoor een specifieke host is mislukt, selecteert u de host die u wilt onderzoeken. 

    > [!TIP]
    > Als u op de pagina Asset Inventory bent begonnen en deze aanbeveling van daaruit hebt bereikt, selec de knop **actie ondernemen** op de pagina aanbeveling.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Actie knop ondernemen om Log Analytics te starten":::

    Log Analytics wordt geopend met een aangepaste bewerking die kan worden uitgevoerd. De aangepaste standaard query bevat een lijst met alle mislukte regels die zijn geëvalueerd, samen met richt lijnen voor het oplossen van de problemen.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Log Analytics pagina met de query alle mislukte CIS-besturings elementen weer geven":::

1. Pas de query parameters zo nodig aan.

1. Wanneer u zeker weet dat de opdracht geschikt is en gereed is voor uw host, selecteert u **uitvoeren**.


## <a name="next-steps"></a>Volgende stappen

Docker-beveiliging is slechts één aspect van de beveiligings functies van Security Center-container. 

Meer informatie over de [beveiliging van de container in Security Center](container-security.md).