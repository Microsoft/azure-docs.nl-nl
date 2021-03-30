---
title: Een Lab of virtuele machine in een lab in Azure DevTest Labs verwijderen
description: In dit artikel leest u hoe u een Lab verwijdert of een virtuele machine in een Lab verwijdert met behulp van de Azure Portal (Azure DevTest Labs).
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 495fb98f3da41a47d316dd64554ba616ede0af47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481218"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Een Lab of virtuele machine in een lab in Azure DevTest Labs verwijderen
In dit artikel wordt beschreven hoe u een Lab of virtuele machine in een Lab verwijdert.

## <a name="delete-a-lab"></a>Een Lab verwijderen
Wanneer u een DevTest Labs-exemplaar uit een resource groep verwijdert, voert de DevTest Labs-service de volgende acties uit: 

- Alle resources die automatisch zijn gemaakt op het moment van het maken van het lab, worden automatisch verwijderd. De resource groep zelf wordt niet verwijderd. Als u hand matig resources deze resource groep hebt gemaakt, worden deze niet verwijderd door de service. 
- Alle virtuele machines in het lab en de resource groepen die zijn gekoppeld aan deze Vm's, worden automatisch verwijderd. Wanneer u een virtuele machine in een Lab maakt, maakt de service bronnen (schijf, netwerk interface, openbaar IP-adres, enz.) voor de virtuele machine in een afzonderlijke resource groep. Als u echter hand matig extra resources in deze resource groepen maakt, worden deze resources en de resource groep niet verwijderd door de service DevTest Labs. 

Voer de volgende acties uit om een lab te verwijderen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** in het menu aan de linkerkant, selecteer **DevTest Labs** voor het type service en selecteer het lab.

    ![Uw Lab selecteren](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Klik op de pagina **DevTest Lab** op **verwijderen** op de werk balk. 

    ![De knop Verwijderen](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Voer op de pagina **bevestiging** de **naam** van uw Lab in en selecteer **verwijderen**. 

    ![Bevestigen](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Als u de status van de bewerking wilt weer geven, selecteert u **meldingen** pictogram (Bel). 

    ![Meldingen](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Een virtuele machine in een Lab verwijderen
Als ik een virtuele machine in een Lab Verwijder, worden sommige resources (niet alle) die zijn gemaakt op het moment van het maken van het lab, verwijderd. De volgende resources worden niet verwijderd: 

-   Sleutel kluis in de hoofd resource groep
-   Beschikbaarheidsset, load balancer, openbaar IP-adres in de resource groep van de virtuele machine. Deze resources worden gedeeld door meerdere Vm's in een resource groep. 

De virtuele machine, de netwerk interface en de schijf die aan de VM zijn gekoppeld, worden verwijderd. 

Als u een virtuele machine in een Lab wilt verwijderen, voert u de volgende handelingen uit: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** in het menu aan de linkerkant, selecteer **DevTest Labs** voor het type service en selecteer het lab.

    ![Uw Lab selecteren](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Selecteren **... (weglatings tekens)** voor de virtuele machine in de lijst met virtuele machines en selecteer **verwijderen**. 

    ![Virtuele machine in menu verwijderen](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Selecteer **OK** in het **bevestigings** venster. 
5. Als u de status van de bewerking wilt weer geven, selecteert u **meldingen** pictogram (Bel). 

Als u een VM wilt verwijderen van de **pagina virtuele machine**, selecteert u **verwijderen** in de werk balk, zoals wordt weer gegeven in de volgende afbeelding:

![VM verwijderen van VM-pagina](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Volgende stappen
Als u een Lab wilt maken, raadpleegt u de volgende artikelen: 

- [Een lab maken](devtest-lab-create-lab.md)
- [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)