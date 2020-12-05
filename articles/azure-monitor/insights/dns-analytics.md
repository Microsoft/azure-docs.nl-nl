---
title: DNS-analyse oplossing in Azure Monitor | Microsoft Docs
description: Stel de DNS-analyse-oplossing in Azure Monitor in en gebruik deze om inzicht te krijgen in de DNS-infra structuur voor beveiliging, prestaties en bewerkingen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 7bdea9239faa4ec66fffa236bea40afd5e628e62
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607140"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Inzichten over uw DNS-infra structuur verzamelen met de preview-oplossing van DNS-analyse

![DNS-analyse-symbool](./media/dns-analytics/dns-analytics-symbol.png)

In dit artikel wordt beschreven hoe u de oplossing voor Azure DNS Analytics kunt instellen en gebruiken in Azure Monitor om inzicht te krijgen in de DNS-infra structuur voor beveiliging, prestaties en bewerkingen.

DNS-analyse helpt u bij het volgende:

- Identificeer clients die proberen om schadelijke domein namen om te zetten.
- Verouderde bron records identificeren.
- Identificeer regel matig opgevraagde domein namen en DNS-clients van dremepelwaarde.
- Bekijk de belasting van de aanvraag op DNS-servers.
- Dynamische DNS-registratie fouten weer geven.

De oplossing verzamelt, analyseert en correleert Windows DNS analytic-en audit logboeken en andere gerelateerde gegevens van uw DNS-servers.

## <a name="connected-sources"></a>Verbonden bronnen

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| **Verbonden bron** | **Ondersteuning** | **Beschrijving** |
| --- | --- | --- |
| [Windows-agents](../platform/agent-windows.md) | Ja | De oplossing verzamelt DNS-gegevens van Windows-agents. |
| [Linux-agents](../learn/quick-collect-linux-computer.md) | Nee | De oplossing verzamelt geen DNS-gegevens van direct Linux-agents. |
| [Beheergroep System Center Operations Manager](../platform/om-agents.md) | Ja | De oplossing verzamelt DNS-gegevens van agents in een verbonden Operations Manager-beheer groep. Een directe verbinding van de Operations Manager agent naar Azure Monitor is niet vereist. Gegevens worden doorgestuurd van de beheer groep naar de Log Analytics-werk ruimte. |
| [Azure Storage-account](../platform/resource-logs.md#send-to-log-analytics-workspace) | Nee | Azure Storage wordt niet gebruikt door de oplossing. |

### <a name="data-collection-details"></a>Details van gegevens verzameling

De oplossing verzamelt DNS-inventarisatie-en DNS-gegevens over gebeurtenissen van de DNS-servers waarop een Log Analytics-agent is geïnstalleerd. Deze gegevens worden vervolgens geüpload naar Azure Monitor en weer gegeven in het dash board van de oplossing. Inventaris-gerelateerde gegevens, zoals het aantal DNS-servers, zones en bron records, worden verzameld door de DNS Power shell-cmdlets uit te voeren. De gegevens worden elke twee dagen bijgewerkt. De gebeurtenis gegevens worden in realtime verzameld van de [analyse-en audit logboeken](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) die zijn opgenomen in de verbeterde DNS-logboek registratie en diagnoses in Windows Server 2012 R2.

## <a name="configuration"></a>Configuratie

Gebruik de volgende informatie om de oplossing te configureren:

- U moet beschikken over een [Windows](../platform/agent-windows.md) -of [Operations Manager](../platform/om-agents.md) -agent op elke DNS-server die u wilt bewaken.
- U kunt de DNS-analyse oplossing toevoegen aan uw Log Analytics-werk ruimte vanuit [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). U kunt ook het proces dat wordt beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](solutions.md)gebruiken.

De oplossing begint met het verzamelen van gegevens zonder dat er verdere configuratie nodig is. U kunt echter de volgende configuratie gebruiken om gegevens verzameling aan te passen.

### <a name="configure-the-solution"></a>De oplossing configureren

Klik in het dash board van de oplossing op **configuratie** om de pagina DNS-analyse configuratie te openen. Er zijn twee typen configuratie wijzigingen die u kunt aanbrengen:

- **Domein namen Allowlisted**. De oplossing verwerkt niet alle opzoek query's. Het onderhoudt een allowlist van domein naam achtervoegsels. De opzoek query's die worden omgezet in de domein namen die overeenkomen met de domein naam achtervoegsels in deze allowlist, worden niet verwerkt door de oplossing. Geen verwerking van allowlisted-domein namen helpt bij het optimaliseren van de gegevens die naar Azure Monitor worden verzonden. De standaard allowlist bevat populaire open bare domein namen, zoals www.google.com en www.facebook.com. U kunt de volledige standaard lijst weer geven door te schuiven.

  U kunt de lijst wijzigen om een domein naam achtervoegsel toe te voegen waarvoor u opzoek inzichten wilt weer geven. U kunt ook elk domein naam achtervoegsel verwijderen waarvoor u geen lookup Insights-inzichten wilt weer geven.

- **Drempel waarde voor dremepelwaarde-client**. DNS-clients die de drempel voor het aantal lookup-aanvragen overschrijden, worden gemarkeerd op de Blade **DNS-clients** . De standaard drempel waarde is 1.000. U kunt de drempel waarde bewerken.

    ![Allowlisted domein namen](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Management packs

Als u micro soft monitoring agent gebruikt om verbinding te maken met uw Log Analytics-werk ruimte, wordt de volgende management pack geïnstalleerd:

- Micro soft DNS data collector Intelligence Pack (micro soft. intelligence packs. DNS)

Als uw Operations Manager-beheer groep is verbonden met uw Log Analytics-werk ruimte, worden de volgende Management Packs geïnstalleerd in Operations Manager wanneer u deze oplossing toevoegt. Er is geen vereiste configuratie of onderhoud van deze Management Packs:

- Micro soft DNS data collector Intelligence Pack (micro soft. intelligence packs. DNS)
- Micro soft System Center Advisor DNS-analyse-configuratie (Microsoft.IntelligencePack.Dns.Configuratie)

Zie [Operations Manager koppelen aan Log Analytics](../platform/om-agents.md) voor meer informatie over de manier waarop uw management packs voor oplossingen worden bijgewerkt.

## <a name="use-the-dns-analytics-solution"></a>De DNS-analyse oplossing gebruiken

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


De DNS-tegel bevat het aantal DNS-servers waarop de gegevens worden verzameld. Het bevat ook het aantal aanvragen dat clients hebben gedaan om schadelijke domeinen in de afgelopen 24 uur op te lossen. Wanneer u op de tegel klikt, wordt het dash board van de oplossing geopend.

![DNS-analyse tegel](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard van de oplossing

Het oplossings Dashboard toont een overzicht van de verschillende functies van de oplossing. Het bevat ook koppelingen naar de gedetailleerde weer gave voor forensische analyse en diagnose. Standaard worden de gegevens weer gegeven voor de afgelopen zeven dagen. U kunt het datum-en tijds bereik wijzigen met behulp van het **besturings element datum-tijd selectie**, zoals wordt weer gegeven in de volgende afbeelding:

![Besturings element voor tijd selectie](./media/dns-analytics/dns-time.png)

Het dash board van de oplossing toont de volgende blades:

**DNS-beveiliging**. Hiermee worden de DNS-clients gerapporteerd die proberen te communiceren met schadelijke domeinen. Met behulp van micro soft Threat Intelligence-feeds kunt DNS-analyse IP-adressen van clients detecteren die toegang proberen te krijgen tot schadelijke domeinen. In veel gevallen worden door schadelijke software geïnfecteerde apparaten ' bellen ' naar het ' opdracht en besturings element ' van het schadelijke domein door de domein naam van de schadelijke software te herleiden.

![Blade DNS-beveiliging](./media/dns-analytics/dns-security-blade.png)

Wanneer u op een client-IP in de lijst klikt, wordt de zoek opdracht in Logboeken geopend en worden de Zoek gegevens van de respectieve query weer gegeven. In het volgende voor beeld heeft DNS-analyse gedetecteerd dat de communicatie is uitgevoerd met een [IRCbot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Resultaten van zoeken in logboeken weer geven IRCBot](./media/dns-analytics/ircbot.png)

De informatie helpt u bij het identificeren van het volgende:

- Client-IP die de communicatie heeft gestart.
- Domein naam die wordt omgezet in het schadelijke IP-adres.
- IP-adressen waarnaar de domein naam wordt omgezet.
- Schadelijk IP-adres.
- Ernst van het probleem.
- Reden voor het blocklisting van het schadelijke IP-adres.
- Detectie tijd.

**Query's in domeinen**. Biedt de meest voorkomende domein namen die worden doorzocht door de DNS-clients in uw omgeving. U kunt de lijst weer geven met alle domein namen waarvoor een query is uitgevoerd. U kunt ook inzoomen op de details van de lookup-aanvraag van een specifieke domein naam in Logboeken zoeken.

![Op Blade opgevraagde domeinen](./media/dns-analytics/domains-queried-blade.png)

**DNS-clients**. Rapporteert de clients die *de drempel waarde* voor het aantal query's in de gekozen tijds periode hebben geschonden. U kunt de lijst met alle DNS-clients en de details van de query's die door hen zijn gemaakt, weer geven in zoeken in Logboeken.

![Blade DNS-clients](./media/dns-analytics/dns-clients-blade.png)

**Dynamische DNS-registraties**. Rapport naam registratie fouten. Alle registratie fouten voor adres [bron records](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (type A en AAAA) worden gemarkeerd samen met de client-ip's die de registratie aanvragen hebben ingediend. U kunt deze informatie vervolgens gebruiken om de hoofd oorzaak van de registratie fout te vinden door de volgende stappen uit te voeren:

1. Zoek de zone die gemachtigd is voor de naam die de client probeert bij te werken.

1. Gebruik de oplossing om de inventarisatie gegevens van die zone te controleren.

1. Controleer of de dynamische update voor de zone is ingeschakeld.

1. Controleer of de zone is geconfigureerd voor beveiligde dynamische updates of niet.

    ![Blade dynamische DNS-registraties](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Naam registratie aanvragen**. De bovenste tegel toont een trend lijn van geslaagde en mislukte aanvragen voor dynamische DNS-updates. De onderste tegel geeft de Top 10-clients weer die mislukte DNS-update aanvragen verzenden naar de DNS-servers, gesorteerd op het aantal fouten.

![Blade naam registratie aanvragen](./media/dns-analytics/name-reg-req-blade.png)

Voor **beelden van DDI Analytics-query's**. Bevat een lijst met de meest voorkomende Zoek query's voor het rechtstreeks ophalen van RAW Analytics-gegevens.


![Voorbeeldquery's](./media/dns-analytics/queries.png)

U kunt deze query's gebruiken als uitgangs punt voor het maken van uw eigen query's voor aangepaste rapportage. De query wordt gekoppeld aan de zoek pagina voor DNS-analyse logboeken waar de resultaten worden weer gegeven:

- **Lijst met DNS-servers**. Geeft een lijst weer van alle DNS-servers met de bijbehorende FQDN, domein naam, Forestnaam en server-Ip's.
- **Lijst met DNS-zones**. Geeft een lijst weer van alle DNS-zones met de gekoppelde zone naam, status van dynamische updates, naam servers en status van DNSSEC-ondertekening.
- **Ongebruikte resource records**. Geeft een lijst weer van alle ongebruikte/verlopen bron records. Deze lijst bevat de naam van de bron record, het bron record type, de bijbehorende DNS-server, de generatie tijd van de record en de zone naam. U kunt deze lijst gebruiken om de DNS-bron records te identificeren die niet meer worden gebruikt. Op basis van deze informatie kunt u deze vermeldingen verwijderen van de DNS-servers.
- **DNS-servers query laden**. Toont informatie zodat u een perspectief kunt krijgen van de DNS-belasting op uw DNS-servers. Deze informatie kan u helpen bij het plannen van de capaciteit voor de servers. U kunt naar het tabblad **metrische gegevens** gaan om de weer gave te wijzigen in een grafische visualisatie. Deze weer gave helpt u te begrijpen hoe de DNS-belasting wordt gedistribueerd op uw DNS-servers. Het geeft de frequentie trends van een DNS-query voor elke server.

    ![Zoek resultaten voor het query logboek van DNS-servers](./media/dns-analytics/dns-servers-query-load.png)

- **DNS-zones query belasting**. Hier worden de DNS-zone-query-per-seconde-statistieken weer gegeven van alle zones op de DNS-servers die worden beheerd door de oplossing. Klik op het tabblad **metrische gegevens** om de weer gave te wijzigen van gedetailleerde records in een grafische visualisatie van de resultaten.
- **Configuratie gebeurtenissen**. Geeft alle wijzigings gebeurtenissen van de DNS-configuratie en de bijbehorende berichten. U kunt deze gebeurtenissen vervolgens filteren op basis van de tijd van de gebeurtenis, gebeurtenis-ID, DNS-server of taak categorie. De gegevens kunnen u helpen bij het controleren van wijzigingen die zijn aangebracht aan specifieke DNS-servers op specifieke tijdstippen.
- **Analytisch logboek van DNS**. Geeft alle analyse gebeurtenissen weer op alle DNS-servers die worden beheerd door de oplossing. U kunt deze gebeurtenissen vervolgens filteren op basis van de tijd van de gebeurtenis, gebeurtenis-ID, DNS-server, client-IP die de zoek query heeft uitgevoerd, en taak categorie query type. Met de analyse gebeurtenissen van DNS-server kunnen activiteiten worden bijgehouden op de DNS-server. Elke keer dat de server DNS-gegevens verzendt of ontvangt, wordt een analytische gebeurtenis vastgelegd.

### <a name="search-by-using-dns-analytics-log-search"></a>Zoeken met behulp van DNS-analyse zoeken in Logboeken

Op de pagina zoeken in Logboeken kunt u een query maken. U kunt de zoek resultaten filteren met behulp van facet besturings elementen. U kunt ook geavanceerde query's maken om uw resultaten te transformeren, te filteren en te rapporteren. Begin met de volgende query's:

1. Typ in het **vak Zoek opdracht** `DnsEvents` om alle DNS-gebeurtenissen weer te geven die zijn gegenereerd door de DNS-servers die worden beheerd door de oplossing. De resultaten lijst de logboek gegevens voor alle gebeurtenissen met betrekking tot opzoek query's, dynamische registraties en configuratie wijzigingen.

    ![Zoeken in DnsEvents-logboek](./media/dns-analytics/log-search-dnsevents.png)  

    a. Als u de logboek gegevens voor opzoek query's wilt weer geven, selecteert u **LookUpQuery** als **subtype** filter in het facet besturings element aan de linkerkant. Er wordt een tabel weer gegeven met alle opzoek query gebeurtenissen voor de geselecteerde tijds periode.

    b. Als u de logboek gegevens voor dynamische registraties wilt weer geven, selecteert u **DynamicRegistration** als **subtype** filter in het facet besturings element aan de linkerkant. Een tabel met een lijst met alle dynamische registratie gebeurtenissen voor de geselecteerde tijds periode wordt weer gegeven.

    c. Als u de logboek gegevens voor configuratie wijzigingen wilt weer geven, selecteert u **ConfigurationChange** als **subtype** filter in het facet besturings element aan de linkerkant. Een tabel met een lijst met alle configuratie wijzigings gebeurtenissen voor de geselecteerde tijds periode wordt weer gegeven.

1. Typ in het **vak Zoek opdracht** `DnsInventory` om alle DNS-inventaris gegevens weer te geven voor de DNS-servers die worden beheerd door de oplossing. De resultaten lijst met de logboek gegevens voor DNS-servers, DNS-zones en bron records.

    ![Zoeken in DnsInventory-logboek](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Problemen oplossen

Veelvoorkomende stappen voor probleem oplossing:

1. Ontbrekende DNS-Zoek gegevens: als u dit probleem wilt oplossen, probeert u het configuratie bestand opnieuw in te stellen of zojuist de configuratie pagina in de portal te laden. Als u de instelling opnieuw wilt instellen, wijzigt u een instelling in een andere waarde, wijzigt u deze in de oorspronkelijke waarde en slaat u de configuratie op.

## <a name="suggestions"></a>Suggesties

Als u feedback wilt geven, gaat u naar de [pagina log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) om ideeën te plaatsen voor DNS-analyse functies waarmee u kunt werken. 

## <a name="next-steps"></a>Volgende stappen

[Query logboeken](../log-query/log-query-overview.md) om gedetailleerde DNS-logboek records weer te geven.
