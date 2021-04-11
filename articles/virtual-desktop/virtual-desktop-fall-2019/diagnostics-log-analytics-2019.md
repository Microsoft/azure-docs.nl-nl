---
title: Logboek analyse voor diagnostische gegevens van Windows virtueel bureau blad (klassiek)-Azure
description: Log Analytics gebruiken met de Windows-functie diagnostische gegevens over virtueel bureau blad (klassiek).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9cfa50e13756692295c84b02d02dd71228b16eb9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444996"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Log Analytics gebruiken voor de functie voor diagnostische gegevens in Windows virtueel bureau blad (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../diagnostics-log-analytics.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Virtueel bureau blad van Windows biedt een functie voor diagnostische gegevens waarmee de beheerder problemen kan identificeren via één interface. Met deze functie worden diagnostische gegevens vastgelegd wanneer de service wordt gebruikt door iemand die is toegewezen aan de Windows-functie virtueel bureau blad. Elk logboek bevat informatie over welke Windows-functie voor virtueel bureau blad is betrokken bij de activiteit, eventuele fout berichten die tijdens de sessie worden weer gegeven, informatie over de Tenant en gebruikers gegevens. De functie diagnostische gegevens maakt activiteiten logboeken voor gebruikers-en beheer acties. Elk activiteiten logboek valt onder drie hoofd categorieën:

- Activiteiten voor feed-abonnementen: wanneer een gebruiker verbinding probeert te maken met hun feed via Microsoft Extern bureaublad toepassingen.
- Verbindings activiteiten: wanneer een gebruiker verbinding probeert te maken met een bureau blad of RemoteApp via Microsoft Extern bureaublad toepassingen.
- Beheer activiteiten: wanneer een beheerder beheer bewerkingen op het systeem uitvoert, zoals het maken van hostgroepen, het toewijzen van gebruikers aan app-groepen en het maken van roltoewijzingen.

Verbindingen die zich niet in het virtuele bureau blad van Windows bevinden, worden niet weer gegeven in de resultaten van diagnostische gegevens omdat de functie Service diagnostiek zelf deel uitmaakt van Windows virtueel bureau blad. Er kunnen problemen met de Windows-verbinding met virtueel bureau blad optreden wanneer de gebruiker problemen met de netwerk verbinding ondervindt.

## <a name="why-you-should-use-log-analytics"></a>Waarom u Log Analytics moet gebruiken

U kunt het beste Log Analytics gebruiken voor het analyseren van diagnostische gegevens in de Azure-client die meer dan één gebruiker problemen oplossen. Als u prestatie meter items voor virtuele machines in Log Analytics hebt, hebt u één hulp programma om informatie te verzamelen voor uw implementatie.

## <a name="before-you-get-started"></a>Voordat u aan de slag gaat

Voordat u Log Analytics kunt gebruiken met de functie diagnostische gegevens, moet u [een werk ruimte maken](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

Nadat u uw werk ruimte hebt gemaakt, volgt u de instructies in [Windows-computers verbinden met Azure monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) om de volgende informatie te verkrijgen:

- De werk ruimte-ID
- De primaire sleutel van uw werk ruimte

U hebt deze gegevens later nodig in het installatie proces.

## <a name="push-diagnostics-data-to-your-workspace"></a>Diagnostische gegevens naar uw werk ruimte pushen

U kunt Diagnostische gegevens van uw Windows Virtual Desktop-Tenant pushen naar de Log Analytics voor uw werk ruimte. U kunt deze functie meteen instellen wanneer u uw Tenant voor het eerst maakt door uw werk ruimte te koppelen aan uw Tenant of u kunt deze later instellen met een bestaande Tenant.

Als u uw Tenant wilt koppelen aan uw Log Analytics-werk ruimte terwijl u uw nieuwe Tenant instelt, voert u de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met uw TenantCreator-gebruikers account:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Als u een bestaande Tenant wilt koppelen in plaats van een nieuwe Tenant, voert u de volgende cmdlet uit:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

U moet deze cmdlets uitvoeren voor elke Tenant die u wilt koppelen aan Log Analytics.

>[!NOTE]
>Als u de Log Analytics-werk ruimte niet wilt koppelen wanneer u een Tenant maakt, voert u `New-RdsTenant` in plaats daarvan de cmdlet uit.

## <a name="cadence-for-sending-diagnostic-events"></a>Uitgebracht voor het verzenden van diagnostische gebeurtenissen

Diagnostische gebeurtenissen worden verzonden naar Log Analytics wanneer deze zijn voltooid.

## <a name="example-queries"></a>Voorbeelden van query's

De volgende voorbeeld query's laten zien hoe de diagnostische functie een rapport genereert voor de meest voorkomende activiteiten in uw systeem:

In dit eerste voor beeld ziet u verbindings activiteiten die worden geïnitieerd door gebruikers met ondersteunde extern bureau blad-clients:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

In dit volgende voor beeld worden beheer activiteiten door beheerders op tenants weer gegeven:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Verzenden van gegevens naar Log Analytics stoppen

Als u wilt stoppen met het verzenden van gegevens van een bestaande Tenant naar Log Analytics, voert u de volgende cmdlet uit en stelt u lege teken reeksen in:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

U moet deze cmdlet uitvoeren voor elke Tenant waarvan u wilt stoppen met het verzenden van gegevens.

## <a name="next-steps"></a>Volgende stappen

Zie [problemen identificeren en diagnosticeren](diagnostics-role-service-2019.md#common-error-scenarios)als u veelvoorkomende fout scenario's wilt bekijken die door de diagnostische functie kunnen worden geïdentificeerd.
