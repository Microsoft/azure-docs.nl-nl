---
title: Status van agent oplossing in Azure Monitor | Microsoft Docs
description: Dit artikel is bedoeld om u te helpen begrijpen hoe u deze oplossing kunt gebruiken om de status van uw agents die rechtstreeks aan Log Analytics of System Center Operations Manager rapporteren, te controleren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 171230dc2ce6189e36c601c6c7d3b3612fce160c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711056"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Status van agent oplossing in Azure Monitor
De Status van agent oplossing in azure helpt u te begrijpen, voor alle agents die rechtstreeks aan de Log Analytics-werk ruimte rapporteren in Azure Monitor of een System Center Operations Manager beheer groep die is verbonden met Azure Monitor, die niet reageert en geen operationele gegevens verzenden.  U kunt ook bijhouden hoeveel agents er zijn geïmplementeerd en waar deze zich geografisch gezien bevinden. Bovendien kunt u query's uitvoeren om op de hoogte te blijven van de verdeling van agents over Azure, andere cloudomgevingen of on-premises.    

## <a name="prerequisites"></a>Vereisten
Voordat u deze oplossing implementeert, moet u controleren of u momenteel ondersteunde [Windows-agents](../agents/agent-windows.md) rapporteert aan de log Analytics-werk ruimte of dat u rapporteert aan een [Operations Manager-beheer groep](../agents/om-agents.md) die is geïntegreerd met uw werk ruimte.

## <a name="solution-components"></a>Oplossingsonderdelen
Deze oplossing bestaat uit de volgende resources die worden toegevoegd aan uw werkruimte en rechtstreeks verbonden agents of verbonden Operations Manager-beheergroepen.

### <a name="management-packs"></a>Management packs
Als uw System Center Operations Manager-beheer groep is verbonden met een Log Analytics-werk ruimte, worden de volgende Management Packs in Operations Manager geïnstalleerd.  Deze management packs worden na het toevoegen van deze oplossing ook op rechtstreeks verbonden Windows-computers geïnstalleerd. Met deze management packs hoeft u niets te configureren of te beheren.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Zie [Operations Manager koppelen aan Log Analytics](../agents/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="configuration"></a>Configuratie
Voeg de Status van agent oplossing toe aan uw Log Analytics-werk ruimte met behulp van het proces dat wordt beschreven in [oplossingen toevoegen](solutions.md). Er is geen verdere configuratie nodig.


## <a name="data-collection"></a>Gegevens verzamelen
### <a name="supported-agents"></a>Ondersteunde agents
De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing.

| Verbonden bron | Ondersteund | Description |
| --- | --- | --- |
| Windows-agents | Yes | Er worden heartbeat-gebeurtenissen verzameld van direct verbonden Windows-agents.|
| Beheergroep System Center Operations Manager | Yes | Heartbeat-gebeurtenissen worden elke 60 seconden verzameld van agents die rapporteren aan de beheer groep en vervolgens doorgestuurd naar Azure Monitor. Een directe verbinding van Operations Manager agents naar Azure Monitor is niet vereist. Heartbeat-gebeurtenis gegevens worden doorgestuurd van de beheer groep naar de Log Analytics-werk ruimte.|

## <a name="using-the-solution"></a>De oplossing gebruiken
Wanneer u de oplossing toevoegt aan uw Log Analytics-werk ruimte, wordt de tegel **status van agent** toegevoegd aan het dash board. Op deze tegel ziet u het totale aantal agents en het aantal agents dat de afgelopen 24 uur niet heeft gereageerd.<br><br> ![De tegel Status van agent in het dashboard](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Klik op de tegel **Status van agent** om het **gelijknamige** dashboard te openen.  Het dashboard bevat de kolommen in de volgende tabel. Elke kolom bevat de tien belangrijkste gebeurtenissen naar aantal die overeenkomen met de criteria van die kolom voor de opgegeven periode. U kunt de volledige lijst met gebeurtenissen weergeven door linksonder elke kolom **Alles weergeven** te selecteren. Dit kan overigens ook door op de kolomkop te klikken.

| Kolom | Beschrijving |
|--------|-------------|
| Agent count over time | Een trend van het aantal agents gedurende een periode van zeven dagen voor Linux- en Windows-agents.|
| Count of unresponsive agents | Een lijst met agents die in de afgelopen 24 uur geen heartbeat hebben verzonden.|
| Distribution by OS Type | Een visualisatie van het aantal Windows- en Linux-agents in uw omgeving.|
| Verdeling naar agent-versie | Een visualisatie van de verschillende agentversies die zijn geïnstalleerd in uw omgeving en het aantal van elke versie.|
| Verdeling naar agent-catgeorie | Een visualisatie van de verschillende categorieën agents die heartbeat-gebeurtenissen verzenden: directe agents, OpsMgr-agents of OpsMgr Management Server.|
| Verdeling naar beheergroep | Een partitie van de verschillende Operations Manager-beheer groepen in uw omgeving.|
| Geo-location of Agents | Een partitie van de verschillende landen/regio's waar u agents hebt en het totale aantal agents dat in elk land of elke regio is geïnstalleerd.|
| Count of Gateways Installed | Het aantal servers waarop de Log Analytics-gateway is geïnstalleerd en een lijst met deze servers.|

![Voorbeeld van het dashboard van de oplossing Status van agent](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records
De oplossing maakt één type record in de werk ruimte Log Analytics.  

### <a name="heartbeat-records"></a>Heartbeat-records
Er wordt een record van het type **Heartbeat** gemaakt.  Deze records hebben de eigenschappen uit de volgende tabel.  

| Eigenschap | Beschrijving |
| --- | --- |
| `Type` | *Hartslag*|
| `Category` | De waarde is *Direct Agent*, *SCOM Agent* of *SCOM Management Server*.|
| `Computer` | De naam van de computer.|
| `OSType` | Windows- of Linux-besturingssysteem.|
| `OSMajorVersion` | De primaire versie van het besturingssysteem.|
| `OSMinorVersion` | De secundaire versie van het besturingssysteem.|
| `Version` | Versie van Log Analytics agent of Operations Manager agent.|
| `SCAgentChannel` | De waarde is *Direct* en/of *SCManagementServer*.|
| `IsGatewayInstalled` | Als Log Analytics gateway is geïnstalleerd, is waarde *True*, anders is de waarde *False*.|
| `ComputerIP` | Het open bare IP-adres van de computer. Op virtuele machines van Azure wordt het open bare IP-adres weer gegeven als er een beschikbaar is. Voor Vm's die persoonlijke Ip's gebruiken, wordt hiermee het Azure SNAT-adres weer gegeven (niet het privé-IP-adres). |
| `RemoteIPCountry` | De geografische locatie waar de computer is geïmplementeerd.|
| `ManagementGroupName` | De naam van de beheergroep van Operations Manager.|
| `SourceComputerId` | De unieke ID van de computer.|
| `RemoteIPLongitude` | De lengtegraad van geografische locatie van de computer.|
| `RemoteIPLatitude` | De breedtegraad van de geografische locatie van de computer.|

Elke agent die aan een Operations Manager-beheer Server rapporteert, verzendt twee heartbeats en de waarde van de SCAgentChannel-eigenschap bevat zowel **directe** als **SCManagementServer** , afhankelijk van de gegevens bronnen en bewakings oplossingen die u in uw abonnement hebt ingeschakeld. Als u intrekt, worden gegevens van oplossingen rechtstreeks van een Operations Manager-beheer server naar Azure Monitor verzonden, of vanwege het volume van de gegevens die op de agent zijn verzameld, worden ze rechtstreeks van de agent naar Azure Monitor verzonden. Voor heartbeat-gebeurtenissen met de waarde **SCManagementServer** bestaat de waarde van ComputerIP uit het IP-adres van de beheerserver, aangezien de gegevens door deze server worden geüpload.  Voor heartbeats waarvan de eigenschap SCAgentChannel is ingesteld op **Direct**, is de waarde het openbare IP-adres van de agent.  

## <a name="sample-log-searches"></a>Voorbeeldzoekopdrachten in logboeken
De volgende tabel bevat voorbeelden van zoekopdrachten in logboeken voor records die zijn verzameld met deze oplossing.

| Query | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |Het totale aantal agents |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Het aantal agents dat de afgelopen 24 uur niet heeft gereageerd |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Het aantal agents dat de afgelopen 15 minuten niet heeft gereageerd |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Computers die online waren (in de afgelopen 24 uur) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Het totale aantal agents dat offline was in de afgelopen 30 minuten (voor de afgelopen 24 uur) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Een trend van het aantal agents in de tijd per type besturingssysteem|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Distribution by OS Type |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Verdeling naar agent-versie |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Verdeling naar agent-catgeorie |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Verdeling naar beheergroep |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Geo-location of Agents |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Aantal geïnstalleerde Log Analytics gateways |




## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [waarschuwingen in azure monitor](../alerts/alerts-overview.md) voor meer informatie over het genereren van waarschuwingen uit logboek query's. 

