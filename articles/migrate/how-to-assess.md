---
title: Beoordelings hulpprogramma's toevoegen in Azure Migrate
description: Meer informatie over het toevoegen van beoordelings hulpprogramma's in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786801"
---
# <a name="add-assessment-tools"></a>Evaluatiehulpprogramma's toevoegen

In dit artikel wordt beschreven hoe u beoordelings hulpprogramma's toevoegt in [Azure migrate](./migrate-services-overview.md). 

- Als u een evaluatie programma wilt toevoegen en u nog geen Azure Migrate project hebt, volgt u dit [artikel](create-manage-projects.md).
- Als u een ISV-hulp programma hebt toegevoegd, of als u het wilt inpakken, [voert u de stappen uit](prepare-isv-movere.md)om het voor te bereiden op het werken met het hulp programma.

## <a name="select-an-assessment-scenario"></a>Een evaluatie scenario selecteren

1. Klik in het Azure Migrate-project op **Overzicht**.
2. Selecteer het beoordelings scenario:

    - Als u servers (fysiek of virtueel) van uw Data Center of andere Clouds naar Azure wilt detecteren, evalueren en migreren, selecteert u **detecteren, evalueren en migreren**. U kunt nu ook SQL Server van uw VMware-omgeving detecteren en beoordelen met behulp van dit migratie doel.
    - Als u on-premises SQL Server data bases wilt beoordelen, selecteert u **data bases evalueren en migreren**.
    - Als u on-premises Web-Apps wilt beoordelen of migreren, selecteert u **meer**  >  **Web apps** verkennen.
    - Als u de infra structuur van uw virtuele bureau blad wilt beoordelen, selecteert u **meer**  >  **virtuele desktop infrastructuur** verkennen.

    ![Opties voor het selecteren van een evaluatie scenario](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>Een hulp programma voor detectie en evaluatie selecteren 


1. Een hulp programma toevoegen:

    - Als u een Azure Migrate project hebt gemaakt met behulp van de optie **servers beoordelen en migreren** in de portal, wordt het hulp programma voor Azure migrate detectie en evaluatie automatisch toegevoegd aan het project. Als u extra beoordelings hulpprogramma's wilt toevoegen, klikt u in **Windows, Linux en SQL Server**, naast **beoordelings hulpprogramma's**, op **meer hulp middelen toevoegen**.

         ![Knop om extra evaluatie hulpprogramma's toe te voegen](./media/how-to-assess/add-assessment-tool.png)

    - Als u een project hebt gemaakt met behulp van een andere optie en nog geen evaluatie hulpprogramma's hebt, klikt u in **Windows, Linux en SQL Server**  >  **assessment tools** **op hier**.

        ![Knop voor het toevoegen van het eerste evaluatie programma](./media/how-to-assess/no-assessment-tool.png)

2. Selecteer in **Azure migrate**  >  **hulp middelen toevoegen** de hulpprogram ma's die u wilt toevoegen. Selecteer vervolgens **hulp programma toevoegen**.

    ![Beoordelings hulpprogramma's selecteren in lijst](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Selecteer een hulp programma voor het evalueren van data bases

1. Een hulp programma toevoegen:

    - Als u een Azure Migrate project hebt gemaakt met behulp van de optie **Data Base evalueren en migreren** in de portal, wordt het hulp programma voor het evalueren van data bases automatisch toegevoegd aan het project. Als u extra beoordelings hulpprogramma's wilt toevoegen, klikt u in **data bases** naast **beoordelings hulpprogramma's** op **meer hulp middelen toevoegen**.

    - Als u een project hebt gemaakt met behulp van een andere optie en nog geen data base-evaluatie hulpprogramma's hebt, selecteert u in **data bases**  >  -**analyse Programma's** de optie **Klik hier**.

2. Selecteer in **Azure migrate**  >  **hulp middelen toevoegen** de hulpprogram ma's die u wilt toevoegen. Selecteer vervolgens **hulp programma toevoegen**.

    ![Hulpprogram ma's voor database evaluatie selecteren in de lijst](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Selecteer een hulp programma voor het evalueren van webtoepassingen

Als u een Azure migrate project hebt gemaakt met behulp van de optie **meer**  >  **webapps** verkennen in de portal, wordt het hulp programma voor het evalueren van webtoepassingen automatisch toegevoegd aan het project. 


1. Als het hulp programma voor de evaluatie van de web-app zich niet in het project bevindt, selecteert u in **Web apps**  >  -**beoordelings hulpprogramma's** **op klikken**.
    
    ![Hulp middelen voor het beoordelen van web-apps toevoegen](./media/how-to-assess/no-web-app-assessment-tool.png)


2. Selecteer in **Azure migrate**  >  **hulp middelen toevoegen** het hulp programma voor het evalueren van webtoepassingen. Selecteer vervolgens **hulp programma toevoegen**.

    ![Het hulp programma voor database migratie selecteren in de lijst](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Volgende stappen

On-premises servers detecteren voor evaluatie met behulp van Azure Migrate detectie-en evaluatie hulpprogramma voor [VMware](./tutorial-discover-vmware.md)-, [Hyper-V](./tutorial-discover-hyper-v.md)-of [fysieke servers](./tutorial-discover-physical.md)