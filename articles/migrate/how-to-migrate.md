---
title: Migratie hulpprogramma's toevoegen in Azure Migrate
description: Meer informatie over het toevoegen van hulpprogram ma's voor migratie in Azure Migrate.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 97051e97ec9868f6941b579241e16e62fdd2162b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751781"
---
# <a name="add-migration-tools"></a>Migratiehulpprogramma's toevoegen

In dit artikel wordt beschreven hoe u migratie hulpprogramma's toevoegt in [Azure migrate](./migrate-services-overview.md).

- Als u een hulp programma voor migratie wilt toevoegen en nog geen Azure Migrate project hebt ingesteld, volgt u dit [artikel](create-manage-projects.md).
- Als u een ISV-hulp programma voor migratie hebt toegevoegd, [volgt u de stappen](prepare-isv-movere.md)om te voor bereiding op het werken met het hulp programma.

## <a name="select-a-migration-scenario"></a>Een migratie scenario selecteren

1. Klik in het Azure Migrate-project op **Overzicht**.
2. Selecteer het migratie scenario dat u wilt gebruiken:

    - Als u machines en werk belastingen wilt migreren naar Azure, selecteert u **servers beoordelen en migreren**.
    - Als u on-premises data bases wilt migreren, selecteert u **data bases evalueren en migreren**.
    - Als u on-premises Web-Apps wilt migreren, selecteert u **meer**  >  **Web apps** verkennen.
    - Als u gegevens wilt migreren naar Azure met behulp van het vak Data, selecteert u **meer**  >  **Data Box** verkennen.

    ![Opties voor het selecteren van een migratie scenario](./media/how-to-migrate/migrate-scenario.png)


## <a name="select-a-server-migration-tool"></a>Selecteer een hulp programma voor server migratie

1. Een hulp programma toevoegen:

    - Als u een Azure Migrate project hebt gemaakt met behulp van de optie **servers beoordelen en migraties** in de portal, wordt het hulp programma voor migratie van Azure migrate server automatisch toegevoegd aan het project. Als u aanvullende hulpprogram ma's voor migratie wilt toevoegen, in **servers**, naast **migratie hulpprogramma's**, selecteert u **meer hulp middelen toevoegen**.
    
         ![Knop voor het toevoegen van aanvullende hulpprogram ma's voor migratie](./media/how-to-migrate/add-migration-tools.png)

    - Als u een project hebt gemaakt met behulp van een andere optie en nog geen migratie hulpprogramma's hebt , selecteert u in de  >  **hulpprogram ma's** voor servers migreren **hier klikken**.

    ![Knop om eerste migratie hulpprogramma's toe te voegen](./media/how-to-migrate/no-migration-tool.png)

2. Selecteer in **Azure migrate**  >  **hulp middelen toevoegen** de hulpprogram ma's die u wilt toevoegen. Selecteer vervolgens **hulp programma toevoegen**.

    ![Beoordelings hulpprogramma's selecteren in lijst](./media/how-to-migrate/select-migration-tool.png)


## <a name="select-a-database-migration-tool"></a>Selecteer een hulp programma voor database migratie

Als u een Azure Migrate project hebt gemaakt met behulp van de optie **Data Base evalueren en migreren** in de portal, wordt het hulp programma voor database migratie automatisch toegevoegd aan het project. 

1. Als het hulp programma voor database migratie zich niet in het project bevindt, selecteert u in **data bases**  >  -**evaluatie Programma's** de optie **Klik hier**.
    
    ![Hulp programma voor database migratie toevoegen](./media/how-to-migrate/no-database-migration-tool.png)


2. In **Azure migrate**  >  **hulp middelen toevoegen** selecteert u het hulp programma voor database migratie. Selecteer vervolgens **hulp programma toevoegen**.

    ![Het hulp programma voor database migratie selecteren in de lijst](./media/how-to-migrate/select-database-migration-tool.png)

    

## <a name="select-a-web-app-migration-tool"></a>Een hulp programma voor migratie van web-apps selecteren

Als u een Azure migrate project hebt gemaakt met behulp van de optie **meer**  >  **webapps** verkennen in de portal, wordt het hulp programma voor migratie van de web-app automatisch toegevoegd aan het project. 

1. Als het hulp programma voor migratie van de web-app zich niet in het project bevindt, selecteert u in **Web apps**  >  -**evaluatie hulpprogramma's** **hier op**.

    ![Hulp programma voor migratie van web-apps toevoegen](./media/how-to-migrate/no-web-app-migration-tool.png)
 

2. In **Azure migrate**  >  **hulp middelen toevoegen** selecteert u het hulp programma voor migratie van webtoepassingen. Selecteer vervolgens **hulp programma toevoegen**.

    ![De webapp-evaluatie hulpprogramma's selecteren in de lijst](./media/how-to-migrate/select-web-app-migration-tool.png)


## <a name="order-an-azure-data-box"></a>Een Azure Data Box best Ellen

Als u grote hoeveel heden gegevens naar Azure wilt migreren, kunt u een Azure Data Box voor offline gegevens overdracht best Ellen.

1. Selecteer in **overzicht** de optie **meer verkennen**.
2. In **verkennen** selecteert u **Data Box**.
3. Selecteer in aan de **slag met data Box** het abonnement en de resource groep die u wilt gebruiken voor het best Ellen van een Data box.
4. Het **overdrachts type** is een import naar Azure. Geef het land op waarin de gegevens zich bevinden en de Azure-regio waarnaar u de gegevens wilt overdragen. 
5. Klik op **Toep assen** om de instellingen op te slaan.

## <a name="next-steps"></a>Volgende stappen

Voer een migratie uit met behulp van het hulp programma voor migratie van de Azure Migrate-server voor [Hyper-V-](tutorial-migrate-hyper-v.md) of [VMware](tutorial-migrate-vmware.md) -vm's.
