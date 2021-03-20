---
title: Installatie kopie-instellingen voor Azure Marketplace configureren in Azure DevTest Labs
description: Configureren welke Azure Marketplace-installatie kopieën kunnen worden gebruikt bij het maken van een virtuele machine in Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86512432"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Installatie kopie-instellingen voor Azure Marketplace configureren in Azure DevTest Labs
DevTest Labs biedt ondersteuning voor het maken van Vm's op basis van installatie kopieën van Azure Marketplace, afhankelijk van hoe u Azure Marketplace-installatie kopieën hebt geconfigureerd om te worden gebruikt in uw Lab. In dit artikel wordt beschreven hoe u kunt opgeven welke Azure Marketplace-installatie kopieën kunnen worden gebruikt bij het maken van Vm's in een lab. Zo zorgt u ervoor dat uw team alleen toegang heeft tot de Marketplace-installatie kopieën die ze nodig hebben. 

## <a name="specify-allowed-images-for-creating-vms"></a>Toegestane installatie kopieën voor het maken van Vm's opgeven
Volg deze stappen om op te geven welke installatie kopieën van Azure Marketplace zijn toegestaan bij het maken van een virtuele machine. 

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs uw Lab. 
4. Op de start pagina van de pagina Lab selecteert u **configuratie en beleid**.
5. Selecteer op de pagina **configuratie en beleid** van Lab onder basis van **Virtual Machine bases** de optie **Marketplace-installatie kopieën**.
6. Geef op of u wilt dat alle gekwalificeerde Azure Marketplace-installatie kopieën beschikbaar zijn voor gebruik als basis van een nieuwe virtuele machine. Als u **Ja** selecteert, zijn alle Azure Marketplace-installatie kopieën die voldoen aan de volgende criteria, toegestaan in het Lab:
   
   * De installatie kopie maakt één virtuele machine **en**
   * De installatie kopie gebruikt Azure Resource Manager voor het inrichten van Vm's **en**
   * De installatie kopie vereist geen aanschaf van een extra licentie plan
     
     Als u geen installatie kopieën wilt toestaan of als u wilt opgeven welke installatie kopieën kunnen worden gebruikt, selecteert u **Nee**.
     
     ![Optie waarmee alle Marketplace-installatie kopieën kunnen worden gebruikt als basis installatie kopieën voor virtuele machines](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Als u **Nee** naar de vorige stap selecteert, is het selectie vakje **toegestane installatie kopieën/alles selecteren** ingeschakeld. 
   U kunt deze optie samen met het zoekvak gebruiken om snel alle items die in de lijst worden weer gegeven, te selecteren of te deselecteren.
   * Selecteer de installatie kopieën van Azure Marketplace die u afzonderlijk wilt toestaan voor het maken van VM'S door het selectie vakje van elke afbeelding in te scha kelen.
   * Selecteer niets in de lijst als u geen installatie kopieën van Azure Marketplace wilt toestaan die in het lab moeten worden gebruikt.
   
     ![U kunt opgeven welke Azure Marketplace-installatie kopieën kunnen worden gebruikt als basis installatie kopieën voor virtuele machines](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Problemen oplossen
Als u geen specifieke installatie kopie kunt vinden om in te scha kelen voor de test omgeving, voert u de volgende stappen uit: 

- Controleer of u de installatie kopie kunt weer geven tijdens het maken van een compute-VM.
- De installatie kopie is mogelijk niet beschikbaar in het type abonnement dat u gebruikt. Neem contact op met de abonnements beheerder voor het type abonnement (bijvoorbeeld: MSDN, gratis, betalen naar gebruik, enzovoort). 
- Ondersteuning voor gen 2-installatie kopieën in DevTest Labs is beperkt. Als zowel versies van 1 en Diversen 2 beschikbaar zijn voor een installatie kopie, toont DevTest Labs alleen de versie 1 van de installatie kopie bij het maken van een virtuele machine. Tijdelijke oplossing is het maken van een aangepaste installatie kopie van de gen 2 buiten het lab en deze gebruikt om een virtuele machine te maken. Als er slechts versie 1 van de installatie kopie beschikbaar is, wordt deze in de lijst door DevTest Labs ondersteund en weer gegeven. 
      


## <a name="next-steps"></a>Volgende stappen
Zodra u hebt geconfigureerd hoe Azure Marketplace-installatie kopieën zijn toegestaan tijdens het maken van een virtuele machine, is de volgende stap het [toevoegen van een virtuele machine aan uw Lab](devtest-lab-add-vm.md).

