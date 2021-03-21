---
title: Azure Backup-rapporten configureren
description: Rapporten voor Azure Backup configureren en weer geven met behulp van Log Analytics en Azure-werkmappen
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e9f3d9dfa33e71d827a338258001f2b52af62b06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509358"
---
# <a name="configure-azure-backup-reports"></a>Azure Backup-rapporten configureren

Een veelvoorkomende vereiste voor back-upbeheerders is het verkrijgen van inzichten over back-ups op basis van gegevens die een lange periode bevallen. Voor deze oplossing gelden de volgende cases:

- Het toewijzen en voors pellen van verbruikte Cloud opslag.
- Controle van back-ups en herstel bewerkingen.
- De belangrijkste trends op verschillende niveaus nauw keurig te identificeren.

Momenteel biedt Azure Backup een rapportage oplossing die gebruikmaakt van [Azure monitor-logboeken](../azure-monitor/logs/log-analytics-tutorial.md) en [Azure-werkmappen](../azure-monitor/visualize/workbooks-overview.md). Met deze resources krijgt u inzicht in uw back-ups over uw hele back-ups. In dit artikel wordt uitgelegd hoe u Azure Backup-rapporten configureert en weergeeft.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

- Back-uprapporten worden ondersteund voor virtuele Azure-machines, SQL in azure Vm's, SAP HANA in azure Vm's, Microsoft Azure Recovery Services (MARS) agent, Microsoft Azure Backup Server (MABS) en System Center Data Protection Manager (DPM). Voor Azure file share backup worden gegevens weer gegeven voor records die zijn gemaakt op of na 1 juni 2020.
- Voor Azure file share backup worden gegevens over beveiligde instanties weer gegeven voor records die zijn gemaakt na 1 februari, 2021 (standaard ingesteld op nul voor oudere records).
- Voor DPM-workloads worden back-uprapporten ondersteund voor de DPM-versie 5.1.363.0 en hoger en de agent versie 2.0.9127.0 en hoger.
- Voor MABS-werk belastingen worden back-uprapporten ondersteund voor de MABS-versie 13.0.415.0 en hoger en de agent versie 2.0.9170.0 en hoger.
- Back-uprapporten kunnen worden weer gegeven voor alle back-upitems, kluizen, abonnementen en regio's, zolang hun gegevens worden verzonden naar een Log Analytics werk ruimte waartoe de gebruiker toegang heeft. Als u rapporten voor een set kluizen wilt weer geven, hoeft u alleen lezers toegang te hebben tot de Log Analytics werk ruimte waarnaar de kluizen hun gegevens verzenden. U hebt geen toegang tot de afzonderlijke kluizen.
- Als u een [Azure Lighthouse](../lighthouse/index.yml) -gebruiker bent met gedelegeerde toegang tot de abonnementen van uw klanten, kunt u deze rapporten gebruiken met Azure Lighthouse om rapporten weer te geven over al uw tenants.
- Op dit moment kunnen gegevens worden weer gegeven in back-uprapporten over Maxi maal 100 Log Analytics werk ruimten (tussen tenants).
- Gegevens voor logboek back-uptaken worden momenteel niet weer gegeven in de rapporten.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="get-started"></a>Aan de slag

Volg deze stappen om de rapporten te gaan gebruiken.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. een Log Analytics-werk ruimte maken of een bestaande gebruiken

Stel een of meer Log Analytics-werk ruimten in om de gegevens van uw back-uprapport op te slaan. De locatie en het abonnement waar deze Log Analytics werk ruimte kunnen worden gemaakt, zijn onafhankelijk van de locatie en het abonnement waar uw kluizen bestaan.

Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md)om een log Analytics-werk ruimte in te stellen.

De gegevens in een Log Analytics-werk ruimte worden standaard 30 dagen bewaard. Als u gegevens voor een langere periode wilt zien, wijzigt u de Bewaar periode van de Log Analytics werk ruimte. Als u de Bewaar periode wilt wijzigen, raadpleegt u [gebruik en kosten beheren met Azure monitor-logboeken](../azure-monitor/logs/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configureer de diagnostische instellingen voor uw kluizen

Azure Resource Manager resources, zoals Recovery Services kluizen, registreert u informatie over geplande bewerkingen en door de gebruiker geactiveerde bewerkingen als diagnostische gegevens.

Selecteer in de sectie bewaking van uw Recovery Services kluis **Diagnostische instellingen** en geef het doel op voor de diagnostische gegevens van de Recovery Services kluis. Zie [Diagnostische instellingen voor Recovery Services kluizen gebruiken voor](./backup-azure-diagnostic-events.md)meer informatie over het gebruik van diagnostische gebeurtenissen.

![Het deelvenster Diagnostische instellingen](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

Azure Backup biedt ook een ingebouwde Azure Policy definitie, waarmee de configuratie van diagnostische instellingen voor alle kluizen in een bepaald bereik wordt geautomatiseerd. Zie voor meer informatie over het gebruik van dit beleid de [instellingen voor diagnostische gegevens van de kluis configureren op schaal](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Nadat u de diagnostische gegevens hebt geconfigureerd, kan het tot 24 uur duren voordat de eerste push bewerking is voltooid. Nadat de gegevens in de werk ruimte Log Analytics zijn gestart, worden de gegevens in de rapporten mogelijk niet onmiddellijk weer gegeven omdat de gegevens voor de huidige gedeeltelijke dag niet in de rapporten zijn opgenomen. Zie [conventies die worden gebruikt in back-uprapporten](#conventions-used-in-backup-reports)voor meer informatie. We raden u aan om de rapporten twee dagen na het configureren van uw kluizen te bekijken om gegevens naar Log Analytics te verzenden.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. rapporten weer geven in de Azure Portal

Nadat u uw kluizen hebt geconfigureerd om gegevens naar Log Analytics te verzenden, kunt u de back-uprapporten bekijken door naar het deel venster van de kluis te gaan en **back-uprapporten** te selecteren.

![Kluis dashboard](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecteer deze koppeling om de werkmap van het back-uprapport te openen.

> [!NOTE]
>
> - De aanvankelijke belasting van het rapport kan tot wel 1 minuut duren.
> - De Recovery Services kluis is slechts een ingangs punt voor back-uprapporten. Nadat de werkmap van het back-uprapport wordt geopend vanuit een kluis deel venster, selecteert u de juiste set Log Analytics-werk ruimten om gegevens te bekijken die zijn samengevoegd in al uw kluizen.

Het rapport bevat verschillende tabbladen:

##### <a name="summary"></a>Samenvatting

Gebruik dit tabblad om een overzicht te krijgen van uw back-ups op hoog niveau. U kunt een kort overzicht krijgen van het totale aantal back-upitems, het totale verbruikte Cloud opslag, het aantal beveiligde instanties en het percentage van de taak geslaagd per type werk belasting. Voor meer informatie over een specifiek type back-upartefact gaat u naar de desbetreffende tabbladen.

   ![Het tabblad Samenvatting](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Back-upitems

Gebruik dit tabblad om informatie en trends te bekijken over Cloud opslag die op een back-upitemniveau wordt verbruikt. Als u bijvoorbeeld SQL gebruikt in een back-up van een Azure-VM, ziet u de gebruikte Cloud opslag voor elke SQL database waarvan een back-up wordt gemaakt. U kunt er ook voor kiezen om gegevens te bekijken voor back-upitems van een bepaalde beveiligings status. Als u bijvoorbeeld de tegel **beveiliging gestopt** selecteert boven aan het tabblad, filtert u alle onderliggende objecten zodanig dat alleen gegevens worden weer gegeven voor back-upitems met de status gestopt voor beveiliging.

   ![Tabblad Back-upitems](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Gebruik

Gebruik dit tabblad om de para meters voor de sleutel facturering voor uw back-ups weer te geven. De informatie die op dit tabblad wordt weer gegeven, bevindt zich op het niveau van een facturerings entiteit (beveiligde container). Als er bijvoorbeeld een back-up van een DPM-server wordt gemaakt naar Azure, kunt u de trend van beveiligde exemplaren en Cloud opslag weer geven die zijn gebruikt voor de DPM-server. Op dezelfde manier kunt u, als u SQL gebruikt in Azure Backup of SAP HANA in Azure Backup, op dit tabblad informatie over het gebruik weer geven op het niveau van de virtuele machine waarin deze data bases zich bevinden.

   ![Tabblad gebruik](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> Voor DPM-werk belastingen zien gebruikers mogelijk een enigszins verschil (van de volg orde van 20 MB per DPM-server) tussen de gebruiks waarden die worden weer gegeven in de rapporten, vergeleken met de waarde voor geaggregeerde gebruik zoals weer gegeven op het tabblad Recovery Services kluis **overzicht** . Dit verschil wordt verwerkt door het feit dat elke DPM-server die voor back-up wordt geregistreerd, een gekoppelde meta gegevens bron heeft die niet wordt weer gegeven als een artefact voor rapportage.

##### <a name="jobs"></a>Taken

Gebruik dit tabblad om langlopende trends voor taken weer te geven, zoals het aantal mislukte taken per dag en de belangrijkste oorzaken van een mislukte taak. U kunt deze informatie zowel op het niveau van een geaggregeerde als op een back-upitem weer geven. Selecteer een bepaald back-upitem in een raster om gedetailleerde informatie weer te geven over elke taak die is geactiveerd op het back-upitem in het geselecteerde tijds bereik.

   ![Tabblad taken](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Beleidsregels

Gebruik dit tabblad om informatie weer te geven over al uw actieve beleids regels, zoals het aantal gekoppelde items en de totale opslag ruimte in de cloud die wordt gebruikt door items waarvan een back-up is gemaakt onder een bepaald beleid. Selecteer een bepaald beleid om informatie weer te geven over elk van de bijbehorende back-upitems.

   ![Tabblad beleid](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Optimaliseren

Gebruik dit tabblad om inzicht te krijgen in potentiële mogelijkheden voor kosten optimalisatie voor uw back-ups. Hieronder vindt u de scenario's waarvoor het tabblad Optimize momenteel inzichten bevat:

###### <a name="inactive-resources"></a>Inactieve resources

In deze weer gave kunt u de back-upitems identificeren waarvan de back-up gedurende een aanzienlijk tijds duur niet is geslaagd. Dit kan betekenen dat de onderliggende computer waarvan een back-up wordt gemaakt, niet meer bestaat (en dus wat resulteert in mislukte back-ups), of dat er een probleem is met de computer waardoor back-ups niet betrouwbaar kunnen worden uitgevoerd.

Als u inactieve resources wilt weer geven, gaat u naar het tabblad **optimaliseren** en selecteert u de tegel **inactieve resources** . Selecteer deze tegel bevat een raster met details van alle inactieve resources die in het geselecteerde bereik bestaan. Het raster toont standaard items die geen herstel punt hebben in de afgelopen zeven dagen. Als u inactieve resources wilt zoeken voor een ander tijds bereik, kunt u het filter **tijd bereik** aanpassen boven aan het tabblad.

Als u een inactieve resource hebt geïdentificeerd, kunt u het probleem verder onderzoeken door te navigeren naar het dash board back-upitem of het deel venster Azure-resource voor die resource (waar van toepassing). Afhankelijk van uw scenario kunt u ervoor kiezen om de back-up voor de machine te stoppen (als deze niet meer bestaat) en overbodige back-ups te verwijderen, waarmee kosten worden bespaard, of u kunt problemen op de machine oplossen om ervoor te zorgen dat back-ups betrouwbaar worden gemaakt.

![Tabblad optimaliseren: inactieve resources](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Back-upitems met een lange bewaarperiode

In deze weer gave kunt u de items identificeren die back-ups gedurende een langere duur hebben bewaard dan is vereist door uw organisatie.

Als u de tegel **beleids optimalisatie** selecteert, gevolgd door de tegel **optimalisaties voor retentie** , wordt een raster weer gegeven met alle back-upitems waarvoor het dagelijks, wekelijks, maandelijks of jaarlijkse bewaar punt (RP) groter is dan een opgegeven waarde. Standaard worden in het raster alle back-upitems in de geselecteerde scope weer gegeven. U kunt de filters voor dagelijkse, wekelijkse, maandelijkse en jaarlijkse RP-retentie gebruiken om het raster verder te filteren en de items te identificeren waarvoor bewaren mogelijk kan worden gereduceerd om de opslag kosten voor back-ups op te slaan.

Voor database werkbelastingen, zoals SQL en SAP HANA, komen de Bewaar perioden die worden weer gegeven in het raster overeen met de Bewaar perioden van de volledige back-uppunten en niet van de differentiële back-uppunten. Dit geldt ook voor de retentie filters.  

![Optimalisaties voor tabblad behoud optimaliseren](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Databases geconfigureerd voor dagelijkse volledige back-up 

In deze weer gave kunt u de data base-workloads identificeren die zijn geconfigureerd voor dagelijkse volledige back-up. Vaak is het gebruik van dagelijkse differentiële back-ups en wekelijkse volledige back-ups rendabeler.

Als u de tegel **beleids optimalisatie** selecteert, gevolgd door de tegel **optimalisatie van back-upschema** , wordt een raster met alle data bases met een dagelijks volledig back-upbeleid weer gegeven. U kunt ervoor kiezen om naar een bepaald back-upitem te gaan en het beleid te wijzigen voor het gebruik van dagelijkse differentiële back-ups met een wekelijkse volledige back-up.

Het filter **type back-upbeheer** boven aan het tabblad moet de items **SQL hebben in azure VM** en **SAP Hana in azure VM** geselecteerd, zodat het raster de data bases op de verwachte manier kan weer geven.

![Optimalisatie van het tabblad optimaliseren](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

###### <a name="policy-adherence"></a>Inachtneming van beleid

Op dit tabblad kunt u aangeven of al uw back-upinstanties elke dag minstens één geslaagde back-up hebben. Voor items met een wekelijks back-upbeleid kunt u dit tabblad gebruiken om te bepalen of alle back-upinstanties een week met een geslaagde back-up hebben gehad.

Er zijn twee soorten beleids beschik bare weer gaven beschikbaar:

* **Inachtneming van het beleid voor de tijds periode**: in deze weer gave kunt u bepalen hoeveel items ten minste één geslaagde back-up hebben gehad in een bepaalde dag en hoeveel er in die dag geen geslaagde back-up is gemaakt. U kunt klikken op een rij om details weer te geven van alle back-uptaken die op de geselecteerde dag zijn geactiveerd. Houd er rekening mee dat als u het tijds bereik verhoogt naar een grotere waarde, zoals de laatste 60 dagen, het raster wordt weer gegeven in een wekelijkse weer gave en wordt het aantal items weer gegeven met ten minste één geslaagde back-up op elke dag in de opgegeven week. Op dezelfde manier is er een maandelijkse weer gave voor grotere Peri Oden.

In het geval van items die wekelijks worden opgeslagen, kunt u met dit raster alle items identificeren waarvoor ten minste één geslaagde back-up in de opgegeven week. Voor een groter tijds bereik, zoals de laatste 120 dagen, wordt het raster weer gegeven in de maand weergave en wordt het aantal items weer gegeven met ten minste één geslaagde back-up in elke week in de opgegeven maand. Raadpleeg [conventies die in back-uprapporten worden gebruikt](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) voor meer informatie over dagelijkse, wekelijkse en maandelijkse weer gaven.

![Inachtneming van het beleid voor de tijds periode](./media/backup-azure-configure-backup-reports/policy-adherence-by-time-period.png)

* Beleid dat wordt gebruikt **in de back-upinstantie**: in deze weer gave kunt u op het niveau van de back-upinstantie beleid nakijken over de details. Een cel die groen is, geeft aan dat het back-upexemplaar ten minste één geslaagde back-up op de opgegeven dag had. Een cel die rood is, geeft aan dat het back-upexemplaar nog niet een geslaagde back-up heeft gemaakt op de opgegeven dag. Dagelijkse, wekelijkse en maandelijkse aggregaties volgen hetzelfde gedrag als het beleid dat wordt bepaald door de weer gave van de tijds periode. U kunt klikken op een wille keurige rij om alle back-uptaken op het opgegeven back-upexemplaar in het geselecteerde tijds bereik weer te geven.

![Beleid van toepassing op back-upexemplaar](./media/backup-azure-configure-backup-reports/policy-adherence-by-backup-instance.png)

###### <a name="email-azure-backup-reports"></a>E-mail Azure Backup rapporten

Met de functie voor **e-mail** rapporten die beschikbaar is in back-uprapporten, kunt u geautomatiseerde taken maken voor het ontvangen van periodieke rapporten via e-mail. Deze functie werkt door een logische app te implementeren in uw Azure-omgeving die gegevens opvraagt van uw geselecteerde Log Analytics (LA)-werk ruimten, op basis van de invoer die u opgeeft.

Zodra de logische app is gemaakt, moet u verbindingen met Azure Monitor-logboeken en Office 365 autoriseren. Als u dit wilt doen, gaat u naar **Logic apps** in de Azure Portal en zoekt u naar de naam van de taak die u hebt gemaakt. Als u het menu-item **API-verbindingen** selecteert, wordt de lijst met API-verbindingen weer gegeven die u moet autoriseren. Meer [informatie over het configureren van e-mail berichten en het oplossen van problemen](backup-reports-email.md).

###### <a name="customize-azure-backup-reports"></a>Azure Backup-rapporten aanpassen

Back-uprapporten gebruiken [systeem functies in azure monitor logboeken](backup-reports-system-functions.md). Deze functies werken op gegevens in de onbewerkte Azure Backup tabellen in LA en retour neren gegevens die u helpen bij het ophalen van gegevens van al uw back-upentiteiten, met behulp van eenvoudige query's. 

Als u uw eigen rapport werkmappen wilt maken met behulp van back-uprapporten als basis, kunt u naar back-uprapporten gaan, op **bewerken** boven aan het rapport klikken en de query's weer geven/bewerken die in de rapporten worden gebruikt. Raadpleeg de [documentatie van Azure Workbooks](https://docs.microsoft.com/azure/azure-monitor/visualize/workbooks-overview) voor meer informatie over het maken van aangepaste rapporten. 

## <a name="export-to-excel"></a>Exporteren naar Excel

Selecteer de pijl-omlaag in de rechter bovenhoek van een wille keurige widget, zoals een tabel of grafiek, om de inhoud van die widget te exporteren als een Excel-werk blad, waarbij bestaande filters worden toegepast. Als u meer rijen van een tabel wilt exporteren naar Excel, kunt u het aantal rijen dat wordt weer gegeven op de pagina verhogen met behulp van de vervolg keuze pijl voor **rijen per pagina** boven aan elk raster.

## <a name="pin-to-dashboard"></a>Vastmaken aan dashboard

Selecteer de knop vastmaken aan de bovenkant van elke widget om de widget aan uw Azure Portal dash board vast te maken. Deze functie helpt u bij het maken van aangepaste Dash boards die zijn aangepast om de belangrijkste informatie die u nodig hebt, weer te geven.

## <a name="cross-tenant-reports"></a>Rapporten over meerdere tenants

Als u [Azure Lighthouse](../lighthouse/index.yml) gebruikt met gedelegeerde toegang tot abonnementen in meerdere Tenant omgevingen, kunt u het standaard abonnements filter gebruiken. Selecteer de filter knop in de rechter bovenhoek van de Azure Portal om alle abonnementen te kiezen waarvoor u gegevens wilt weer geven. Als u dit doet, kunt u Log Analytics werk ruimten in uw tenants selecteren om multi tenant rapporten weer te geven.

## <a name="conventions-used-in-backup-reports"></a>Conventies die in back-uprapporten worden gebruikt

- Filters werken van links naar rechts en van boven naar beneden op elk tabblad. Dat wil zeggen dat alle filters alleen van toepassing zijn op alle widgets die aan de rechter kant van het filter of onder dat filter worden geplaatst.
- Als u een gekleurde tegel selecteert, worden de objecten onder de tegel gefilterd op records die betrekking hebben op de waarde van die tegel. Als u bijvoorbeeld de tegel **beveiliging gestopt** selecteert op het tabblad **Back-upitems** , worden de rasters en de onderstaande grafieken gefilterd om gegevens weer te geven voor back-upitems met de status gestopt voor beveiliging.
- Tegels die niet zijn gekleurd, zijn niet te selecteren.
- De gegevens voor de huidige gedeeltelijke dag worden niet weer gegeven in de rapporten. Dus als de geselecteerde waarde van het **tijds bereik** de **laatste 7 dagen** is, worden in het rapport records weer gegeven voor de afgelopen zeven voltooide dagen. De huidige dag is niet opgenomen.
- Het rapport bevat details van taken (naast logboek taken) die zijn *geactiveerd* in het geselecteerde tijds bereik.
- De waarden die worden weer gegeven voor **Cloud opslag** en **beveiligde instanties** bevinden zich aan het *einde* van het geselecteerde tijds bereik.
- De back-upitems die in de rapporten worden weer gegeven, zijn de items die aan het *einde* van het geselecteerde tijds bereik bestaan. Back-upitems die in het midden van het geselecteerde tijds bereik zijn verwijderd, worden niet weer gegeven. Dezelfde Conventie geldt ook voor back-upbeleid.
- Als het geselecteerde tijds bereik een periode van 30 dagen minder bevat, worden grafieken weer gegeven in de dagelijkse weer gave, waarbij één gegevens punt voor elke dag is. Als het tijds bereik een periode heeft die groter is dan 30 dagen en kleiner dan (of gelijk is aan) 90 dagen, worden grafieken weer gegeven in een wekelijkse weer gave. Voor grotere Peri Oden worden grafieken weer gegeven in de maand weergave. Het samen voegen van gegevens wekelijks of maandelijks helpt bij het verbeteren van de prestaties van query's en een betere Lees baarheid van gegevens in grafieken.
- De beleids regels voor de naleving van het beleid volgen ook een vergelijk bare aggregatie logica zoals hierboven wordt beschreven. Er zijn echter enkele kleine verschillen. Het eerste verschil is dat er voor items met een wekelijks back-upbeleid geen dagelijkse weer gave is (alleen wekelijkse en maandelijkse weer gaven zijn beschikbaar). In de rasters voor items met een wekelijks back-upbeleid wordt een ' maand ' gezien als een periode van 4 weken (28 dagen) en niet 30 dagen, om te voor komen dat gedeeltelijke weken van overweging worden genomen.

## <a name="query-load-times"></a>Laad tijden query

De widgets in het back-uprapport worden aangedreven door Kusto-query's, die worden uitgevoerd op de Log Analytics-werk ruimten van de gebruiker. Deze query's omvatten doorgaans de verwerking van grote hoeveel heden gegevens, met meerdere samen voegingen om uitgebreid inzicht mogelijk te maken. Als gevolg hiervan worden de widgets mogelijk niet direct geladen wanneer de gebruiker rapporten weergeeft over een grote back-up. Deze tabel bevat een ruwe schatting van de tijd die het laden van verschillende widgets kan uitvoeren, op basis van het aantal back-upitems en het tijds bereik waarvoor het rapport wordt weer gegeven.

| **Aantal gegevens bronnen**                         | **Tijd horizon** | **Geschatte laad tijden**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~ 5 K                       | 1 maand          | Tegels: 5-10 seconden <br> Rasters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapport niveau: 5-10 seconden|
| ~ 5 K                       | 3 maanden          | Tegels: 5-10 seconden <br> Rasters: 5-10 seconden <br> Grafieken: 5-10 seconden <br> Filters op rapport niveau: 5-10 seconden|
| ~ 10 K                       | 3 maanden          | Tegels: 15-20 seconden <br> Rasters: 15-20 seconden <br> Grafieken: 1-2 minuten <br> Filters op rapport niveau: 25-30 seconden|
| ~ 15 K                       | 1 maand          | Tegels: 15-20 seconden <br> Rasters: 15-20 seconden <br> Grafieken: 50-60 seconden <br> Filters op rapport niveau: 20-25 seconden|
| ~ 15 K                       | 3 maanden          | Tegels: 20-30 seconden <br> Rasters: 20-30 seconden <br> Grafieken: 2-3 minuten <br> Filters op rapport niveau: 50-60 seconden |

## <a name="what-happened-to-the-power-bi-reports"></a> Wat is er gebeurd met de Power BI-rapporten? 

- De eerdere Power BI sjabloon-app voor rapportage, waarvan de bron gegevens afkomstig zijn uit een Azure-opslag account, bevindt zich op een afschaffing-pad. U kunt het beste de diagnostische gegevens van de kluis naar Log Analytics verzenden om rapporten weer te geven.

- Daarnaast bevindt het [v1-schema](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) voor het verzenden van diagnostische gegevens naar een opslag account of een La-werk ruimte zich ook op een afschaffing pad. Dit betekent dat als u aangepaste query's of Automatiseringen hebt geschreven op basis van het v1-schema, u wordt geadviseerd om deze query's bij te werken om het momenteel ondersteunde v2-schema te gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over bewaking en rapportage met Azure Backup](./backup-azure-monitor-alert-faq.md)
