---
title: Een installatie kopie-Factory uitvoeren vanuit Azure DevOps in Azure DevTest Labs
description: In dit artikel worden alle voor bereidingen behandeld die nodig zijn om de installatie kopie-Factory uit te voeren vanuit Azure DevOps (voorheen Visual Studio Team Services).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476135"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Een fabrieksinstallatiekopie uitvoeren vanuit Azure DevOps
In dit artikel worden alle voor bereidingen behandeld die nodig zijn om de installatie kopie-Factory uit te voeren vanuit Azure DevOps (voorheen Visual Studio Team Services).

> [!NOTE]
> Een Orchestration-engine werkt. Azure DevOps is niet verplicht. De installatie kopie fabriek wordt uitgevoerd met Azure PowerShell scripts, zodat deze hand matig kan worden uitgevoerd met behulp van Windows Task Scheduler, andere CI/CD-systemen, enzovoort.

## <a name="create-a-lab-for-the-image-factory"></a>Een lab maken voor de installatie kopie-Factory
De eerste stap bij het instellen van de installatie kopie Factory is het maken van een lab in Azure DevTest Labs. Dit Lab is het lab van de installatie kopie waar u de virtuele machines maakt en aangepaste installatie kopieën opslaat. Dit lab wordt beschouwd als onderdeel van het algehele proces van image Factory. Nadat u een Lab hebt gemaakt, moet u ervoor zorgen dat u de naam opslaat, want u hebt deze later nodig.

## <a name="scripts-and-templates"></a>Scripts en sjablonen
De volgende stap bij het aannemen van de image Factory voor uw team is om te begrijpen wat er beschikbaar is. De scripts en sjablonen van de installatie kopie-Factory zijn openbaar beschikbaar in de [DevTest Labs github opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Hier volgt een overzicht van de onderdelen:

- Image Factory. Het is de hoofdmap.
    - Configuratie. De invoer voor de image Factory
        - GoldenImages. Deze map bevat JSON-bestanden die de definities van aangepaste installatie kopieën vertegenwoordigen.
        - Labs.jsop. Bestand waarin teams zich registreren voor het ontvangen van specifieke aangepaste installatie kopieën.
- Scripts. De engine voor de image Factory.

In de artikelen in deze sectie vindt u meer informatie over deze scripts en sjablonen.

## <a name="create-an-azure-devops-team-project"></a>Een Azure DevOps-team project maken
Met Azure DevOps kunt u de bron code opslaan, de Azure PowerShell op één plek uitvoeren. U kunt terugkerende uitvoeringen plannen om installatie kopieën up-to-date te houden. Er zijn goede voorzieningen voor het vastleggen van de resultaten bij het vaststellen van problemen.  Het gebruik van Azure DevOps is niet vereist, maar u kunt ook een harnas/engine gebruiken waarmee verbinding kan worden gemaakt met Azure en Azure PowerShell kan worden uitgevoerd.

Als u een bestaand DevOps-account of-project hebt dat u in plaats daarvan wilt gebruiken, slaat u deze stap over.

Als u aan de slag wilt gaan, maakt u een gratis account in azure DevOps. Ga naar https://www.visualstudio.com/ en selecteer **aan de slag** met de rechter kant onder **Azure DevOps** (voorheen VSTS). U moet een unieke account naam kiezen en ervoor zorgen dat u code beheert met behulp van Git. Zodra dit is gemaakt, slaat u de URL op in uw team project. Hier volgt een voor beeld-URL: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>De image Factory in Git inchecken
Alle Power shell-, sjablonen-en configuratie-instellingen voor de installatie kopie-Factory bevinden zich in de [open bare DevTest Labs github opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). De snelste manier om de code op te halen in uw nieuwe team project is het importeren van een opslag plaats. Dit pull-in de hele DevTest Labs-opslag plaats (zodat u extra documenten en voor beelden krijgt).

1. Ga naar het Azure DevOps-project dat u in de vorige stap hebt gemaakt (URL ziet eruit als **https:// \<accountname> . VisualStudio.com/MyFirstProject**).
2. Selecteer **een opslag plaats importeren**.
3. Voer de **kloon-URL** in voor de DevTest Labs opslag plaats: `https://github.com/Azure/azure-devtestlab` .
4. Selecteer **Importeren**.

    ![Git-opslag plaats importeren](./media/set-up-devops-lab/import-git-repo.png)

Als u ervoor kiest om alleen precies te controleren wat er nodig is (de image Factory-bestanden), volgt u de stappen [hier](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) om de Git-opslag plaats te klonen en alleen de bestanden in de map **scripts/ImageFactory** te pushen.

## <a name="create-a-build-and-connect-to-azure"></a>Een build maken en verbinding maken met Azure
Op dit moment hebt u de bron bestanden opgeslagen in een Git-opslag plaats in azure DevOps. Nu moet u een pijp lijn instellen om de Azure PowerShell uit te voeren. Er zijn veel opties om deze stappen uit te voeren. In dit artikel gebruikt u de build-definitie voor eenvoud, maar werkt u met DevOps build, DevOps release (enkele of meerdere omgevingen), andere uitvoerings engines, zoals Windows taak planner of een andere harnas die Azure PowerShell kan uitvoeren.

> [!NOTE]
> Een belang rijk punt om er rekening mee te houden dat een aantal van de Power Shell-bestanden veel tijd in beslag neemt wanneer er veel (10 +) aangepaste installatie kopieën zijn om te maken. Gratis DevOps-agents voor Build/release van hosten hebben een time-out van 30 minuten, dus u kunt de gratis gehoste agent niet gebruiken als u veel installatie kopieën hebt gemaakt. Deze time-outuitdaging is van toepassing op de manier die u wilt gebruiken. het is goed te controleren of u de gebruikelijke time-outs kunt uitbreiden voor lange uitvoeringen Azure PowerShell scripts. In het geval van een Azure-DevOps kunt u betaalde gehoste agents gebruiken of uw eigen build-agent gebruiken.

1. Als u wilt beginnen, selecteert u **Build instellen** op de start pagina van uw DevOps-project:

    ![Knop opbouwen instellen](./media/set-up-devops-lab/setup-build-button.png)
2. Geef een **naam** op voor de build (bijvoorbeeld: installatie kopieën bouwen en leveren in DevTest Labs).
3. Selecteer een **lege** build-definitie en selecteer **Toep assen** om uw build te maken.
4. In deze fase kunt u een **host** kiezen voor de build-agent.
5. **Sla** de build-definitie op.

    ![Definitie van de build](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>De opbouw variabelen configureren
Als u de opdracht regel parameters wilt vereenvoudigen, moet u de sleutel waarden die de installatie kopie-Factory naar een set bouwen variabelen, inkapselen. Selecteer het tabblad **variabelen** en u ziet een lijst met verschillende standaard variabelen. Hier ziet u de lijst met variabelen die u kunt invoeren in azure DevOps:


| Naam variabele | Waarde | Opmerkingen |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Dit is het volledige pad naar de map **Configuration** van de opslag plaats. Als u de hele opslag plaats hierboven hebt geïmporteerd, is de waarde links correct. Anders bijwerken om naar de configuratie locatie te verwijzen. |
| DevTestLabName | MyImageFactory | De naam van het lab in Azure DevTest Labs gebruikt als de fabriek voor het produceren van installatie kopieën. Als u er nog geen hebt, maakt u er een. Zorg ervoor dat het lab zich in hetzelfde abonnement bevindt waartoe het service-eind punt toegang heeft. |
| ImageRetention | 1 | Het aantal installatie kopieën dat u van elk type wilt opslaan. Stel de standaard waarde in op 1. |
| MachinePassword | ******* | Het ingebouwde wacht woord voor het beheerders account voor de virtuele machines. Dit is een tijdelijk account, dus zorg ervoor dat het beveiligd is. Selecteer het pictogram met de kleine vergren deling aan de rechter kant om te controleren of het een veilige teken reeks is. |
| MachineUserName | ImageFactoryUser | De ingebouwde gebruikers naam voor het beheerders account voor de virtuele machines. Dit is een tijdelijk account. |
| StandardTimeoutMinutes | 30 | De time-out voor normale Azure-bewerkingen moet worden gewacht. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | De ID van het abonnement waarin het lab bestaat en dat het service-eind punt toegang heeft tot. |
| VMSize | Standard_A3 | De grootte van de virtuele machine die moet worden gebruikt voor de stap **maken** . De virtuele machines die worden gemaakt, zijn tijdelijk. De grootte moet een waarde zijn die is [ingeschakeld voor het lab](devtest-lab-set-lab-policy.md). Controleer of er voldoende [quota voor abonnements kernen](../azure-resource-manager/management/azure-subscription-service-limits.md)zijn.

![Variabelen bouwen](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Verbinding maken met Azure
De volgende stap is het instellen van de Service-Principal. Dit is een identiteit in Azure Active Directory die ervoor zorgt dat de DevOps-build-agent in azure namens de gebruiker kan werken. Als u deze wilt instellen, begint u met het toevoegen van eerst Azure PowerShell build-stap.

1. Selecteer **taak toevoegen**.
2. Zoeken naar **Azure PowerShell**.
3. Zodra u het hebt gevonden, selecteert u **toevoegen** om de taak toe te voegen aan de build. Wanneer u dit doet, ziet u dat de taak aan de linkerkant wordt weer gegeven.

![Power shell-stap instellen](./media/set-up-devops-lab/set-up-powershell-step.png)

De snelste manier om een Service-Principal in te stellen, is door Azure DevOps voor ons te laten doen.

1. Selecteer de **taak** die u zojuist hebt toegevoegd.
2. Kies **Azure Resource Manager**voor het **verbindings type Azure**.
3. Selecteer de koppeling **beheren** om de Service-Principal in te stellen.

Zie dit [blogbericht](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) voor meer informatie. Wanneer u de koppeling **beheren** selecteert, gaat u naar de juiste plaats in DevOps (tweede scherm afbeelding in het blog bericht) om de verbinding met Azure in te stellen. Zorg ervoor dat u **Azure Resource Manager service-eind punt** kiest wanneer u dit instelt.

## <a name="complete-the-build-task"></a>De taak bouwen volt ooien
Als u de taak maken selecteert, ziet u alle details in het rechterdeel venster dat moet worden ingevuld.

1. Geef eerst de taak Build op: **Create Virtual Machines**.
2. Kies de **Service-Principal** die u hebt gemaakt door **Azure Resource Manager** te kiezen
3. Kies het **service-eind punt**.
4. Selecteer voor **pad**naar script **... (weglatings tekens)** aan de rechter kant.
5. Navigeer naar **MakeGoldenImageVMs.ps1** script.
6. Script parameters moeten er als volgt uitzien: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![De build-definitie volt ooien](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>De build in de wachtrij plaatsen
U kunt controleren of u alles goed hebt ingesteld door een nieuwe build te bezorgen. Terwijl de build wordt uitgevoerd, gaat u naar de [Azure Portal](https://portal.azure.com) en selecteert u op **alle virtual machines** in het lab van de installatie kopie om te controleren of alles goed werkt. U ziet dat er drie virtuele machines worden gemaakt in het lab.

![Vm's in het lab](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Volgende stappen
De eerste stap bij het instellen van de installatie kopie-Factory op basis van Azure DevTest Labs is voltooid. In het volgende artikel in de serie krijgt u de Vm's gegeneraliseerd en opgeslagen in aangepaste installatie kopieën. Vervolgens hebt u ze gedistribueerd naar al uw andere Labs. Zie het volgende artikel in de reeks: [aangepaste installatie kopieën opslaan en distribueren naar meerdere lessen](image-factory-save-distribute-custom-images.md).
