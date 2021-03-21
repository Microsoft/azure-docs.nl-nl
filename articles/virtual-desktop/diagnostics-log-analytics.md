---
title: Logboek analyse van diagnostische gegevens van Windows Virtual Desktop-Azure
description: Log Analytics gebruiken met de Windows-functie diagnostische gegevens over virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 37990cc4322717f090c7a35c62512ba0e1a04293
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576146"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Log Analytics gebruiken voor de functie voor diagnostische gegevens

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Het virtuele bureau blad van Windows maakt gebruik van [Azure monitor](../azure-monitor/overview.md) voor bewaking en waarschuwingen, zoals veel andere Azure-Services. Hiermee kunnen beheerders problemen identificeren via één interface. De service maakt activiteiten logboeken voor zowel gebruikers-als beheer acties. Elk activiteiten logboek valt onder de volgende categorieën:

- Beheer activiteiten:
    - Controleren of er wordt geprobeerd Windows virtueel-bureaublad objecten te wijzigen met behulp van Api's of Power shell. Kan iemand bijvoorbeeld een hostgroep maken met behulp van Power shell?
- Voerder
    - Kunnen gebruikers zich abonneren op werk ruimten?
    - Zien gebruikers alle resources die zijn gepubliceerd in de Extern bureaublad-client?
- Verbindingen:
    - Wanneer gebruikers verbindingen met de service initiëren en volt ooien.
- Registratie van de host:
    - Is de sessiehost geregistreerd bij de service bij het verbinden?
- Fouten:
    - Ondervinden gebruikers problemen met specifieke activiteiten? Met deze functie kan een tabel worden gegenereerd die de activiteit gegevens voor u registreert, zolang de gegevens worden gekoppeld aan de activiteiten.
- Controle punten
    - Specifieke stappen in de levens duur van een activiteit die is bereikt. Tijdens een sessie is een gebruiker bijvoorbeeld gelijkmatig verdeeld over een bepaalde host. vervolgens is de gebruiker aangemeld tijdens een verbinding, enzovoort.

Verbindingen die zich niet in het virtuele bureau blad van Windows bevinden, worden niet weer gegeven in de resultaten van diagnostische gegevens omdat de functie Service diagnostiek zelf deel uitmaakt van Windows virtueel bureau blad. Er kunnen problemen met de Windows-verbinding met virtueel bureau blad optreden wanneer de gebruiker problemen met de netwerk verbinding ondervindt.

Met Azure Monitor kunt u gegevens van Windows virtueel bureau blad analyseren en de prestatie meter items van virtuele machines (VM) controleren, allemaal binnen hetzelfde hulp programma. In dit artikel vindt u meer informatie over het inschakelen van diagnostische gegevens voor uw virtuele Windows-desktop omgeving.

>[!NOTE]
>Zie [Azure virtual machines bewaken met Azure monitor](../azure-monitor/vm/monitor-vm-azure.md)voor meer informatie over het bewaken van uw Vm's in Azure. Zorg er ook voor dat u [de drempel waarden voor prestatie meter items bekijkt](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) voor een beter inzicht in uw gebruikers ervaring op de sessiehost.

## <a name="before-you-get-started"></a>Voordat u aan de slag gaat

Voordat u Log Analytics kunt gebruiken, moet u een werk ruimte maken. Volg hiervoor de instructies in een van de volgende twee artikelen:

- Als u liever Azure Portal gebruikt, raadpleegt u [een log Analytics-werk ruimte maken in azure Portal](../azure-monitor/logs/quick-create-workspace.md).
- Zie [een log Analytics-werk ruimte maken met Power shell als u de voor](../azure-monitor/logs/powershell-workspace-configuration.md)keur geeft aan Power shell.

Nadat u uw werk ruimte hebt gemaakt, volgt u de instructies in [Windows-computers verbinden met Azure monitor](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) om de volgende informatie te verkrijgen:

- De werk ruimte-ID
- De primaire sleutel van uw werk ruimte

U hebt deze gegevens later nodig in het installatie proces.

Zorg ervoor dat u het machtigings beheer voor Azure Monitor controleert om gegevens toegang in te scha kelen voor degenen die uw virtuele Windows-desktop omgeving controleren en onderhouden. Zie [aan de slag met rollen, machtigingen en beveiliging met Azure monitor](../azure-monitor/roles-permissions-security.md)voor meer informatie.

## <a name="push-diagnostics-data-to-your-workspace"></a>Diagnostische gegevens naar uw werk ruimte pushen

U kunt Diagnostische gegevens van uw virtuele bureau blad-objecten van Windows naar de Log Analytics voor uw werk ruimte pushen. U kunt deze functie meteen instellen wanneer u uw objecten voor het eerst maakt.

Log Analytics instellen voor een nieuw object:

1. Meld u aan bij de Azure Portal en ga naar het **virtuele bureau blad van Windows**.

2. Navigeer naar het object (zoals een hostgroep, app-groep of werk ruimte) waarvoor u Logboeken en gebeurtenissen wilt vastleggen.

3. Selecteer **Diagnostische instellingen** in het menu aan de linkerkant van het scherm.

4. Selecteer **Diagnostische instelling toevoegen** in het menu dat wordt weer gegeven aan de rechter kant van het scherm.

    De opties die worden weer gegeven op de pagina Diagnostische instellingen variëren, afhankelijk van het soort object dat u bewerkt.

    Wanneer u bijvoorbeeld diagnostische gegevens inschakelt voor een app-groep, ziet u opties voor het configureren van controle punten, fouten en beheer. Voor werk ruimten configureren deze categorieën een feed om te volgen wanneer gebruikers zich abonneren op de lijst met apps. Zie voor meer informatie over diagnostische instellingen [Diagnostische instelling maken om bron logboeken en metrische gegevens in azure te verzamelen](../azure-monitor/essentials/diagnostic-settings.md).

     >[!IMPORTANT]
     >Vergeet niet om diagnostische gegevens in te scha kelen voor elk Azure Resource Manager object dat u wilt bewaken. Er zijn gegevens beschikbaar voor activiteiten nadat de diagnose is ingeschakeld. Het kan enkele uren duren voordat de eerste keer is ingesteld.

5. Voer een naam in voor de configuratie van uw instellingen en selecteer vervolgens **verzenden naar log Analytics**. De naam die u gebruikt, mag geen spaties bevatten en moet voldoen aan de [naamgevings conventies van Azure](../azure-resource-manager/management/resource-name-rules.md). Als onderdeel van de logboeken kunt u alle opties selecteren die u wilt toevoegen aan uw Log Analytics, zoals controle punt, fout, beheer, enzovoort.

6. Selecteer **Opslaan**.

>[!NOTE]
>Log Analytics biedt u de mogelijkheid om gegevens te streamen naar [Event hubs](../event-hubs/event-hubs-about.md) of deze te archiveren in een opslag account. Zie [Azure-bewakings gegevens streamen naar een event hub](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md) en [Azure-resource logboeken archiveren in een opslag account](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)voor meer informatie over deze functie.

## <a name="how-to-access-log-analytics"></a>Toegang tot Log Analytics

U hebt toegang tot Log Analytics-werk ruimten op het Azure Portal of Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Toegang tot Log Analytics op een Log Analytics werk ruimte

1. Meld u aan bij Azure Portal.

2. Zoeken naar **log Analytics-werk ruimte**.

3. Onder Services selecteert u **log Analytics werk ruimten**.

4. Selecteer in de lijst de werk ruimte die u hebt geconfigureerd voor het virtueel-bureaublad object van Windows.

5. Selecteer **Logboeken** in uw werk ruimte. U kunt de menu lijst filteren met de functie **zoeken** .

### <a name="access-log-analytics-on-azure-monitor"></a>Toegang tot Log Analytics op Azure Monitor

1. Aanmelden bij Azure Portal

2. Zoek en selecteer **monitor**.

3. Selecteer **Logboeken**.

4. Volg de instructies op de pagina logboek registratie om het bereik van uw query in te stellen.

5. U kunt een query uitvoeren op diagnostische gegevens. Alle diagnostische tabellen hebben het voor voegsel ' WVD '.

>[!NOTE]
>Voor meer informatie over de tabellen die zijn opgeslagen in Azure Monitor logboeken, raadpleegt u de [Azure monitor gegevens opnieuw opfence](/azure/azure-monitor/reference/). Alle tabellen die betrekking hebben op virtuele Bureau bladen van Windows, hebben de naam ' WVD '.

## <a name="cadence-for-sending-diagnostic-events"></a>Uitgebracht voor het verzenden van diagnostische gebeurtenissen

Diagnostische gebeurtenissen worden verzonden naar Log Analytics wanneer deze zijn voltooid.

Log Analytics alleen rapporten in deze tussenliggende status voor verbindings activiteiten:

- Gestart: wanneer een gebruiker een app of bureau blad in de Extern bureaublad-client selecteert en hiermee verbinding maakt.
- Verbonden: wanneer de gebruiker verbinding maakt met de virtuele machine waarop de app of het bureau blad wordt gehost.
- Voltooid: wanneer de gebruiker of de server de sessie verbreekt, wordt de activiteit uitgevoerd in.

## <a name="example-queries"></a>Voorbeelden van query's

Access-voorbeeld query's via de Azure Monitor Log Analytics gebruikers interface:
1. Ga naar uw Log Analytics-werk ruimte en selecteer vervolgens **Logboeken**. De voorbeeld query gebruikers interface wordt automatisch weer gegeven.
1. Wijzig het filter in **categorie**.
1. Selecteer **virtueel bureau blad voor Windows** om beschik bare query's te controleren.
1. Selecteer **uitvoeren** om de geselecteerde query uit te voeren.

Meer informatie over de voorbeeld query interface in [opgeslagen query's in Azure Monitor Log Analytics](../azure-monitor/logs/example-queries.md).

Met de volgende query lijst kunt u verbindings gegevens of-problemen voor één gebruiker controleren. U kunt deze query's uitvoeren in de [log Analytics query-editor](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query). Vervang elke query door `userupn` de UPN van de gebruiker die u wilt zoeken.


Alle verbindingen voor één gebruiker zoeken:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


Zoeken naar het aantal keren dat een gebruiker is verbonden per dag:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

Sessie duur zoeken op gebruiker:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

Fouten voor een specifieke gebruiker zoeken:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

Controleren of er een specifieke fout is opgetreden voor andere gebruikers:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Wanneer een gebruiker volledig bureau blad opent, wordt het app-gebruik in de sessie niet bijgehouden als controle punten in de tabel WVDCheckpoints.
>- De kolom ResourcesAlias in de tabel WVDConnections geeft aan of een gebruiker verbinding heeft met een volledig bureau blad of een gepubliceerde app. In de kolom wordt alleen de eerste app weer gegeven die wordt geopend tijdens de verbinding. Alle gepubliceerde apps die de gebruiker opent, worden bijgehouden in WVDCheckpoints.
>- De tabel WVDErrors toont u beheer fouten, registratie problemen voor hosts en andere problemen die zich voordoen wanneer de gebruiker zich abonneert op een lijst met apps of Bureau bladen.
>- WVDErrors helpt u bij het identificeren van problemen die door beheer taken kunnen worden opgelost. De waarde op ServiceError geeft altijd ' false ' aan voor dit soort problemen. Als ServiceError = "True", moet u het probleem escaleren naar micro soft. Zorg ervoor dat u de CorrelationID opgeeft voor de fouten die u wilt escaleren.

## <a name="next-steps"></a>Volgende stappen

Zie [problemen identificeren en diagnosticeren](diagnostics-role-service.md#common-error-scenarios)als u veelvoorkomende fout scenario's wilt bekijken die door de diagnostische functie kunnen worden geïdentificeerd.