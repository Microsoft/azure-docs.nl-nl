---
title: Problemen met Log Analytics-agent voor Windows oplossen
description: Beschrijf de symptomen, oorzaken en oplossingen voor de meest voorkomende problemen met de Log Analytics agent voor Windows in Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 7ab67602ebba2ae5446ecc0052ef4b03bba1e1bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952980"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Problemen met de Log Analytics-agent voor Windows oplossen 

In dit artikel vindt u informatie over het oplossen van problemen die u mogelijk ondervindt met de Log Analytics-agent voor Windows in Azure Monitor en worden mogelijke oplossingen voorgesteld om deze op te lossen.

Als geen van deze stappen voor u werkt, zijn de volgende ondersteunings kanalen ook beschikbaar:

* Klanten met premier-ondersteunings voordelen kunnen een ondersteunings aanvraag openen met [premier](https://premier.microsoft.com/).
* Klanten met ondersteunings overeenkomsten voor Azure kunnen een ondersteunings aanvraag openen [in de Azure Portal](https://azure.microsoft.com/support/options/).
* Ga naar de Log Analytics feedback pagina om de verzonden ideeën en bugs te bekijken [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) of een nieuwe bestand te openen. 

## <a name="log-analytics-troubleshooting-tool"></a>Hulp programma voor probleem oplossing Log Analytics

Het Windows-hulp programma voor probleem oplossing van Log Analytics agent is een verzameling Power shell-scripts die zijn ontworpen om problemen met de Log Analytics-agent te vinden en te onderzoeken. Het wordt bij de installatie automatisch opgenomen in de-agent. Het uitvoeren van het hulp programma moet de eerste stap zijn bij het vaststellen van een probleem.

### <a name="how-to-use"></a>Gebruik
1. Open Power shell-prompt als Administrator op de computer waarop Log Analytics-agent is geïnstalleerd.
1. Ga naar de map waarin het hulp programma zich bevindt.
   * `cd "C:\Program Files\Microsoft Monitoring Agent\Agent\Troubleshooter"`
1. Voer het hoofd script uit met behulp van de volgende opdracht:
   * `.\GetAgentInfo.ps1`
1. Selecteer een scenario voor het oplossen van problemen.
1. Volg de instructies op de console. (Opmerking: voor de stappen van traceer logboeken moet hand matige interventie worden uitgevoerd om de logboek verzameling te stoppen. Op basis van de reproduceer baarheid van het probleem, wacht u tot de tijds duur en druk op ' om de logboek verzameling te stoppen en door te gaan naar de volgende stap).

   Locaties van het bestand met resultaten worden geregistreerd na voltooiing en een nieuw Verkenner-venster wordt geopend.

### <a name="installation"></a>Installatie
Het hulp programma voor probleem oplossing wordt automatisch opgenomen bij de installatie van de Log Analytics agent build 10.20.18053.0 en later.

### <a name="scenarios-covered"></a>Scenario's die worden behandeld
Hieronder ziet u een lijst met scenario's die worden gecontroleerd door het hulp programma voor probleem oplossing:

- Agent die geen gegevens of vitale gegevens rapporteert, ontbreken
- Implementatie van agent-extensie mislukt
- Agent loopt vast
- Agent die hoge CPU/geheugen verbruikt
- Mislukte installatie/verwijdering
- Probleem met aangepaste logboeken
- Probleem met OMS-gateway
- Probleem met prestatie meter items
- Alle logboeken verzamelen

>[!NOTE]
>Voer het hulp programma voor probleem oplossing uit wanneer u een probleem ondervindt. Bij het openen van een ticket helpt het ondersteunings team het probleem sneller op te lossen met de logboeken.

## <a name="important-troubleshooting-sources"></a>Belang rijke bronnen voor probleem oplossing

 Voor hulp bij het oplossen van problemen met de Log Analytics-agent voor Windows, registreert de agent gebeurtenissen in het Windows-gebeurtenis logboek, specifiek onder *Application and Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Connectiviteitsproblemen

Als de agent communiceert via een proxy server of firewall, kunnen er beperkingen gelden voor de locatie van de communicatie tussen de bron computer en de Azure Monitor service. Als communicatie wordt geblokkeerd vanwege een onjuiste configuratie, kan de registratie bij een werk ruimte mislukken tijdens het installeren van de agent of het configureren van de agent na de installatie om te rapporteren aan een extra werk ruimte. Agent communicatie kan mislukken na een geslaagde registratie. In deze sectie worden de methoden beschreven voor het oplossen van dit type probleem met de Windows-agent.

Controleer of de firewall of proxy is geconfigureerd voor het toestaan van de volgende poorten en Url's die in de volgende tabel worden beschreven. Controleer ook of HTTP-controle niet is ingeschakeld voor webverkeer, omdat dit een beveiligd TLS-kanaal tussen de agent en Azure Monitor kan voor komen.  

|Agentresource|Poorten |Richting |HTTPS-controle overslaan|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Poort 443 |Uitgaand|Yes |  
|*.oms.opinsights.azure.com |Poort 443 |Uitgaand|Yes |  
|*.blob.core.windows.net |Poort 443 |Uitgaand|Yes |  
|*. agentsvc.azure-automation.net |Poort 443 |Uitgaand|Yes |  

Zie [Azure Government Management](../../azure-government/compare-azure-government-global-azure.md#azure-monitor)voor informatie over de firewall die vereist is voor Azure Government. Als u van plan bent de Azure Automation Hybrid Runbook Worker te gebruiken om verbinding te maken met de Automation-Service voor het gebruik van runbooks of beheer oplossingen in uw omgeving, moet deze toegang hebben tot het poort nummer en de Url's die worden beschreven in [uw netwerk configureren voor de Hybrid Runbook worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Er zijn verschillende manieren waarop u kunt controleren of de agent met succes communiceert met Azure Monitor.

- De [Azure Log Analytics status van agent-beoordeling](../insights/solution-agenthealth.md) inschakelen in de werk ruimte. Bekijk in het dash board van Status van agent de kolom **aantal agents** die niet meer reageert om snel te zien of de agent wordt weer gegeven.  

- Voer de volgende query uit om te bevestigen dat de agent een heartbeat verzendt naar de werk ruimte waarmee deze is geconfigureerd om te rapporteren. Vervang door `<ComputerName>` de werkelijke naam van de computer.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Als de computer met succes communiceert met de service, retourneert de query een resultaat. Als de query geen resultaat heeft geretourneerd, controleert u eerst of de agent is geconfigureerd om te rapporteren aan de juiste werk ruimte. Als de configuratie correct is geconfigureerd, gaat u verder met stap 3 en zoekt u in het Windows-gebeurtenis logboek om te bepalen of de agent een logboek registratie voor het probleem kan verhinderen dat deze communiceert met Azure Monitor.

- Een andere methode om een connectiviteits probleem te identificeren, is door het **TestCloudConnectivity** -hulp programma uit te voeren. Het hulp programma wordt standaard geïnstalleerd met de agent in de map *%systemroot%\Program Files\Microsoft monitoring Agent\Agent*. Ga vanaf een opdrachtprompt met verhoogde bevoegdheid naar de map en voer het hulpprogramma uit. Het hulp programma retourneert de resultaten en markeert waar de test is mislukt (bijvoorbeeld als deze is gekoppeld aan een bepaalde poort/URL die is geblokkeerd). 

    ![Resultaten van de uitvoering van het TestCloudConnection-hulp programma](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filter het *Operations Manager* gebeurtenis logboek op **gebeurtenis bronnen**  -  *Health Service modules*, *HealthService* en *service connector* en filter op **gebeurtenis niveau** *waarschuwing* en *fout* om te bevestigen of het een geschreven gebeurtenis in de volgende tabel heeft. Als dat het geval is, raadpleegt u de oplossings stappen die voor elke mogelijke gebeurtenis zijn opgenomen.

    |Gebeurtenis-id |Bron |Beschrijving |Oplossing |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Health Service |De verbinding met de service via de agent is mislukt |Deze fout kan optreden wanneer de agent niet rechtstreeks of via een firewall of proxy server naar de Azure Monitor-service kan communiceren. Controleer de proxy-instellingen van de agent of Controleer of de netwerk firewall/-proxy TCP-verkeer van de computer naar de service toestaat.|
    |2138 |Health Service modules |Proxy vereist verificatie |Configureer de proxy-instellingen voor de agent en geef de gebruikers naam en het wacht woord op die moeten worden geverifieerd bij de proxy server. |
    |2129 |Health Service modules |Mislukte verbinding/TLS-onderhandeling |Controleer de TCP/IP-instellingen en agent proxy instellingen van de netwerk adapter.|
    |2127 |Health Service modules |Fout code voor het verzenden van ontvangen gegevens is mislukt |Als deze alleen periodiek plaatsvindt gedurende de dag, zou dit een wille keurige afwijkingen kunnen zijn die kunnen worden genegeerd. Monitor om te begrijpen hoe vaak het gebeurt. Als het vaak gedurende de dag gebeurt, controleert u eerst uw netwerk configuratie en proxy-instellingen. Als de beschrijving HTTP-fout code 404 bevat en de eerste keer dat de agent gegevens verzendt naar de service, wordt er een 500-fout met een interne 404-fout code opgenomen. 404 betekent niet gevonden. Dit geeft aan dat het opslag gebied voor de nieuwe werk ruimte nog steeds wordt ingericht. Bij de volgende nieuwe poging worden gegevens naar de werk ruimte geschreven zoals verwacht. Een HTTP-fout 403 kan duiden op een probleem met machtigingen of referenties. Er is meer informatie opgenomen in de 403-fout om het probleem op te lossen.|
    |4000 |Service connector |De DNS-naam omzetting is mislukt |De computer kan het Internet adres dat wordt gebruikt voor het verzenden van gegevens naar de service niet omzetten. Dit kunnen de instellingen voor de DNS-resolver op uw computer, onjuiste proxy-instellingen of een tijdelijk DNS-probleem met uw provider zijn. Als het regel matig gebeurt, kan dit worden veroorzaakt door een tijdelijk netwerk probleem.|
    |4001 |Service connector |De verbinding met de service is mislukt. |Deze fout kan optreden wanneer de agent niet rechtstreeks of via een firewall of proxy server naar de Azure Monitor-service kan communiceren. Controleer de proxy-instellingen van de agent of Controleer of de netwerk firewall/-proxy TCP-verkeer van de computer naar de service toestaat.|
    |4002 |Service connector |De service heeft de HTTP-status code 403 geretourneerd als antwoord op een query. Neem contact op met de service beheerder voor de status van de service. De query wordt later opnieuw geprobeerd. |Deze fout wordt geschreven tijdens de eerste registratie fase van de agent. er wordt een URL weer gegeven die er ongeveer als volgt uitziet: *https:// \<workspaceID> . OMS.opinsights.Azure.com/AgentService.SVC/AgentTopologyRequest*. De fout code 403 betekent verboden en kan worden veroorzaakt door een niet-getypte werk ruimte-ID of-sleutel, of de gegevens en tijd zijn onjuist op de computer. Als de tijd +/-15 minuten vanaf de huidige tijd is, mislukt de onboarding. U kunt dit corrigeren door de datum en/of tijd zone van uw Windows-computer bij te werken.|

## <a name="data-collection-issues"></a>Problemen met gegevens verzameling

Nadat de agent is geïnstalleerd en rapporteert aan de geconfigureerde werk ruimte of werk ruimten, kan deze de configuratie stoppen, verzamelen of door sturen van prestaties, Logboeken of andere gegevens naar de service, afhankelijk van wat is ingeschakeld en gericht op de computer. Het is nood zakelijk om te bepalen of:

- Is het een specifiek gegevens type of alle gegevens die niet beschikbaar zijn in de werk ruimte?
- Is het gegevens type dat is opgegeven door een oplossing of opgegeven als onderdeel van de configuratie van de gegevens verzameling voor de werk ruimte?
- Hoeveel computers heeft het probleem? Is het een of meer computers die rapporteren aan de werk ruimte?
- Werkte het naar een bepaald tijdstip van de dag, of is het nog nooit verzameld? 
- Wordt de zoek opdracht van de logboeken die u wilt gebruiken, correct? 
- Heeft de agent ooit de configuratie ontvangen van Azure Monitor?

De eerste stap in het oplossen van problemen is om te bepalen of de computer een heartbeat-gebeurtenis verzendt.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Als de query resultaten retourneert, moet u bepalen of een bepaald gegevens type niet wordt verzameld en doorgestuurd naar de service. Dit kan worden veroorzaakt doordat de agent geen bijgewerkte configuratie van de service ontvangt of een andere symptoom die verhindert dat de agent normaal werkt. Voer de volgende stappen uit om verder te gaan met het oplossen van problemen.

1. Open een opdracht prompt met verhoogde bevoegdheid op de computer en start de Agent-service opnieuw door te typen `net stop healthservice && net start healthservice` .
2. Open het gebeurtenis logboek van *Operations Manager* en zoek naar **gebeurtenis-id's** *7023, 7024, 7025, 7028* en *1210* van **gebeurtenis bron** *HealthService*.  Deze gebeurtenissen geven aan dat de agent configuratie kan ontvangen van Azure Monitor en dat de computer actief wordt gecontroleerd. De gebeurtenis beschrijving voor gebeurtenis-ID 1210 wordt ook op de laatste regel op alle oplossingen en inzichten vermeld die zijn opgenomen in het bewakings bereik van de agent.  

    ![Beschrijving gebeurtenis-ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Als de verwachte gegevens na enkele minuten niet worden weer gegeven in de query resultaten of visualisatie, afhankelijk van of u de gegevens uit een oplossing of inzicht bekijkt, zoekt u in het gebeurtenis logboek van *Operations Manager* naar **gebeurtenis bronnen** *HealthService* en *Health Service modules* en filtert u op **gebeurtenis niveau** *waarschuwing* en *fout* om te bevestigen of de volgende tabel geschreven gebeurtenissen bevat.

    |Gebeurtenis-id |Bron |Beschrijving |Oplossing |
    |---------|-------|------------|
    |8000 |HealthService |Met deze gebeurtenis wordt aangegeven of een werk stroom die is gerelateerd aan prestaties, gebeurtenis of ander gegevens type dat is verzameld, niet kan worden doorgestuurd naar de service voor opname naar de werk ruimte. | Gebeurtenis-ID 2136 van de bron HealthService wordt samen met deze gebeurtenis geschreven en kan erop wijzen dat de agent niet kan communiceren met de service, mogelijk vanwege een onjuiste configuratie van de proxy-en verificatie-instellingen, netwerk storing of de netwerk firewall/-proxy geen TCP-verkeer van de computer naar de service toestaat.| 
    |10102 en 10103 |Health Service modules |De gegevens bron kan niet worden omgezet met de werk stroom. |Dit kan gebeuren als het opgegeven prestatie meter item of exemplaar niet aanwezig is op de computer of onjuist is gedefinieerd in de instellingen voor de werkruimte gegevens. Als dit een door de gebruiker opgegeven [prestatie meter item](data-sources-performance-counters.md#configuring-performance-counters)is, controleert u of de opgegeven informatie de juiste indeling heeft en aanwezig is op de doel computers. |
    |26002 |Health Service modules |De gegevens bron kan niet worden omgezet met de werk stroom. |Dit kan gebeuren als het opgegeven Windows-gebeurtenis logboek niet aanwezig is op de computer. Deze fout kan veilig worden genegeerd als er niet wordt verwacht dat dit gebeurtenis logboek is geregistreerd op de computer, anders als dit een door de gebruiker opgegeven [gebeurtenis logboek](data-sources-windows-events.md#configuring-windows-event-logs)is, controleert u of de opgegeven informatie juist is. |
