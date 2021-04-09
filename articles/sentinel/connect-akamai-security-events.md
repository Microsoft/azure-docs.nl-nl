---
title: Uw Akamai-beveiligings gebeurtenissen verzamelaar koppelen aan Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Akamai-connector voor beveiligings gebeurtenissen om beveiligings logboeken van Akamai-oplossingen in azure Sentinel te halen. Bekijk Akamai-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711957"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Uw Akamai-beveiligings gebeurtenissen verzamelaar koppelen aan Azure Sentinel

> [!IMPORTANT]
> De Akamai-beveiligings gebeurtenissen connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u de Akamai-beveiligings gebeurtenissen verzamelaar verbindt met Azure Sentinel. Met de Akamai Security Events Data Connector kunt u eenvoudig uw Akamai-logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, er query's op moet uitvoeren om aangepaste waarschuwingen te maken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen de Akamai-beveiligings gebeurtenissen Collector en Azure-Sentinel maakt gebruik van syslog, een op Linux gebaseerde logboek doorstuur server en de Log Analytics-agent. Er wordt ook een aangepaste, gegenereerde logboek-parser gebruikt op basis van een Kusto-functie.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Akamai-beveiligings gebeurtenis logboeken naar Azure Sentinel verzenden

Als u de logboeken van Azure Sentinel wilt ontvangen, moet u de Akamai-beveiligings gebeurtenissen verzamelaar configureren om syslog-berichten te verzenden in de CEF-indeling naar een op Linux gebaseerde logboek server voor door sturen (met rsyslog of syslog-aardgas). Op deze server wordt de Log Analytics-agent geïnstalleerd en de agent stuurt de logboeken door naar uw Azure Sentinel-werk ruimte.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de optie **Akamai-beveiligings gebeurtenissen (preview)** en open vervolgens de **pagina connector**.

1. Volg de instructies op het tabblad **instructies** onder **configuratie**:

    1. Onder **1. Configuratie van Linux syslog-agent** : Voer deze stap uit als u nog geen logboek doorstuur server hebt of als u er een hebt. Zie [stap 1: de doorstuur server voor logboeken implementeren](connect-cef-agent.md) in de Azure-Sentinel-documentatie voor informatie over de grootte, gedetailleerde instructies en uitgebreide uitleg.

    1. Onder **2. CEF-Logboeken (common Event Format) door sturen naar syslog-agent** -Volg de instructies van Akamai om [Siem-integratie te configureren](https://developer.akamai.com/tools/integrations/siem) en [een CEF-connector](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector)in te stellen. Deze connector ontvangt in bijna realtime beveiligings gebeurtenissen van uw Akamai-oplossingen met behulp van de SIEM OPEN API en converteert deze van JSON naar CEF-indeling.
    
        Deze configuratie moet de volgende elementen bevatten:
    
        - Logboek bestemming: de hostnaam en/of het IP-adres van de server voor het door sturen van Logboeken
        - Protocol en poort – TCP 514 (als u dit niet doet, moet u ervoor zorgen dat u de parallelle wijziging aanbrengt in de syslog-daemon op de server voor het door sturen van Logboeken)
        - Logboek indeling – CEF
        - Logboek typen: alle beschikbaar

    1. Minder dan **3. Verbinding valideren** : Controleer de gegevens opname door de opdracht op de pagina connector te kopiëren en op de logboek-doorstuur server uit te voeren. Zie [stap 3: connectiviteit valideren](connect-cef-verify.md) in de informatie over de Azure-Sentinel voor meer gedetailleerde instructies en uitleg.

        Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. Gebruik de volgende stappen om de functie **AkamaiSIEMEvent** Kusto in te stellen voor gebruik in query's en werkmappen.

1. Selecteer **Logboeken** in het navigatie menu van de Azure-Sentinel.

1. Kopieer de volgende query en plak deze in het query venster.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Klik op de vervolg keuzelijst **Opslaan** en klik op **Opslaan**. In het deel venster **Opslaan** ,

    1. Voer onder **naam** **AkamaiSIEMEvent** in.

    1. Onder **Opslaan als** kiest u **functie**.

    1. Voer onder **functie alias** **AkamaiSIEMEvent** in.

    1. Onder **categorie** voert u **functies** in.

    1. Klik op **Opslaan**.

    Functie-apps nemen doorgaans tussen 10 en 15 minuten om te activeren.

Nu kunt u een query uitvoeren op Akamai-gegevens door `AkamaiSIEMEvent` in de bovenste regel van het query venster in te voeren.

Zie het tabblad **volgende stappen** op de pagina connector voor meer query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Akamai-beveiligings gebeurtenissen verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.