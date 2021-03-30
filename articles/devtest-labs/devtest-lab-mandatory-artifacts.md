---
title: Verplichte artefacten opgeven voor uw Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het opgeven van verplichte artefacten die moeten worden geïnstalleerd voordat u een door de gebruiker geselecteerde artefacten op virtuele machines (Vm's) in het lab kunt installeren.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0677f8bea35cb34735fdcf34e717eea349fad8bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480300"
---
# <a name="specify-mandatory-artifacts-for-your-lab-in-azure-devtest-labs"></a>Verplichte artefacten opgeven voor uw Lab in Azure DevTest Labs
Als eigenaar van een lab kunt u verplichte artefacten opgeven die worden toegepast op elke machine die in het lab wordt gemaakt. Stel dat elke machine in uw lab moet worden verbonden met uw bedrijfs netwerk. In dit geval moet elke test gebruiker tijdens het maken van de virtuele machine een domein lidmaatschaps artefact toevoegen om er zeker van te zijn dat hun computer is verbonden met het bedrijfs domein. Met andere woorden: Lab-gebruikers moeten in wezen een machine opnieuw maken voor het geval ze verg eten verplichte artefacten op hun computer toe te passen. Als eigenaar van een Lab maakt u het lidmaatschap van het domein als verplicht artefact in uw Lab. Met deze stap zorgt u ervoor dat elke computer is verbonden met het bedrijfs netwerk en dat de tijd en inspanningen voor uw test gebruikers worden bespaard.
 
Andere verplichte artefacten kunnen een algemeen hulp programma bevatten dat uw team gebruikt, of een platform-gerelateerde beveiligings pakket dat elke machine standaard moet hebben, enzovoort. Kortom, alle algemene software die elke computer in uw lab moet een verplicht artefact zijn. Als u een aangepaste installatie kopie maakt van een computer waarop verplichte artefacten zijn toegepast en vervolgens een nieuwe machine maakt op basis van die installatie kopie, worden de verplichte artefacten opnieuw op de machine toegepast tijdens het maken. Dit gedrag houdt ook in dat hoewel de aangepaste installatie kopie oud is, elke keer dat u een machine maakt, de meest recente versie van de verplichte artefacten wordt toegepast tijdens de aanmaak stroom. 
 
Alleen artefacten die geen para meters hebben, worden als verplicht ondersteund. Uw Lab-gebruiker hoeft geen aanvullende para meters in te voeren tijdens het maken van het lab en het proces voor het maken van een virtuele machine zo eenvoudig mogelijk maakt. 

## <a name="specify-mandatory-artifacts"></a>Verplichte artefacten opgeven
U kunt verplichte artefacten voor Windows-en Linux-machines afzonderlijk selecteren. U kunt de volg orde van deze artefacten ook wijzigen, afhankelijk van de volg orde waarin u deze wilt Toep assen. 

1. Selecteer op de start pagina van uw Lab **configuratie en beleid** onder **instellingen**. 
3. Selecteer **verplichte artefacten** onder **externe resources**. 
4. Selecteer **bewerken** in de sectie **Windows** of het gedeelte **Linux** . In dit voor beeld wordt de **Windows** -optie gebruikt. 

    ![Pagina verplichte artefacten-knop bewerken](media/devtest-lab-mandatory-artifacts/mandatory-artifacts-edit-button.png)
4. Selecteer een artefact. In dit voor beeld wordt de optie **7-zip** gebruikt. 
5. Selecteer op de pagina **artefact toevoegen** de optie **toevoegen**. 

    ![Pagina verplichte artefacten-toevoegen 7-zip](media/devtest-lab-mandatory-artifacts/add-seven-zip.png)
6. Als u een ander artefact wilt toevoegen, selecteert u het artikel en selecteert u **toevoegen**. In dit voor beeld wordt **Chrome** toegevoegd als het tweede verplichte artefact.

    ![Pagina verplichte artefacten: Chrome toevoegen](media/devtest-lab-mandatory-artifacts/add-chrome.png)
7. Op de pagina **verplichte artefacten** wordt een bericht weer gegeven met het aantal geselecteerde artefacten. Als u op het bericht klikt, ziet u de artefacten die u hebt geselecteerd. Selecteer **Opslaan** om op te slaan. 

    ![Pagina verplichte artefacten-artefacten opslaan](media/devtest-lab-mandatory-artifacts/save-artifacts.png)
8. Herhaal de stappen om verplichte artefacten voor Linux Vm's op te geven. 
    
    ![Pagina verplichte artefacten-Windows-en Linux-artefacten](media/devtest-lab-mandatory-artifacts/windows-linux-artifacts.png)
9. Als u een artefact wilt **verwijderen** uit de lijst, selecteert u **... (weglatings tekens)** aan het einde van de rij en selecteer **verwijderen**. 
10. Als u artefacten in de lijst wilt **ordenen** , houdt u de muis aanwijzer boven het artefact, selecteert u **... (weglatings tekens)** die aan het begin van de rij wordt weer gegeven en sleep het item naar de nieuwe positie. 
11. Als u verplichte artefacten in het Lab wilt opslaan, selecteert u **Opslaan**. 

    ![Pagina verplichte artefacten: artefacten opslaan in Lab](media/devtest-lab-mandatory-artifacts/save-to-lab.png)
12. Sluit de pagina **configuratie en beleid** (Selecteer **X** in de rechter bovenhoek) om terug te gaan naar de start pagina van uw Lab.  

## <a name="delete-a-mandatory-artifact"></a>Een verplicht artefact verwijderen
Voer de volgende acties uit om een verplicht artefact uit een lab te verwijderen: 

1. Selecteer **configuratie en beleid** onder **instellingen**. 
2. Selecteer **verplichte artefacten** onder **externe resources**. 
3. Selecteer **bewerken** in de sectie **Windows** of het gedeelte **Linux** . In dit voor beeld wordt de **Windows** -optie gebruikt. 
4. Selecteer het bericht met het aantal verplichte artefacten bovenaan. 

    ![Pagina verplichte artefacten: Selecteer het bericht](media/devtest-lab-mandatory-artifacts/select-message-artifacts.png)
5. Selecteer op de pagina **geselecteerde artefacten** **... (weglatings tekens)** om het artefact te verwijderen en selecteer **verwijderen**. 
    
    ![Pagina verplichte artefacten-artefact verwijderen](media/devtest-lab-mandatory-artifacts/remove-artifact.png)
6. Selecteer **OK** om de **geselecteerde artefacten** pagina te sluiten. 
7. Selecteer **Opslaan** op de pagina **verplichte artefacten** .
8. Herhaal de stappen voor **Linux** -installatie kopieën, indien nodig. 
9. Selecteer **Opslaan** om alle wijzigingen in het lab op te slaan. 

## <a name="view-mandatory-artifacts-when-creating-a-vm"></a>Verplichte artefacten weer geven bij het maken van een VM
Als test gebruiker kunt u nu de lijst met verplichte artefacten bekijken tijdens het maken van een virtuele machine in het lab. U kunt geen verplichte artefacten die in het lab zijn ingesteld, bewerken of verwijderen door de eigenaar van uw Lab.

1. Op de start pagina van uw Lab selecteert u **overzicht** in het menu.
2. Selecteer **+ toevoegen** om een virtuele machine toe te voegen aan het lab. 
3. Selecteer een **basis installatie kopie**. In dit voor beeld wordt **Windows Server versie 1709** gebruikt.
4. U ziet dat er een bericht wordt weer gegeven voor **artefacten** met het aantal verplichte artefacten dat is geselecteerd. 
5. Selecteer **artefacten**. 
6. Controleer of u de **vereiste artefacten** ziet die u hebt opgegeven in de configuratie en het beleid van het lab. 

    ![Een VM-verplicht artefacten maken](media/devtest-lab-mandatory-artifacts/create-vm-artifacts.png)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een Git-artefact opslagplaats aan een Lab](devtest-lab-add-artifact-repo.md).

