---
title: Gegevens verzameling configureren voor de Azure Monitor-agent (preview)
description: Hierin wordt beschreven hoe u een regel voor het verzamelen van gegevens maakt voor het verzamelen van gegevens van virtuele machines met behulp van de Azure Monitor-agent.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 8943986bf8e8c082889d3a0b18618ac54c75e6d6
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022973"
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

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Gegevens verzameling beperken met aangepaste XPath-query's
Omdat er in rekening worden gebracht voor gegevens die zijn verzameld in een Log Analytics-werk ruimte, moet u alleen de gegevens verzamelen die u nodig hebt. Met behulp van de basis configuratie in de Azure Portal hebt u slechts beperkte mogelijkheid om gebeurtenissen te filteren die moeten worden verzameld. Voor toepassings-en systeem logboeken zijn dit alle logboeken met een bepaalde ernst. Voor beveiligings Logboeken is dit alle geslaagde controle slagen of Logboeken voor het controleren van fouten.

Als u aanvullende filters wilt opgeven, moet u aangepaste configuratie gebruiken en een XPath opgeven dat de gebeurtenissen die u niet wilt filteren. XPath-vermeldingen worden in het formulier geschreven `LogName!XPathQuery` . U kunt bijvoorbeeld alleen gebeurtenissen uit het toepassings gebeurtenis logboek retour neren met de gebeurtenis-ID 1035. De XPathQuery voor deze gebeurtenissen zouden zijn `*[System[EventID=1035]]` . Aangezien u de gebeurtenissen uit het toepassings gebeurtenis logboek wilt ophalen, zou het XPath worden `Application!*[System[EventID=1035]]`

Zie de [beperkingen van xpath 1,0](/windows/win32/wes/consuming-events#xpath-10-limitations) voor een lijst met beperkingen in het XPath dat wordt ondersteund door het Windows-gebeurtenis logboek.

> [!TIP]
> Gebruik de Power shell-cmdlet `Get-WinEvent` met de `FilterXPath` para meter om de geldigheid van een XPathQuery te testen. Het volgende script toont een voor beeld.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Als er gebeurtenissen worden geretourneerd, is de query geldig.
> - Als u het bericht ontvangt *dat er geen gebeurtenissen zijn gevonden die overeenkomen met de opgegeven selectie criteria*, kan de query geldig zijn, maar er zijn geen overeenkomende gebeurtenissen op de lokale computer.
> - Als u het bericht ontvangt *dat de opgegeven query ongeldig is* , is de query syntaxis ongeldig. 

De volgende tabel bevat voor beelden voor het filteren van gebeurtenissen met een aangepast XPath.

| Beschrijving |  XPath |
|:---|:---|
| Alleen systeem gebeurtenissen verzamelen met gebeurtenis-ID = 4648 |  `System!*[System[EventID=4648]]`
| Verzamel alleen systeem gebeurtenissen met gebeurtenis-ID = 4648 en een proces naam van consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Alle gebeurtenissen met betrekking tot kritiek, fout, waarschuwing en informatie verzamelen in het gebeurtenis logboek van het systeem, met uitzonde ring van gebeurtenis-ID = 6 (stuur programma geladen) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Alle geslaagde en mislukte beveiligings gebeurtenissen verzamelen, behalve gebeurtenis-ID 4624 (geslaagde aanmelding) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


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
