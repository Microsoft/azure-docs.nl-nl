---
title: Azure Backup-rapporten configureren
description: Rapporten configureren en weergeven voor Azure Backup met behulp van Log Analytics en Azure-werkmappen
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 0f3638e7649fc02f050c575ee621ce9dc237c24f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517263"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten over back-ups op basis van gegevens die een lange periode bespannen. Gebruiksgevallen voor een dergelijke oplossing zijn onder andere:

- Het toewijzen en voorspellen van de verbruikte cloudopslag.
- Controle van back-ups en herstel.
- Belangrijke trends identificeren op verschillende granulariteitsniveaus.

Momenteel biedt Azure Backup een rapportageoplossing die gebruikmaakt van [Azure Monitor logboeken](../azure-monitor/logs/log-analytics-tutorial.md) en [Azure-werkmappen.](../azure-monitor/visualize/workbooks-overview.md) Deze resources helpen u uitgebreide inzichten te krijgen in uw back-ups in uw hele back-up. In dit artikel wordt uitgelegd hoe u rapporten kunt configureren Azure Backup weergeven.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

- Back-uprapporten worden ondersteund voor Azure-VM's, SQL in Azure-VM's, SAP HANA in Azure-VM's, Microsoft Azure Recovery Services-agent (MARS), Microsoft Azure Backup Server (MABS) en System Center Data Protection Manager (DPM). Voor back-ups van Azure-bestands share worden gegevens weergegeven voor records die zijn gemaakt op of na 1 juni 2020.
- Voor back-ups van Azure-bestands share worden gegevens op beveiligde exemplaren weergegeven voor records die zijn gemaakt na 1 februari 2021 (standaard nul voor oudere records).
- Voor DPM-workloads worden back-uprapporten ondersteund voor DPM-versie 5.1.363.0 en hoger en agentversie 2.0.9127.0 en hoger.
- Voor MABS-workloads worden back-uprapporten ondersteund voor MABS-versie 13.0.415.0 en hoger en agentversie 2.0.9170.0 en hoger.
- Back-uprapporten kunnen worden bekeken voor alle back-upitems, kluizen, abonnementen en regio's zolang hun gegevens worden verzonden naar een Log Analytics-werkruimte waar de gebruiker toegang tot heeft. Als u rapporten voor een set kluizen wilt weergeven, hoeft u alleen lezerstoegang te hebben tot de Log Analytics-werkruimte waar de kluizen hun gegevens verzenden. U hoeft geen toegang te hebben tot de afzonderlijke kluizen.
- Als u een [](../lighthouse/index.yml) Azure Lighthouse bent met gedelegeerde toegang tot de abonnementen van uw klanten, kunt u deze rapporten gebruiken met Azure Lighthouse om rapporten over al uw tenants weer te geven.
- Momenteel kunnen gegevens worden bekeken in Back-uprapporten maximaal 100 Log Analytics-werkruimten (tussen tenants).
- Gegevens voor back-uptaken voor logboeken worden momenteel niet weergegeven in de rapporten.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Aan de slag

Volg deze stappen om aan de slag te gaan met de rapporten.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Een Log Analytics-werkruimte maken of een bestaande werkruimte gebruiken

Stel een of meer Log Analytics-werkruimten in om uw Back-uprapportagegegevens op te slaan. De locatie en het abonnement waar deze Log Analytics-werkruimte kan worden gemaakt, zijn onafhankelijk van de locatie en het abonnement waar uw kluizen zich bevinden.

Zie Een Log Analytics-werkruimte maken in de Azure Portal om een [Log Analytics-werkruimte in te Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)

Standaard worden de gegevens in een Log Analytics-werkruimte 30 dagen bewaard. Als u gegevens voor een langere periode wilt bekijken, wijzigt u de bewaarperiode van de Log Analytics-werkruimte. Zie Gebruik en kosten beheren met Azure Monitor logboeken om [de retentieperiode te wijzigen.](../azure-monitor/logs/manage-cost-storage.md)

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Diagnostische instellingen voor uw kluizen configureren

Azure Resource Manager resources, zoals Recovery Services-kluizen, registreren informatie over geplande bewerkingen en door de gebruiker geactiveerde bewerkingen als diagnostische gegevens.

Selecteer diagnostische instellingen in de  sectie Bewaking van uw Recovery Services-kluis en geef het doel op voor de diagnostische gegevens van de Recovery Services-kluis. Zie Diagnostische instellingen gebruiken voor Recovery Services-kluizen voor meer informatie over het gebruik van [diagnostische gebeurtenissen.](./backup-azure-diagnostic-events.md)

![Het deelvenster Diagnostische instellingen](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup biedt ook een ingebouwde definitie Azure Policy, waarmee de configuratie van diagnostische instellingen voor alle kluizen in een bepaald bereik wordt automatiseren. Zie Diagnostische instellingen voor kluizen configureren op schaal voor meer informatie over het [gebruik van dit beleid.](./azure-policy-configure-diagnostics.md)

> [!NOTE]
> Nadat u diagnostische gegevens hebt geconfigureerd, kan het tot 24 uur duren voordat de eerste gegevens-push is voltooid. Nadat de gegevens naar de Log Analytics-werkruimte stromen, ziet u mogelijk niet direct gegevens in de rapporten omdat de gegevens voor de huidige gedeeltelijke dag niet worden weergegeven in de rapporten. Zie Conventies die worden gebruikt [in Back-uprapporten voor meer informatie.](#conventions-used-in-backup-reports) We raden u aan om de rapporten twee dagen nadat u uw kluizen hebt geconfigureerd voor het verzenden van gegevens naar Log Analytics te bekijken.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Rapporten weergeven in de Azure Portal

Nadat u uw kluizen hebt geconfigureerd voor het verzenden van gegevens naar Log Analytics, bekijkt u uw Back-uprapporten door naar het deelvenster van een kluis te gaan en vervolgens Back-uprapporten **.**

![Kluisdashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecteer deze koppeling om de werkmap Back-uprapport te openen.

> [!NOTE]
>
> - Op dit moment kan het laden van het rapport maximaal 1 minuut duren.
> - De Recovery Services-kluis is slechts een ingangspunt voor Back-uprapporten. Nadat de werkmap Back-uprapport wordt geopend vanuit het deelvenster van een kluis, selecteert u de juiste set Log Analytics-werkruimten om gegevens te bekijken die zijn samengevoegd in al uw kluizen.

Het rapport bevat verschillende tabbladen:

##### <a name="summary"></a>Samenvatting

Gebruik dit tabblad om een overzicht op hoog niveau van uw back-up te krijgen. U kunt een kort overzicht krijgen van het totale aantal back-upitems, de totale verbruikte cloudopslag, het aantal beveiligde exemplaren en het slagingspercentage van de taak per workloadtype. Ga naar de desbetreffende tabbladen voor meer informatie over een specifiek type back-upartefact.

   ![Het tabblad Samenvatting](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Back-upitems

Gebruik dit tabblad om informatie en trends te bekijken over cloudopslag die wordt gebruikt op het niveau van back-upitem. Als u bijvoorbeeld SQL gebruikt in een back-up van een Azure-VM, kunt u zien welke cloudopslag wordt gebruikt voor elke SQL database van wie een back-up wordt gemaakt. U kunt er ook voor kiezen om gegevens te zien voor back-upitems met een bepaalde beveiligingsstatus. Als u bijvoorbeeld de tegel **Beveiliging** gestopt selecteert boven aan het tabblad, worden alle widgets eronder gefilterd om alleen gegevens weer te geven voor Back-upitems met de status Beveiliging gestopt.

   ![Tabblad Back-upitems](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Gebruik

Gebruik dit tabblad om de belangrijkste factureringsparameters voor uw back-ups weer te geven. De informatie op dit tabblad staat op het niveau van een factureringsentiteit (beveiligde container). Als er bijvoorbeeld een back-up wordt van een DPM-server naar Azure, kunt u de trend bekijken van beveiligde exemplaren en cloudopslag die worden gebruikt voor de DPM-server. En als u SQL gebruikt in Azure Backup of SAP HANA in Azure Backup, bevat dit tabblad informatie over gebruik op het niveau van de virtuele machine waarin deze databases zijn opgenomen.

   ![Tabblad Gebruik](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> Voor DPM-workloads zien gebruikers mogelijk een klein verschil (van 20 MB per DPM-server) tussen de gebruikswaarden die in  de rapporten worden weergegeven in vergelijking met de samengevoegde gebruikswaarde, zoals wordt weergegeven op het tabblad Overzicht van de Recovery Services-kluis. Dit verschil wordt veroorzaakt door het feit dat elke DPM-server die wordt geregistreerd voor back-up een gekoppelde gegevensbron met metagegevens heeft die niet wordt weergegeven als een artefact voor rapportage.

##### <a name="jobs"></a>Taken

Gebruik dit tabblad om langlopende trends voor taken weer te geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van een mislukte taak. U kunt deze informatie zowel op aggregatieniveau als op het niveau van back-upitem bekijken. Selecteer een bepaald back-upitem in een raster om gedetailleerde informatie weer te geven over elke taak die is geactiveerd op dat back-upitem in het geselecteerde tijdsbereik.

   ![Tabblad Taken](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Beleidsregels

Gebruik dit tabblad om informatie weer te geven over al uw actieve beleidsregels, zoals het aantal gekoppelde items en de totale cloudopslag die wordt verbruikt door items waar een back-up van is gemaakt onder een bepaald beleid. Selecteer een bepaald beleid om informatie weer te geven over elk van de bijbehorende back-upitems.

   ![Tabblad Beleid](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optimaliseren

Gebruik dit tabblad om inzicht te krijgen in mogelijke mogelijkheden voor kostenoptimalisatie voor uw back-ups. Hier volgen de scenario's waarvoor het tabblad Optimaliseren momenteel inzichten biedt:

###### <a name="inactive-resources"></a>Inactieve resources

Met deze weergave kunt u de back-upitems identificeren die gedurende een lange tijd geen back-up hebben gehad. Dit kan betekenen dat de onderliggende machine waar een back-up van wordt gemaakt, niet meer bestaat (wat leidt tot mislukte back-ups) of dat er een probleem is met de computer waardoor back-ups niet betrouwbaar kunnen worden gemaakt.

Als u inactieve resources wilt weergeven, gaat u naar het **tabblad** Optimaliseren en selecteert u de **tegel Inactieve resources.** Selecteer deze tegel geeft een raster weer met details van alle inactieve resources die in het geselecteerde bereik bestaan. Standaard worden in het raster items weergegeven die de afgelopen zeven dagen geen herstelpunt hebben. Als u inactieve resources voor een ander tijdsbereik wilt zoeken, kunt u het filter **Tijdsbereik** bovenaan het tabblad aanpassen.

Zodra u een inactieve resource hebt geïdentificeerd, kunt u het probleem verder onderzoeken door te navigeren naar het dashboard van het back-upitem of het deelvenster Azure-resources voor die resource (indien van toepassing). Afhankelijk van uw scenario kunt u ervoor kiezen om de back-up voor de machine te stoppen (als deze niet meer bestaat) en onnodige back-ups te verwijderen, waardoor kosten worden bespaard, of u kunt problemen in de machine oplossen om ervoor te zorgen dat back-ups betrouwbaar worden gemaakt.

![Tabblad Optimaliseren - Inactieve resources](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Back-upitems met een lange bewaarperiode

Met deze weergave kunt u items identificeren die back-ups hebben die langer worden bewaard dan door uw organisatie is vereist.

Als  u de tegel Beleidsoptimalisaties selecteert, gevolgd door de tegel Retentieoptimalisaties, wordt een raster weergegeven met alle back-upitems waarvoor de retentie van het dagelijkse, wekelijkse, maandelijkse of jaarlijkse RETENTIEPUNT (RP) groter is dan een opgegeven waarde.  Standaard worden in het raster alle back-upitems in het geselecteerde bereik weergegeven. U kunt de filters voor dagelijkse, wekelijkse, maandelijkse en jaarlijkse RP-retentie gebruiken om het raster verder te filteren en de items te identificeren waarvoor de retentie mogelijk kan worden verminderd om te besparen op de kosten voor back-upopslag.

Voor databaseworkloads zoals SQL en SAP HANA komen de bewaarperioden die in het raster worden weergegeven overeen met de bewaarperioden van de volledige back-uppunten en niet de differentiële back-uppunten. Hetzelfde geldt ook voor de retentiefilters.  

![Tabblad Optimaliseren - Optimalisaties voor retentie](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Databases geconfigureerd voor dagelijkse volledige back-up 

Met deze weergave kunt u databaseworkloads identificeren die zijn geconfigureerd voor dagelijkse volledige back-up. Het gebruik van dagelijkse differentiële back-ups en wekelijkse volledige back-ups is vaak rendabeler.

Als u de **tegel Beleidsoptimalisaties** selecteert, gevolgd door de tegel **Optimalisaties** van back-upschema's, wordt een raster weergegeven met alle databases met een dagelijks volledige back-upbeleid. U kunt ervoor kiezen om naar een bepaald back-upitem te navigeren en het beleid aan te passen voor het gebruik van dagelijkse differentiële back-up met wekelijkse volledige back-up.

Het  filter Type back-upbeheer boven aan het tabblad moet de items **SQL in Azure VM** en SAP HANA in **Azure VM** hebben geselecteerd, om databaseworkloads op de verwachte manier weer te geven in het raster.

![Tabblad Optimaliseren - Optimalisaties van back-upschema's](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Naleving van het beleid

Op dit tabblad kunt u bepalen of al uw back-up-exemplaren elke dag ten minste één geslaagde back-up hebben gehad. Voor items met wekelijks back-upbeleid kunt u dit tabblad gebruiken om te bepalen of alle back-up-exemplaren ten minste één geslaagde back-up per week hebben gehad.

Er zijn twee soorten weergaven voor naleving van het beleid beschikbaar:

* **Nalevingsbeleid per tijdsperiode:** met deze weergave kunt u bepalen hoeveel items op een bepaalde dag ten minste één geslaagde back-up hebben gehad en hoeveel er op die dag geen geslaagde back-up hebben gehad. U kunt op een rij klikken om details weer te geven van alle back-uptaken die op de geselecteerde dag zijn geactiveerd. Als u het tijdsbereik verhoogt tot een grotere waarde, zoals de afgelopen 60 dagen, wordt het raster weergegeven in de wekelijkse weergave en wordt het aantal items weergegeven met ten minste één geslaagde back-up op elke dag in de opgegeven week. Op dezelfde manier is er een maandelijkse weergave voor grotere tijdsbereiken.

In het geval van items waar wekelijks een back-up van wordt gemaakt, kunt u met dit raster alle items identificeren die ten minste één geslaagde back-up in de opgegeven week hebben gehad. Voor een groter tijdsbereik, zoals de afgelopen 120 dagen, wordt het raster weergegeven in de maandelijkse weergave en wordt het aantal items weergegeven met ten minste één geslaagde back-up per week in de opgegeven maand. Raadpleeg [Conventies die worden gebruikt in Back-uprapporten](#conventions-used-in-backup-reports) voor meer informatie over dagelijkse, wekelijkse en maandelijkse weergaven.

![Naleving van het beleid op tijdsperiode](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* **Naleving van het beleid per back-up-exemplaar:** met behulp van deze weergave kunt u nalevingsdetails op het niveau van een back-up-exemplaar beleid maken. Een cel die groen is, geeft aan dat het back-up-exemplaar ten minste één geslaagde back-up had op de opgegeven dag. Een cel die rood is, geeft aan dat het back-up-exemplaar niet eens één geslaagde back-up op de opgegeven dag heeft. Dagelijkse, wekelijkse en maandelijkse aggregaties volgen hetzelfde gedrag als de weergave Naleving van beleid per periode. U kunt op een rij klikken om alle back-uptaken op het opgegeven back-upin exemplaar in het geselecteerde tijdsbereik weer te geven.

![Naleving van het beleid door back-up-exemplaar](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>E-Azure Backup mailrapporten

Met de **functie E-mailrapport** die beschikbaar is in Back-uprapporten, kunt u geautomatiseerde taken maken om periodieke rapporten te ontvangen via e-mail. Deze functie werkt door een logische app te implementeren in uw Azure-omgeving die gegevens opvraagt uit uw geselecteerde Log Analytics-werkruimten (LA), op basis van de invoer die u opvraagt.

Zodra de logische app is gemaakt, moet u verbindingen met de logboeken Azure Monitor Office 365 autoreren. Hiervoor gaat u naar **Logic Apps** in de Azure Portal zoek naar de naam van de taak die u hebt gemaakt. Als u **het menu-item API-verbindingen** selecteert, wordt de lijst met API-verbindingen geopend die u moet autoreren. [Meer informatie over het configureren van e-mailberichten en het oplossen van problemen](backup-reports-email.md).

###### <a name="customize-azure-backup-reports"></a>Rapporten Azure Backup aanpassen

Back-uprapporten gebruikt [systeemfuncties in Azure Monitor logboeken.](backup-reports-system-functions.md) Deze functies worden gebruikt voor gegevens in de onbewerkte Azure Backup tabellen in LA en retourneren opgemaakte gegevens waarmee u eenvoudig informatie over al uw back-upgerelateerde entiteiten kunt ophalen met behulp van eenvoudige query's. 

Als u uw eigen rapportwerkboeken wilt maken met behulp van Back-uprapporten als  basis, gaat u naar Back-uprapporten, klikt u boven aan het rapport op Bewerken en bekijkt/bewerkt u de query's die in de rapporten worden gebruikt. Raadpleeg de [documentatie voor Azure-werkmappen](../azure-monitor/visualize/workbooks-overview.md) voor meer informatie over het maken van aangepaste rapporten. 

## <a name="export-to-excel"></a>Exporteren naar Excel

Selecteer de pijl-omlaag in de rechterbovenhoek van een widget, zoals een tabel of grafiek, om de inhoud van die widget te exporteren als een Excel-werkblad waarin bestaande filters zijn toegepast. Als u meer rijen van een tabel naar Excel wilt exporteren, kunt u het aantal rijen dat op de pagina wordt weergegeven, verhogen met behulp van de vervolgkeuzepijl Rijen **per** pagina boven aan elk raster.

## <a name="pin-to-dashboard"></a>Vastmaken aan dashboard

Selecteer de knop Vastmaken bovenaan elke widget om de widget vast te maken aan Azure Portal dashboard. Met deze functie kunt u aangepaste dashboards maken die zijn afgestemd op het weergeven van de belangrijkste informatie die u nodig hebt.

## <a name="cross-tenant-reports"></a>Rapporten voor meerdere tenants

Als u [Azure Lighthouse](../lighthouse/index.yml) gedelegeerde toegang tot abonnementen in meerdere tenantomgevingen gebruikt, kunt u het standaardabonnementsfilter gebruiken. Selecteer de filterknop in de rechterbovenhoek van de Azure Portal om alle abonnementen te kiezen waarvoor u gegevens wilt bekijken. Als u dit doet, kunt u Log Analytics-werkruimten in uw tenants selecteren om rapporten met meerdere tenants weer te geven.

## <a name="conventions-used-in-backup-reports"></a>Conventies die worden gebruikt in Back-uprapporten

- Filters werken op elk tabblad van links naar rechts en van boven naar beneden. Dat wil zeggen dat een filter alleen van toepassing is op alle widgets die rechts van dat filter of onder dat filter zijn geplaatst.
- Als u een gekleurde tegel selecteert, worden de widgets onder de tegel gefilterd op records die betrekking hebben op de waarde van die tegel. Als u bijvoorbeeld de tegel  **Beveiliging gestopt** selecteert op het tabblad Back-upitems, worden de rasters en grafieken hieronder gefilterd om gegevens voor back-upitems weer te geven met de status Beveiliging gestopt.
- Tegels die niet gekleurd zijn, kunnen niet worden geselecteerd.
- Gegevens voor de huidige gedeeltelijke dag worden niet weergegeven in de rapporten. Dus wanneer de geselecteerde waarde van **Tijdsbereik** Afgelopen **7 dagen** is, toont het rapport records voor de afgelopen zeven voltooide dagen. De huidige dag is niet inbegrepen.
- Het rapport bevat details van taken (behalve logboektaken) die zijn *geactiveerd* in het geselecteerde tijdsbereik.
- De waarden voor **Cloud Storage en** Protected **Instances staan** aan het *einde* van het geselecteerde tijdsbereik.
- De back-upitems die in de rapporten worden weergegeven, zijn de items die bestaan aan *het einde* van het geselecteerde tijdsbereik. Back-upitems die zijn verwijderd in het midden van het geselecteerde tijdsbereik worden niet weergegeven. Dezelfde conventie geldt ook voor Back-upbeleid.
- Als het geselecteerde tijdsbereik een periode van 30 dagen minder omvat, worden grafieken weergegeven in de dagelijkse weergave, waarbij er één gegevenspunt voor elke dag is. Als het tijdsbereik een periode van meer dan 30 dagen en minder dan (of gelijk aan) 90 dagen omvat, worden grafieken weergegeven in de wekelijkse weergave. Voor grotere tijdsbereiken worden grafieken weergegeven in de maandelijkse weergave. Het wekelijks of maandelijks samenvoegen van gegevens zorgt voor betere prestaties van query's en een eenvoudigere leesbaarheid van gegevens in grafieken.
- De rasters voor naleving van het beleid volgen ook een vergelijkbare aggregatielogica zoals hierboven wordt beschreven. Er zijn echter enkele kleine verschillen. Het eerste verschil is dat er voor items met een wekelijks back-upbeleid geen dagelijkse weergave is (alleen wekelijkse en maandelijkse weergaven zijn beschikbaar). Bovendien wordt een maand in de rasters voor items met een wekelijks back-upbeleid beschouwd als een periode van 4 weken (28 dagen) en niet als 30 dagen, om gedeeltelijke weken uit overweging te elimineren.

## <a name="query-load-times"></a>Laadtijden van query's

De widgets in het back-uprapport zijn powered by Kusto-query's die worden uitgevoerd op de Log Analytics-werkruimten van de gebruiker. Deze query's omvatten doorgaans de verwerking van grote hoeveelheden gegevens, met meerdere joins om uitgebreidere inzichten mogelijk te maken. Als gevolg hiervan worden de widgets mogelijk niet onmiddellijk geladen wanneer de gebruiker rapporten over een grote back-upweergave bekijkt. Deze tabel bevat een ruwe schatting van de tijd die verschillende widgets kunnen hebben om te laden, op basis van het aantal back-upitems en het tijdsbereik waarvoor het rapport wordt bekeken.

| **Aantal gegevensbronnen**                         | **Tijdperiode** | **Laadtijden bij benadering**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5.000                       | 1 maand          | Tegels: 5-10 sec. <br> Rasters: 5-10 sec. <br> Grafieken: 5-10 sec. <br> Filters op rapportniveau: 5-10 sec.|
| ~5.000                       | 3 maanden          | Tegels: 5-10 sec. <br> Rasters: 5-10 sec. <br> Grafieken: 5-10 sec. <br> Filters op rapportniveau: 5-10 sec.|
| ~10.000                       | 3 maanden          | Tegels: 15-20 sec. <br> Rasters: 15-20 sec. <br> Grafieken: 1-2 minuten <br> Filters op rapportniveau: 25-30 sec.|
| ~15.000                       | 1 maand          | Tegels: 15-20 sec. <br> Rasters: 15-20 sec. <br> Grafieken: 50-60 sec. <br> Filters op rapportniveau: 20-25 sec.|
| ~15.000                       | 3 maanden          | Tegels: 20-30 sec. <br> Rasters: 20-30 sec. <br> Grafieken: 2-3 minuten <br> Filters op rapportniveau: 50-60 sec. |

## <a name="what-happened-to-the-power-bi-reports"></a> Wat is er gebeurd met de Power BI-rapporten? 

- De eerdere Power BI-app voor rapportage, die gegevens uit een Azure-opslagaccount heeft afkomstig, is op een afschaffingspad. We raden u aan om diagnostische gegevens van de kluis naar Log Analytics te verzenden om rapporten weer te geven.

- Bovendien is het [V1-schema voor](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) het verzenden van diagnostische gegevens naar een opslagaccount of een LA-werkruimte ook op een afschaffingspad. Dit betekent dat als u aangepaste query's of automatiseringen hebt geschreven op basis van het V1-schema, u wordt aangeraden deze query's bij te werken om het momenteel ondersteunde V2-schema te gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over bewaking en rapportage met Azure Backup](./backup-azure-monitor-alert-faq.yml)