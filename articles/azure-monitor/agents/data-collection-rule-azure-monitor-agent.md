---
title: Gegevens verzameling configureren voor de Azure Monitor-agent (preview)
description: Hierin wordt beschreven hoe u een regel voor het verzamelen van gegevens maakt voor het verzamelen van gegevens van virtuele machines met behulp van de Azure Monitor-agent.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 93e244706d6d478155ac001d20fa3ce74fa6a887
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723636"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Gegevens verzameling configureren voor de Azure Monitor-agent (preview)

Met regels voor gegevens verzameling (DCR) kunt u gegevens in Azure Monitor definiëren en opgeven waar deze moeten worden verzonden. In dit artikel wordt beschreven hoe u een regel voor het verzamelen van gegevens maakt voor het verzamelen van gegevens van virtuele machines met behulp van de Azure Monitor-agent.

Zie [regels voor gegevens verzameling in azure monitor (preview)](data-collection-rule-overview.md)voor een volledige beschrijving van regels voor het verzamelen van gegevens.

> [!NOTE]
> In dit artikel wordt beschreven hoe u gegevens configureert voor virtuele machines met de Azure Monitor-agent die momenteel als preview-versie beschikbaar is. Zie [overzicht van Azure monitor agents](agents-overview.md) voor een beschrijving van de agents die algemeen beschikbaar zijn en hoe u deze kunt gebruiken om gegevens te verzamelen.

## <a name="data-collection-rule-associations"></a>Regel koppelingen voor gegevens verzameling

Als u een DCR wilt Toep assen op een virtuele machine, maakt u een koppeling voor de virtuele machine. Een virtuele machine kan een koppeling hebben met meerdere Dcr's en aan een DCR kunnen meerdere virtuele machines zijn gekoppeld. Zo kunt u een set Dcr's definiëren, elk met een bepaalde vereiste en deze Toep assen op alleen de virtuele machines waarop ze van toepassing zijn. 

Denk bijvoorbeeld aan een omgeving met een set virtuele machines waarop een line-of-Business-toepassing en andere SQL Server worden uitgevoerd. Mogelijk hebt u één standaard regel voor gegevens verzameling die van toepassing is op alle virtuele machines en afzonderlijke regels voor het verzamelen van gegevens die specifiek zijn voor de line-of-Business-toepassing en voor SQL Server. De koppelingen voor de virtuele machines met de regels voor het verzamelen van gegevens zouden er ongeveer uit zien als in het volgende diagram.

![In het diagram worden virtuele machines weer gegeven met een line-of-Business-toepassing en SQL Server gekoppeld aan regels voor gegevens verzameling met de naam centraal-i t-standaard en LOB-app voor line-of-Business-toepassing en centraal-i t-standaard en s q l voor SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Regel en koppeling maken in Azure Portal

U kunt de Azure Portal gebruiken om een regel voor het verzamelen van gegevens te maken en virtuele machines in uw abonnement aan die regel te koppelen. De Azure Monitor-agent wordt automatisch geïnstalleerd en een beheerde identiteit die is gemaakt voor een virtuele machine waarop deze nog niet is geïnstalleerd.

> [!IMPORTANT]
> Er is momenteel een bekend probleem waarbij de door de gebruiker toegewezen identiteit wordt uitgeschakeld als de regel voor het verzamelen van gegevens een beheerde identiteit maakt op een virtuele machine die al een door de gebruiker toegewezen beheerde identiteit heeft.

Selecteer in het menu **Azure monitor** van de Azure Portal **gegevens verzamelings regels** uit de sectie **instellingen** . Klik op **toevoegen** om een nieuwe regel en toewijzing voor het verzamelen van gegevens toe te voegen.

[![Regels voor gegevens verzameling](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Klik op **toevoegen** om een nieuwe regel en een set met koppelingen te maken. Geef een **regel naam** op en geef een **abonnement** en een **resource groep** op. Hiermee wordt aangegeven waar de DCR wordt gemaakt. De virtuele machines en de bijbehorende koppelingen kunnen zich in elk abonnement of resource groep in de Tenant bevindt.

[![Basis principes van gegevens verzamelings regel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Voeg op het tabblad **virtuele machines** virtuele machines toe waarop de regel voor het verzamelen van gegevens moet worden toegepast. Zowel virtuele Azure-machines als Azure Arc-servers in uw omgeving moeten worden weer gegeven. De Azure Monitor-agent wordt geïnstalleerd op virtuele machines waarop deze nog niet is geïnstalleerd.

[![Gegevens verzamelings regel virtuele machines](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Klik op het tabblad **verzamelen en leveren** op **gegevens bron toevoegen** om een gegevens bron en een doel set toe te voegen. Selecteer een **type gegevens bron** en de bijbehorende details die moeten worden geselecteerd, worden weer gegeven. Voor prestatie meter items kunt u een keuze uit een vooraf gedefinieerde set met objecten en de bijbehorende sampling frequentie selecteren. Voor gebeurtenissen kunt u kiezen uit een set Logboeken of opslag ruimten en het Ernst niveau. 

[![Basis gegevens bron](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Als u andere logboeken en prestatie meter items van de [momenteel ondersteunde gegevens bronnen](azure-monitor-agent-overview.md#data-sources-and-destinations) wilt opgeven of als u gebeurtenissen wilt filteren met XPath-query's, selecteert u **aangepast**. U kunt vervolgens een [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) opgeven voor alle specifieke waarden die moeten worden verzameld. Zie voor beelden van [DCR](data-collection-rule-overview.md#sample-data-collection-rule) voor.

[![Aangepaste gegevens bron](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Voeg op het tabblad **bestemming** een of meer bestemmingen voor de gegevens bron toe. Windows-gebeurtenis-en syslog-gegevens bronnen kunnen alleen worden verzonden naar Azure Monitor-Logboeken. Prestatie meter items kunnen worden verzonden naar Azure Monitor metrische gegevens en Azure Monitor Logboeken.

[![Doel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klik op **gegevens bron toevoegen** en vervolgens op **+ maken** om de details te bekijken van de regel voor het verzamelen van gegevens en de koppeling met de set vm's. Klik op **maken** om de app te maken.

> [!NOTE]
> Nadat de regels voor gegevens verzameling en-koppelingen zijn gemaakt, kan het tot vijf minuten duren voordat gegevens naar de doelen worden verzonden.


## <a name="create-rule-and-association-using-rest-api"></a>Regel en koppeling maken met behulp van REST API

Volg de onderstaande stappen om een regel en koppelingen voor het verzamelen van gegevens te maken met behulp van de REST API.

1. Maak het DCR-bestand hand matig met de JSON-indeling die wordt weer gegeven in voor [beeld van DCR](data-collection-rule-overview.md#sample-data-collection-rule).

2. Maak de regel met behulp van de [rest API](/rest/api/monitor/datacollectionrules/create#examples).

3. Maak een koppeling voor elke virtuele machine met de gegevens verzamelings regel met behulp van de [rest API](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Koppeling maken met Resource Manager-sjabloon

U kunt geen regel voor het verzamelen van gegevens maken met een resource manager-sjabloon, maar een koppeling tussen een Azure virtual machine of een Azure Arc-server maken met behulp van een resource manager-sjabloon. Zie voor [beelden van Resource Manager-sjablonen voor regels voor gegevens verzameling in azure monitor](./resource-manager-data-collection-rules.md) voor voorbeeld sjablonen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Azure monitor-agent](azure-monitor-agent-overview.md).
- Meer informatie over [regels](data-collection-rule-overview.md)voor het verzamelen van gegevens.