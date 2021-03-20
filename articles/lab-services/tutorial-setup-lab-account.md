---
title: Een lab-account met Azure Lab Services instellen | Microsoft Docs
description: Meer informatie over het instellen van een lab-account met Azure Lab Services, het toevoegen van een labmaker en het specificeren van Marketplace-afbeelding die door labs in het lab-account worden gebruikt.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85445063"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Zelfstudie: Een lab-account instellen met Azure Lab Services
In Azure Lab Services fungeert een lab-account als het centrale account waarin alle labs in uw organisatie worden beheerd. In uw lab-account machtigt u anderen om labs te maken en stelt u beleidsregels in die van toepassing zijn op alle labs in het lab-account. In deze zelfstudie leert u hoe u een lab-account maakt. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een lab-account maken
> * Een gebruiker toevoegen aan de rol Labmaker

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-lab-account"></a>Een lab-account maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **DevOps** in **Categorieën**. Selecteer vervolgens **Lab Services**. Selecteer de ster (`*`) naast de **Lab Services** om er een toe te voegen aan de sectie **FAVORIETEN** in het menu links. Vanaf de volgende keer selecteert u **Lab Services** onder **FAVORIETEN**.

    ![Alle Services -> Lab Services](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer op de pagina **Lab Services** **Toevoegen** op de werk balk of selecteer de knop **Lab-account maken** op de pagina. 

    ![Selecteer de Toevoegen op de pagina Lab-accounts](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Voer op het tabblad **Basis** van de pagina **Een Lab-account maken** de volgende acties uit: 
    1. Voer een naam in bij **lab-accountnaam**. 
    2. Selecteer het **Azure-abonnement** waarin u het lab-account wilt maken.
    3. Voor **Resourcegroep**: selecteer een bestaande resourcegroup of selecteer **Nieuwe maken** en geef een naam op voor de resourcegroep.
    4. Selecteer voor **Locatie** een locatie/regio waarin u het lab-account wilt maken. 

        ![Lab-account - Basisinformatie](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Selecteer **Controleren + maken**.
    6. Controleer de samenvatting en selecteer **Maken**. 

        ![Controleren + maken -> Maken](./media/tutorial-setup-lab-account/create-button.png)    
5. Nadat de implementatie is voltooid, gaat u naar **Volgende stappen** en selecteert u **Ga naar resource**. 

    ![Ga naar de pagina Lab-account](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Controleer of de pagina **Lab-account** wordt weergeven. 

    ![Pagina lab-account](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Een gebruiker toevoegen aan de rol Labmaker
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Als u machtigingen wilt toekennen aan docenten om labs te maken voor hun lessen, voegt u hen toe aan de rol **Labmaker**:

> [!NOTE]
> Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent om in deze zelfstudie een leslokaallab te maken met hetzelfde gebruikersaccount, kunt u deze stap overslaan. 

1. Selecteer op de pagina **Labaccount** de optie **Toegangsbeheer (IAM)** , selecteer **+ Toevoegen** op de werkbalk en klik vervolgens op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab-account gemaakt. Ga voor meer informatie over het maken van een lab als docent naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Een leslokaallab instellen](tutorial-setup-classroom-lab.md)

