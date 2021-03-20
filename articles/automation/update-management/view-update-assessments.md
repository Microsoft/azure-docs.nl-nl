---
title: Azure Automation update-evaluaties weer geven
description: In dit artikel leest u hoe u update-evaluaties weergeeft voor Updatebeheer-implementaties.
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 39df5888a330a92ae043e34c3043da5b1f566345
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92222210"
---
# <a name="view-update-assessments-in-update-management"></a>Update-evaluaties weer geven in Updatebeheer

In Updatebeheer kunt u informatie weer geven over uw computers, ontbrekende updates, update-implementaties en geplande update-implementaties. U kunt de beoordelings gegevens weer geven die zijn gericht op de geselecteerde virtuele machine van Azure, van de geselecteerde server voor de Arc of van het Automation-account op alle geconfigureerde computers en servers.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com)

## <a name="view-update-assessment"></a>Update-evaluatie bekijken

Als u de update-evaluatie van een Azure-VM wilt bekijken, gaat u naar **virtual machines** en selecteert u uw virtuele machine in de lijst. Selecteer in het menu links **gast en host updates** en selecteer vervolgens **Ga naar updatebeheer** op de pagina **gast en host-updates** .

In Updatebeheer kunt u informatie weer geven over uw computer, ontbrekende updates, update-implementaties en geplande update-implementaties.

[![Evaluatie weergave updatebeheer voor Azure VM](./media/view-update-assessments/update-assessment-azure-vm.png)](./media/view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Als u de update-evaluatie van een server met Arc wilt bekijken, gaat u naar **servers-Azure-boog** en selecteert u de server in de lijst. Selecteer in het menu links de optie **gast en host-updates**. Selecteer op de pagina **gast en host-updates** de optie **Ga naar updatebeheer**.

In Updatebeheer kunt u informatie weer geven over uw computer met Arc, ontbrekende updates, update-implementaties en geplande update-implementaties.

[![Beoordelings weergave updatebeheer voor servers met Arc-functionaliteit](./media/view-update-assessments/update-assessment-arc-server.png)](./media/view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Als u de update-evaluatie op alle computers wilt weer geven, inclusief de Arc ingeschakelde servers van uw Automation-account, gaat u naar **Automation-accounts** en selecteert u uw Automation-account met updatebeheer ingeschakeld in de lijst. Selecteer in uw Automation-account **Update beheer** in het menu links.

De updates voor uw omgeving worden vermeld op de pagina **Update beheer** . Als er updates worden geïdentificeerd als ontbrekend, wordt een lijst met deze weer gegeven op het tabblad **ontbrekende updates** .

[![Standaard weergave updatebeheer](./media/overview/update-management-view.png)](./media/overview/update-management-view-expanded.png#lightbox)

Onder de kolom **compatibiliteit** ziet u de laatste keer dat de computer is geëvalueerd. In de **gereedheids** kolom van de Update-Agent kunt u de status van de Update Agent bekijken. Als er een probleem is, selecteert u de koppeling om naar probleemoplossings documentatie te gaan waarmee u het probleem kunt verhelpen.

Selecteer onder **informatie koppeling** de koppeling voor een update om het ondersteunings artikel te openen waarmee u belang rijke informatie over de update krijgt.

[![Update status weer geven](./media/view-update-assessments/missing-updates.png)](./media/view-update-assessments/missing-updates-expanded.png#lightbox)

Klik ergens anders in de update om het deel venster zoeken in Logboeken te openen. De query voor zoeken in logboeken is vooraf gedefinieerd voor deze specifieke update. U kunt deze query wijzigen of uw eigen query maken om gedetailleerde informatie weer te geven.

[![Query resultaten van logboek weer geven](./media/view-update-assessments/logsearch-results.png)](./media/view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Ontbrekende updates weer geven

Selecteer **ontbrekende updates** om de lijst met updates weer te geven die ontbreken op uw computers. Elke update wordt weer gegeven en kan worden geselecteerd. Informatie over het aantal machines dat moet worden bijgewerkt, Details van het besturings systeem en een koppeling voor meer informatie, worden weer gegeven. Het deel venster zoeken in Logboeken bevat ook meer informatie over de updates.

![Ontbrekende updates](./media/view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Werken met update classificaties

De volgende tabellen geven een lijst van de ondersteunde update classificaties in Updatebeheer, met een definitie voor elke classificatie.

### <a name="windows"></a>Windows

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële updates     | Updates voor specifieke problemen die kritiek zijn, niet-beveiligings fouten.        |
|Beveiligingsupdates     | Updates voor productspecifieke, beveiligings problemen.        |
|Updatepakketten     | Sets van hotfixes die samen zijn verpakt voor een eenvoudige implementatie.        |
|Functiepakketten     | Nieuwe product functies die worden gedistribueerd buiten een product release.        |
|Servicepacks     | Sets van hotfixes die op een toepassing worden toegepast.        |
|Definitie-updates     | Updates van virus-of andere definitie bestanden.        |
|Hulpprogramma's     | Hulpprogram ma's of functies die helpen bij het volt ooien van een of meer taken.        |
|Updates     | Updates voor toepassingen of bestanden die momenteel worden geïnstalleerd.        |

### <a name="linux"></a>Linux

|Classificatie  |Beschrijving  |
|---------|---------|
|Essentiële en beveiligingsupdates     | Updates voor een specifiek probleem of een productspecifiek beveiligings probleem.         |
|Andere Updates     | Alle andere updates die niet kritiek zijn of die geen beveiligings updates zijn.        |

Voor Linux kan Updatebeheer een onderscheid maken tussen essentiële updates en beveiligings updates in de Cloud, terwijl beoordelings gegevens worden weer gegeven. (Deze granulatie is mogelijk vanwege gegevens verrijking in de Cloud.) Voor patching is Updatebeheer afhankelijk van de classificatie gegevens die op de computer beschikbaar zijn. In tegens telling tot andere distributies is CentOS deze informatie niet beschikbaar in de RTM-versies van het product. Als er CentOS machines zijn geconfigureerd voor het retour neren van beveiligings gegevens voor de volgende opdracht, kunt Updatebeheer patch op basis van classificaties uitvoeren:

```bash
sudo yum -q --security check-update
```

Er is momenteel geen ondersteunde methode voor het inschakelen van systeem eigen classificatie-gegevens beschikbaarheid op CentOS. Op dit moment wordt alleen ondersteuning voor de beste werk belasting gegeven aan klanten die hun eigen functionaliteit hebben ingeschakeld.

Als u updates wilt classificeren voor Red Hat Enter prise versie 6, moet u de yum-beveiligings-invoeg toepassing installeren. Op Red Hat Enterprise Linux 7 maakt de invoeg toepassing al deel uit van yum. u hoeft niets te installeren. Zie het volgende Red Hat [Knowledge-artikel](https://access.redhat.com/solutions/10021)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De volgende fase in het proces is het [implementeren van updates](deploy-updates.md) op niet-compatibele computers en de implementatie resultaten te controleren.
