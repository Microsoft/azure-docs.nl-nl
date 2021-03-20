---
title: Planning maken voor Labs in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken van planningen voor Labs in Azure Lab Services zodat Vm's in de Labs op een opgegeven tijdstip worden gestart en afgesloten.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434022"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Schema's maken en beheren voor Labs in Azure Lab Services 
Met planningen kunt u een leslokaal Lab zodanig configureren dat de virtuele machines in het lab automatisch worden gestart en op een opgegeven tijdstip worden afgesloten. U kunt een eenmalig schema of een terugkerende planning definiëren. De volgende procedures geven u stappen voor het maken en beheren van planningen voor een klas Lab: 

> [!IMPORTANT]
> De geplande uitvoerings tijd van Vm's telt niet op het [quotum dat aan een gebruiker is toegewezen](how-to-configure-student-usage.md#set-quotas-for-users). Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt. 

## <a name="set-a-schedule-for-the-lab"></a>Een planning instellen voor het lab
Maak een geplande gebeurtenis voor het lab, zodat VM's in het lab op opgegeven momenten automatisch worden gestart/gestopt. Het gebruikers quotum dat u eerder hebt opgegeven, is de extra tijd die wordt toegewezen aan elke gebruiker buiten dit geplande tijdstip. 

> [!NOTE]
> Voordat we aan de slag gaan, is dit van invloed op de planning van virtuele machines van Lab: 
>- De virtuele machine van de sjabloon is niet opgenomen in schema's. 
>- Alleen toegewezen virtuele machines worden gestart. Dit betekent dat als een computer niet door een eind gebruiker (student) wordt geclaimd, de computer niet kan worden gestart op de geplande uren. 
>- Alle virtuele machines (of dit wordt geclaimd door een gebruiker of niet), worden gestopt op basis van het lab-schema. 

1. Ga naar de pagina **Planningen** en selecteer **Geplande gebeurtenis toevoegen** in de werkbalk. 

    ![Scherm opname van de Azure Lab Services pagina ' schema ', met de knop schema toevoegen geselecteerd.](./media/how-to-create-schedules/add-schedule-button.png)
2. Controleer of **Standaard** is geselecteerd voor het **Gebeurtenistype**. U selecteert **alleen starten** om alleen de begin tijd voor de virtuele machines op te geven. U selecteert **alleen stoppen** om alleen de eind tijd voor de virtuele machines op te geven. 
7. Selecteer in de sectie **herhalen** het huidige schema. 

    ![Knop Planning toevoegen op de pagina Planningen](./media/how-to-create-schedules/select-current-schedule.png)
5. Voer in het dialoogvenster **Herhalen** de volgende stappen uit:
    1. Bevestig dat **elke week** is ingesteld in het veld **Herhalen**. 
    3. De **begin datum** opgeven.
    4. Geef de **begin tijd** op waarop u de vm's wilt starten.
    5. Geef de **eind tijd** op waarop de vm's moeten worden afgesloten. 
    6. Geef de **tijd zone** op voor de start-en stop tijden die u hebt opgegeven. 
    2. Selecteer de dagen waarop de planning moet gelden. In het volgende voor beeld is Monday-Thursday geselecteerd. 
    8. Selecteer **Opslaan**. 

        ![Herhalingsplanning instellen](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Voer nu op de pagina **Geplande gebeurtenis toevoegen** voor **Opmerkingen (optioneel)** een beschrijving of opmerkingen voor de planning in. 
4. Selecteer **Opslaan** op de pagina **Geplande gebeurtenis toevoegen**. 

    ![Wekelijkse planning](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Schema's in de agenda weer geven
U ziet dat de geplande datums en tijden zijn gemarkeerd in de weer gave kalender, zoals wordt weer gegeven in de volgende afbeelding:

![Schema's in de agenda weergave](./media/how-to-create-schedules/schedules-calendar.png)

Selecteer de knop **vandaag** in de rechter bovenhoek om over te scha kelen naar de huidige datum in de agenda. Selecteer **pijl-links** om over te scha kelen naar de vorige week en de **pijl-rechts** om over te scha kelen naar de volgende week in de agenda. 

## <a name="edit-a-schedule"></a>Een planning bewerken
Wanneer u een gemarkeerde planning in de agenda selecteert, ziet u knoppen om de planning te **bewerken** of te **verwijderen** . 

![Schema pagina bewerken](./media/how-to-create-schedules/schedule-edit-button.png)

Op de pagina **geplande gebeurtenis bewerken** kunt u het schema bijwerken en vervolgens **Opslaan** selecteren. 

## <a name="delete-a-schedule"></a>Een schema verwijderen

1. Als u een planning wilt verwijderen, selecteert u een gemarkeerde planning in de agenda en selecteert u de knop met het prullenbak pictogram (verwijderen):

    ![Knop verwijderen op de werk balk](./media/how-to-create-schedules/schedule-delete-button.png)
2. Selecteer **Ja** in het dialoog venster **geplande gebeurtenis verwijderen** om de verwijdering te bevestigen. 



## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker, Access Labs](how-to-use-classroom-lab.md)
