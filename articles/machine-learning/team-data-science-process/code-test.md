---
title: Gegevens wetenschappen code testen met Azure DevOps Services-team data Science process
description: Gegevens wetenschappen code testen op Azure met de gegevensset voor de voor spellingen van de ICB volwassene met het team data Science process en Azure DevOps Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: ce37ee51e549a2bd6f2747f28af8c038c39d8f1f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656832"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Gegevens wetenschappen code testen op Azure met het team data Science process en Azure DevOps Services
Dit artikel bevat voorlopige richt lijnen voor het testen van code in een Data Science-werk stroom. Dergelijke tests bieden gegevens wetenschappers een systematische en efficiënte manier om de kwaliteit en het verwachte resultaat van hun code te controleren. We gebruiken een team data Science process (TDSP)- [project dat gebruikmaakt van de gegevensset van de ICB volwassene voor volwassenen](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) die eerder is gepubliceerd om te laten zien hoe code tests kunnen worden uitgevoerd. 

## <a name="introduction-on-code-testing"></a>Inleiding op code tests
' Unit tests ' is een spante praktijk voor het ontwikkelen van software. Maar voor een gegevens wetenschap is het vaak niet duidelijk wat ' eenheids testen ' betekent en hoe u code moet testen voor verschillende stadia van een levens cyclus van data wetenschappen, zoals:

* Gegevensvoorbereiding
* Kwaliteits onderzoek van gegevens
* Modellen maken
* Modelimplementatie 

In dit artikel wordt de term ' eenheid testen ' vervangen door ' code testen '. Er wordt gerefereerd aan het testen als de functies die helpen te beoordelen of de code voor een bepaalde stap van een levens cyclus van een data technologie de resultaten levert zoals verwacht. De persoon die de test schrijft, definieert wat er wordt verwacht, afhankelijk van het resultaat van de functie, bijvoorbeeld gegevens kwaliteit controleren of model leren.

Dit artikel bevat verwijzingen naar nuttige bronnen.

## <a name="azure-devops-for-the-testing-framework"></a>Azure-DevOps voor het test raamwerk
In dit artikel wordt beschreven hoe u tests uitvoert en automatiseert met behulp van Azure DevOps. U kunt ervoor kiezen om alternatieve hulpprogram ma's te gebruiken. We laten ook zien hoe u een automatische build kunt instellen met behulp van Azure DevOps en-agents. Voor Build-agenten gebruiken we Azure data Science Virtual Machines (Dsvm).

## <a name="flow-of-code-testing"></a>Code testen stroom
De algemene werk stroom voor het testen van code in een Data Science-project ziet er als volgt uit: 

![Stroom diagram van code testen](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Gedetailleerde stappen

Gebruik de volgende stappen voor het instellen en uitvoeren van code testen en een geautomatiseerde build met behulp van een build-agent en Azure DevOps:

1. Een project maken in de Visual Studio Desktop-toepassing:

    ![Het scherm nieuw project maken in Visual Studio](./media/code-test/create_project.PNG)

   Nadat u het project hebt gemaakt, kunt u het vinden in Solution Explorer in het rechterdeel venster:
    
    ![Stappen voor het maken van een project](./media/code-test/create_python_project_in_vs.PNG)

    ![Solution Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. Uw project code in de Azure DevOps project code-opslag plaats invoeren: 

    ![Project code opslagplaats](./media/code-test/create_repo.PNG)

1. Stel dat u een gegevens voorbereidings werk hebt uitgevoerd, zoals gegevens opname, functie techniek en het maken van label kolommen. U wilt ervoor zorgen dat uw code de verwachte resultaten genereert. Hier volgt een code die u kunt gebruiken om te testen of de code voor de gegevens verwerking goed werkt:

    * Controleer of de kolom namen goed zijn:
    
      ![Code voor overeenkomende kolom namen](./media/code-test/check_column_names.PNG)

    * Controleer of het antwoord niveau goed is:

      ![Code voor overeenkomende niveaus](./media/code-test/check_response_levels.PNG)

    * Controleer of het respons percentage redelijk is:

      ![Code voor het antwoord percentage](./media/code-test/check_response_percentage.PNG)

    * Controleer de ontbrekende frequentie van elke kolom in de gegevens:
    
      ![Code voor ontbrekend aantal](./media/code-test/check_missing_rate.PNG)


1. Nadat u klaar bent met het verwerken van gegevens en het werken met onderdelen, en u een goed model hebt getraind, moet u ervoor zorgen dat het model dat u hebt getraind, nieuwe gegevens sets correct kan scoren. U kunt de volgende twee tests gebruiken om de voorspellings niveaus en de distributie van label waarden te controleren:

    * Voorspellings niveaus controleren:
    
      ![Code voor het controleren van voorspellings niveaus](./media/code-test/check_prediction_levels.PNG)

    * De verdeling van voorspellings waarden controleren:

      ![Code voor het controleren van voorspellings waarden](./media/code-test/check_prediction_values.PNG)

1. Plaats alle test functies samen in een python-script met de naam **test_funcs. py**:

    ![Python-script voor test functies](./media/code-test/create_file_test_func.PNG)


1. Nadat de test codes zijn voor bereid, kunt u de test omgeving instellen in Visual Studio.

   Maak een python-bestand met de naam **test1.py**. Maak in dit bestand een klasse die alle tests bevat die u wilt uitvoeren. In het volgende voor beeld ziet u zes tests die zijn voor bereid:
    
    ![Python-bestand met een lijst van tests in een klasse](./media/code-test/create_file_test1_class.PNG)

1. Deze tests kunnen automatisch worden gedetecteerd als u **codetest. testCase** achter de naam van de klasse plaatst. Open test Verkenner in het rechterdeel venster en selecteer **alles uitvoeren**. Alle tests worden opeenvolgend uitgevoerd en u krijgt een melding als de test is geslaagd.

    ![De tests uitvoeren](./media/code-test/run_tests.PNG)

1. Controleer uw code in de project opslagplaats met behulp van Git-opdrachten. Uw meest recente werk wordt kort weer gegeven in azure DevOps.

    ![Git-opdrachten voor het controleren van code](./media/code-test/git_check_in.PNG)

    ![Meest recente werk in azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Automatische build en test instellen in azure DevOps:

    a. Selecteer in de project opslagplaats **Build en release** en selecteer vervolgens **+ Nieuw** om een nieuw bouw proces te maken.

    ![Selecties voor het starten van een nieuwe bouw proces](./media/code-test/create_new_build.PNG)

    b. Volg de aanwijzingen om de locatie van de bron code, de project naam, de opslag plaats en de vertakkings gegevens te selecteren.
    
    ![Bron-, naam-, opslag plaats-en vertakkings gegevens](./media/code-test/fill_in_build_info.PNG)

    c. Selecteer een sjabloon. Omdat er geen python-project sjabloon is, begint u met het selecteren van een **leeg proces**. 

    ![Lijst met sjablonen en de knop ' leeg proces '](./media/code-test/start_empty_process_template.PNG)

    d. Noem de build en selecteer de agent. U kunt hier de standaard optie kiezen als u een DSVM wilt gebruiken om het bouw proces te volt ooien. Zie [Build and release agents](/azure/devops/pipelines/agents/agents?view=vsts)(Engelstalig) voor meer informatie over het instellen van agents.
    
    ![Selecties voor Build en agent](./media/code-test/select_agent.PNG)

    e. Selecteer **+** in het linkerdeel venster om een taak voor deze compilatie fase toe te voegen. Omdat we het python-script **test1.py** uitvoeren om alle controles te volt ooien, gebruikt deze taak een Power shell-opdracht voor het uitvoeren van python-code.
    
    ![Deel venster taken toevoegen met Power shell geselecteerd](./media/code-test/add_task_powershell.PNG)

    f. Vul in de Power shell-Details de vereiste gegevens in, zoals de naam en de versie van Power shell. Kies in- **line script** als het type. 
    
    In het vak onder **inline-script** kunt u **python test1.py** typen. Zorg ervoor dat de omgevings variabele juist is ingesteld voor python. Als u een andere versie of een andere kernel van python nodig hebt, kunt u het pad expliciet opgeven, zoals wordt weer gegeven in de afbeelding: 
    
    ![Power shell-Details](./media/code-test/powershell_scripts.PNG)

    g. Selecteer **& wachtrij opslaan** om het build pipeline-proces te volt ooien.

    ![Knop voor opslaan van & wachtrij](./media/code-test/save_and_queue_build_definition.PNG)

Telkens wanneer een nieuwe commit-bewerking naar de code opslagplaats wordt gepusht, wordt het buildproces automatisch gestart. U kunt elke vertakking definiëren. Het proces voert het **test1.py** -bestand in de agent machine uit om ervoor te zorgen dat alles wat in de code is gedefinieerd, correct wordt uitgevoerd. 

Als waarschuwingen correct zijn ingesteld, ontvangt u een melding in het e-mail bericht wanneer de build is voltooid. U kunt ook de status van de build in azure DevOps controleren. Als dit mislukt, kunt u de details van de build controleren en nagaan welk gedeelte wordt verbroken.

![E-mail melding van een geslaagde build](./media/code-test/email_build_succeed.PNG)

![Azure DevOps-melding van een geslaagde build](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Volgende stappen
* Raadpleeg de [opslag plaats](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) voor de uitkering van icb's voor concrete voor beelden van eenheids tests voor data Science-scenario's.
* Volg de voor gaande overzichten en voor beelden van het scenario van de ICB-baten prognose in uw eigen data Science-projecten.

## <a name="references"></a>Referenties
* [Team Data Science Process](./index.yml)
* [Hulpprogram Ma's voor het testen van Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps-test bronnen](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)