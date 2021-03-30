---
title: Omgevingen integreren in azure-pijp lijnen in Azure DevTest Labs
description: Meer informatie over het integreren van Azure DevTest Labs omgevingen in uw Azure DevOps Continuous Integration-(CI) en continue Delivery-pijp lijnen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483020"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Omgevingen integreren in uw Azure DevOps CI/CD-pijp lijnen
U kunt de uitbrei ding Azure DevTest Labs taken die is geïnstalleerd in azure DevOps Services (voorheen bekend als Visual Studio Team Services) gebruiken om eenvoudig uw build-en release-pijp lijn voor continue integratie (CI)/continue levering (CD) te integreren met Azure DevTest Labs. Deze uitbrei dingen maken het eenvoudig om snel een [omgeving](devtest-lab-test-env.md) voor een specifieke test taak te implementeren en deze vervolgens te verwijderen wanneer de test is voltooid. 

In dit artikel wordt beschreven hoe u een omgeving maakt en implementeert en vervolgens de omgeving verwijdert, allemaal in één volledige pijp lijn. Normaal gesp roken voert u al deze taken afzonderlijk uit in uw eigen hand leiding voor het implementeren van tests en implementaties. De uitbrei dingen die in dit artikel worden gebruikt, zijn naast deze [DTL VM-taken maken/verwijderen](devtest-lab-integrate-ci-cd.md):

- Een omgeving maken
- Een omgeving verwijderen

## <a name="before-you-begin"></a>Voordat u begint
Voordat u uw CI/CD-pijp lijn kunt integreren met Azure DevTest Labs, installeert u [Azure DevTest Labs taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) -extensie van Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Het lab voor omgevingen maken en configureren
In deze sectie wordt beschreven hoe u een Lab maakt en configureert waarin de Azure-omgeving wordt geïmplementeerd.

1. [Maak een Lab](devtest-lab-create-lab.md) als u er nog geen hebt. 
2. Configureer het lab en maak een omgevings sjabloon door de instructies in dit artikel te volgen: [multi-VM-omgevingen en Paas-resources maken met Azure Resource Manager sjablonen](devtest-lab-create-environment-from-arm.md).
3. Voor dit voor beeld gebruikt u een bestaande Azure Quick Start-sjabloon [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) .
4. Kopieer de map **201-web-app-redis-cache-SQL-data base** naar de map **ArmTemplate** in de opslag plaats die u in stap 2 hebt geconfigureerd.

## <a name="create-a-release-definition"></a>Release-definitie maken
Ga als volgt te werk om de release definitie te maken:

1.  Op het tabblad **releases** van de **hub build & release** selecteert u de knop met het **plus teken (+)** .
2.  Selecteer in het venster **release definitie maken** de sjabloon **Empty** en selecteer vervolgens **volgende**.
3.  Selecteer **later kiezen** en selecteer vervolgens **maken** om een nieuwe release definitie met één standaard omgeving en zonder gekoppelde artefacten te maken.
4.  Als u het snelmenu wilt openen, selecteert u in de definitie nieuwe release het **beletsel teken (...)** naast de naam van de omgeving en selecteert **u vervolgens variabelen configureren**.
5.  Voer in het venster **Configure-Environment** de volgende waarden in voor de variabelen die u in de release definitie taken gebruikt:
1.  Voer bij **Administrator Login** de aanmeldings naam van de SQL-beheerder in.
2.  Voer voor **administratorLoginPassword** het wacht woord in dat moet worden gebruikt door de aanmelding van de SQL-beheerder. Gebruik het pictogram hang slot om het wacht woord te verbergen en te beveiligen.
3.  Voer voor **DATABASENAME** de SQL database naam in.
4.  Deze variabelen zijn specifiek voor de voorbeeld omgevingen. verschillende omgevingen kunnen verschillende variabelen hebben.

## <a name="create-an-environment"></a>Een omgeving maken
De volgende fase van de implementatie is het maken van de omgeving die moet worden gebruikt voor ontwikkelings-en test doeleinden.

1. Selecteer in de release definitie **taken toevoegen**.
2. Voeg op het tabblad **taken** een Azure DevTest Labs omgeving maken-taak toe. Configureer de taak als volgt:
    1. Selecteer voor **Azure RM-abonnement** een verbinding in de lijst **beschik bare Azure-service verbindingen** of maak een meer beperkte machtigingen verbinding met uw Azure-abonnement. Zie [Azure Resource Manager service-eind punt](/azure/devops/pipelines/library/service-endpoints)voor meer informatie.
2. Selecteer bij **Lab-naam** de naam van het exemplaar dat u eerder hebt gemaakt *.
3. Voor de naam van de **opslag plaats** selecteert u de opslag plaats waar de Resource Manager-sjabloon (201) is gepusht naar *.
4. Voor **sjabloon naam** selecteert u de naam van de omgeving die u hebt opgeslagen in de opslag plaats van de bron code *. 
5. De naam van het **Lab**, de naam van de **opslag plaats** en de **sjabloon naam** zijn de beschrijvende representaties van de Azure-resource-id's. Als u de beschrijvende naam hand matig invoert, worden er fouten weer gegeven, gebruikt u de vervolg keuzelijsten om de gegevens te selecteren.
6. Voer bij **omgevings naam** een naam in om het omgevings exemplaar in het lab uniek te identificeren.  Het moet uniek zijn binnen het lab.
7. Het **parameter bestand** en de **para meters**, toestaan dat aangepaste para meters worden door gegeven aan de omgeving. Een van beide of beide kunnen worden gebruikt om de parameter waarden in te stellen. Voor dit voor beeld wordt de sectie para meters gebruikt. Gebruik de namen van de variabelen die u hebt gedefinieerd in de omgeving, bijvoorbeeld: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Gegevens in de omgevings sjabloon kunnen worden door gegeven in de sectie uitvoer van de sjabloon. Controleer **uitvoer variabelen maken op basis van de uitvoer van de omgevings sjabloon** , zodat andere taken de gegevens kunnen gebruiken. `$(Reference name.Output Name)` is het patroon dat moet worden gevolgd. Als de naam van de referentie bijvoorbeeld DTL is en de naam van de uitvoer in de sjabloon de locatie van de variabele zou zijn `$(DTL.location)` .

## <a name="delete-the-environment"></a>De omgeving verwijderen
In de laatste fase verwijdert u de omgeving die u in uw Azure DevTest Labs-exemplaar hebt geïmplementeerd. Normaal gesp roken verwijdert u de omgeving nadat u de ontwikkel taken hebt uitgevoerd of voert u de tests uit die u nodig hebt op de geïmplementeerde resources.

Selecteer in de release definitie **taken toevoegen** en voeg vervolgens op het tabblad **implementeren** een **Azure DevTest Labs omgeving verwijderen** -taak toe. Configureer deze als volgt:

1. Als u de virtuele machine wilt verwijderen, raadpleegt u [Azure DevTest Labs taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Selecteer voor **Azure RM-abonnement** een verbinding in de lijst **beschik bare Azure-service verbindingen** of maak een meer beperkte machtigingen verbinding met uw Azure-abonnement. Zie [Azure Resource Manager service-eind punt](/azure/devops/pipelines/library/service-endpoints)voor meer informatie.
    2. Selecteer bij **Lab-naam** het Lab waar de omgeving zich bevindt.
    3. Voer bij **omgevings naam** de naam in van de omgeving die u wilt verwijderen.
2. Voer een naam in voor de release definitie en sla deze op.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 
- [Maak omgevingen met meerdere vm's met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md).
- Quick Start-sjablonen voor DevTest Labs Automation van de [DevTest Labs github-opslag plaats](https://github.com/Azure/azure-quickstart-templates).
- [Pagina VSTS problemen oplossen](/azure/devops/pipelines/troubleshooting)

