---
title: Overzicht van ACR-taken
description: Een inleiding tot ACR-taken, een suite met functies in Azure Container Registry die veilige, geautomatiseerde build, beheer en patching van containerafbeeldingen in de cloud biedt.
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: a42a2bfcdc1621689421940c4db2fcf4f5e64b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780997"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Builds en onderhoud van containerafbeeldingen automatiseren met ACR-taken

Containers bieden nieuwe virtualisatieniveaus, die afhankelijkheden van toepassingen en ontwikkelaars isoleren van infrastructuur- en operationele vereisten. Wat overblijft, is echter de noodzaak om aan te pakken hoe deze toepassingsvirtualisatie wordt beheerd en gepatcht gedurende de levenscyclus van de container.

## <a name="what-is-acr-tasks"></a>Wat is ACR-taken?

**ACR-taken** is een suite met functies binnen Azure Container Registry. Het biedt het bouwen van op de cloud gebaseerde containerafbeeldingen voor [platformen,](#image-platforms) waaronder Linux, Windows en ARM, en kan besturingssysteem- en [frameworkpatching](#automate-os-and-framework-patching) voor uw Docker-containers automatiseren. ACR-taken breidt niet alleen uw interne ontwikkelingscyclus uit naar de cloud met builds van containerafbeeldingen op aanvraag, maar maakt ook geautomatiseerde builds mogelijk die worden geactiveerd door broncode-updates, updates voor de basisafbeelding van een container of timers. Met updatetriggers voor basisafbeeldingen kunt u bijvoorbeeld de patchwerkstroom voor uw besturingssysteem en toepassings framework automatiseren, en veilige omgevingen onderhouden terwijl u zich houdt aan de principes van onveranderbare containers.

## <a name="task-scenarios"></a>Taakscenario's

ACR-taken ondersteunt verschillende scenario's voor het bouwen en onderhouden van containerafbeeldingen en andere artefacten. Zie de volgende secties in dit artikel voor meer informatie.

* **[Snelle taak:](#quick-task)** bouw en push een enkele containerinstallatie installatie van een containerregister op aanvraag in Azure, zonder dat u een lokale installatie van de Docker-engine nodig hebt. Denk `docker build` aan , in de `docker push` cloud.
* **Automatisch geactiveerde taken:** schakel een of meer *triggers in om* een afbeelding te bouwen:
  * **[Activeren bij broncode-update](#trigger-task-on-source-code-update)** 
  * **[Activeren bij update van basisafbeelding](#automate-os-and-framework-patching)** 
  * **[Activeren volgens een schema](#schedule-a-task)** 
* **[Taak met meerdere stappen:](#multi-step-tasks)** breid de build-and-push-mogelijkheid van één ACR-taken uit met werkstromen met meerdere stappen en meerdere containers. 

Elke ACR-taak heeft een [gekoppelde broncodecontext:](#context-locations) de locatie van een set bronbestanden die wordt gebruikt om een containerafbeelding of ander artefact te bouwen. Voorbeeldcontexten zijn een Git-opslagplaats of een lokaal bestandssysteem.

Taken kunnen ook profiteren van [runvariabelen,](container-registry-tasks-reference-yaml.md#run-variables)zodat u taakdefinities opnieuw kunt gebruiken en tags voor afbeeldingen en artefacten kunt standaardiseren.

## <a name="quick-task"></a>Snelle taak

De interne ontwikkelingscyclus, het iteratieve proces van het schrijven van code, het bouwen en testen van uw toepassing voordat u zich verbindt tot broncodebeheer, is in het echt het begin van het levenscyclusbeheer van containers.

Voordat u uw eerste coderegel doorregelt, [](container-registry-tutorial-quick-task.md) ACR-taken de functie voor snelle taken een geïntegreerde ontwikkelervaring bieden door de builds van uw container-image te offloaden naar Azure. Met snelle taken kunt u uw geautomatiseerde builddefinities controleren en potentiële problemen ondervangen voordat u uw code gaat uitvoeren.

Met de vertrouwde indeling neemt de `docker build` [opdracht az acr build][az-acr-build] in de Azure CLI een [context](#context-locations) (de set bestanden die moet worden gebouwd), verzendt deze naar ACR-taken en pusht standaard de gebouwde afbeelding naar het register wanneer deze is voltooid.

Zie voor een inleiding de quickstart voor het bouwen en uitvoeren van [een containerafbeelding](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

ACR-taken is ontworpen als een primitieve containerlevenscyclus. Integreer bijvoorbeeld ACR-taken in uw CI/CD-oplossing. Door az login uit [te voeren met][az-login] een [service-principal,][az-login-service-principal]kan uw CI/CD-oplossing vervolgens [az acr build-opdrachten][az-acr-build] uitgeven om builds van de afbeeldingen te laten uitvoeren.

Meer informatie over het gebruik van snelle taken in de eerste ACR-taken zelfstudie: [Containerafbeeldingen bouwen in de cloud met Azure Container Registry-taken](container-registry-tutorial-quick-task.md).

> [!TIP]
> Als u een afbeelding rechtstreeks vanuit de broncode wilt bouwen en pushen, zonder een Dockerfile, biedt Azure Container Registry de [opdracht az acr pack build][az-acr-pack-build] (preview). Dit hulpprogramma bouwt en pusht een afbeelding uit de broncode van de toepassing met [behulp van Cloud Native Buildpacks.](https://buildpacks.io/)

## <a name="trigger-task-on-source-code-update"></a>Taak activeren bij bijwerken van broncode

Activeer een build van een container-container of een taak met meerdere stappen wanneer code wordt vastgelegd, of wanneer een pull-aanvraag wordt gemaakt of bijgewerkt, naar een openbare of persoonlijke Git-opslagplaats in GitHub of Azure DevOps. Configureer bijvoorbeeld een build-taak met de Azure CLI-opdracht [az acr task create][az-acr-task-create] door een Git-opslagplaats en eventueel een vertakking en Dockerfile op te geven. Wanneer uw team code in de opslagplaats bij werkt, ACR-taken door een webhook gemaakt een build van de container-afbeelding die is gedefinieerd in de opslagplaats. 

ACR-taken ondersteunt de volgende triggers wanneer u een Git-repo in stelt als context van de taak:

| Trigger | Standaard ingeschakeld |
| ------- | ------------------ |
| Doorvoeren | Yes |
| Pull-aanvraag | No |

Als u een trigger voor het bijwerken van de broncode wilt configureren, moet u de taak een persoonlijk toegangsteken (PAT) verstrekken om de webhook in te stellen in de openbare of persoonlijke GitHub- of Azure DevOps-opslagplaats.

> [!NOTE]
> Op dit moment biedt ACR-taken geen ondersteuning voor de aanvraagtriggers commit of pull in GitHub Enterprise-opslagplaatsen.

Meer informatie over het activeren van builds op broncode-commit in de tweede ACR-taken zelfstudie, Builds van containerafbeeldingen automatiseren [met Azure Container Registry-taken](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Patches voor besturingssystemen en frameworks automatiseren

De kracht van ACR-taken om uw werkstroom voor het bouwen van containers echt te verbeteren, is afkomstig van de mogelijkheid om een update van een *basisafbeelding te detecteren.* Een functie van de meeste containerafbeeldingen: een basisafbeelding is een bovenliggende afbeelding waarop een of meer toepassingsafbeeldingen zijn gebaseerd. Basisinstallatieprogramma's bevatten doorgaans het besturingssysteem en soms toepassingskaders. 

U kunt een ACR-taak instellen om een afhankelijkheid van een basisafbeelding bij te houden bij het bouwen van een toepassingsafbeelding. Wanneer de bijgewerkte basisafbeelding naar uw register wordt pushen of een basisafbeelding wordt bijgewerkt in een openbare repo, zoals in Docker Hub, kunnen ACR-taken automatisch toepassingsafbeeldingen bouwen op basis van deze basis.
Met deze automatische detectie en herbouwing bespaart ACR-taken u de tijd en moeite die normaal gesproken nodig is om elke toepassingsafbeelding die verwijst naar uw bijgewerkte basisafbeelding handmatig bij te houden en bij te werken.

Meer informatie over [updatetriggers voor basisafbeeldingen](container-registry-tasks-base-images.md) voor ACR-taken. En meer informatie over het activeren van een build van een afbeelding wanneer een basisafbeelding naar een containerregister wordt pushen in de zelfstudie Builds van containerafbeeldingen automatiseren wanneer een basisafbeelding wordt bijgewerkt in een [Azure-containerregister](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Een taak plannen

U kunt eventueel een taak plannen door een of meer *timertriggers in te stellen* wanneer u de taak maakt of bij te werken. Het plannen van een taak is handig voor het uitvoeren van containerworkloads volgens een gedefinieerd schema of het uitvoeren van onderhoudsbewerkingen of tests op afbeeldingen die regelmatig naar uw register worden pusht. Zie Een [ACR-taak uitvoeren volgens](container-registry-tasks-scheduled.md)een gedefinieerd schema voor meer informatie.

## <a name="multi-step-tasks"></a>Taken met meerdere stappen

Taken met meerdere stappen biedt een stapsgewijze taakdefinitie en -uitvoering voor het ontwikkelen, testen en patchen van containerinstallatiekopieën in de cloud. Taakstappen die in een [YAML-bestand zijn gedefinieerd,](container-registry-tasks-reference-yaml.md) geven afzonderlijke build- en pushbewerkingen op voor containerafbeeldingen of andere artefacten. Ze kunnen ook de uitvoering definiëren van een of meer containers, waarbij elke stap de container als uitvoeringsomgeving gebruikt.

U kunt bijvoorbeeld een taak met meerdere stappen maken die het volgende automatiseert:

1. Een webtoepassingsafbeelding bouwen
1. De webtoepassingscontainer uitvoeren
1. Een testafbeelding voor een webtoepassing bouwen
1. Voer de testcontainer voor de webtoepassing uit, waarmee tests worden uitgevoerd op de toepassingscontainer die wordt uitgevoerd
1. Als de tests slagen, bouwt u een helm-grafiekarchiefpakket
1. Een uitvoeren met `helm upgrade` behulp van het nieuwe helm-grafiekarchiefpakket

Met taken met meerdere stappen kunt u het bouwen, uitvoeren en testen van een afbeelding opsplitsen in meer samen te stellen stappen, met afhankelijkheidsondersteuning tussen stappen. Met taken met meerdere stappen in ACR-taken hebt u gedetailleerdere controle over het bouwen, testen en patchen van besturingssystemen en frameworks.

Meer informatie over taken met meerdere stappen in [Run multi-step build, test, and patch tasks in ACR-taken](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Contextlocaties

De volgende tabel bevat voorbeelden van ondersteunde contextlocaties voor ACR-taken:

| Contextlocatie | Beschrijving | Voorbeeld |
| ---------------- | ----------- | ------- |
| Lokaal bestandssysteem | Bestanden in een map op het lokale bestandssysteem. | `/home/user/projects/myapp` |
| GitHub-main branch | Bestanden in de hoofdvertakking (of een andere standaardvertakking) van een openbare of persoonlijke GitHub-opslagplaats.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-vertakking | Specifieke vertakking van een openbare of persoonlijke GitHub-opslagplaats.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-submap | Bestanden in een submap in een openbare of persoonlijke GitHub-opslagplaats. Voorbeeld toont een combinatie van een vertakking en een submapspecificatie. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub-door commit | Specifieke door commit in een openbare of persoonlijke GitHub-opslagplaats. Voorbeeld toont een combinatie van een commit-hash (SHA) en een submapspecificatie. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Azure DevOps-submap | Bestanden in een submap in een openbare of persoonlijke Azure-repo. Voorbeeld toont een combinatie van vertakkings- en submapspecificatie. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Externe tarball | Bestanden in een gecomprimeerd archief op een externe webserver. | `http://remoteserver/myapp.tar.gz` |
| Artefact in containerregister | [OCI-artefactbestanden](container-registry-oci-artifacts.md) in een containerregisteropslagplaats. | `oci://myregistry.azurecr.io/myartifact:mytag` |

> [!NOTE]
> Wanneer u een persoonlijke Git-repo als context voor een taak gebruikt, moet u een persoonlijk toegang token (PAT) verstrekken.

## <a name="image-platforms"></a>Platforms voor afbeeldingen

Standaard bouwt ACR-taken voor het Linux-besturingssysteem en de amd64-architectuur. Geef de `--platform` tag op voor het bouwen van Windows-afbeeldingen of Linux-afbeeldingen voor andere architecturen. Geef het besturingssysteem en eventueel een ondersteunde architectuur op in de indeling van het besturingssysteem/de architectuur (bijvoorbeeld `--platform Linux/arm` ). Voor ARM-architecturen kunt u eventueel een variant opgeven in os/architectuur/variant-indeling (bijvoorbeeld `--platform Linux/arm64/v8` ):

| Besturingssysteem | Architectuur|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Taakuitvoer weergeven

Elke taakuitvoer genereert logboekuitvoer die u kunt inspecteren om te bepalen of de taakstappen zijn uitgevoerd. Wanneer u een taak handmatig activeert, wordt logboekuitvoer voor de taakuitvoer gestreamd naar de console en ook opgeslagen voor later ophalen. Wanneer een taak automatisch wordt geactiveerd, bijvoorbeeld door een door te voeren broncode of een update van de basisafbeelding, worden taaklogboeken alleen opgeslagen. Bekijk de logboeken voor de Azure Portal of gebruik de [opdracht az acr task logs.](/cli/azure/acr/task#az_acr_task_logs)

Zie meer over het [weergeven en beheren van taaklogboeken.](container-registry-tasks-logs.md)

## <a name="next-steps"></a>Volgende stappen

Wanneer u klaar bent om builds en onderhoud van containerafbeeldingen in de cloud te automatiseren, raadpleegt u [de reeks ACR-taken zelfstudie](container-registry-tutorial-quick-task.md).

Installeer eventueel de [Docker-extensie voor Visual Studio-code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)extensie om te werken met uw Azure-containerregisters. Pull en push installatiekopieën naar een Azure-containerregister of voer ACR-taken uit, allemaal in Visual Studio-code.

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
