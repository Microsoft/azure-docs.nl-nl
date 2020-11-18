---
title: Gids voor kosten beheer voor Azure Lab Services
description: Meer informatie over de verschillende manieren om kosten voor Lab-services weer te geven.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 29f6be5319c5a142ad3ea0d73deb2f95d8cb0d7a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659723"
---
# <a name="cost-management-for-azure-lab-services"></a>Kosten beheer voor Azure Lab Services

Voor Azure Lab Services kan het kosten beheer worden onderverdeeld in twee verschillende gebieden: kosten raming en kosten analyse. Kosten raming vindt plaats wanneer u het lab instelt om ervoor te zorgen dat de oorspronkelijke structuur van het lab binnen het verwachte budget past. Kosten analyse vindt meestal plaats aan het einde van de maand om de vereiste acties voor de volgende maand te bepalen.

## <a name="estimate-the-lab-costs"></a>De kosten van het lab schatten

Elk lab-dash board bevat een **kosten & facturerings** sectie waarin een ruwe schatting wordt opgenomen van wat het lab voor de maand kost. De schatting van de kosten geeft een overzicht van het uurverbruik met het maximum aantal gebruikers op basis van de geschatte kosten per uur. Als u de meest nauw keurige schatting wilt krijgen, stelt u het lab in, met inbegrip van het [schema](how-to-create-schedules.md). Het dash board geeft de geschatte kosten weer. 

In deze schatting worden mogelijk niet alle mogelijke kosten weer gegeven. Er zijn geen resources opgenomen:

- De kosten voor de sjabloon voorbereiding. Het kan aanzienlijk verschillen in de tijd die nodig is om de sjabloon te maken. De kosten voor het uitvoeren van de sjabloon zijn hetzelfde als de totale Lab-kosten per uur. 
- Alle kosten voor de [Galerie met gedeelde afbeeldingen](how-to-use-shared-image-gallery.md) , omdat een galerie kan worden gedeeld tussen meerdere Labs. 
- Uren die worden gemaakt wanneer de Lab-Maker een virtuele machine (VM) start.

> [!div class="mx-imgBorder"]
> ![Scherm opname van de kosten raming van het dash board.](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>Het gebruik van de vorige maand analyseren

De kosten analyse is voor het controleren van het gebruik van de vorige maand om u te helpen bij het bepalen van eventuele aanpassingen van het lab. U vindt de uitsplitsing van de kosten van het [abonnement in de kosten analyse van abonnementen](../cost-management-billing/costs/quick-acm-cost-analysis.md). In de Azure Portal kunt u **abonnementen** invoeren in het zoekvak en vervolgens de optie **abonnementen** selecteren. 

> [!div class="mx-imgBorder"]
> ![Scherm opname van het zoekvak en de optie abonnementen.](./media/cost-management-guide/subscription-search.png)

Selecteer het specifieke abonnement dat u wilt controleren.

> [!div class="mx-imgBorder"]
> ![Scherm opname van de selectie van het abonnement.](./media/cost-management-guide/subscription-select.png)

Selecteer **kosten analyse** in het linkerdeel venster onder **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Scherm opname van de kosten analyse van een abonnement op een grafiek.](./media/cost-management-guide/subscription-cost-analysis.png)

Dit dash board biedt gedetailleerde kosten analyse, waaronder de mogelijkheid om te exporteren naar verschillende bestands typen volgens een schema. Zie [Cost Management + facturering Overview](../cost-management-billing/cost-management-billing-overview.md)(Engelstalig) voor meer informatie.

U kunt filteren op resource type. Met `microsoft.labservices/labaccounts` worden alleen de kosten weer gegeven die zijn gekoppeld aan Lab-Services.

## <a name="understand-the-usage"></a>Inzicht in het gebruik

De volgende scherm afbeelding is een voor beeld van een kosten analyse.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van een voor beeld van een kosten analyse voor een abonnement.](./media/cost-management-guide/cost-analysis.png)

Standaard zijn er zes kolommen: **resource**, **resource type**, **locatie**, **resource groeps naam**, **Tags** en **kosten**. De kolom **resource** bevat informatie over het lab-account, de naam van het lab en de VM. De rijen met het lab-account, de naam van het lab en de standaard waarde (tweede en derde rij) zijn de kosten voor het lab. De gebruikte Vm's hebben kosten die u kunt zien voor de rijen met het lab-account, de naam van het lab, de standaard naam en de VM. 

In dit voor beeld geeft het toevoegen van de eerste en tweede rij (beide beginnen met **aaalab/dockerlab**) u de totale kosten voor het Lab ' dockerlab ' in het lab-account ' aaalab '.

Als u de totale kosten voor de galerie met afbeeldingen wilt ophalen, wijzigt u het resource type in `Microsoft.Compute/Galleries` . Een galerie met gedeelde afbeeldingen wordt mogelijk niet weer gegeven in de kosten, afhankelijk van waar de galerie is opgeslagen.

> [!NOTE]
> Er is een galerie met gedeelde afbeeldingen verbonden met het lab-account. Dit betekent dat meerdere Labs dezelfde afbeelding kunnen gebruiken.

## <a name="separate-the-costs"></a>De kosten scheiden

Sommige universiteiten hebben het lab-account en de resource groep gebruikt als manieren om de klassen van elkaar te scheiden. Elke klasse heeft een eigen lab-account en resource groep. 

Voeg in het deel venster kosten analyse een filter toe op basis van de naam van de resource groep met de juiste naam voor de resource groep voor de klasse. Alleen de kosten voor die klasse zijn dan zichtbaar. Dit maakt een duidelijk beeld van de klassen mogelijk wanneer u de kosten bekijkt. U kunt de functie voor [geplande export](../cost-management-billing/costs/tutorial-export-acm-data.md) van de kosten analyse gebruiken om de kosten van elke klasse in afzonderlijke bestanden te downloaden.

## <a name="manage-costs"></a>Kosten beheren

Afhankelijk van het type klasse zijn er manieren om de kosten te beheren, zodat exemplaren van Vm's die zonder een student worden uitgevoerd, worden verminderd.

### <a name="automatic-shutdown-settings-for-cost-control"></a>Instellingen voor automatisch afsluiten voor kosten beheer

Met functies voor automatisch afsluiten kunt u verspilde VM-gebruiks uren in de Labs voor komen. De volgende instellingen ondervangen de meeste gevallen waarin gebruikers onbedoeld hun virtuele machines verlaten:

> [!div class="mx-imgBorder"]
> ![Scherm opname van de drie instellingen voor automatisch afsluiten.](./media/cost-management-guide/auto-shutdown-disconnect.png)

U kunt deze instellingen configureren op het niveau van het lab-account en het lab-niveau. Als u deze optie inschakelt op het niveau van het lab-account, worden deze toegepast op alle Labs binnen het lab-account. Deze instellingen zijn standaard ingeschakeld voor alle nieuwe lab-accounts. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>Gebruikers automatisch verbreken van virtuele machines die het besturings systeem niet-actief acht

> [!NOTE]
> Deze instelling is alleen beschikbaar voor virtuele Windows-machines.

Wanneer de instelling **gebruikers verbinding verbreken wanneer de virtuele machines niet actief** is is ingeschakeld, wordt de verbinding van de gebruiker met alle computers in het lab verbroken wanneer het Windows-besturings systeem de sessie als niet-actief wordt beschouwd (inclusief de virtuele machine van de sjabloon). De [definitie van het Windows-besturings systeem die niet actief](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) is, gebruikt twee criteria: 

* Verzuim van gebruiker: geen toetsen bord of muis invoer.
* Gebrek aan Resource verbruik: alle processors en alle schijven zijn gedurende een bepaald percentage tijd niet actief.

Gebruikers zien een bericht zoals dit in de virtuele machine voordat ze worden losgekoppeld: 

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding met een waarschuwing dat een sessie gedurende de tijds limiet niet actief is en de verbinding wordt verbroken.](./media/cost-management-guide/idle-timer-expired.png)
 
De virtuele machine wordt nog steeds uitgevoerd wanneer de verbinding van de gebruiker wordt verbroken. Als de gebruiker opnieuw verbinding maakt met de virtuele machine door zich aan te melden, worden Windows-of bestanden geopend die niet zijn opgeslagen voordat de verbinding wordt verbroken. Omdat de virtuele machine wordt uitgevoerd in deze status, telt deze nog steeds als actief en worden kosten in rekening gebracht. 
 
Als u niet-actieve virtuele Windows-machines die zijn losgekoppeld automatisch wilt uitschakelen, gebruikt u de combi natie van **gebruikers loskoppelen wanneer virtuele machines inactief zijn** en **virtuele machines afsluiten wanneer gebruikers** de instellingen van de verbinding verbreken.

Als u de instellingen bijvoorbeeld als volgt configureert:
 
* **Gebruikers loskoppelen wanneer virtuele machines niet actief zijn**: 15 minuten nadat de status niet actief is gedetecteerd.
* **Virtuele machines uitschakelen wanneer gebruikers** verbinding maken: 5 minuten nadat de gebruiker de verbinding heeft verbroken.
 
De virtuele Windows-machines worden automatisch 20 minuten afgesloten nadat de gebruiker deze heeft gestopt. 
 
> [!div class="mx-imgBorder"]
> ![Diagram dat de combi natie van instellingen illustreert die het automatisch afsluiten van de virtuele machine veroorzaakt.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>Virtuele machines automatisch uitschakelen wanneer gebruikers de verbinding verbreken
 
De virtuele **machines uitschakelen wanneer gebruikers de verbinding verbreken** ondersteunen zowel Windows-als Linux-virtuele machines. Als deze instelling is ingeschakeld, wordt automatisch afsluiten uitgevoerd wanneer:
 
* Voor Windows is een Extern bureaublad RDP-verbinding verbroken.
* Voor Linux wordt een SSH-verbinding verbroken.
 
> [!NOTE]
> Alleen [specifieke distributies en versies van Linux](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions) worden ondersteund.
 
U kunt opgeven hoe lang de virtuele machines moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>Virtuele machines die zijn gestart automatisch afsluiten, maar gebruikers maken geen verbinding
 
In een Lab kan een gebruiker een virtuele machine starten, maar er geen verbinding mee maken. Bijvoorbeeld:
 
* Met een planning in het lab worden alle virtuele machines voor een klasse-sessie gestart, maar sommige studenten worden niet weer gegeven en er wordt geen verbinding gemaakt met hun machines. 
* Een gebruiker start een virtuele machine, maar vergeet niet om verbinding te maken. 
 
De optie **virtuele machines uitschakelen wanneer gebruikers geen verbinding maken** , worden deze gevallen onderschept en de virtuele machines automatisch afgesloten. 
 
Raadpleeg de volgende artikelen voor meer informatie over het configureren en inschakelen van automatisch afsluiten van Vm's bij het verbreken van de verbinding:

* [Automatisch afsluiten van Vm's voor een Lab-account configureren](how-to-configure-lab-accounts.md)
* [Automatisch afsluiten van Vm's voor een lab configureren](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>Geplande tijd versus quotum tijd

Als u de [geplande tijd](classroom-labs-concepts.md#schedules) en [quotum tijd](classroom-labs-concepts.md#quota) begrijpt, kunt u een Lab zodanig configureren dat ze beter aansluiten op de behoeften van de professor en de studenten. 

Geplande tijd is een periode waarin alle student-Vm's zijn gestart en beschikbaar zijn voor verbinding. De geplande tijd wordt doorgaans gebruikt wanneer alle studenten hun eigen Vm's hebben en de aanwijzingen van de professor op een bepaalde tijd gedurende de dag (zoals class uren) volgen. Het nadeel is dat alle student-Vm's worden gestart en dat ze kosten in beslag nemen, zelfs als een student zich niet bij een virtuele machine aanmeldt. 

Quota tijd is de tijd die aan elke student wordt toegewezen en die wordt gebruikt voor een onafhankelijke studie. De Vm's worden niet gestart totdat de student de virtuele machine start. 

Een Lab kan gebruikmaken van de quota tijd of de geplande tijd, of een combi natie van beide. Als een klasse niet de geplande tijd nodig heeft, gebruikt u alleen de quota tijd voor het meest effectief gebruik van de Vm's.

### <a name="scheduled-event-stop-only"></a>Geplande gebeurtenis: alleen stoppen

In het schema kunt u een alleen-lezen gebeurtenis type toevoegen waarmee alle machines op een specifiek tijdstip worden gestopt. Sommige Lab-eigen aars hebben om middernacht een gebeurtenis met alleen een stop-actie ingesteld om de kosten en het quotum gebruik te verlagen wanneer een student de virtuele machine die ze gebruikt, sluit. Het nadeel van dit type gebeurtenis is dat alle Vm's worden afgesloten, zelfs als een student een virtuele machine gebruikt.

### <a name="other-costs-related-to-labs"></a>Andere kosten met betrekking tot Labs 

Sommige kosten worden niet meegenomen in Lab-Services, maar kunnen worden gekoppeld aan een Lab-service. U kunt een galerie met gedeelde afbeeldingen verbinden met een lab, maar deze wordt niet weer gegeven onder de kosten van de Lab-Services en heeft kosten. Verwijder ongebruikte installatie kopieën uit de galerie, zodat u de totale kosten kunt verlagen, omdat de installatie kopieën een inherente opslag kosten hebben. 

Labs kan verbinding maken met andere Azure-resources via een virtueel netwerk. Wanneer een lab wordt verwijderd, moet u het virtuele netwerk en de andere bronnen verwijderen.

## <a name="conclusion"></a>Conclusie

Hopelijk heeft de informatie in dit artikel een beter inzicht in de hulpprogram ma's die u kunnen helpen bij het verminderen van de gebruiks kosten.