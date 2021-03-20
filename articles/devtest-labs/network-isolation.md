---
title: Netwerk isolatie in Azure DevTest Labs
description: Meer informatie over netwerk isolatie in Azure DevTest Labs.
ms.topic: article
ms.date: 08/25/2020
ms.openlocfilehash: 28bfa048a8a6666deb58a8ecfff38779c91d95b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96352929"
---
# <a name="network-isolation-in-devtest-labs"></a>Netwerk isolatie in DevTest Labs

Een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md) fungeert als een beveiligings grens en isoleert uw Azure-resources van het open bare Internet. U kunt ook een virtueel Azure-netwerk toevoegen aan uw on-premises netwerk om veilig verbinding te kunnen maken met uw resources op locatie. In DevTest Labs kunt u [alle virtuele lab-machines](devtest-lab-configure-vnet.md) en- [omgevingen op uw netwerk](connect-environment-lab-virtual-network.md) isoleren om ervoor te zorgen dat Lab-bronnen het netwerk beleid van de organisatie volgen. 

Als eigenaar van een lab kunt u er ook voor kiezen om het lab volledig te isoleren, wat betekent dat naast het isoleren van virtuele machines en omgevingen met het geselecteerde netwerk, u ook een Lab-opslag account en sleutel kluizen kunt isoleren die in uw abonnement zijn gemaakt. In dit artikel vindt u instructies voor het maken van een geïsoleerd Lab in een netwerk. 

Lees ook de volgende artikelen:

- [Hoe DevTest Labs Lab Storage-account gebruikt](encrypt-storage.md)
- [Hoe DevTest Labs gebruikmaakt van sleutel kluizen](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Netwerk isolatie wordt momenteel alleen ondersteund voor nieuwe Labs-creaties.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Stappen om netwerk isolatie in te scha kelen tijdens het maken van het lab

1. Ga tijdens het maken van het lab naar het tabblad **netwerken** .
1. U kunt een **standaard** netwerk selecteren dat door het lab voor u wordt gemaakt of een bestaand netwerk selecteren in de vervolg keuzelijst. U kunt alleen netwerken selecteren die zich in dezelfde regio en hetzelfde abonnement bevinden als die van Lab. 

    > [!div class="mx-imgBorder"]
    > ![Lab maken](./media/network-isolation/create-lab.png)
1. Selecteer een subnet.

    > [!div class="mx-imgBorder"]
    > ![Een subnet maken](./media/network-isolation/create-lab-subnet.png)
1. Als u ervoor kiest om Lab-resources (Lab-opslag account en sleutel kluis) te isoleren in het standaard netwerk, is er geen verdere actie nodig en zal het lab het isoleren van resources afhandelen.
 
    > [!div class="mx-imgBorder"]
    > ![Netwerkisolatie](./media/network-isolation/isolate-lab-resources.png)
1. Als u ervoor kiest om Lab-resources (Lab-opslag account en sleutel kluis) te isoleren met een bestaand netwerk dat u hebt geselecteerd, moet u de volgende stappen uitvoeren om te zorgen dat het lab in de geïsoleerde modus blijft werken. 
 
    > [!div class="mx-imgBorder"]
    > ![Bron isoleren](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > De eigenaar van het lab moet deze stappen volt ooien voordat er resources in het lab worden geconfigureerd en/of gemaakt.

### <a name="steps-to-follow-post-lab-creation"></a>Stappen voor het maken van een post Lab

1. Op de start pagina van het lab selecteert u de **resource groep** op de pagina **overzicht** . U ziet de pagina **resource groep** voor de resource groep die het Lab bevat. 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso Lab](./media/network-isolation/contoso-lab.png)
1. Selecteer het Azure-opslag account van het lab. De naamgevings Conventie voor het lab-opslag account is: een<*labNameWithoutInvalidCharacters* > *<> van vier cijfers* . Als de naam van het lab bijvoorbeeld contosolab is, kan de naam van het opslag account acontosolab1234 zijn.
 
   > [!div class="mx-imgBorder"]
   > ![Contoso-test](./media/network-isolation/contoso-test.png)
1. Ga op het opslag account naar firewalls en virtuele netwerken en zorg ervoor dat het selectie vakje vertrouwde micro soft-Services toegang geven tot dit opslag account is ingeschakeld. Omdat [DevTest Labs een vertrouwde micro soft-service is](../storage/common/storage-network-security.md#trusted-microsoft-services), kan de test omgeving normaal worden uitgevoerd in een geïsoleerde netwerk modus. 

   > [!div class="mx-imgBorder"]
   > ![Contoso Lab-firewalls](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Klik vervolgens op **+ bestaand virtueel netwerk toevoegen**, selecteer het virtuele netwerk en het subnet dat u hebt gekozen tijdens het maken van het lab en klik op **inschakelen**. 

   > [!div class="mx-imgBorder"]
   > ![Contoso mijn vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Zodra het service-eind punt is ingeschakeld voor het geselecteerde virtuele netwerk, klikt u op **toevoegen**. 

   > [!div class="mx-imgBorder"]
   > ![Toevoegen](./media/network-isolation/contoso-firewall-add.png)
 
Met deze optie worden door Azure Storage binnenkomende verbindingen van het toegevoegde virtuele netwerk toegestaan en kan het lab in een geïsoleerde netwerk modus worden uitgevoerd. 

U kunt er ook voor kiezen om deze stappen te automatiseren voor het configureren van deze instelling voor meerdere lessen. 

[Meer informatie over het beheren van standaard regels voor netwerk toegang voor Azure Storage met behulp van Power shell en CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Wat u moet onthouden tijdens het gebruik van een lab in een geïsoleerd netwerk modus

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Toegang tot het opslag account van Lab buiten het lab 

In een geïsoleerd netwerk moeten voor acties zoals het uploaden van een VHD naar het opslag account van de Lab, voor het maken van aangepaste installatie kopieën van de Lab-eigenaar expliciet toegang tot het opslag account via een toegestaan eind punt worden ingeschakeld. U kunt dit doen door een virtuele machine te maken en veilig toegang te krijgen tot het opslag account van die virtuele machine. 

[Meer informatie over het privé verkrijgen van toegang tot een opslag account vanuit een virtuele machine](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Gebruiks gegevens uit het lab exporteren 

In een geïsoleerd netwerk, om [persoonlijke gebruiks gegevens voor het lab te exporteren](personal-data-delete-export.md), moet de eigenaar van het lab expliciet een opslag account opgeven en een BLOB genereren binnen het account om de gegevens op te slaan. 

Als er geen opslag account is, mislukt deze bewerking in de geïsoleerde netwerk modus omdat het lab het opslag account van de test omgeving niet kan gebruiken in het geval van geen opslag account van de klant. 

[Meer informatie over het exporteren van gebruiks gegevens van Lab in een opgegeven opslag account](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Volgende stappen

[Labs automatisch maken of wijzigen met Azure Resource Manager sjablonen en Power shell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)