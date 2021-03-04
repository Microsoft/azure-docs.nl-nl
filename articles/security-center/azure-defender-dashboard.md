---
title: Het dash board en de functies van Azure Defender
description: Meer informatie over de functies van het Azure Defender-dash board.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096901"
---
# <a name="the-azure-defender-dashboard"></a>Het Azure Defender-dash board

Het Azure Defender-dash board biedt:

- Zicht baarheid van uw Azure Defender-dekking in uw verschillende resource typen
- Koppelingen voor het configureren van geavanceerde beveiliging tegen bedreigingen
- De onboarding-status en installatie van de agent
- Waarschuwingen voor detectie van Azure Defender-dreigingen 

Om toegang te krijgen tot het Azure Defender-dash board, selecteert u **Azure Defender** in het gedeelte Cloud beveiliging van Security Center menu.

## <a name="whats-shown-on-the-dashboard"></a>Wat wordt er op het dash board weer gegeven?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Een voorbeeld van het Azure Defender-dashboard" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Het dash board bevat de volgende secties:

1. **Azure Defender-dekking** : hier kunt u de typen resources zien die zich in uw abonnement bevinden en die in aanmerking komen voor beveiliging door Azure Defender. U kunt waar nodig ook een upgrade uitvoeren. Als u alle mogelijk in aanmerking komende resources wilt bijwerken, selecteert u **Alles bijwerken**.

2. **Beveiligings waarschuwingen** : wanneer Azure Defender een bedreiging in een wille keurig gebied van uw omgeving detecteert, wordt er een waarschuwing gegenereerd. Deze waarschuwingen bevatten details over de relevante bronnen, voorgestelde stappen voor herstel en soms ook de optie om een logische app te activeren. Als u ergens in deze grafiek selecteert, wordt de **pagina beveiligings waarschuwingen** geopend.

3. **Geavanceerde beveiliging** : Azure Defender bevat veel geavanceerde mogelijkheden voor beveiliging tegen bedreigingen voor virtuele machines, SQL-data bases, containers, webtoepassingen, uw netwerk en meer. In deze sectie over geavanceerde beveiliging ziet u de status van de resources in de geselecteerde abonnementen voor elk van deze beveiligingen. Selecteer een van de gewenste items om rechtstreeks naar het configuratie gebied voor dat beveiligings type te gaan.

4. **Inzichten** : in dit vervolg deel venster van nieuws, aanbevolen Lees bewerkingen en waarschuwingen met hoge prioriteit krijgt Security Center inzicht in de beveiligings kwesties die relevant zijn voor u en uw abonnement. Of het nu een lijst is met hoge Ernst CVEs die op uw Vm's zijn gedetecteerd door een analyse programma voor beveiligings problemen of een nieuw blog bericht door een lid van het Security Center team, vindt u dit hier in het deel venster inzichten van uw **Azure Defender-dash board**.




## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het Azure Defender-dash board. 

Zie [Inleiding tot Azure Defender](azure-defender.md) voor meer informatie over Azure Defender

> [!div class="nextstepaction"]
> [Azure Defender inschakelen](enable-azure-defender.md)