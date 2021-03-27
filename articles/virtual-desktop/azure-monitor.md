---
title: De preview-versie van Windows virtueel bureau blad controleren gebruiken-Azure
description: Azure Monitor gebruiken voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1c87763cb2ca482fc8ee15588d7287f0d9275fff
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627163"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Gebruik Azure Monitor voor virtuele Windows-Bureau bladen om uw implementatie te bewaken (preview-versie)

>[!IMPORTANT]
>Azure Monitor voor het virtuele bureau blad van Windows is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Azure Monitor voor virtueel bureau blad van Windows (preview) is een dash board dat is gebaseerd op Azure Monitor werkmappen waarmee IT-professionals hun virtuele Windows-desktop omgevingen kunnen begrijpen. In dit artikel vindt u instructies voor het instellen van Azure Monitor voor virtuele Windows-Bureau bladen voor het bewaken van uw virtuele Windows-bureaublad omgevingen.

## <a name="requirements"></a>Vereisten

Voordat u Azure Monitor voor Windows virtueel bureau blad gaat gebruiken, moet u de volgende dingen instellen:

- Alle Windows virtueel-bureaublad omgevingen die u bewaakt, moeten zijn gebaseerd op de nieuwste versie van het virtuele Windows-bureau blad dat compatibel is met Azure Resource Manager.
- Ten minste één geconfigureerde Log Analytics-werk ruimte. Gebruik een aangewezen Log Analytics-werk ruimte voor uw Windows Virtual Desktop-sessie hosts om ervoor te zorgen dat prestatie meter items en gebeurtenissen alleen worden verzameld van sessiehost in uw Windows-implementatie voor virtueel bureau blad.
- Schakel gegevens verzameling in voor de volgende dingen in uw Log Analytics-werk ruimte:
    - Diagnostische gegevens van uw Windows Virtual Desktop-omgeving
    - Aanbevolen prestatie meter items van uw Windows Virtual Desktop-sessie hosts
    - Aanbevolen Windows-gebeurtenis logboeken van uw Windows Virtual Desktop Session hosts

 De procedure voor het instellen van gegevens die in dit artikel wordt beschreven, is de enige die u nodig hebt voor het bewaken van het virtuele bureau blad van Windows. U kunt alle andere items die gegevens verzenden naar uw Log Analytics-werk ruimte uitschakelen om kosten te besparen.

Iedereen die de controle Azure Monitor voor het virtuele bureau blad van Windows voor uw omgeving heeft, heeft ook de volgende machtigingen voor lees toegang nodig:

- Lees toegang tot de Azure-abonnementen die uw virtueel bureau blad-resources van Windows bevatten
- Lees toegang tot de resource groepen van het abonnement die uw Windows Virtual Desktop-sessie hosts bevatten
- Lees toegang tot de Log Analytics-werk ruimte of-werk ruimten

>[!NOTE]
> Met alleen-lezen toegang kunnen beheerders gegevens weer geven. Ze hebben verschillende machtigingen nodig om resources te beheren in de virtuele bureau blad-portal van Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor voor virtueel bureau blad van Windows openen

U kunt Azure Monitor voor Windows virtueel bureau blad openen met een van de volgende methoden:

- Ga naar [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).
- Zoek en selecteer **Windows virtueel bureau blad** in het Azure Portal en selecteer vervolgens **inzichten**.
- Zoek en selecteer **Azure monitor** in de Azure Portal. Selecteer **Insights hub** onder **inzichten** en selecteer vervolgens **Windows virtueel bureau blad**.
Nadat u de pagina hebt geopend, voert u het **abonnement**, de **resource groep**, de **hostgroep** en het **tijds bereik** in van de omgeving die u wilt bewaken.

>[!NOTE]
>Virtueel bureau blad van Windows biedt momenteel alleen ondersteuning voor bewaking van één abonnement, resource groep en hostgroep per keer. Als u de omgeving die u wilt bewaken niet kunt vinden, raadpleegt u de documentatie voor het [oplossen van problemen met](troubleshoot-azure-monitor.md) bekende functie aanvragen en problemen.

## <a name="log-analytics-settings"></a>Log Analytics instellingen

U hebt ten minste één Log Analytics-werk ruimte nodig om Azure Monitor voor Windows Virtual Desktop te gaan gebruiken. Gebruik een aangewezen Log Analytics-werk ruimte voor uw Windows Virtual Desktop-sessie hosts om ervoor te zorgen dat prestatie meter items en gebeurtenissen alleen worden verzamelde formulier sessie hosts in uw Windows-implementatie voor virtueel bureau blad. Als u al een werk ruimte hebt ingesteld, gaat u verder met [het instellen van de configuratie werkmap](#set-up-using-the-configuration-workbook). Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md)om er een in te stellen.

>[!NOTE]
>Er gelden standaard kosten voor gegevens opslag voor Log Analytics. We raden u aan om het model voor betalen naar gebruik te kiezen en aan te passen wanneer u uw implementatie schaalt en meer gegevens in beslag neemt. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie.

## <a name="set-up-using-the-configuration-workbook"></a>Instellen met behulp van de configuratie werkmap

Als dit de eerste keer is dat u Azure Monitor voor Windows virtueel bureau blad opent, moet u Azure Monitor instellen voor uw virtuele Windows-desktop omgeving. Uw resources configureren:

1. Open Azure Monitor voor virtueel bureau blad van Windows in de Azure Portal op [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)en selecteer vervolgens **configuratie werkmap**.
2. Selecteer een omgeving om te configureren onder **abonnement**, **resource groep** en **hostgroep**.

Met de configuratie werkmap wordt uw bewakings omgeving ingesteld en kunt u de configuratie controleren nadat u het installatie proces hebt voltooid. Het is belang rijk om uw configuratie te controleren als items in het dash board niet correct worden weer gegeven of wanneer de product groep updates publiceert waarvoor nieuwe instellingen zijn vereist.

### <a name="resource-diagnostic-settings"></a>Diagnostische instellingen voor bronnen

Als u informatie wilt verzamelen over uw virtuele bureau blad-infra structuur van Windows, moet u verschillende diagnostische instellingen inschakelen op uw Windows Virtual Desktop-hostgroepen en-werk ruimten (dit is uw virtuele Windows-bureau blad, niet uw Log Analytics-werk ruimte). Zie onze [omgevings handleiding](environment-setup.md)voor meer informatie over hostgroepen, werk ruimten en andere resource objecten van het virtueel bureau blad van Windows.

U vindt meer informatie over de diagnostische gegevens van het virtuele bureau blad van Windows en de ondersteunde diagnostische tabellen bij het [verzenden van diagnostische gegevens van Windows virtueel bureau blad naar log Analytics](diagnostics-log-analytics.md).

De diagnostische instellingen voor bronnen instellen in de configuratie werkmap: 

1. Selecteer het tabblad **Diagnostische instellingen voor bronnen** in de werk blad configuratie. 
2. Selecteer **log Analytics werk ruimte** voor het verzenden van diagnostische gegevens van Windows virtueel bureau blad. 

#### <a name="host-pool-diagnostic-settings"></a>Diagnostische instellingen hostgroep

Als u Diagnostische gegevens van een hostgroep wilt instellen met behulp van de sectie Diagnostische instellingen voor bronnen in de werk blad configuratie:

1. Controleer onder **hostgroep** of Windows diagnostische gegevens over virtueel bureau blad zijn ingeschakeld. Als dat niet het geval is, wordt een fout bericht weer gegeven met de tekst ' er is geen bestaande diagnostische configuratie gevonden voor de geselecteerde hostgroep. ' U moet de volgende ondersteunde diagnostische tabellen inschakelen:

    - Controlepunt
    - Fout
    - Beheer
    - Verbinding
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Als het fout bericht niet wordt weer gegeven, hoeft u de stappen 2 t/m 4 niet uit te voeren.

2. Selecteer **hostgroep configureren**.
3. Selecteer **Implementeren**.
4. Vernieuw de configuratie werkmap.

#### <a name="workspace-diagnostic-settings"></a>Diagnostische instellingen werk ruimte 

Werk ruimte diagnostiek instellen met behulp van de sectie Diagnostische instellingen voor bronnen in de werkmap van de configuratie:

 1. Controleer onder **werk ruimte** of de diagnostische gegevens van Windows virtueel bureau blad zijn ingeschakeld voor de virtuele bureau blad-werk ruimte van Windows. Als dat niet het geval is, wordt een fout bericht weer gegeven met de tekst ' er is geen bestaande diagnostische configuratie gevonden voor de geselecteerde werk ruimte. ' U moet de volgende ondersteunde diagnostische tabellen inschakelen:
 
    - Controlepunt
    - Fout
    - Beheer
    - Feed
    
    >[!NOTE]
    > Als het fout bericht niet wordt weer gegeven, hoeft u stap 2-4 niet uit te voeren.

2. Selecteer **werk ruimte configureren**.
3. Selecteer **Implementeren**.
4. Vernieuw de configuratie werkmap.

### <a name="session-host-data-settings"></a>Instellingen voor Session Host-gegevens

Als u informatie wilt verzamelen over uw Windows Virtual Desktop-sessie-hosts, moet u de Log Analytics-agent installeren op alle sessie hosts in de hostgroep, ervoor zorgen dat de sessie-hosts naar een Log Analytics werkruimte worden verzonden en de instellingen van uw Log Analytics-agent configureren om prestatie gegevens en Windows-gebeurtenis logboeken te verzamelen.

De Log Analytics werk ruimte waarmee u de sessiehost verzendt, hoeft niet hetzelfde te zijn als de gegevens die u naar de diagnostische gegevens verzendt. Als u Azure Session hosts hebt die zich buiten uw virtuele Windows-bureaublad omgeving bevinden, kunt u het beste een aangewezen Log Analytics-werk ruimte hebben voor de Windows-sessie hosts met virtueel bureau blad. 

Instellen van de Log Analytics-werk ruimte waar u de sessiehost wilt verzamelen: 

1. Selecteer het tabblad **Session Host data Settings** in de werkmap van de configuratie. 
2. Selecteer de **log Analytics werk ruimte** waarnaar u de sessiehost wilt verzenden. 

#### <a name="session-hosts"></a>Sessie-hosts

U moet de Log Analytics-agent installeren op alle sessie hosts in de hostgroep en gegevens van die hosts naar de geselecteerde Log Analytics-werk ruimte verzenden. Als Log Analytics niet is geconfigureerd voor alle sessie-hosts in de hostgroep, ziet u boven aan de **Session Host-gegevens instellingen** het gedeelte **Session hosts** in het bericht ' sommige hosts in de hostgroep verzenden geen gegevens naar de geselecteerde log Analytics werk ruimte. '

>[!NOTE]
> Als u de sectie **Session hosts** of het fout bericht niet ziet, worden alle sessie-hosts correct ingesteld. Ga verder met het instellen van instructies voor de [prestatie meter items van de werk ruimte](#workspace-performance-counters).

Om uw resterende sessie-hosts in te stellen met behulp van de configuratie werkmap:

1. Selecteer **hosts toevoegen aan de werk ruimte**. 
2. Vernieuw de configuratie werkmap.

>[!NOTE]
>De hostmachine moet worden uitgevoerd om de Log Analytics-uitbrei ding te installeren. Als automatische implementatie niet werkt, kunt u in plaats daarvan de extensie hand matig installeren op een host. Zie [log Analytics virtuele machine-extensie voor Windows voor](../virtual-machines/extensions/oms-windows.md)meer informatie over het hand matig installeren van de extensie.

#### <a name="workspace-performance-counters"></a>Prestatie meter items voor werk ruimte

U dient specifieke prestatie meter items in te scha kelen voor het verzamelen van prestatie gegevens van uw sessie-hosts en deze naar de Log Analytics-werk ruimte te verzenden.

Als u de prestatie meter items al hebt ingeschakeld en wilt verwijderen, volgt u de instructies in [prestatie meter items configureren](../azure-monitor/agents/data-sources-performance-counters.md). U kunt prestatie meter items op dezelfde locatie toevoegen en verwijderen.

Prestatie meter items instellen met behulp van de configuratie werkmap: 

1. Controleer onder **prestatie meter items voor de werk ruimte** de optie **geconfigureerde items** om de items te zien die u al hebt ingeschakeld voor verzen ding naar de log Analytics-werk ruimte. Controleer de **ontbrekende items** om ervoor te zorgen dat u alle vereiste tellers hebt ingeschakeld.
2. Als u ontbrekende tellers hebt, selecteert u **prestatie meter items configureren**.
3. Selecteer **configuratie Toep assen**.
4. Vernieuw de configuratie werkmap.
5. Controleer of alle vereiste items zijn ingeschakeld door de lijst **ontbrekende items** te controleren. 

#### <a name="configure-windows-event-logs"></a>Windows-gebeurtenis logboeken configureren

U moet ook specifieke Windows-gebeurtenis logboeken inschakelen voor het verzamelen van fouten, waarschuwingen en informatie van de sessie-hosts en deze naar de Log Analytics-werk ruimte verzenden.

Als u Windows-gebeurtenis logboeken al hebt ingeschakeld en deze wilt verwijderen, volgt u de instructies in [Windows-gebeurtenis logboeken configureren](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  U kunt Windows-gebeurtenis logboeken op dezelfde locatie toevoegen en verwijderen.

Windows-gebeurtenis logboeken instellen met behulp van de configuratie werkmap:

1. Controleer onder **configuratie van Windows-gebeurtenis logboeken** **geconfigureerde gebeurtenis logboeken** om te zien welke gebeurtenis logboeken u al hebt ingeschakeld om naar de log Analytics-werk ruimte te verzenden. Controleer de **ontbrekende gebeurtenis logboeken** om ervoor te zorgen dat u alle Windows-gebeurtenis Logboeken hebt ingeschakeld.
2. Als u Windows-gebeurtenis Logboeken hebt ontbreken, selecteert u **gebeurtenissen configureren**.
3. Selecteer **Implementeren**.
4. Vernieuw de configuratie werkmap.
5. Zorg ervoor dat alle vereiste Windows-gebeurtenis logboeken zijn ingeschakeld door de lijst **ontbrekende gebeurtenis logboeken** te controleren. 

>[!NOTE]
>Als automatische gebeurtenis implementatie mislukt, selecteert u **configuratie van agent openen** in de werkmap configuratie om hand matig ontbrekende Windows-gebeurtenis Logboeken toe te voegen. 

## <a name="optional-configure-alerts"></a>Optioneel: waarschuwingen configureren

Met Azure Monitor voor virtueel bureau blad van Windows kunt u Azure Monitor waarschuwingen in uw geselecteerde abonnement bewaken in de context van de Windows-gegevens van virtueel bureau blad. Azure Monitor waarschuwingen zijn een optioneel onderdeel van uw Azure-abonnementen en u moet deze afzonderlijk instellen van Azure Monitor voor Windows virtueel bureau blad. U kunt het Framework van Azure Monitor-waarschuwingen gebruiken om aangepaste waarschuwingen in te stellen voor gebeurtenissen van het virtueel bureau blad van Windows, diagnostische gegevens en bronnen. Zie [reageren op gebeurtenissen met Azure monitor-waarschuwingen](../azure-monitor/alerts/tutorial-response.md)voor meer informatie over Azure monitor-waarschuwingen.

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiks gegevens

Micro soft verzamelt automatisch gebruiks-en prestatie gegevens via uw gebruik van de Azure Monitor service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren.

Om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden, bevatten de verzamelde gegevens de sessie-ID van de portal, Azure Active Directory gebruikers-ID en de naam van het tabblad Portal waar de gebeurtenis heeft plaatsgevonden. Micro soft verzamelt geen namen, adressen of andere contact gegevens.

Zie de [privacyverklaring voor micro soft Online Services](https://privacy.microsoft.com/privacystatement)voor meer informatie over het verzamelen en gebruiken van gegevens.

>[!NOTE]
>Zie [Azure data subject-aanvragen voor de AVG voor](/microsoft-365/compliance/gdpr-dsr-azure)meer informatie over het weer geven of verwijderen van uw persoonlijke gegevens die door de service zijn verzameld. Zie [de sectie AVG van de service Trust-Portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)voor meer informatie over AVG.

## <a name="next-steps"></a>Volgende stappen

Nu u Azure Monitor hebt geconfigureerd voor uw virtuele Windows-bureau blad-omgeving, zijn er enkele resources die u kunnen helpen bij het bewaken van uw omgeving:

- Bekijk onze [verklarende woorden lijst](azure-monitor-glossary.md) voor meer informatie over de termen en concepten die betrekking hebben op Azure monitor voor virtueel bureau blad van Windows.
- Als er een probleem optreedt, raadpleegt u de [gids voor probleem oplossing](troubleshoot-azure-monitor.md) voor hulp en bekende problemen.
