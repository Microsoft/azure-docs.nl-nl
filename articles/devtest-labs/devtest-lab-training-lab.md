---
title: Azure DevTest Labs voor training gebruiken | Microsoft Docs
description: In dit artikel vindt u gedetailleerde stappen die u kunt volgen om een lab in te stellen voor training in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2de9550c62f04286a4f9ad42238bfefb9846477
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294846"
---
# <a name="use-azure-devtest-labs-for-training"></a>Azure DevTest Labs gebruiken voor training
Azure DevTest Labs kunnen worden gebruikt voor het implementeren van veel belang rijke scenario's naast dev/test. Een van deze scenario's is het instellen van een Lab voor training. Met Azure DevTest Labs kunt u een lab maken waarin u aangepaste sjablonen kunt opgeven die elke getrainde kan gebruiken om identieke en geïsoleerde omgevingen te maken voor training. U kunt beleids regels Toep assen om ervoor te zorgen dat de trainings omgevingen alleen beschikbaar zijn voor elke getrainde wanneer ze deze nodig hebben en voldoende resources bevatten, zoals virtuele machines, die vereist zijn voor de training. Ten slotte kunt u het lab eenvoudig delen met trainees, dat ze met één klik kunnen openen.

![DevTest Labs gebruiken voor training](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs voldoet aan de volgende vereisten die nodig zijn voor het uitvoeren van training in elke virtuele omgeving: 

* Trainees kan geen Vm's zien die zijn gemaakt door andere trainees
* Elke trainings machine moet identiek zijn
* Trainees kunnen snel hun trainings omgevingen inrichten
* Beheer kosten door ervoor te zorgen dat trainees niet meer Vm's kan ophalen dan nodig is voor de training en om ook Vm's te kunnen afsluiten wanneer ze deze niet gebruiken
* Deel het trainings Lab eenvoudig met elke getrainde
* Het trainings Lab opnieuw en opnieuw gebruiken

In dit artikel vindt u informatie over diverse Azure DevTest Labs functies die kunnen worden gebruikt om te voldoen aan de eerder beschreven trainings vereisten en gedetailleerde stappen die u kunt volgen om een Lab voor training in te stellen.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Training implementeren met Azure DevTest Labs
1. **Het lab maken** 
   
    Labs is het begin punt in Azure DevTest Labs. Nadat u een Lab hebt gemaakt, kunt u taken zoals gebruikers toevoegen (trainees) aan het Lab uitvoeren, beleids regels instellen om de kosten te controleren, VM-installatie kopieën definiëren die snel kunnen worden gemaakt en meer.   
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken in Azure DevTest Labs](devtest-lab-create-lab.md) |Meer informatie over het maken van een lab in Azure DevTest Labs in het Azure Portal. |
2. **Maak binnen enkele minuten trainings-Vm's met behulp van kant-en-klare Marketplace-installatie kopieën en aangepaste installatie kopieën** 
   
    U kunt kant-en-klare installatie kopieën kiezen uit een groot aantal installatie kopieën op de Azure Marketplace en deze beschikbaar maken voor de trainees in het lab. Als de kant-en-klare installatie kopieën niet voldoen aan uw vereisten, kunt u een aangepaste installatie kopie maken door een Lab-VM te maken met behulp van een kant-en-klare installatie kopie vanuit Azure Marketplace, alle software die u nodig hebt voor de training te installeren en de VM op te slaan als aangepaste installatie kopie in het lab. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Azure Marketplace-installatiekopieën configureren](devtest-lab-configure-marketplace-images.md) |Meer informatie over hoe u installatie kopieën van Azure Marketplace kunt toestaan. u kunt alleen de installatie kopieën die u voor de training wilt selecteren beschikbaar maken. |
   | [Een aangepaste installatiekopie maken](devtest-lab-create-template.md) |Maak een aangepaste installatie kopie door de software die u nodig hebt voor de training vooraf te installeren, zodat trainees snel een virtuele machine kan maken met behulp van de aangepaste installatie kopie. |
3. **Herbruikbare sjablonen voor trainings machines maken** 
   
    Een formule in Azure DevTest Labs is een lijst met standaard eigenschaps waarden die worden gebruikt om een virtuele machine te maken. U kunt een formule in het lab maken door een installatie kopie, een VM-grootte (een combi natie van CPU en RAM) en een virtueel netwerk te kiezen. Elke trainer kan de formule in het lab zien en gebruiken om een virtuele machine te maken. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [DevTest Labs-formules beheren om Vm's te maken](devtest-lab-manage-formulas.md) |Meer informatie over het maken van een formule door een afbeelding, VM-grootte (combi natie van CPU en RAM) en een virtueel netwerk op te halen. |
4. **Beheerkosten**
   
    Met Azure DevTest Labs kunt u een beleid in het lab instellen om het maximum aantal Vm's op te geven dat door een getrainer in het Lab kan worden gemaakt. 
   
    Als u een training voor meerdere dagen wilt uitvoeren en u alle Vm's op een bepaald tijdstip van de dag wilt stoppen en vervolgens de volgende dag automatisch opnieuw wilt opstarten, kunt u dit eenvoudig doen door automatisch afsluiten en beleid voor automatisch starten in te stellen in het lab. 
   
    Ten slotte, wanneer de training is voltooid, kunt u alle Vm's tegelijk verwijderen door één Power shell-script uit te voeren. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Beleid voor lab maken](devtest-lab-set-lab-policy.md) |Beheer kosten door beleids regels in te stellen in het lab. |
   | [Alle Lab-Vm's verwijderen met een Power shell-script](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Verwijder alle Labs in één bewerking wanneer de training is voltooid. |
5. **Deel het lab met elke getrainde**
   
    Labs kan rechtstreeks worden geopend met behulp van een koppeling die u deelt met uw trainees. Uw trainees hebben zelfs een Azure-account nodig, zolang ze een [Microsoft-account](devtest-lab-faq.md#what-is-a-microsoft-account)hebben. Trainees kan geen Vm's zien die zijn gemaakt door andere trainees.  
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een Train toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Gebruik de Azure Portal om trainees toe te voegen aan uw trainings Lab. |
   | [Trainees toevoegen aan het lab met behulp van een Power shell-script](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Gebruik Power shell om het toevoegen van trainees aan uw trainings Lab te automatiseren. |
   | [Een koppeling naar het lab ophalen](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Meer informatie over hoe een Lab rechtstreeks kan worden geopend via een Hyper link. |
6. **Het lab opnieuw en opnieuw gebruiken** 
   
    U kunt het maken van een Lab automatiseren, met inbegrip van aangepaste instellingen, door een resource manager-sjabloon te maken en deze te gebruiken om opnieuw identieke lessen te maken. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken met een resource manager-sjabloon](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Maak Labs in Azure DevTest Labs met behulp van Resource Manager-sjablonen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
