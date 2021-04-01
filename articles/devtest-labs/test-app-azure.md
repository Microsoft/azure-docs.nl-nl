---
title: Uw app testen in azure | Microsoft Docs
description: Meer informatie over het maken van een bestands share in een lab en het koppelen van het bestand op uw lokale computer en een virtuele machine in het lab, en het implementeren van bureau blad/webtoepassingen op de bestands share en het testen ervan.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2dbbf349da4e352fe20a22db03cc9063d801990
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282242"
---
# <a name="test-your-app-in-azure"></a>Uw app in Azure testen 
Dit artikel bevat stappen voor het testen van uw toepassing in azure met behulp van DevTest Labs. Eerst stelt u een bestands share in een lab in en koppelt u deze als een station op uw lokale ontwikkel computer en een virtuele machine in een lab. Vervolgens gebruikt u Visual Studio 2019 om uw app te implementeren op de bestands share, zodat u de app op de virtuele machine in het lab kunt uitvoeren.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten 
1. [Maak een Azure-abonnement](https://azure.microsoft.com/free/) als u er nog geen hebt en meld u aan [Azure Portal](https://portal.azure.com).
2. Volg de instructies in [dit artikel](devtest-lab-create-lab.md) om een lab te maken met behulp van Azure DevTest Labs. Vastmaken aan uw dash board, zodat u het eenvoudig kunt terugvinden wanneer u zich aanmeldt. Met Azure DevTest Labs kunt u snel resources maken in azure door verspilling en kosten te beperken. Zie [overzicht](devtest-lab-overview.md)voor meer informatie over DevTest Labs. 
3. Maak een Azure Storage-account in de resource groep van het lab door de instructies in het artikel [een opslag account maken](../storage/common/storage-account-create.md) te volgen. Selecteer op de pagina **opslag account maken** de optie **bestaande gebruiken** voor **resource groep** en selecteer de **resource groep van de test omgeving**. 
4. Maak een bestands share in uw Azure-opslag met behulp van de instructies in het artikel [een bestands share maken in azure files](../storage/files/storage-how-to-create-file-share.md) . 

## <a name="mount-the-file-share-on-your-local-machine"></a>De bestands share op uw lokale machine koppelen
1. Gebruik op uw lokale computer het script uit [de sectie de Azure-bestands share koppelen](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) van [een Azure-bestands share met Windows](../storage/files/storage-how-to-use-files-windows.md) -artikel. 
2. Gebruik vervolgens `net use` de opdracht om de bestands share op uw computer te koppelen. Hier volgt een voor beeld van de opdracht: Geef de naam van uw Azure-opslag en de bestands share op voordat u de opdracht uitvoert. 

    `net use Z: \\<YOUR AZURE STORAGE NAME>.file.core.windows.net\<YOUR FILE SHARE NAME> /persistent:yes`

## <a name="create-a-vm-in-the-lab"></a>Een virtuele machine maken in het lab
1. Selecteer op de pagina **Bestands share** de **resource groep** in het breadcrumb-menu bovenaan. U ziet de pagina **resource groep** . 
    
    ![Resource groep selecteren in het breadcrumb-menu](media/test-app-in-azure/select-resource-group-bread-crump.png)
2. Selecteer op de pagina **resource groep** het **Lab** dat u hebt gemaakt in DevTest Labs.

    ![Het lab selecteren](media/test-app-in-azure/select-devtest-lab-in-resource-group.png)
3. Selecteer op de pagina **DevTest Lab** voor uw Lab **+ toevoegen** op de werk balk. 

    ![Knop toevoegen voor het lab](media/test-app-in-azure/add-button-in-lab.png)
4. Zoek op de pagina **een basis kiezen** naar **smalldisk** en selecteer **[Smalldisk] Windows Server 2016 Data Center**. 

    ![Small disk Windows Server kiezen](media/test-app-in-azure/choose-small-disk-windows-server.png)
5. Geef op de pagina **virtuele machine** de **naam van de virtuele machine**, de **gebruikers naam**, het **wacht woord** op en selecteer **maken**.    
    
    ![Pagina virtuele machine maken](media/test-app-in-azure/create-virtual-machine-page.png)    

## <a name="mount-the-file-share-on-your-vm"></a>De bestands share op uw virtuele machine koppelen
1. Nadat de virtuele machine is gemaakt, selecteert u de **virtuele machine** in de lijst.    

    ![De VM van het lab selecteren](media/test-app-in-azure/select-lab-vm.png)
2. Selecteer **verbinding maken** op de werk balk om verbinding te maken met de virtuele machine. 
3. [Installeer Azure PowerShell](/powershell/azure/install-az-ps).
4. Volg de instructies in de sectie de bestands share koppelen. 

## <a name="publish-your-app-from-visual-studio"></a>Uw app publiceren vanuit Visual Studio
In deze sectie publiceert u uw app vanuit Visual Studio naar een test-VM in de Cloud.

1. Een desktop-of webtoepassing maken met behulp van Visual Studio 2019.
2. Bouw uw app.
3. Als u uw app wilt publiceren, klikt u met de rechter muisknop op het project in de **Solution Explorer** en selecteert u **publiceren**. 
4. Voer in de **wizard Publiceren** het **station** in dat is toegewezen aan de bestands share.

    **Bureau blad-app:**

    ![Desktop-app](media/test-app-in-azure/desktop-app.png)

    **Web-app:**

    ![Web-app](media/test-app-in-azure/web-app.png)

1. Selecteer **volgende** om de publicatie werk stroom te volt ooien en selecteer **volt ooien**. Wanneer u de stappen in de wizard voltooit, bouwt Visual Studio uw toepassing en publiceert deze naar uw bestands share. 


## <a name="test-the-app-on-your-test-vm-in-the-lab"></a>Test de app op uw test-VM in het lab

1. Ga naar de pagina virtuele machine voor uw VM in het lab. 
2. Selecteer **Start** op de werk balk om de VM te starten als deze de status gestopt heeft. U kunt automatisch starten en beleid voor automatisch afsluiten instellen voor uw virtuele machine om te voor komen dat elke keer wordt gestart en gestopt. 
3. Selecteer **Verbinding maken**.

    ![Pagina virtuele machine](media/test-app-in-azure/virtual-machine-page.png)
4. Open in de virtuele machine de **bestanden Verkenner** en selecteer **deze PC** om uw bestands share te vinden.

    ![Share op virtuele machine zoeken](media/test-app-in-azure/find-share-on-vm.png)

    > [!NOTE]
    > Als u de bestands share niet op uw virtuele machine of op uw lokale computer kunt vinden, koppelt u deze opnieuw door de opdracht uit te voeren `net use` . U vindt de `net use` opdracht in de wizard **Connect** van uw **bestands share** in de Azure Portal.
1. Open de bestands share en controleer of de app die u hebt geïmplementeerd vanuit Visual Studio wordt weer gegeven. 

    ![Share op VM openen](media/test-app-in-azure/open-file-share.png)

    U kunt uw app nu openen en testen binnen de test-VM die u in azure hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Vm's in een lab. 

- [Een virtuele machine aan een lab toevoegen](devtest-lab-add-vm.md)
- [Een Lab-VM opnieuw opstarten](devtest-lab-restart-vm.md)
- [Het formaat van een Lab VM wijzigen](devtest-lab-resize-vm.md)
