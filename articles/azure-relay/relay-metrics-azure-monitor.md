---
title: Azure Relay metrische gegevens in Azure Monitor | Microsoft Docs
description: Dit artikel bevat informatie over hoe u Azure Monitor kunt gebruiken om te controleren op de status van Azure Relay.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586832"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Azure Relay metrische gegevens in Azure Monitor 
Azure Relay metrische gegevens geven u de status van resources in uw Azure-abonnement. Met een uitgebreide set metrische gegevens kunt u de algemene status van uw doorstuur bronnen beoordelen, niet alleen op het niveau van de naam ruimte, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van Azure Relay kunt bewaken. Metrische gegevens kunnen ook helpen bij het oplossen van problemen met de hoofd oorzaak zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Azure Monitor biedt uniforme gebruikers interfaces voor bewaking in verschillende Azure-Services. Zie voor meer informatie [bewaking in Microsoft Azure](../azure-monitor/overview.md) en het voor beeld [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op github.

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de Hybride verbindingen functie van Azure Relay, niet op de WCF Relay. 

## <a name="access-metrics"></a>Toegangs gegevens

Azure Monitor biedt meerdere manieren om toegang te krijgen tot metrische gegevens. U hebt toegang tot metrische gegevens via de [Azure Portal](https://portal.azure.com), of u kunt gebruikmaken van de Azure monitor API'S (rest en .net) en analyse oplossingen zoals Operation Management Suite en Event hubs. Zie [bewaking van gegevens die zijn verzameld door Azure monitor](../azure-monitor/data-platform.md)voor meer informatie.

Metrische gegevens zijn standaard ingeschakeld, en u kunt toegang krijgen tot de meest recente 30 dagen. Als u gegevens gedurende een langere periode wilt behouden, kunt u metrische gegevens archiveren naar een Azure Storage-account. Dit wordt geconfigureerd in [Diagnostische instellingen](../azure-monitor/essentials/diagnostic-settings.md) in azure monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens gedurende een bepaalde periode bewaken in de [Azure Portal](https://portal.azure.com). In het volgende voor beeld ziet u hoe u geslaagde aanvragen en inkomende aanvragen kunt weer geven op account niveau:

![Een pagina met de titel ' monitor-metrische gegevens (preview) ' toont een lijn diagram van het geheugen gebruik voor de afgelopen 30 dagen.][1]

U kunt ook rechtstreeks toegang krijgen tot metrische gegevens via de naam ruimte. Hiertoe selecteert u uw naam ruimte en klikt u op **metrische gegevens**. 

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde.

## <a name="billing"></a>Billing

Het gebruik van metrische gegevens in Azure Monitor is momenteel gratis tijdens de preview-versie. Als u echter aanvullende oplossingen gebruikt waarmee metrische gegevens worden opgenomen, worden deze oplossingen mogelijk gefactureerd. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. U wordt ook gefactureerd door Azure Monitor-Logboeken als u metrische gegevens streamt naar Azure Monitor-logboeken voor geavanceerde analyse.

Met de volgende metrische gegevens krijgt u een overzicht van de status van uw service. 

> [!NOTE]
> Er worden verschillende metrische gegevens overgezet omdat deze onder een andere naam worden geplaatst. Hiervoor moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutel woord ' afgeschaft ' worden niet verder ondersteund.

Alle waarden voor metrische gegevens worden elke minuut naar Azure Monitor verzonden. De granulatie tijd definieert het tijds interval waarvoor metrische waarden worden weer gegeven. Het ondersteunde tijds interval voor alle Azure Relay meet waarden is 1 minuut.

## <a name="connection-metrics"></a>Verbindings gegevens

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Het aantal geslaagde listener-verbindingen dat gedurende een opgegeven periode is gemaakt aan Azure Relay. <br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|ListenerConnections-ClientError |Het aantal client fouten op listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|ListenerConnections-ServerError |Het aantal server fouten op de listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|SenderConnections-Success |Het aantal geslaagde verzender verbindingen dat gedurende een opgegeven periode is gemaakt.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|SenderConnections-ClientError |Het aantal client fouten op de verbindingen van de afzender gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|SenderConnections-ServerError |Het aantal server fouten op de verzender verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|ListenerConnections-TotalRequests |Het totale aantal listener-verbindingen gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|SenderConnections-TotalRequests |De verbindings aanvragen van de afzenders gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|ActiveConnections |Het aantal actieve verbindingen. Deze waarde is een waarde voor een bepaald tijd punt.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|ActiveListeners |Het aantal actieve listeners. Deze waarde is een waarde voor een bepaald tijd punt.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|ListenerDisconnects |Het aantal niet-verbonden listeners gedurende een opgegeven periode.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|
|SenderDisconnects |Het aantal niet-verbonden afzenders gedurende een opgegeven periode.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="memory-usage-metrics"></a>Metrische gegevens over geheugen gebruik

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|BytesTransferred |Het aantal bytes dat is overgedragen gedurende een opgegeven periode.<br/><br/> Eenheid: bytes <br/> Aggregatie type: totaal <br/> Dimensie: EntityName|

## <a name="metrics-dimensions"></a>Metrische dimensies

Azure Relay ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Het toevoegen van dimensies aan uw metrische gegevens is optioneel. Als u geen dimensies toevoegt, worden metrische gegevens opgegeven op het niveau van de naam ruimte. 

|Dimensie naam|Description|
| ------------------- | ----------------- |
|EntityName| Azure Relay ondersteunt Messa ging-entiteiten in de naam ruimte.|

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Azure-bewaking](../azure-monitor/overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
