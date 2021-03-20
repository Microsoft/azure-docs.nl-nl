---
title: Een virtuele machine opnieuw implementeren in een lab in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over hoe u een virtuele machine opnieuw implementeert (van het ene naar het andere Azure-knoop punt naar het andere) in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a38b112165b893d877733b967c21bb62b20ca2f6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530315"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Een virtuele machine opnieuw implementeren in een lab in Azure DevTest Labs
Als u geen verbinding kunt maken met een virtuele machine (VM) in een lab via een verbinding met een extern bureau blad, implementeert u de VM opnieuw en probeert u opnieuw verbinding te maken. Wanneer u een virtuele machine opnieuw implementeert, verplaatst DevTest Labs de VM van het knoop punt waarop deze wordt uitgevoerd naar een nieuw knoop punt in de Azure-infra structuur. Vervolgens wordt de virtuele machine gestart en blijven alle configuratie opties en bijbehorende resources behouden. Met deze functie bespaart u de tijd die wordt besteed aan het oplossen van problemen met verbinding met extern bureau blad of toepassing op Windows-Vm's in het lab. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Stappen voor het opnieuw implementeren van een virtuele machine in een Lab 
Voer de volgende stappen uit om een virtuele machine in een lab in Azure DevTest Labs opnieuw te implementeren: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het lab dat de VM bevat die u opnieuw wilt implementeren.  
4. Selecteer in het linkerdeel venster **mijn virtual machines**. 
5. Selecteer een virtuele machine in de lijst met Vm's.
6. Selecteer op de pagina virtuele machine voor uw VM opnieuw **implementeren** onder **bewerkingen** in het menu links.

    ![Scherm opname toont de pagina virtuele machine waarvoor opnieuw implementeren is geselecteerd.](media/devtest-lab-redeploy-vm/redeploy.png)
7. Lees de informatie op de pagina en selecteer vervolgens de knop opnieuw **implementeren** . 9. Controleer de status van de bewerking voor opnieuw implementeren in het venster **meldingen** .

    ![Status opnieuw implementeren](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Volgende stappen
Zie het [formaat van een virtuele machine wijzigen](devtest-lab-resize-vm.md)voor meer informatie over het wijzigen van het formaat van een virtuele machine in azure DevTest Labs.


