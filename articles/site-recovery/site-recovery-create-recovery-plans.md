---
title: Herstel plannen maken/aanpassen in Azure Site Recovery
description: Meer informatie over het maken en aanpassen van herstel plannen voor herstel na nood gevallen met behulp van de Azure Site Recovery-service.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009718"
---
# <a name="create-and-customize-recovery-plans"></a>Herstelplannen maken en aanpassen

In dit artikel wordt beschreven hoe u een herstel plan voor failover kunt maken en aanpassen in [Azure site Recovery](site-recovery-overview.md). Voordat u begint, kunt u [meer te weten komen](recovery-plan-overview.md) over herstel plannen.

## <a name="create-a-recovery-plan"></a>Een herstelplan maken

1. Selecteer in de Recovery Services kluis **herstel plannen (site Recovery)**  >  **+ herstel plan**.
2. In **herstel plan maken** geeft u een naam op voor het plan.
3. Kies een bron en doel op basis van de computers in het plan en selecteer **Resource Manager** voor het implementatie model. De bron locatie moet machines hebben die zijn ingeschakeld voor failover en herstel. 

    **Failover** | **Bron** | **Doel** 
   --- | --- | ---
   Azure naar Azure | De Azure-regio selecteren | De Azure-regio selecteren
   VMware naar Azure | De configuratie server selecteren | Azure selecteren
   Fysieke machines naar Azure | De configuratie server selecteren | Azure selecteren   
   Hyper-V naar Azure | De naam van de Hyper-V-site selecteren | Azure selecteren
   Hyper-V (beheerd door VMM) naar Azure  | De VMM-server selecteren | Azure selecteren
  
    Houd rekening met het volgende:
    - U kunt een herstel plan gebruiken voor zowel failover naar Azure als failback vanuit Azure.
    - De bron locatie moet machines hebben die zijn ingeschakeld voor failover en herstel.
    - Een herstel plan kan machines met dezelfde bron en hetzelfde doel bevatten.
    - U kunt virtuele VMware-machines en virtuele Hyper-V-machines die worden beheerd door VMM, in hetzelfde abonnement toevoegen.
    - Virtuele VMware-machines en fysieke servers kunnen zich in hetzelfde abonnement bevindt.

4. Selecteer in **virtuele machines voor items selecteren** de machines (of replicatie groep) die u aan het plan wilt toevoegen. Klik vervolgens op **OK**.
    - Computers zijn standaard groep (groep 1) toegevoegd in het plan. Na een failover starten alle computers in deze groep op hetzelfde moment.
    - U kunt alleen computers selecteren in de bron-en doel locatie die u hebt opgegeven. 
5. Klik op **OK** om het plan te maken.

## <a name="add-a-group-to-a-plan"></a>Een groep toevoegen aan een plan

U maakt extra groepen en voegt machines toe aan verschillende groepen, zodat u per groep verschillende gedrag kunt opgeven. U kunt bijvoorbeeld opgeven wanneer computers in een groep moeten beginnen na een failover, of aangepaste acties per groep opgeven.

1. Klik in **herstel plannen** met de rechter muisknop op het abonnement dat > **aanpassen**. Nadat u een plan hebt gemaakt, bevinden alle machines die u hebt toegevoegd, zich standaard in de standaard groep 1.
2. Klik op **+ groep**. Een nieuwe groep wordt standaard genummerd in de volg orde waarin deze is toegevoegd. U kunt Maxi maal zeven groepen hebben.
3. Selecteer de computer die u naar de nieuwe groep wilt verplaatsen, klik op **groep wijzigen** en selecteer vervolgens de nieuwe groep. U kunt ook met de rechter muisknop op de groeps naam > **beveiligde items** klikken en machines toevoegen aan de groep. Een computer of replicatie groep kan slechts deel uitmaken van één groep in een herstel plan.


## <a name="add-a-script-or-manual-action"></a>Een script of hand matige actie toevoegen

U kunt een herstel plan aanpassen door een script of hand matige actie toe te voegen. Opmerking:

- Als u repliceert naar Azure, kunt u Azure Automation-runbooks integreren in uw herstel plan. [Meer informatie](site-recovery-runbook-automation.md).
- Als u virtuele Hyper-V-machines repliceert die worden beheerd door System Center VMM, kunt u een script maken op de on-premises VMM-server en dit toevoegen aan het herstel plan.
- Wanneer u een script toevoegt, wordt er een nieuwe set acties voor de groep toegevoegd. Een set vooraf-stappen voor groep 1 wordt bijvoorbeeld gemaakt met de naam *groep 1: voorbereidende stappen*. Alle voorafgaande stappen worden in deze set vermeld. U kunt een script alleen op de primaire site toevoegen als u een VMM-server hebt geïmplementeerd.
- Als u een hand matige actie toevoegt wanneer het herstel plan wordt uitgevoerd, stopt dit op het punt waarop u de hand matige actie hebt ingevoegd. In een dialoog venster wordt u gevraagd om op te geven dat de hand matige actie is voltooid.
- Volg de instructies in [dit artikel](hyper-v-vmm-recovery-script.md)om een script te maken op de VMM-server.
- Scripts kunnen worden toegepast tijdens de failover naar de secundaire site en tijdens het failback van de secundaire site naar de primaire locatie. Ondersteuning is afhankelijk van uw replicatie scenario:
    
    **Scenario** | **Failover** | **Herstel**
    --- | --- | --- 
    Azure naar Azure  | Runbook | Runbook
    VMware naar Azure | Runbook | NA 
    Hyper-V met VMM naar Azure | Runbook | Script
    Hyper-V-site naar Azure | Runbook | NA
    VMM naar secundaire VMM | Script | Script

1. Klik in het herstel plan op de stap waaraan de actie moet worden toegevoegd en geef op wanneer de actie moet worden uitgevoerd:
    1. Als u wilt dat de actie wordt uitgevoerd voordat de computers in de groep na een failover worden gestart, selecteert u voor **actie toevoegen**.
    1. Als u wilt dat de actie wordt uitgevoerd nadat de computers in de groep na een failover zijn gestart, selecteert u **post actie toevoegen**. Als u de positie van de actie wilt verplaatsen, selecteert u de knoppen **omhoog** of **omlaag** .
2. Selecteer in **actie invoegen** de optie **script** of **hand matige actie**.
3. Als u een hand matige actie wilt toevoegen, gaat u als volgt te werk:
    1. Typ een naam voor de actie en voer de actie-instructies in. De persoon die de failover uitvoert, ziet deze instructies.
    1. Geef op of u de hand matige actie wilt toevoegen voor alle typen failover (testen, failover, geplande failover (indien van toepassing)). Klik vervolgens op **OK**.
4. Als u een script wilt toevoegen, gaat u als volgt te werk:
    1. Als u een VMM-script wilt toevoegen, selecteert u **failover naar VMM-script** en typt u bij **pad naar script** het relatieve pad naar de share. Als de share zich bijvoorbeeld op \MSSCVMMLibrary\RPScripts bevindt \\ \<VMMServerName> , geeft u het pad op: \RPScripts\RPScript.PS1.
    1. Als u een Azure Automation-boek wilt toevoegen, geeft u het **Azure Automation account** op waarin het runbook zich bevindt en selecteert u het juiste **Azure-runbook-script**.
5. Voer een testfailover van het herstel plan uit om ervoor te zorgen dat het script werkt zoals verwacht.

## <a name="watch-a-video"></a>Bekijk een video

Bekijk een video waarin wordt getoond hoe u een herstel plan bouwt.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [uitvoeren van failovers](site-recovery-failover.md).  

    
