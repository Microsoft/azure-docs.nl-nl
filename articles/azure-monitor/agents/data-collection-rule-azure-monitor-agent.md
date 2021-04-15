---
title: Gegevensverzameling configureren voor de Azure Monitor agent (preview)
description: Beschrijft hoe u een regel voor gegevensverzameling maakt voor het verzamelen van gegevens van virtuele machines met behulp van Azure Monitor agent.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 884f048ac099cfc6b799fe266172a0eecef3db6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478366"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Gegevensverzameling configureren voor de Azure Monitor agent (preview)

DcR-regels (Data Collection Rules) definiëren gegevens die in de Azure Monitor en geven aan waar deze moeten worden verzonden. In dit artikel wordt beschreven hoe u een regel voor gegevensverzameling maakt voor het verzamelen van gegevens van virtuele machines met behulp van Azure Monitor agent.

Zie Regels voor gegevensverzameling in Azure Monitor (preview) voor een volledige beschrijving van regels [voor gegevensverzameling.](data-collection-rule-overview.md)

> [!NOTE]
> In dit artikel wordt beschreven hoe u gegevens voor virtuele machines configureert met de Azure Monitor agent die momenteel in preview is. Zie [Overzicht van Azure Monitor agents voor](agents-overview.md) een beschrijving van agents die algemeen beschikbaar zijn en hoe u deze kunt gebruiken om gegevens te verzamelen.

## <a name="data-collection-rule-associations"></a>Regel verbanden voor gegevensverzameling

Als u een DCR wilt toepassen op een virtuele machine, maakt u een associatie voor de virtuele machine. Een virtuele machine kan een associatie hebben met meerdere DCR's en er kunnen meerdere virtuele machines aan een DCR zijn gekoppeld. Hiermee kunt u een set dcr's definiëren, die elk overeenkomen met een bepaalde vereiste, en deze alleen toepassen op de virtuele machines waarop ze van toepassing zijn. 

Denk bijvoorbeeld aan een omgeving met een set virtuele machines met een Line-Of-Business-toepassing en andere machines die SQL Server. Mogelijk hebt u één standaardregel voor gegevensverzameling die van toepassing is op alle virtuele machines en afzonderlijke regels voor gegevensverzameling die specifiek gegevens verzamelen voor de Line-Of-Business-toepassing en voor SQL Server. De associaties voor de virtuele machines met de regels voor gegevensverzameling zouden er ongeveer uitzien als in het volgende diagram.

![Diagram met virtuele machines die line-of-business-toepassing hosten en SQL Server gekoppeld aan regels voor gegevensverzameling met de naam central-i t-default en lob-app voor line-of-business-toepassing en central-i t-default en s q l voor SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Regel en associatie maken in Azure Portal

U kunt de Azure Portal om een regel voor gegevensverzameling te maken en virtuele machines in uw abonnement aan die regel te koppelen. De Azure Monitor-agent wordt automatisch geïnstalleerd en er wordt een beheerde identiteit gemaakt voor virtuele machines die deze nog niet hebben geïnstalleerd.

> [!IMPORTANT]
> Er is momenteel een bekend probleem waarbij de door de gebruiker toegewezen identiteit wordt uitgeschakeld als de regel voor het verzamelen van gegevens een beheerde identiteit maakt op een virtuele machine die al een door de gebruiker toegewezen beheerde identiteit heeft.

Selecteer in **Azure Monitor** menu in Azure Portal de optie **Regels** voor gegevensverzameling in de **sectie** Instellingen. Klik **op Toevoegen** om een nieuwe regel en toewijzing voor gegevensverzameling toe te voegen.

[![Regels voor gegevensverzameling](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Klik **op Toevoegen** om een nieuwe regel en set verbanden te maken. Geef een **regelnaam op** en geef een **abonnement en** **resourcegroep op.** Hiermee geeft u op waar de DCR wordt gemaakt. De virtuele machines en hun verbanden kunnen zich in elk abonnement of elke resourcegroep in de tenant.

[![Basisbeginselen van gegevensverzamelingsregel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

Voeg op **het tabblad Virtuele machines** virtuele machines toe die moeten worden toegepast op de regel voor gegevensverzameling. Zowel virtuele Azure-machines als Azure Arc ingeschakelde servers in uw omgeving moeten worden vermeld. De Azure Monitor Agent wordt geïnstalleerd op virtuele machines die deze nog niet hebben geïnstalleerd.

[![Virtuele machines voor gegevensverzamelingsregel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Klik op **het tabblad Verzamelen en** leveren op **Gegevensbron toevoegen om** een gegevensbron en doelset toe te voegen. Selecteer het **gegevensbrontype**. De bijbehorende details die u wilt selecteren, worden weergegeven. Voor prestatiemeters kunt u kiezen uit een vooraf gedefinieerde set objecten en de samplingfrequentie. Voor gebeurtenissen kunt u kiezen uit een set logboeken of faciliteiten en het ernstniveau. 

[![Basisgegevensbron](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Als u andere logboeken en prestatiemeters uit de [momenteel](azure-monitor-agent-overview.md#data-sources-and-destinations) ondersteunde gegevensbronnen wilt opgeven of gebeurtenissen wilt filteren met behulp van XPath-query's, selecteert u **Aangepast.** U kunt vervolgens een [XPath opgeven voor ](https://www.w3schools.com/xml/xpath_syntax.asp) alle specifieke waarden die u wilt verzamelen. Zie [Voorbeeld van DCR](data-collection-rule-overview.md#sample-data-collection-rule) voor voorbeelden.

[![Aangepaste gegevensbron](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

Voeg op **het** tabblad Doel een of meer bestemmingen toe voor de gegevensbron. Windows-gebeurtenis- en Syslog-gegevensbronnen kunnen alleen naar de logboeken Azure Monitor verzenden. Prestatiemeters kunnen zowel naar de metrische gegevens als Azure Monitor logboeken Azure Monitor verzenden.

[![Doel](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Klik **op Gegevensbron toevoegen** en vervolgens op Beoordelen en maken **om** de details van de regel voor gegevensverzameling en de associatie met de set VM's te bekijken. Klik **op Maken** om deze te maken.

> [!NOTE]
> Nadat de gegevensverzamelingsregel en -verbanden zijn gemaakt, kan het tot vijf minuten duren voordat gegevens naar de bestemmingen worden verzonden.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Gegevensverzameling beperken met aangepaste XPath-query's
Omdat er kosten in rekening worden gebracht voor gegevens die zijn verzameld in een Log Analytics-werkruimte, moet u alleen de gegevens verzamelen die u nodig hebt. Met behulp van de basisconfiguratie in Azure Portal hebt u slechts beperkte mogelijkheid om gebeurtenissen te filteren die u wilt verzamelen. Voor toepassings- en systeemlogboeken zijn dit alle logboeken met een bepaalde ernst. Voor beveiligingslogboeken is dit alles controle geslaagd of alle auditfoutlogboeken.

Als u aanvullende filters wilt opgeven, moet u Aangepaste configuratie gebruiken en een XPath opgeven die de gebeurtenissen filtert die u niet gebruikt. XPath-vermeldingen worden geschreven in de vorm `LogName!XPathQuery` . U wilt bijvoorbeeld alleen gebeurtenissen uit het toepassingsgebeurtenislogboek retourneren met de gebeurtenis-id 1035. De XPathQuery voor deze gebeurtenissen is `*[System[EventID=1035]]` . Omdat u de gebeurtenissen uit het toepassingsgebeurtenislogboek wilt ophalen, is het XPath `Application!*[System[EventID=1035]]`

Zie [XPath 1.0 limitations (Beperkingen voor XPath 1.0)](/windows/win32/wes/consuming-events#xpath-10-limitations) voor een lijst met beperkingen in het XPath dat wordt ondersteund door het Windows-gebeurtenislogboek.

> [!TIP]
> Gebruik de PowerShell-cmdlet met de parameter om de geldigheid van een `Get-WinEvent` `FilterXPath` XPathQuery te testen. In het volgende script ziet u een voorbeeld.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Als er gebeurtenissen worden geretourneerd, is de query geldig.
> - Als u het bericht No events were found that match the *specified selection criteria.* ontvangt, kan de query geldig zijn, maar er zijn geen overeenkomende gebeurtenissen op de lokale computer.
> - Als u het bericht De *opgegeven query is ongeldig ontvangt, is* de querysyntaxis ongeldig. 

De volgende tabel bevat voorbeelden voor het filteren van gebeurtenissen met behulp van een aangepast XPath.

| Beschrijving |  Xpath |
|:---|:---|
| Alleen systeemgebeurtenissen met gebeurtenis-id = 4648 verzamelen |  `System!*[System[EventID=4648]]`
| Alleen systeemgebeurtenissen verzamelen met gebeurtenis-id = 4648 en een procesnaam van consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Verzamel alle kritieke, fout-, waarschuwings- en informatiegebeurtenissen uit het gebeurtenislogboek van het systeem, met uitzondering van gebeurtenis-id = 6 (stuurprogramma geladen) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Verzamel alle geslaagde en mislukte beveiligingsgebeurtenissen, met uitzondering van gebeurtenis-id 4624 (geslaagde aanmelding) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Regel en associatie maken met behulp van REST API

Volg de onderstaande stappen om een regel voor gegevensverzameling en -verbanden te maken met behulp van de REST API.

1. Maak handmatig het DCR-bestand met behulp van de JSON-indeling die wordt weergegeven in [Voorbeeld van DCR.](data-collection-rule-overview.md#sample-data-collection-rule)

2. Maak de regel met behulp van [REST API](/rest/api/monitor/datacollectionrules/create#examples).

3. Maak voor elke virtuele machine een associatie met de regel voor gegevensverzameling met behulp van [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples).


## <a name="create-association-using-resource-manager-template"></a>Een associatie maken met behulp Resource Manager sjabloon

U kunt een verband maken tussen een virtuele Azure-machine of Azure Arc server met behulp van een Resource Manager sjabloon. Zie [Resource Manager sjabloonvoorbeelden voor](./resource-manager-data-collection-rules.md) regels voor gegevensverzameling in Azure Monitor voorbeeldsjablonen.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Azure Monitor Agent](azure-monitor-agent-overview.md).
- Meer informatie over [regels voor gegevensverzameling.](data-collection-rule-overview.md)
