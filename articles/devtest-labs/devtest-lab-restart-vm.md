---
title: Een VM opnieuw opstarten in een lab in Azure DevTest Labs | Microsoft Docs
description: In dit artikel worden de stappen beschreven voor het snel en eenvoudig opnieuw opstarten van virtuele machines (VM) in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d2bcbcbea613aa84da91789fb9f118afd7023fab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481986"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Een virtuele machine opnieuw starten in een lab in Azure DevTest Labs
U kunt snel en eenvoudig een virtuele machine in DevTest Labs opnieuw opstarten door de stappen in dit artikel te volgen. Houd rekening met het volgende voordat u een virtuele machine opnieuw opstart:

- De virtuele machine moet worden uitgevoerd om de functie voor opnieuw opstarten in te scha kelen.
- Als een gebruiker is verbonden met een actieve virtuele machine wanneer de computer opnieuw wordt opgestart, moeten ze opnieuw verbinding maken met de virtuele machine nadat deze opnieuw is opgestart.
- Als er een artefact wordt toegepast wanneer u de virtuele machine opnieuw opstart, ontvangt u een waarschuwing dat het artefact mogelijk niet wordt toegepast.

    ![Waarschuwing bij het opnieuw opstarten bij het Toep assen van artefacten](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Als de virtuele machine is vastgelopen tijdens het Toep assen van een artefact, kunt u de functie VM opnieuw opstarten gebruiken om het probleem op te lossen.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Stappen voor het opnieuw opstarten van een virtuele machine in een lab in Azure DevTest Labs
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs het lab dat de virtuele machine bevat die u opnieuw wilt opstarten.
1. Selecteer in het linkerdeel venster **mijn virtual machines**.
1. Selecteer een actieve virtuele machine in de lijst met Vm's.
1. Selecteer boven in het deel venster VM-beheer de optie **opnieuw opstarten**.

    ![Knop VM opnieuw opstarten](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Bewaak de status van het opnieuw starten door het pictogram **meldingen** rechtsboven in het venster te selecteren.

    ![De status van het opnieuw opstarten van de VM weer geven](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

U kunt een actieve virtuele machine ook opnieuw starten door het beletsel teken (...) te selecteren in de lijst met **mijn virtual machines**.

![De VM opnieuw starten via een weglatings teken](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Volgende stappen
* Zodra deze opnieuw is gestart, kunt u opnieuw verbinding maken met de virtuele machine door **verbinding maken** te selecteren in het deel venster beheer.
* De [Galerie met DevTest Labs Azure Resource Manager Quick](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) Start verkennen
