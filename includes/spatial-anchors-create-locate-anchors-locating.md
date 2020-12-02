---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993125"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Een ruimtelijk anker in de cloud lokaliseren

Een van de belangrijkste redenen om Azure Spatial Anchors te gebruiken, is dat u een eerder opgeslagen ruimtelijk anker in de cloud kunt lokaliseren. Er zijn verschillende manieren waarop u een ruimtelijk anker in de cloud kunt lokaliseren. Voor een watcher kunt u één strategie per keer gebruiken.
- Lokaliseer ankers op id.
- Lokaliseer ankers die zijn verbonden met een eerder gelokaliseerd anker. U vindt [hier](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md) informatie over ankerrelaties.
- Lokaliseer een anker met [grove herlokalisatie](../articles/spatial-anchors/concepts/coarse-reloc.md).

> [!NOTE]
> Telkens wanneer u een anker lokaliseert, probeert Azure Spatial Anchors de omgevingsgegevens te gebruiken die zijn verzameld om de visuele informatie op het anker te verbeteren. Als u problemen ondervindt bij het lokaliseren van een anker, kan het nuttig zijn om een anker te maken en deze vervolgens meerdere keren vanuit verschillende hoeken en lichtomstandigheden te lokaliseren.

Als u ruimtelijke ankers in de cloud lokaliseert op id, moet u deze id in de back-endservice van de toepassing opslaan en toegankelijk maken voor alle apparaten die er op de juiste manier kunnen worden geverifieerd. Voor een voorbeeld hiervan raadpleegt u [Zelfstudie: ruimtelijke ankers met meerdere apparaten delen](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Instantieer een `AnchorLocateCriteria`-object, stel de id's in die u zoekt en roep de `CreateWatcher`-methode voor de sessie aan door uw `AnchorLocateCriteria` op te geven.