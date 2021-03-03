---
title: VMware ESXi gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de VMware ESXi Data Connector voor het ophalen van ESXi-Logboeken in azure Sentinel. Bekijk ESXi-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2021
ms.author: yelevin
ms.openlocfilehash: 3d478a9ac3cf91f3f6815859b8534efff88f07b1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700868"
---
# <a name="connect-your-vmware-esxi-to-azure-sentinel"></a>Uw VMware ESXi verbinden met Azure Sentinel

> [!IMPORTANT]
> De VMware ESXi-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw VMware ESXi apparaat verbindt met Azure Sentinel. Met de VMware ESXi Data Connector kunt u eenvoudig uw VMware ESXi-logboeken opnemen in azure Sentinel, zodat u meer inzicht hebt in de ESXi-activiteiten van uw organisatie en de mogelijkheden voor beveiligings bewerkingen verbetert. Integratie tussen VMware ESXi en Azure-Sentinel maakt gebruik van een syslog-server waarop de Log Analytics-agent is geïnstalleerd. Er wordt ook een aangepaste, gegenereerde logboek-parser gebruikt op basis van een Kusto-functie.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Uw VMware ESXi-oplossing moet worden geconfigureerd voor het exporteren van Logboeken via syslog.

## <a name="send-vmware-esxi-logs-to-the-syslog-agent"></a>VMware ESXi logboeken naar de syslog-agent verzenden  

Configureer VMware ESXi voor het door sturen van syslog-berichten naar uw Azure Sentinel-werk ruimte via de syslog-agent.
3. Volg de instructies op de pagina **VMware ESXi** .


1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **Data connectors** de **VMware ESXi (preview)** -connector en open vervolgens de **pagina connector**.

1. Volg de instructies op de pagina **VMware ESXi** connector:

    1. De agent voor Linux installeren en vrijgeven

        - Kies een Azure Linux-VM of een niet-Azure Linux-machine (fysiek of virtueel).

    1. De logboeken configureren die moeten worden verzameld

        - Selecteer de faciliteiten en ernst in de configuratie van de **werkruimte agent**.

    1. De VMware ESXi configureren en verbinden

        - Volg deze instructies om de VMware ESXi te configureren voor het door sturen van syslog:
            - [VMware ESXi 3,5 en 4. x](https://kb.vmware.com/s/article/1016621)
            - [VMware ESXi 5,0 en hoger](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.monitoring.doc/GUID-9F67DB52-F469-451F-B6C8-DAE8D95976E7.html)

            Voor de externe server gebruikt u het IP-adres van de Linux-computer waarop u de Linux-agent hebt geïnstalleerd.

## <a name="validate-connectivity-and-find-your-data"></a>Connectiviteit valideren en uw gegevens zoeken

Het kan Maxi maal 20 minuten duren voordat uw logboeken in azure Sentinel worden weer gegeven. 

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie *logboek beheer* in de *syslog* -tabel.

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. [Volg deze stappen](https://aka.ms/sentinel-vmwareesxi-parser) om de alias voor de functie **VMwareESXi** Kusto te maken. Vervolgens kunt u `VMwareESXi` in een query venster in azure Sentinel typen om query's uit te voeren op de gegevens.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u VMware ESXi kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
