---
title: Uw taken plannen
description: Gebruik taak planning om uw taken te beheren.
ms.topic: how-to
ms.date: 02/20/2020
ms.custom: seodec18
ms.openlocfilehash: 4661c9fc22868870af147998467c9f355f30580e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219677"
---
# <a name="schedule-jobs-for-efficiency"></a>Taken plannen voor efficiëntie

Met batch-taken plannen kunt u de taken die u voor het eerst wilt uitvoeren, rangschikken terwijl u rekening houdt met taken die afhankelijkheden hebben met andere taken. Door uw taken te plannen, kunt u ervoor zorgen dat u de kleinste hoeveelheid resources gebruikt. Knoop punten kunnen worden buiten gebruik gesteld wanneer dit niet nodig is. taken die afhankelijk zijn van andere taken, worden alleen in de tijd voor het optimaliseren van de werk stromen. Er wordt slechts één taak tegelijk uitgevoerd. Er wordt pas een nieuw item gestart wanneer het vorige item is voltooid. U kunt de taak instellen op automatisch aanvullen. 

## <a name="benefit-of-job-scheduling"></a>Voor deel van taak planning

Het voor deel van het plannen van taken is dat u een planning kunt opgeven voor het maken van taken. De taken die u plant met taak beheer taak zijn gekoppeld aan een taak. Met de taak taak beheer worden taken voor de taak gemaakt. Om dit te doen, moet de taak beheer taak verifiëren met het batch-account. Gebruik het toegangs token AZ_BATCH_AUTHENTICATION_TOKEN. Het token staat toegang tot de rest van de taak toe. 

## <a name="use-the-portal-to-schedule-a-job"></a>De portal gebruiken om een taak te plannen

   1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

   2. Selecteer het batch-account waarin u taken wilt plannen.

   3. Selecteer **toevoegen** om een nieuwe taak planning te maken en het **basis formulier** te volt ooien.



![Een taak plannen][1]

**Taak schema-ID**: de unieke id voor deze taak planning.

**Weergave naam**: de weergave naam voor de taak hoeft niet uniek te zijn, maar heeft een maximale lengte van 1024 tekens.

**Niet uitvoeren tot**: geeft de vroegste tijd aan waarop de taak wordt uitgevoerd. Als u deze niet instelt, wordt de planning gereed om taken direct uit te voeren.

**Niet uitvoeren na**: er worden geen taken uitgevoerd na de tijd die u hier hebt ingesteld. Als u geen tijd opgeeft, maakt u een terugkerende taak planning die actief blijft totdat u deze expliciet beëindigt.

**Herhalings interval**: u kunt de hoeveelheid tijd tussen taken opgeven. U kunt slechts één taak tegelijk gepland hebben, dus als het tijd is om een nieuwe taak te maken onder een taak schema, maar de vorige taak nog steeds wordt uitgevoerd, maakt de batch-service de nieuwe taak pas nadat de vorige taak is voltooid.  

**Start venster**: Hier geeft u het tijds interval op, beginnend bij het tijdstip waarop de planning aangeeft dat een taak moet worden gemaakt, totdat deze is voltooid. Als de huidige taak niet is voltooid tijdens het venster, wordt de volgende taak niet gestart.

Onder aan het basis formulier geeft u de pool op waarop u de taak wilt uitvoeren. Selecteer **Update** om de gegevens van uw groeps-id te vinden. 

![Groep opgeven][2]


**Groeps-id**: Identificeer de pool waarop u de taak wilt uitvoeren.

Taak taak **configuratie**: Selecteer **bijwerken** om de taak taak beheer een naam te geven, evenals de taak voorbereidings-en release taken als u deze gebruikt.

**Prioriteit**: Geef de taak een prioriteit.

**Maximale klok tijd** van de wand: Stel de maximale hoeveelheid tijd in waarop de taak kan worden uitgevoerd. Als deze niet binnen het tijds bestek wordt voltooid, wordt de taak door batch beëindigd. Als u dit niet instelt, is er geen tijds limiet voor de taak.

**Maximum aantal nieuwe pogingen** voor de taak: Geef op hoe vaak een taak Maxi maal vier keer kan worden uitgevoerd. Dit is niet hetzelfde als het aantal nieuwe pogingen dat een API-aanroep kan hebben.

**Wanneer alle taken zijn voltooid**: de standaard waarde is geen actie.

**Wanneer een taak mislukt**: de standaard waarde is geen actie. Een taak mislukt als het aantal nieuwe pogingen is uitgeput of als er een fout is opgetreden bij het starten van de taak. 

Nadat u **Opslaan** hebt geselecteerd en u in de linkernavigatiebalk naar **taak planningen** gaat, kunt u de uitvoering van de taak volgen door **uitvoerings gegevens** te selecteren.


## <a name="for-more-information"></a>Voor meer informatie

Zie [AZ batch job-Schedule](/cli/azure/batch/job-schedule)voor informatie over het beheren van een taak met behulp van de Azure cli.

## <a name="next-steps"></a>Volgende stappen

[Maak taak afhankelijkheden om taken uit te voeren die afhankelijk zijn van andere taken](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add-job-schedule-02.png
[2]: ./media/batch-job-schedule/add-job-schedule-03.png


