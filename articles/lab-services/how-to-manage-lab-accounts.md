---
title: Lab-accounts beheren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken van een Lab-account, het weer geven van alle Lab-accounts of het verwijderen van een Lab-account in een Azure-abonnement.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 65ff28401b73698e320735613b32c96bf3de58f2
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434087"
---
# <a name="create-and-manage-lab-accounts"></a>Labaccounts maken en beheren
In Azure Lab Services is een Lab-account een container voor beheerde Lab-typen zoals Labs. Een beheerder stelt een Lab-account in met Azure Lab Services en biedt toegang tot Lab-eigen aars die in het account Labs kunnen maken. In dit artikel wordt beschreven hoe u een Lab-account maakt, alle Lab-accounts weergeeft of een Lab-account verwijdert.

## <a name="create-a-lab-account"></a>Een lab-account maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab-accounts** in de sectie **DevOps** . Selecteer de ster (`*`) naast de **Lab-accounts** om er een toe te voegen aan de sectie **FAVORIETEN** in het menu links. Vanaf de volgende keer selecteert u **Lab-accounts** onder **Favorieten**.

    ![Alle Services -> Lab-accounts](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer op de pagina **Lab-accounts** de optie **toevoegen** op de werk balk of maak een **Lab-account** op de pagina. 

    ![Selecteer de Toevoegen op de pagina Lab-accounts](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Voer op het tabblad **Basis** van de pagina **Een Lab-account maken** de volgende acties uit: 
    1. Voer een naam in bij **lab-accountnaam**. 
    2. Selecteer het **Azure-abonnement** waarin u het lab-account wilt maken.
    3. Selecteer voor **Resourcegroep** de optie **Nieuwe maken** en voer een naam in voor de resourcegroep.
    4. Selecteer voor **Locatie** een locatie/regio waarin het lab-account moet worden gemaakt.
    5. Voor het veld **Labmaker toestaan om lablocatie te kiezen** geeft u op of u wilt dat labmakers een locatie voor het lab kunnen selecteren. De optie is standaard uitgeschakeld. Wanneer deze is uitgeschakeld, kunnen labmakers geen locatie opgeven voor het lab dat ze maken. De labs worden gemaakt in de dichtstbijzijnde geografische locatie in het labaccount. Wanneer deze is ingeschakeld, kan de labmaker een locatie selecteren wanneer hij een lab maakt. Zie voor meer informatie [toestaan dat Lab Creator de locatie voor het lab selecteert](allow-lab-creator-pick-lab-location.md). 

        ![Een Lab-account maken-basis beginselen >](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. Selecteer **volgende: Geavanceerd** onder aan de pagina om naar het tabblad **Geavanceerd** te gaan en voer de volgende stappen uit: 
    1. Selecteer een bestaande **Galerie met gedeelde afbeeldingen** of maak er een. U kunt de sjabloon-VM in de galerie met gedeelde afbeeldingen opslaan, zodat deze door anderen opnieuw kan worden gebruikt. Zie [een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services](how-to-use-shared-image-gallery.md)voor meer informatie over de galerieën met gedeelde afbeeldingen.
    2. Opgeven of **virtuele Windows-machines automatisch** moeten worden afgesloten wanneer gebruikers de verbinding Hiermee verbreken. Geef op hoe lang de virtuele machines moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten. 
    3. Selecteer voor het **virtuele netwerk** van de peer een virtueel netwerk (VNet) voor het lab-netwerk. Labs die in dit account is gemaakt, zijn verbonden met het geselecteerde VNet en hebben toegang tot de resources in het geselecteerde VNet. Zie [verbinding maken met het virtuele netwerk van uw Lab met een virtueel netwerk op hetzelfde niveau](how-to-connect-peer-virtual-network.md)voor meer informatie.    
    8. Geef een **adres bereik** voor vm's op in het lab. Het adres bereik moet in de CIDR-notatie (Classless Inter-Domain Routing) staan (voor beeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adres bereik. Zie [een adres bereik voor vm's in het lab opgeven](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account) voor meer informatie.  

        > [!NOTE]
        > De eigenschap **adres bereik** is alleen van toepassing als er een **virtueel peer netwerk** is ingeschakeld voor het lab.

        ![Een Lab-account maken-> Geavanceerd](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. Selecteer **volgende: Labels** onder aan de pagina om over te scha kelen naar het tabblad **labels** . Voeg labels toe die u wilt koppelen aan het lab-account. Tags zijn naam/waarde-paren waarmee u resources kunt categoriseren en een geconsolideerde factuur kunt weer geven door hetzelfde label op meerdere resources en resource groepen toe te passen. Raadpleeg [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md) voor meer informatie.

    ![Scherm afbeelding waarin de pagina Lab-account maken wordt weer gegeven op het tabblad Labels gemarkeerd.](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. Selecteer onder aan deze pagina de optie **controleren + maken** om over te scha kelen naar het tabblad **controleren + maken** . 
4. Bekijk de samenvattings informatie op deze pagina en selecteer **maken**. 

    ![Een Lab-account maken-> Tags](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. Wacht tot de implementatie is voltooid, vouw **volgende stappen** uit en selecteer **Ga naar resource** zoals wordt weer gegeven in de volgende afbeelding: 

    U kunt ook het **klok pictogram** op de werk balk (**meldingen**) selecteren, controleren of de implementatie is gelukt en vervolgens **Ga naar resource** selecteren. 

    U kunt ook **Vernieuwen** selecteren op de **Lab-accounts**-pagina en het lab-account selecteren dat u hebt gemaakt. 

    ![Venster Een lab-account maken](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. U ziet de volgende pagina **lab-account**:

    ![Pagina lab-account](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Lab-accounts weer geven
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** in het menu. 
3. Selecteer een **Lab-account** voor het **type**. 
    U kunt ook filteren op abonnement, resource groep, locaties en tags. 

    ![Alle resources-> Lab-accounts](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Een Lab-account verwijderen
Volg de instructies in de vorige sectie waarin Lab-accounts in een lijst worden weer gegeven. Gebruik de volgende instructies om een Lab-account te verwijderen: 

1. Selecteer het **Lab-account** dat u wilt verwijderen. 
2. Selecteer **verwijderen** in de werk balk. 

    ![Lab-accounts-> knop verwijderen](./media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ja** ter bevestiging.
1. Selecteer **Verwijderen**. 

    ![Lab-account verwijderen-bevestiging](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> U kunt ook de Power shell-module AZ. LabServices (preview) gebruiken voor het beheren van Lab-accounts. Zie de [Introductie pagina AZ. LabServices op github](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie andere artikelen in de sectie **instructies voor** het  ->  **maken en configureren van Lab-accounts (eigenaar van het lab-account)** van de tabel-of-content (TOC). 