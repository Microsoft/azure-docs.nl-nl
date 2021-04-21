---
title: Updates van basisafbeeldingen - Taken
description: Meer informatie over basisafbeeldingen voor containerafbeeldingen van toepassingen en over hoe een update van een basisafbeelding een Azure Container Registry activeren.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: fc501cc1db654c11675e5a4f0d19a5a56b63a165
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781177"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Over updates van basisafbeeldingen voor ACR-taken

Dit artikel bevat achtergrondinformatie over updates van de basisafbeelding van een toepassing en hoe deze updates een Azure Container Registry activeren.

## <a name="what-are-base-images"></a>Wat zijn basisafbeeldingen?

Dockerfiles die de meeste containerafbeeldingen definiëren, geven een bovenliggende afbeelding op van waaruit de afbeelding is gebaseerd, vaak aangeduid als de *basisafbeelding*. Basisinstallatiekopieën bevatten doorgaans het besturingssysteem, bijvoorbeeld [Alpine Linux][base-alpine] of [Windows Nano Server][base-windows], waarop de overige lagen van de container worden toegepast. Ook kunnen basisinstallatiekopieën toepassingsframeworks zoals [Node.js][base-node] of [.NET Core][base-dotnet] bevatten. Deze basisafbeeldingen zijn doorgaans gebaseerd op openbare upstream-afbeeldingen. Verschillende toepassingsafbeeldingen kunnen een gemeenschappelijke basisafbeelding delen.

Een basisinstallatiekopie wordt vaak door de installatiekopie-maintainer bijgewerkt om nieuwe functies of verbeteringen toe te voegen aan het besturingssysteem of framework in de installatiekopie. Beveiligingspatches zijn een andere veelvoorkomende oorzaak van een update van de basisinstallatiekopie. Wanneer deze upstream-updates optreden, moet u ook uw basisafbeeldingen bijwerken om de kritieke oplossing op te nemen. Elke toepassingsafbeelding moet vervolgens ook opnieuw worden opgebouwd om deze upstream-oplossingen op te nemen die nu zijn opgenomen in uw basisafbeelding.

In sommige gevallen, zoals een persoonlijk ontwikkelteam, kan een basisafbeelding meer dan besturingssysteem of framework opgeven. Een basisafbeelding kan bijvoorbeeld een gedeelde-serviceonderdeel-afbeelding zijn die moet worden bijgespoord. Leden van een team moeten deze basisafbeelding mogelijk bijhouden om te testen of moeten de afbeelding regelmatig bijwerken bij het ontwikkelen van toepassingsafbeeldingen.

## <a name="maintain-copies-of-base-images"></a>Kopieën van basisafbeeldingen onderhouden

Voor inhoud in uw registers die afhankelijk is van de basisinhoud die wordt onderhouden in een openbaar register, zoals Docker Hub, wordt u aangeraden de inhoud te kopiëren naar een Azure-containerregister of een ander persoonlijk register. Zorg er vervolgens voor dat u uw toepassingsafbeeldingen bouwt door te verwijzen naar de persoonlijke basisafbeeldingen. Azure Container Registry biedt een mogelijkheid [voor het importeren](container-registry-import-images.md) van afbeeldingen om eenvoudig inhoud te kopiëren uit openbare registers of andere Azure-containerregisters. In de volgende sectie wordt beschreven hoe u ACR-taken updates van basisafbeeldingen kunt bijhouden bij het bouwen van toepassingsupdates. U kunt updates van basisafbeeldingen bijhouden in uw eigen Azure-containerregisters en eventueel in upstream openbare registers.

## <a name="track-base-image-updates"></a>Updates van basisafbeeldingen bijhouden

Met ACR Tasks hebt u de mogelijkheid om automatisch installatiekopieën te maken wanneer een containerinstallatiekopie wordt bijgewerkt. U kunt deze mogelijkheid gebruiken om kopieën van openbare basiskopieën in uw Azure-containerregisters te onderhouden en bij te werken en vervolgens toepassingsafbeeldingen te herbouwen die afhankelijk zijn van basisafbeeldingen.

ACR-taken worden afhankelijkheden van basisafbeeldingen dynamisch ontdekt wanneer er een containerafbeelding wordt gebouwd. Hierdoor kan worden gedetecteerd wanneer de basisafbeelding van een toepassingsafbeelding wordt bijgewerkt. Met één vooraf geconfigureerde buildtaak kan ACR-taken automatisch elke toepassingsafbeelding herbouwen die verwijst naar de basisafbeelding. Met deze automatische detectie en herbouwing bespaart ACR-taken u de tijd en moeite die normaal gesproken nodig is om elke toepassingsafbeelding die verwijst naar uw bijgewerkte basisafbeelding handmatig bij te houden en bij te werken.

## <a name="base-image-locations"></a>Locaties van basisafbeeldingen

Voor builds van een dockerfile detecteert een ACR-taak afhankelijkheden van basisafbeeldingen op de volgende locaties:

* Hetzelfde Azure-containerregister waar de taak wordt uitgevoerd
* Een ander privé-Azure-containerregister in dezelfde of een andere regio 
* Een openbare repo in Docker Hub 
* Een openbare repo in Microsoft Container Registry

Als de basisafbeelding die is opgegeven in de instructie zich op een van deze locaties bevindt, voegt de ACR-taak een hook toe om ervoor te zorgen dat de afbeelding opnieuw wordt opgebouwd wanneer de `FROM` basis ervan wordt bijgewerkt.

## <a name="base-image-notifications"></a>Basisafbeeldingsmeldingen

De tijd tussen het moment waarop een basisafbeelding wordt bijgewerkt en wanneer de afhankelijke taak wordt geactiveerd, is afhankelijk van de locatie van de basisafbeelding:

* Basisafbeeldingen van een openbare opslagplaats in Docker Hub of **MCR:** voor basisafbeeldingen in openbare opslagplaatsen controleert een ACR-taak op updates van afbeeldingen met een willekeurig interval tussen 10 en 60 minuten. Afhankelijke taken worden dienovereenkomstig uitgevoerd.
* **Basisafbeeldingen van een Azure-containerregister:** voor basisafbeeldingen in Azure-containerregisters activeert een ACR-taak onmiddellijk een run wanneer de basisafbeelding wordt bijgewerkt. De basisafbeelding kan zich in dezelfde ACR-omgeving als waar de taak wordt uitgevoerd of in een andere ACR in elke regio.

## <a name="additional-considerations"></a>Aanvullende overwegingen

* **Basisafbeeldingen voor toepassingsafbeeldingen:** momenteel houdt een ACR-taak alleen updates van basisafbeeldingen bij voor toepassingsafbeeldingen *(runtime).* Updates van basisafbeeldingen voor tussenliggende *(buildtime)-afbeeldingen* die worden gebruikt in Dockerfiles met meerdere fases, worden niet bij het bijhouden.  

* **Standaard ingeschakeld:** wanneer u een ACR-taak maakt met de opdracht [az acr task create,][az-acr-task-create] *wordt* de taak standaard ingeschakeld voor trigger door een update van de basisafbeelding. Dat wil zeggen dat `base-image-trigger-enabled` de eigenschap is ingesteld op Waar. Als u dit gedrag in een taak wilt uitschakelen, moet u de eigenschap bijwerken naar False. Voer bijvoorbeeld de volgende [opdracht az acr task update][az-acr-task-update] uit:

  ```azurecli
  az acr task update --registry myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Trigger** voor het bijhouden van afhankelijkheden: als u een ACR-taak wilt inschakelen om de afhankelijkheden van een container-image te bepalen en bij te houden , die de basisafbeelding bevatten, moet u eerst de taak activeren om de afbeelding ten minste één keer te **bouwen.** Activeer de taak bijvoorbeeld handmatig met de [opdracht az acr task run.][az-acr-task-run]

* **Stabiele tag voor basisafbeelding:** als u een taak wilt activeren bij het bijwerken van de basisafbeelding, moet de basisafbeelding een stabiele *tag* hebben, zoals `node:9-alpine` . Dit taggen is gebruikelijk voor een basisafbeelding die is bijgewerkt met patches voor het besturingssysteem en framework naar een nieuwste stabiele release. Als de basisafbeelding wordt bijgewerkt met een nieuwe versietag, wordt er geen taak getriggerd. Zie de richtlijnen voor best practices voor meer informatie over het [taggen van afbeeldingen.](container-registry-image-tag-version.md) 

* **Andere taaktriggers:** in een taak die wordt geactiveerd door [](container-registry-tutorial-build-task.md) updates van basisafbeeldingen, kunt u ook triggers inschakelen op basis van het invoeren van de broncode [of een schema](container-registry-tasks-scheduled.md). Een update van een basisafbeelding kan ook een [taak met meerdere stappen activeren.](container-registry-tasks-multi-step.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende zelfstudies voor scenario's voor het automatiseren van builds van toepassingsafbeeldingen nadat een basisafbeelding is bijgewerkt:

* [Builds van containerafbeeldingen automatiseren wanneer een basisafbeelding wordt bijgewerkt in hetzelfde register](container-registry-tutorial-base-image-update.md)

* [Builds van containerafbeeldingen automatiseren wanneer een basisafbeelding wordt bijgewerkt in een ander register](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
