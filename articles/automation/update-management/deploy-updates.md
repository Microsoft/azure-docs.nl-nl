---
title: Update-implementaties maken voor Azure Automation Updatebeheer
description: In dit artikel wordt beschreven hoe u update-implementaties kunt plannen en de status ervan kunt controleren.
services: automation
ms.subservice: update-management
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e410e5de529bde122fe42d21b593a6fc483dcbc0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726669"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Updates implementeren en resultaten bekijken

In dit artikel wordt beschreven hoe u een update-implementatie kunt plannen en het proces kunt controleren nadat de implementatie is voltooid. U kunt een update-implementatie configureren vanaf een geselecteerde virtuele Azure-machine, vanaf de geselecteerde Arc-server of vanuit het Automation-account op alle geconfigureerde machines en servers.

In elk scenario maakt u doelen voor de geselecteerde computer of server, of in het geval van het maken van een implementatie vanuit uw Automation-account, kunt u zich richten op een of meer machines. Wanneer u een update-implementatie vanaf een Azure-VM of Arc-server inplant, zijn de stappen hetzelfde als de implementatie vanuit uw Automation-account, met de volgende uitzonderingen:

* Het besturingssysteem wordt automatisch vooraf geselecteerd op basis van het besturingssysteem van de machine
* De doelmachine die moet worden bijgewerkt, wordt automatisch als doel ingesteld
* Wanneer u de planning configureert, kunt u **Nu** bijwerken opgeven, één keer plaatsvinden of een terugkerend schema gebruiken.

> [!IMPORTANT]
> Door een update-implementatie te maken, accepteert u de voorwaarden van de softwarelicentievoorwaarden (EULA) die worden verstrekt door het bedrijf die updates aanbieden voor hun besturingssysteem.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Bij het plannen van [](../shared-resources/schedules.md) een update-implementatie wordt een planningsresource gemaakt die is gekoppeld aan het runbook **Patch-MicrosoftOMSComputers** dat de update-implementatie op de doelcomputer of -machines verwerkt. U moet een implementatie plannen die volgt op uw releaseplanning en servicevenster om updates te installeren. U kunt de typen updates kiezen die moeten worden opgenomen in de implementatie. U kunt bijvoorbeeld essentiële of beveiligingsupdates opnemen en update-rollups uitsluiten.

>[!NOTE]
>Als u na het maken van de implementatie de planningsresource verwijdert via de Azure-portal of met behulp van PowerShell, wordt de geplande update-implementatie door de verwijdering verbroken en wordt er een foutmelding weergegeven wanneer u probeert de planningsresource opnieuw te configureren vanuit de portal. U kunt de planningsresource alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  

Voer de volgende stappen uit om een nieuwe update-implementatie te plannen. Afhankelijk van de geselecteerde resource (dat wil zeggen Automation-account, Arc-server, Azure VM), zijn de onderstaande stappen van toepassing op alle resources met kleine verschillen tijdens het configureren van het implementatieschema.

1. In de portal kunt u een implementatie plannen voor:

   * Ga op een of meer computers naar **Automation-accounts** en selecteer uw Automation-account met Updatebeheer ingeschakeld in de lijst.
   * Voor een Azure-VM navigeert u **naar Virtuele machines** en selecteert u uw VM in de lijst.
   * Voor een Arc-server navigeert u **naar Servers - Azure Arc** selecteert u uw server in de lijst.

2. Afhankelijk van de resource die u hebt geselecteerd, gaat u naar Updatebeheer:

   * Als u uw Automation-account hebt geselecteerd, gaat u naar **Updatebeheer** onder **Updatebeheer** en selecteert u **update-implementatie plannen.**
   * Als u een azure-VM hebt geselecteerd, gaat u naar **Gast - en hostupdates** en selecteert u **vervolgens Ga naar Updatebeheer**.
   * Als u een Arc-server hebt geselecteerd, gaat u **naar Updatebeheer** en selecteert u **update-implementatie plannen.**

3. Voer **onder Nieuwe update-implementatie** in **het veld Naam** een unieke naam in voor uw implementatie.

4. Selecteer het besturingssysteem dat u wilt gebruiken voor de update-implementatie.

    > [!NOTE]
    > Deze optie is niet beschikbaar als u een Azure-VM of Arc-server hebt geselecteerd. Het besturingssysteem wordt automatisch geïdentificeerd.

5. Definieer **in De** bij te werken regio Groepen een query waarin abonnementen, resourcegroepen, locaties en tags worden gecombineerd om een dynamische groep virtuele Azure-VM's te bouwen die u in uw implementatie wilt opnemen. Zie Dynamische groepen gebruiken [met een Updatebeheer voor meer Updatebeheer.](configure-groups.md)

    > [!NOTE]
    > Deze optie is niet beschikbaar als u een Azure-VM of Arc-server hebt geselecteerd. De computer wordt automatisch gericht op de geplande implementatie.

6. Selecteer in **de regio Machines die moeten worden** bijgewerkt een opgeslagen zoekopdracht, een geïmporteerde groep of kies **Machines** in de vervolgkeuzelijst en selecteer afzonderlijke machines. Met deze optie kunt u de gereedheid van de Log Analytics-agent voor elke machine zien. Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/logs/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken. U kunt maximaal 1000 machines opnemen in een geplande update-implementatie.

    > [!NOTE]
    > Deze optie is niet beschikbaar als u een Azure-VM of Arc-server hebt geselecteerd. De computer is automatisch gericht op de geplande implementatie.

7. Gebruik de regio **Updateclassificaties** om [updateclassificaties voor producten](view-update-assessments.md#work-with-update-classifications) op te geven. Voor elk product moet u de selectie van alle ondersteunde updateclassificaties ongedaan maken, behalve de classificaties die u wilt opnemen in uw update-implementatie.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Voorbeeld van de selectie van specifieke updateclassificaties.":::

    Als uw implementatie is bedoeld om alleen een bepaalde set updates toe te passen, moet u de selectie van alle vooraf geselecteerde updateclassificaties ongedaan maken bij het configureren van de optie **Updates opnemen/uitsluiten,** zoals beschreven in de volgende stap. Dit zorgt ervoor dat alleen de updates die u hebt opgegeven om op te *nemen* in deze implementatie op de doelmachines worden geïnstalleerd.

   >[!NOTE]
   > Het implementeren van updates op updateclassificatie werkt niet op RTM-versies van CentOS. Als u updates voor CentOS op de juiste manier wilt implementeren, selecteert u alle classificaties om ervoor te zorgen dat updates worden toegepast. Er is momenteel geen ondersteunde methode voor het inschakelen van de beschikbaarheid van native classificatiegegevens in CentOS. Zie het volgende voor meer informatie over [updateclassificaties.](overview.md#update-classifications)

8. Gebruik de regio **Updates opnemen/uitsluiten** om geselecteerde updates toe te voegen aan of uit te sluiten van de implementatie. Op de **pagina Opnemen/uitsluiten** voert u KB-artikel-id-nummers in die u wilt opnemen of uitsluiten voor Windows-updates. Voor ondersteunde Linux-distributies geeft u de pakketnaam op.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Voorbeeld waarin wordt getoond hoe u specifieke updates op kunt nemen.":::

   > [!IMPORTANT]
   > Denk eraan dat uitsluitingen insluitingen overschrijven. Als u bijvoorbeeld een uitsluitingsregel van definieert, worden Updatebeheer patches of pakketten `*` uitgesloten van de installatie. Uitgesloten patches worden nog steeds als ontbrekend van de machines weer geven. Als u op Linux-computers een pakket opneemt dat een afhankelijk pakket bevat dat is uitgesloten, installeert Updatebeheer het hoofdpakket niet.

   > [!NOTE]
   > U kunt geen updates opgeven die zijn verded om op te nemen in de update-implementatie.

   Hier volgen enkele voorbeeldscenario's om u te helpen begrijpen hoe u insluiting/uitsluiting en updateclassificatie tegelijkertijd kunt gebruiken in update-implementaties:

   * Als u alleen een specifieke lijst met updates wilt installeren, moet u geen **updateclassificaties** selecteren en een lijst met updates verstrekken die moeten worden toegepast met de **optie Opnemen.**

   * Als u alleen beveiligingsupdates en essentiële updates wilt installeren, samen  met een of meer optionele stuurprogramma-updates, selecteert u Beveiliging en kritiek **onder** **Updateclassificaties.** Geef vervolgens voor **de optie** Opnemen de stuurprogramma-updates op.

   * Als u alleen beveiligingsupdates en essentiële updates wilt installeren, maar een of meer  updates  voor Python wilt overslaan om te voorkomen dat uw verouderde toepassing wordt breken, selecteert u Beveiliging en kritiek onder **Updateclassificaties.** Voeg vervolgens voor **de optie** Uitsluiten de Python-pakketten toe die u wilt overslaan.

9. Selecteer **Planningsinstellingen.** De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

    > [!NOTE]
    > Deze optie is anders als u een server met Arc hebt geselecteerd. U kunt **Nu bijwerken of** een begintijd van 20 minuten in de toekomst selecteren.

10. Gebruik **terugkeerpatroon om** op te geven of de implementatie eenmaal plaatsvindt of gebruikmaakt van een terugkerend schema. Selecteer vervolgens **OK.**

11. Selecteer in **de regio Pre-scripts + Post-scripts** de scripts die vóór en na de implementatie moeten worden uitgevoerd. Zie Prescripts en [post-scripts beheren voor meer informatie.](pre-post-scripts.md)

12. Gebruik het **veld Onderhoudsvenster (minuten)** om de hoeveelheid tijd op te geven die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhoudsvenster opgeeft:

    * Onderhoudsvensters bepalen hoeveel updates worden geïnstalleerd.
    * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
    * Updatebeheer wordt niet beëindigd als er updates worden uitgevoerd wanneer het onderhoudsvenster wordt overschreden. Eventuele resterende updates die moeten worden geïnstalleerd, worden niet geprobeerd. Als dit consistent gebeurt, moet u de duur van het onderhoudsvenster opnieuw beoordelen.
    * Als het onderhoudsvenster op Windows wordt overschreden, komt dat vaak doordat het installeren van een servicepack-update lang duurt.

    > [!NOTE]
    > Om te voorkomen dat updates worden toegepast buiten een onderhoudsvenster op Ubuntu, moet u het pakket opnieuw configureren om `Unattended-Upgrade` automatische updates uit te schakelen. Zie het onderwerp Automatische updates in de [Ubuntu-serverhandleiding](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor meer informatie over het configureren van het pakket.

13. Gebruik het **veld Opties voor opnieuw** opstarten om de manier op te geven waarop het opnieuw opstarten tijdens de implementatie moet worden verwerkt. De volgende opties zijn beschikbaar: 
    * Opnieuw opstarten indien nodig (standaard)
    * Altijd opnieuw opstarten
    * Nooit opnieuw opstarten
    * Alleen opnieuw opstarten; Met deze optie worden geen updates geïnstalleerd

    > [!NOTE]
    > De registersleutels onder [Registry keys used to manage restart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) (registersleutels voor het beheren van opnieuw opstarten) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **Opties voor opnieuw opstarten** is ingesteld op **Nooit opnieuw opstarten**.

14. Wanneer u klaar bent met het configureren van het implementatieschema, selecteert u **Maken.**

    ![Deelvenster Planningsinstellingen bijwerken](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Wanneer u klaar bent met het configureren van het implementatieschema voor een geselecteerde Arc-server, selecteert u **Controleren en maken.**

15. U keert nu terug naar het statusdashboard. Selecteer **Implementatieschema's** om de implementatieplanning weer te geven die u hebt gemaakt. Er worden maximaal 500 schema's weergegeven. Als u meer dan 500 planningen hebt en u de volledige lijst wilt bekijken, bekijkt u de methode [Software-updateconfiguraties -](/rest/api/automation/softwareupdateconfigurations/list) REST API lijst. Geef API-versie 2019-06-01 of hoger op.

## <a name="schedule-an-update-deployment-programmatically"></a>Programmatisch een update-implementatie plannen

Zie [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (software-updateconfiguraties: maken) als u wilt leren hoe u een update-implementatie kunt maken met de REST-API.

U kunt een voorbeeldrunbook gebruiken om een wekelijkse update-implementatie te maken. Zie voor meer informatie over dit runbook [Create a weekly update deployment for one or more VMs in a resource group](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group) (een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep).

## <a name="check-deployment-status"></a>Implementatiestatus controleren

Nadat de geplande implementatie is gestart, kunt u de status ervan bekijken op **het tabblad Geschiedenis** onder **Updatebeheer.** Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Wanneer de implementatie is voltooid, verandert de status in **Geslaagd.** Als er fouten zijn met een of meer updates in de implementatie, is de status **Mislukt.**

## <a name="view-results-of-a-completed-update-deployment"></a>Resultaten van een voltooide update-implementatie weergeven

Wanneer de implementatie is voltooid, kunt u deze selecteren om de resultaten te bekijken.

[![Dashboard implementatiestatus bijwerken voor een specifieke implementatie](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Onder **Updateresultaten vindt** u een samenvatting van het totale aantal updates en implementatieresultaten op de doel-VM's. In de tabel aan de rechterkant ziet u een gedetailleerde uitsplitsing van de updates en de installatieresultaten voor elke update.

De beschikbare waarden zijn:

* **Niet geprobeerd:** de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was, op basis van de gedefinieerde duur van het onderhoudsvenster.
* **Niet geselecteerd:** de update is niet geselecteerd voor implementatie.
* **Geslaagd:** de update is geslaagd.
* **Mislukt:** de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **Uitvoer om** de taakstroom te zien van het runbook dat verantwoordelijk is voor het beheren van de update-implementatie op de doel-VM's.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

* Zie Waarschuwingen maken voor meer informatie over het maken van waarschuwingen om u op de hoogte te stellen van de resultaten van de [update-Updatebeheer.](configure-alerts.md)
* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
