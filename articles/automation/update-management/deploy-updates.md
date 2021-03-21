---
title: Update-implementaties voor Azure Automation maken Updatebeheer
description: In dit artikel wordt beschreven hoe u update-implementaties plant en hoe u de status ervan controleert.
services: automation
ms.subservice: update-management
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: 6d35d6b49ab72d8aa7b25506011147ab624273fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669675"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Updates implementeren en resultaten bekijken

In dit artikel wordt beschreven hoe u een update-implementatie plant en het proces controleert nadat de implementatie is voltooid. U kunt een update-implementatie configureren vanaf een geselecteerde Azure-virtuele machine, van de geselecteerde server met Arc of van het Automation-account op alle geconfigureerde computers en servers.

In elk scenario kunt u een of meer computers richten op de implementatie die u maakt op basis van de geselecteerde machine of server, of in het geval van het maken van een implementatie van uw Automation-account. Wanneer u een update-implementatie plant vanaf een Azure VM of Arc-server, zijn de stappen hetzelfde als bij het implementeren van uw Automation-account, met de volgende uitzonde ringen:

* Het besturings systeem wordt automatisch vooraf geselecteerd op basis van het OS van de computer
* De doel computer die moet worden bijgewerkt, is ingesteld op automatisch als doel zelf instellen
* Wanneer u de planning configureert, kunt u **update nu** en één keer opgeven, of een terugkerend schema gebruiken.

> [!IMPORTANT]
> Door een update-implementatie te maken, gaat u akkoord met de voor waarden van de gebruiksrecht overeenkomst van het bedrijf die updates biedt voor hun besturings systeem.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Bij het plannen van een update-implementatie wordt een [plannings](../shared-resources/schedules.md) resource gemaakt die is gekoppeld aan het **patch-MicrosoftOMSComputers-** runbook dat de update-implementatie op de doel computer of computers verwerkt. U moet een implementatie plannen die volgt op de release planning en het service venster om updates te installeren. U kunt de typen updates kiezen die moeten worden opgenomen in de implementatie. U kunt bijvoorbeeld essentiële of beveiligings updates opnemen en update pakketten uitsluiten.

>[!NOTE]
>Als u na het maken van de implementatie de planningsresource verwijdert via de Azure-portal of met behulp van PowerShell, wordt de geplande update-implementatie door de verwijdering verbroken en wordt er een foutmelding weergegeven wanneer u probeert de planningsresource opnieuw te configureren vanuit de portal. U kunt de planningsresource alleen verwijderen door het bijbehorende implementatieschema te verwijderen.  

Als u een nieuwe update-implementatie wilt plannen, voert u de volgende stappen uit. Afhankelijk van de geselecteerde resource (dat wil zeggen, een Automation-account, een Arc-server, een Azure-VM), worden de onderstaande stappen toegepast op alle met kleine verschillen tijdens het configureren van het implementatie schema.

1. In de portal kunt u een implementatie plannen voor:

   * Een of meer computers, navigeer naar **Automation-accounts** en selecteer uw Automation-account met updatebeheer ingeschakeld in de lijst.
   * Ga voor een Azure VM naar **virtuele machines** en selecteer de virtuele machine in de lijst.
   * Voor een server waarop een Arc is ingeschakeld, gaat u naar **servers-Azure-boog** en selecteert u de server in de lijst.

2. Afhankelijk van de resource die u hebt geselecteerd, gaat u naar Updatebeheer:

   * Als u uw Automation-account hebt geselecteerd, gaat u naar **Update beheer** onder **Update beheer** en selecteert u **Update-implementatie plannen**.
   * Als u een virtuele machine van Azure hebt geselecteerd, gaat u naar **gast en host-updates** en selecteert u vervolgens **Ga naar updatebeheer**.
   * Als u een server met Arc-functionaliteit hebt geselecteerd, gaat u naar **updatebeheer** en selecteert u **Update-implementatie plannen**.

3. Voer onder **nieuwe update-implementatie** in het veld **naam** een unieke naam in voor uw implementatie.

4. Selecteer het besturings systeem dat u wilt instellen als doel voor de update-implementatie.

    > [!NOTE]
    > Deze optie is niet beschikbaar als u een Azure-VM of een Arc-server hebt geselecteerd. Het besturings systeem wordt automatisch geïdentificeerd.

5. In de regio **groepen die moeten worden bijgewerkt** , definieert u een query die het abonnement, de resource groepen, locaties en Tags combineert voor het samen stellen van een dynamische groep virtuele Azure-machines die in uw implementatie moeten worden meegenomen. Zie voor meer informatie [dynamische groepen gebruiken met updatebeheer](configure-groups.md).

    > [!NOTE]
    > Deze optie is niet beschikbaar als u een Azure-VM of een Arc-server hebt geselecteerd. De machine is automatisch gericht op de geplande implementatie.

6. Selecteer in de regio **computers die u wilt bijwerken** een opgeslagen zoek opdracht, een geïmporteerde groep of kies **machines** in het vervolg keuzemenu en selecteer afzonderlijke machines. Met deze optie kunt u de gereedheid van de Log Analytics-agent voor elke machine bekijken. Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/logs/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken. U kunt Maxi maal 1000 computers in een geplande update-implementatie toevoegen.

    > [!NOTE]
    > Deze optie is niet beschikbaar als u een Azure-VM of een Arc-server hebt geselecteerd. De machine is automatisch gericht op de geplande implementatie.

7. Gebruik de regio **Update classificaties** om [Update classificaties](view-update-assessments.md#work-with-update-classifications) voor producten op te geven. Voor elk product deselecteert u alle ondersteunde update classificaties, maar ook voor de implementatie van de update.

    Als uw implementatie alleen van toepassing is op een bepaalde set updates, is het nood zakelijk om alle vooraf geselecteerde update classificaties te deselecteren bij het configureren van de optie voor het **opnemen/uitsluiten van updates** , zoals wordt beschreven in de volgende stap. Dit zorgt ervoor dat alleen de *updates die u hebt opgegeven in deze* implementatie zijn geïnstalleerd op de doel computers.

8. Gebruik de regio **updates opnemen/uitsluiten** om geselecteerde updates van de implementatie toe te voegen of uit te sluiten. Op de pagina **opnemen/uitsluiten** voert u KB-artikel-id-nummers in om op te nemen of uit te sluiten voor Windows-updates. Voor ondersteunde Linux-distributies geeft u de naam van het pakket op.

   > [!IMPORTANT]
   > Houd er rekening mee dat uitsluitingen insluitingen opheffen. Als u bijvoorbeeld een uitsluitings regel van opgeeft `*` , updatebeheer sluit alle patches of pakketten van de installatie uit. Uitgesloten patches worden nog steeds weer gegeven als ontbrekend op de computers. Als u op Linux-computers een pakket opneemt dat een afhankelijk pakket bevat dat is uitgesloten, installeert Updatebeheer het hoofdpakket niet.

   > [!NOTE]
   > U kunt geen updates opgeven die in de update-implementatie zijn vervangen.

9. Selecteer **schema-instellingen**. De standaard begintijd is 30 minuten na de huidige tijd. U kunt de starttijd op elke gewenste tijd instellen, maar minstens 10 minuten na de huidige tijd.

    > [!NOTE]
    > Deze optie is anders als u een met Arc ingeschakelde server hebt geselecteerd. U kunt **nu een update** of een begin tijd van 20 minuten in de toekomst selecteren.

10. Gebruik het **terugkeer patroon** om op te geven of de implementatie één keer wordt uitgevoerd of een terugkerend schema gebruikt en selecteer **OK**.

11. Selecteer in de regio **pre-scripts + post-scripts** de scripts die moeten worden uitgevoerd vóór en na de implementatie. Zie [pre-scripts en post scripts beheren](pre-post-scripts.md)voor meer informatie.

12. Gebruik het veld **onderhouds venster (minuten)** om de hoeveelheid tijd op te geven die is toegestaan voor het installeren van updates. Houd rekening met de volgende details wanneer u een onderhoudsvenster opgeeft:

    * Onderhoudsvensters bepalen hoeveel updates worden geïnstalleerd.
    * Updatebeheer stopt niet met het installeren van nieuwe updates als het einde van een onderhoudsvenster nadert.
    * Updatebeheer wordt niet beëindigd als er updates worden uitgevoerd wanneer het onderhoudsvenster wordt overschreden. Alle resterende updates die moeten worden geïnstalleerd, worden niet geprobeerd. Als dit consistent gebeurt, moet u de duur van uw onderhouds venster opnieuw evalueren.
    * Als het onderhoudsvenster op Windows wordt overschreden, komt dat vaak doordat het installeren van een servicepack-update lang duurt.

    > [!NOTE]
    > Om te voor komen dat updates buiten een onderhouds venster op Ubuntu worden toegepast, `Unattended-Upgrade` moet u het pakket opnieuw configureren om automatische updates uit te scha kelen. Zie het [onderwerp automatische updates in de Ubuntu-Server handleiding](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)voor informatie over het configureren van het pakket.

13. Gebruik het veld **Opties voor opnieuw opstarten** om op te geven hoe opnieuw opstarten tijdens de implementatie moet worden afgehandeld. De volgende opties zijn beschikbaar: 
    * Opnieuw opstarten indien nodig (standaard)
    * Altijd opnieuw opstarten
    * Nooit opnieuw opstarten
    * Alleen opnieuw opstarten; met deze optie worden geen updates geïnstalleerd

    > [!NOTE]
    > De registersleutels onder [Registry keys used to manage restart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) (registersleutels voor het beheren van opnieuw opstarten) kunnen een gebeurtenis voor opnieuw opstarten veroorzaken als **Opties voor opnieuw opstarten** is ingesteld op **Nooit opnieuw opstarten**.

14. Wanneer u klaar bent met het configureren van de implementatie planning, selecteert u **maken**.

    ![Deelvenster Planningsinstellingen bijwerken](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Wanneer u klaar bent met het configureren van het implementatie schema voor een geselecteerde server die is ingeschakeld voor de Arc, selecteert u **controleren + maken**.

15. U keert nu terug naar het statusdashboard. Selecteer **implementatie planningen** om de implementatie planning weer te geven die u hebt gemaakt. Er worden Maxi maal 500 schema's weer gegeven. Als u meer dan 500 planningen hebt en u de volledige lijst wilt bekijken, raadpleegt u de REST API methode [Software-update configuraties-lijst](/rest/api/automation/softwareupdateconfigurations/list) . Geef API-versie 2019-06-01 of hoger op.

## <a name="schedule-an-update-deployment-programmatically"></a>Een update-implementatie via een programma plannen

Zie [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (software-updateconfiguraties: maken) als u wilt leren hoe u een update-implementatie kunt maken met de REST-API.

U kunt een voor beeld-runbook gebruiken om een wekelijkse update-implementatie te maken. Zie voor meer informatie over dit runbook [Create a weekly update deployment for one or more VMs in a resource group](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group) (een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep).

## <a name="check-deployment-status"></a>Implementatie status controleren

Nadat de geplande implementatie is gestart, ziet u de status ervan op het tabblad **geschiedenis** onder **Update beheer**. Als de implementatie momenteel wordt uitgevoerd, is de status **Wordt uitgevoerd**. Wanneer de implementatie is voltooid, verandert de status in **geslaagd**. Als er fouten zijn met een of meer updates in de implementatie, is de status **mislukt**.

## <a name="view-results-of-a-completed-update-deployment"></a>Resultaten van een voltooide update-implementatie weer geven

Wanneer de implementatie is voltooid, kunt u deze selecteren om de resultaten weer te geven.

[![Het dash board voor de implementatie status bijwerken voor een specifieke implementatie](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

Onder **Update resultaten** bevat een samen vatting het totale aantal updates en de implementatie resultaten op de doel-vm's. In de tabel aan de rechter kant ziet u een gedetailleerde uitsplitsing van de updates en de installatie resultaten voor elke.

De beschikbare waarden zijn:

* **Niet geprobeerd** : de update is niet geïnstalleerd omdat er onvoldoende tijd beschikbaar was op basis van de gedefinieerde duur van het onderhouds venster.
* **Niet geselecteerd** : de update is niet geselecteerd voor implementatie.
* **Geslaagd** : de update is voltooid.
* **Mislukt** : de update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer **uitvoer** om de taak stroom te bekijken van het runbook dat verantwoordelijk is voor het beheren van de update-implementatie op de doel-vm's.

Selecteer **Fouten** voor gedetailleerde informatie over fouten die zijn opgetreden tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [waarschuwingen maken voor updatebeheer](configure-alerts.md)voor meer informatie over het maken van waarschuwingen om u te waarschuwen over update-implementatie resultaten.
* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
