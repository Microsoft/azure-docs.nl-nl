---
title: De preview-versie van Windows virtueel bureau blad controleren gebruiken-Azure
description: Azure Monitor gebruiken voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e0be6decf28fcbb2edacd5019f567d26403b1f31
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466814"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Gebruik Azure Monitor voor virtuele Windows-Bureau bladen om uw implementatie te bewaken (preview-versie)

>[!IMPORTANT]
>Azure Monitor voor het virtuele bureau blad van Windows is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Azure Monitor voor virtueel bureau blad van Windows (preview) is een dash board dat is gebaseerd op Azure Monitor werkmappen waarmee IT-professionals hun virtuele Windows-desktop omgevingen kunnen begrijpen. In dit onderwerp vindt u instructies voor het instellen van Azure Monitor voor virtuele Windows-Bureau bladen voor het bewaken van uw virtuele Windows-bureaublad omgevingen.

## <a name="requirements"></a>Vereisten

Voordat u Azure Monitor voor Windows virtueel bureau blad gaat gebruiken, moet u de volgende dingen instellen:

- Alle Windows virtueel-bureaublad omgevingen die u bewaakt, moeten zijn gebaseerd op de nieuwste versie van het virtuele Windows-bureau blad dat compatibel is met Azure Resource Manager.

- Ten minste één geconfigureerde Log Analytics-werk ruimte.

- Schakel gegevens verzameling in voor de volgende dingen in uw Log Analytics-werk ruimte:
    - Vereiste prestatie meter items
    - Prestatie meter items of gebeurtenissen die worden gebruikt in Azure Monitor voor virtueel bureau blad van Windows
    - Gegevens uit het diagnostische hulp programma voor alle objecten in de omgeving die u wilt bewaken.
    - Virtuele machines (Vm's) in de omgeving die u wilt bewaken.

Iedereen die de Azure Monitor voor het virtuele bureau blad van Windows voor uw omgeving bewaakt, heeft ook de volgende machtigingen voor lees toegang nodig:

- Lees toegang tot de resource groep waar de resources van de omgeving zich bevinden.

- Lees toegang tot de resource groep (en) waar de sessie-hosts van de omgeving zich bevinden

>[!NOTE]
> Met alleen-lezen toegang kunnen beheerders gegevens weer geven. Ze hebben verschillende machtigingen nodig om resources te beheren in de virtuele bureau blad-portal van Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor voor virtueel bureau blad van Windows openen

U kunt Azure Monitor voor Windows virtueel bureau blad openen met een van de volgende methoden:

- Ga naar [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).

- Zoek en selecteer **Windows virtueel bureau blad** in het Azure Portal en selecteer vervolgens **inzichten**.

- Zoek en selecteer **Azure monitor** in de Azure Portal. Selecteer **Insights-hub** onder **inzichten** en selecteer **Other** onder andere **Windows virtueel bureau blad** om het dash board te openen op de pagina Azure monitor.

Wanneer u Azure Monitor voor Windows virtueel bureau blad hebt geopend, selecteert u een of meer van de selectie vakjes met het naam **abonnement**, de **resource groep**, de **hostgroep** en het **tijds bereik** op basis van de omgeving die u wilt bewaken.

>[!NOTE]
>Virtueel bureau blad van Windows biedt momenteel alleen ondersteuning voor bewaking van één abonnement, resource groep en hostgroep per keer. Als u de omgeving die u wilt bewaken niet kunt vinden, raadpleegt u de documentatie voor het [oplossen van problemen met](troubleshoot-azure-monitor.md) bekende functie aanvragen en problemen.

## <a name="set-up-with-the-configuration-workbook"></a>Instellen met de configuratie werkmap

Als dit de eerste keer is dat u Azure Monitor voor Windows virtueel bureau blad opent, moet u Azure Monitor configureren voor uw virtuele Windows-bureau blad-resources. Uw resources configureren:

1. Open de werkmap in de Azure Portal.
2. Selecteer **de werkmap configuratie openen**.

Met de configuratie werkmap wordt uw bewakings omgeving ingesteld en kunt u de configuratie controleren nadat u het installatie proces hebt voltooid. Het is belang rijk om uw configuratie te controleren als items in het dash board niet correct worden weer gegeven of als de product groep updates publiceert waarvoor extra gegevens punten nodig zijn.

## <a name="host-pool-diagnostic-settings"></a>Diagnostische instellingen hostgroep

U moet Azure Monitor Diagnostische instellingen inschakelen voor alle objecten in de virtuele Windows-bureaublad omgeving die deze functie ondersteunen.

1. Open Azure Monitor voor Windows virtueel bureau blad op [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)en selecteer vervolgens **configuratie werkmap**.

2. Selecteer een omgeving om te bewaken onder **abonnement**, **resource groep** en **hostgroep**.

3. Controleer onder **Diagnostische instellingen voor hostgroep** of Windows diagnostische gegevens over virtueel bureau blad zijn ingeschakeld voor de hostgroep. Als dat niet het geval is, wordt een fout bericht weer gegeven met de tekst ' er is geen bestaande diagnostische configuratie gevonden voor de geselecteerde hostgroep. ' 
   
   De volgende tabellen moeten worden ingeschakeld:

    - Controlepunt
    - Fout
    - Beheer
    - Verbinding
    - HostRegistration

    >[!NOTE]
    > Als het fout bericht niet wordt weer gegeven, hoeft u stap 4 niet uit te voeren.

4. Selecteer **hostgroep configureren**.

5. Selecteer **Implementeren**.

6. Vernieuw de werkmap.

Meer informatie over het inschakelen van diagnostische gegevens voor alle objecten in de virtueel-bureaublad omgeving van Windows of toegang tot de Log Analytics-werk ruimte op [Windows virtuele bureau blad diagnostische gegevens verzenden naar log Analytics](diagnostics-log-analytics.md).

## <a name="configure-log-analytics"></a>Log Analytics configureren

Als u Azure Monitor voor Windows virtueel bureau blad wilt gebruiken, hebt u ook ten minste één Log Analytics werk ruimte nodig om gegevens te verzamelen uit de omgeving die u wilt bewaken en deze aan de werkmap te leveren. Als u al een hebt ingesteld, gaat u verder met het [instellen van prestatie meter items](#set-up-performance-counters). Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte in te stellen voor het Azure-abonnement met uw virtuele Windows-desktop omgeving.

>[!NOTE]
>Er gelden standaard kosten voor gegevens opslag voor Log Analytics. We raden u aan om het model voor betalen naar gebruik te kiezen en aan te passen wanneer u uw implementatie schaalt en meer gegevens in beslag neemt. Zie [Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/)voor meer informatie.

### <a name="set-up-performance-counters"></a>Prestatie meter items instellen

U moet specifieke prestatie meter items voor de verzameling inschakelen in het bijbehorende steekproef interval in de werk ruimte Log Analytics. Deze prestatie meter items zijn de enige items die u nodig hebt om het virtuele bureau blad van Windows te bewaken. U kunt alle andere uitschakelen om kosten te besparen.

Als u de prestatie meter items al hebt ingeschakeld en wilt verwijderen, volgt u de instructies in [prestatie meter items configureren](../azure-monitor/platform/data-sources-performance-counters.md) om de prestatie meter items opnieuw te configureren. In het artikel wordt beschreven hoe u items toevoegt, maar u kunt ze ook op dezelfde locatie verwijderen.

Als u prestatie meter items nog niet hebt ingesteld, gaat u als volgt te werk om ze te configureren voor Azure Monitor voor virtuele Windows-bureau blad:

1. Ga naar [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)en selecteer de **configuratie werkmap** onder in het venster.

2. Onder **log Analytics configuratie** selecteert u de werk ruimte die u hebt ingesteld voor uw abonnement.

3. In de **werk ruimte-prestatie meter items** ziet u de lijst met prestatie meter items die nodig zijn voor de bewaking. Controleer aan de rechter kant van de lijst de items in de lijst **ontbrekende items** om de prestatie meter items in te scha kelen die u nodig hebt om uw werk ruimte te bewaken.

4. Selecteer **prestatie meter items configureren**.

5. Selecteer **configuratie Toep assen**.

6. Vernieuw de configuratie werkmap en ga door met het instellen van uw omgeving.

U kunt ook nieuwe prestatie meter items toevoegen na de initiële configuratie wanneer de service wordt bijgewerkt en nieuwe controle hulpprogramma's vereist. U kunt controleren of alle vereiste items zijn ingeschakeld door deze te selecteren in de lijst **ontbrekende items** .

>[!NOTE]
>De prestatie meter items voor de invoer vertraging zijn alleen compatibel met Windows 10 RS5 en hoger, of Windows Server 2019 en hoger.

Zie [prestatie meter items configureren](../azure-monitor/platform/data-sources-performance-counters.md)voor meer informatie over het hand matig toevoegen van prestatie meter items die nog niet zijn ingeschakeld voor verzameling.

### <a name="set-up-windows-events"></a>Windows-gebeurtenissen instellen

Vervolgens moet u specifieke Windows-gebeurtenissen inschakelen voor de verzameling in de werk ruimte Log Analytics. De gebeurtenissen die in deze sectie worden beschreven, zijn de enige Azure Monitor voor virtuele Windows-Desktop behoeften. U kunt alle andere uitschakelen om kosten te besparen.

Windows-gebeurtenissen instellen:

1. Als Windows-gebeurtenissen al zijn ingeschakeld en u deze wilt verwijderen, verwijdert u de gebeurtenissen die u niet wilt gebruiken voordat u de werkmap van de configuratie gebruikt om de vereiste set voor bewaking in te scha kelen.

2. Ga naar Azure Monitor voor virtueel bureau blad van Windows op [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)en selecteer vervolgens **configuratie werkmap** onder aan het venster.

3. In de **configuratie van Windows-gebeurtenissen** vindt u een lijst met Windows-gebeurtenissen die vereist zijn voor de bewaking. Aan de rechter kant van de lijst ziet u de lijst met **ontbrekende gebeurtenissen** , waar u de vereiste gebeurtenis namen en gebeurtenis typen vindt die momenteel niet zijn ingeschakeld voor uw werk ruimte. Noteer elk van deze namen voor later.

4. Selecteer **configuratie van werk ruimten openen**.

5. Selecteer **gegevens**.

6. Selecteer **Windows-gebeurtenis logboeken**.

7. Voeg de ontbrekende gebeurtenis namen uit stap 3 en het vereiste gebeurtenis type voor elk toe.

8. Vernieuw de configuratie werkmap en ga door met het instellen van uw omgeving.

U kunt nieuwe Windows-gebeurtenissen na de eerste configuratie toevoegen als er voor het bewakings hulpprogramma updates nieuwe gebeurtenissen moeten worden ingeschakeld. Als u wilt controleren of alle vereiste gebeurtenissen zijn ingeschakeld, gaat u terug naar de lijst met **ontbrekende** gebeurtenissen en schakelt u de ontbrekende gebeurtenissen in die u ziet.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>De Log Analytics-agent op alle hosts installeren

Ten slotte moet u de Log Analytics-agent installeren op alle hosts in de hostgroep om gegevens te verzenden van de hosts naar de geselecteerde werk ruimte.

De Log Analytics-agent installeren:

1. Ga naar Azure Monitor voor virtueel bureau blad van Windows op [aka.MS/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)en selecteer vervolgens **configuratie werkmap** onder aan het venster.

2. Als Log Analytics niet is geconfigureerd voor alle hosts in de hostgroep, ziet u een fout onder aan de sectie Log Analytics configuratie met het bericht ' sommige hosts in de hostgroep verzenden geen gegevens naar de geselecteerde Log Analytics-werk ruimte. ' Selecteer **hosts toevoegen aan werk ruimte** om de geselecteerde hosts toe te voegen. Als het fout bericht niet wordt weer gegeven, kunt u hier stoppen.

3. Vernieuw de configuratie werkmap.

>[!NOTE]
>De hostmachine moet worden uitgevoerd om de Log Analytics-uitbrei ding te installeren. Als automatische implementatie mislukt op een host, kunt u de extensie altijd hand matig installeren op een host. Zie [log Analytics virtuele machine-extensie voor Windows voor](../virtual-machines/extensions/oms-windows.md)meer informatie over het hand matig installeren van de extensie.

## <a name="optional-configure-alerts"></a>Optioneel: waarschuwingen configureren

U kunt Azure Monitor voor Windows virtueel bureau blad configureren om u te waarschuwen als er ernstige Azure Monitor waarschuwingen optreden binnen het geselecteerde abonnement. Volg hiervoor de instructies in [reageren op gebeurtenissen met Azure monitor-waarschuwingen](../azure-monitor/learn/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Diagnostische en gebruiks gegevens

Micro soft verzamelt automatisch gebruiks-en prestatie gegevens via uw gebruik van de Azure Monitor service. Micro soft gebruikt deze gegevens om de kwaliteit, beveiliging en integriteit van de service te verbeteren.

Om nauw keurige en efficiënte probleemoplossings mogelijkheden te bieden, bevatten de verzamelde gegevens de sessie-ID van de portal, Azure Active Directory gebruikers-ID en de naam van het tabblad Portal waar de gebeurtenis heeft plaatsgevonden. Micro soft verzamelt geen namen, adressen of andere contact gegevens.

Zie de [privacyverklaring voor micro soft Online Services](https://privacy.microsoft.com/privacystatement)voor meer informatie over het verzamelen en gebruiken van gegevens.

>[!NOTE]
>Zie [Azure data subject-aanvragen voor de AVG voor](/microsoft-365/compliance/gdpr-dsr-azure)meer informatie over het weer geven of verwijderen van uw persoonlijke gegevens die door de service zijn verzameld. Zie [de sectie AVG van de service Trust-Portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)voor meer informatie over AVG.

## <a name="next-steps"></a>Volgende stappen

Nu u uw virtuele Windows-bureau blad hebt geconfigureerd Azure Portal, zijn hier enkele bronnen beschikbaar waarmee u het volgende kunt doen:

- Bekijk onze [verklarende woorden lijst](azure-monitor-glossary.md) voor meer informatie over de termen en concepten die betrekking hebben op Azure monitor voor virtueel bureau blad van Windows.
- Als u een probleem ondervindt, raadpleegt u de [hand leiding](troubleshoot-azure-monitor.md) voor het oplossen van problemen.