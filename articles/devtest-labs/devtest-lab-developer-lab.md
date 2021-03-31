---
title: Azure DevTest Labs voor ontwikkel aars gebruiken | Microsoft Docs
description: Meer informatie over Azure DevTest Labs-functies die kunnen worden gebruikt om te voldoen aan de vereisten voor ontwikkel aars en gedetailleerde stappen die u kunt volgen om een lab in te stellen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4f195f843e30ad1a56bad7d5b9ca9e73888783b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89296240"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Azure DevTest Labs voor ontwikkel aars gebruiken
Azure DevTest Labs kan worden gebruikt voor het implementeren van allerlei scenario's. Eén van de belangrijkste scenario's is het inzetten van DevTest Labs voor het hosten van ontwikkelmachines voor ontwikkelaars. In dit scenario biedt DevTest Labs deze voordelen:

- Ontwikkel aars kunnen snel hun ontwikkel machines op aanvraag inrichten.
- Ontwikkelaars kunnen hun ontwikkelmachines eenvoudig aanpassen als dat nodig is.
- Beheerders kunnen kosten bepalen door ervoor te zorgen dat:
  - Ontwikkel aars kunnen niet meer Vm's ophalen dan ze nodig hebben voor de ontwikkeling.
  - Vm's worden afgesloten wanneer ze niet worden gebruikt. 

![DevTest Labs gebruiken voor training](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

In dit artikel vindt u informatie over diverse Azure DevTest Labs functies die kunnen worden gebruikt om te voldoen aan de vereisten voor ontwikkel aars en gedetailleerde stappen die u kunt volgen om een lab in te stellen.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Ontwikkel omgevingen met Azure DevTest Labs implementeren
1. **Het lab maken** 
   
    Labs is het begin punt in Azure DevTest Labs. Nadat u een Lab hebt gemaakt, kunt u taken zoals het toevoegen van gebruikers (ontwikkel aars) aan het Lab uitvoeren, beleids regels instellen voor het beheren van de kosten, het definiëren van VM-installatie kopieën die snel kunnen worden gemaakt en meer.  
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken in Azure DevTest Labs](devtest-lab-create-lab.md) |Meer informatie over het maken van een lab in Azure DevTest Labs in het Azure Portal. |
2. **Binnen enkele minuten Vm's maken met behulp van kant-en-klare Marketplace-installatie kopieën en aangepaste installatie kopieën** 
   
    U kunt kant-en-klare installatie kopieën kiezen uit een groot aantal installatie kopieën op de Azure Marketplace en deze beschikbaar maken in het lab. Als de kant-en-klare installatie kopieën niet voldoen aan uw vereisten, kunt u een aangepaste installatie kopie maken door een Lab-VM te maken met een kant-en-klare installatie kopie vanuit Azure Marketplace, alle software die u nodig hebt, te installeren en de virtuele machine op te slaan als een aangepaste installatie kopie in het lab.

    Als u aangepaste installatie kopieën wilt gebruiken, kunt u een image Factory gebruiken om uw installatie kopieën te maken en te distribueren. Een image Factory is een oplossing voor configuratie-as-code die regel matig uw geconfigureerde installatie kopieën bouwt en distribueert. Dit bespaart de tijd die nodig is om het systeem hand matig te configureren nadat een virtuele machine is gemaakt met het basis besturingssysteem.
  
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Azure Marketplace-installatiekopieën configureren](devtest-lab-configure-marketplace-images.md) |Meer informatie over hoe u installatie kopieën van Azure Marketplace kunt toestaan, zodat alleen de gewenste installatie kopieën voor de ontwikkel aars beschikbaar worden gemaakt.|
   | [Een aangepaste installatiekopie maken](devtest-lab-create-template.md) |Maak een aangepaste installatie kopie door de software die u nodig hebt, vooraf te installeren, zodat ontwikkel aars snel een virtuele machine kunnen maken met behulp van de aangepaste installatie kopie.|
   | [Meer informatie over image Factory](./devtest-lab-faq.md#blog-post) |Bekijk een video met een beschrijving van het instellen en gebruiken van een image Factory.|

3. **Herbruikbare sjablonen maken voor ontwikkel aars-computers** 
   
    Een formule in Azure DevTest Labs is een lijst met standaard eigenschaps waarden die worden gebruikt om een virtuele machine te maken. U kunt een formule in het lab maken door een installatie kopie, een VM-grootte (een combi natie van CPU en RAM) en een virtueel netwerk te kiezen. Elke ontwikkelaar kan de formule in het lab bekijken en gebruiken om een virtuele machine te maken. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [DevTest Labs-formules beheren om Vm's te maken](devtest-lab-manage-formulas.md) |Meer informatie over het maken van een formule door een afbeelding, VM-grootte (combi natie van CPU en RAM) en een virtueel netwerk op te halen.|

4. **Artefacten maken voor het inschakelen van flexibele VM-aanpassing**

   Artefacten worden gebruikt voor het implementeren en configureren van uw toepassing nadat een virtuele machine is ingericht. Artefacten kunnen zijn:

   - Hulpprogram ma's die u wilt installeren op de VM, zoals agents, Fiddler en Visual Studio.
   - Acties die u wilt uitvoeren op de VM, zoals het klonen van een opslag plaats.
   - Toepassingen die u wilt testen.

   Veel artefacten zijn al out-of-the-box beschikbaar. U kunt uw eigen aangepaste artefacten maken als u meer aanpassing wilt voor uw specifieke behoeften.

   Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Aangepaste artefacten maken voor uw DevTest Labs VM](devtest-lab-artifact-author.md) |Maak uw eigen aangepaste artefacten voor de virtuele machines in uw Lab.|
   | [Voeg een Git-opslag plaats toe om aangepaste artefacten en Azure Resource Manager sjablonen op te slaan voor gebruik in Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Meer informatie over het opslaan van uw aangepaste artefacten in uw eigen persoonlijke Git-opslag plaats.|

5. **Beheerkosten**
   
    Met Azure DevTest Labs kunt u een beleid in het lab instellen om het maximum aantal Vm's op te geven dat door een ontwikkelaar in het Lab kan worden gemaakt. 
   
    Als uw ontwikkelaars team een werk schema heeft en u alle Vm's op een bepaald tijdstip van de dag wilt stoppen en vervolgens de volgende dag automatisch opnieuw wilt opstarten, kunt u dit eenvoudig doen door automatisch afsluiten en beleid voor automatisch starten in te stellen in het lab. 
   
    Ten slotte, wanneer het ontwikkelen van apps is voltooid, kunt u alle Vm's tegelijk verwijderen door één Power shell-script uit te voeren. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Beleid voor lab maken](devtest-lab-set-lab-policy.md) |Beheer kosten door beleids regels in te stellen in het lab. |
   | [Alle Lab-Vm's verwijderen met een Power shell-script](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Verwijder alle Labs in één bewerking wanneer de ontwikkeling is voltooid.|

1. **Een virtueel netwerk toevoegen aan een VM** 
   
    DevTest Labs maakt een nieuw virtueel netwerk (VNET) wanneer een lab wordt gemaakt. Als u uw eigen VNET hebt geconfigureerd, bijvoorbeeld door gebruik te maken van ExpressRoute of site-naar-site VPN, kunt u dit VNET toevoegen aan de virtuele netwerk instellingen van uw Lab, zodat deze beschikbaar zijn wanneer u virtuele machines maakt.

    Daarnaast is er een Azure Active Directory domein samenvoegings artefact beschikbaar waarmee een VM wordt toegevoegd aan een domein wanneer de virtuele machine wordt gemaakt. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een virtueel netwerk configureren in Azure DevTest Labs](devtest-lab-configure-vnet.md) |Meer informatie over het configureren van een virtueel netwerk in Azure DevTest Labs met behulp van de Azure Portal.|

6. **Het lab delen met elke ontwikkelaar**
   
    Labs kan rechtstreeks worden geopend met behulp van een koppeling die u deelt met uw ontwikkel aars. Ze hebben zelfs nog geen Azure-account nodig, zolang ze een [Microsoft-account](devtest-lab-faq.md#what-is-a-microsoft-account)hebben. Ontwikkel aars kunnen geen Vm's zien die zijn gemaakt door andere ontwikkel aars.  
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een ontwikkelaar toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Gebruik de Azure Portal om ontwikkel aars toe te voegen aan uw Lab.|
   | [Ontwikkel aars toevoegen aan het lab met behulp van een Power shell-script](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Gebruik Power shell om ontwikkel aars automatisch toe te voegen aan uw Lab. |
   | [Een koppeling naar het lab ophalen](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Meer informatie over hoe ontwikkel aars rechtstreeks toegang hebben tot een lab via een Hyper link.|

7. **Het maken van een Lab automatiseren voor meer teams** 
   
    U kunt het maken van een Lab automatiseren, met inbegrip van aangepaste instellingen, door een resource manager-sjabloon te maken en deze te gebruiken om opnieuw identieke lessen te maken. 
   
    Klik op de koppelingen in de volgende tabel voor meer informatie.
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken met een resource manager-sjabloon](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Maak Labs in Azure DevTest Labs met behulp van Resource Manager-sjablonen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
