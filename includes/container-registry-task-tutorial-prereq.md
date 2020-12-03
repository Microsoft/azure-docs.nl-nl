---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 79e2ca71d6b8178be63c3429edba89cf3bb523e2
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029983"
---
## <a name="prerequisites"></a>Vereisten

### <a name="get-sample-code"></a>Voorbeeldcode ophalen

In deze zelfstudie wordt ervan uitgegaan dat u de stappen in de [vorige zelfstudie](../articles/container-registry/container-registry-tutorial-quick-task.md) hebt voltooid en dat u de voorbeeldopslagplaats hebt gesplitst en gekloond. Als u dit nog niet hebt gedaan, voert u de stappen in de sectie [Vereisten](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) van de vorige zelfstudie uit voordat u doorgaat.

### <a name="container-registry"></a>Containerregister

U moet een Azure-containerregister in uw Azure-abonnement hebben om deze zelfstudie te voltooien. Als u een register nodig hebt, zie dan de [vorige zelfstudie](../articles/container-registry/container-registry-tutorial-quick-task.md) of [Snelstartgids: Een containerregister maken met de Azure CLI](../articles/container-registry/container-registry-get-started-azure-cli.md).

### <a name="create-a-github-personal-access-token"></a>Een persoonlijk toegangstoken van GitHub maken

Als u een taak op een doorvoer naar een Git-opslagplaats wilt activeren, heeft ACR Tasks een persoonlijk toegangstoken (PAT) nodig voor toegang tot de opslagplaats. Als u nog geen PAT hebt, voert u de volgende stappen uit om er een in GitHub te genereren:

1. Navigeer naar de pagina PAT maken op GitHub op https://github.com/settings/tokens/new
1. Geef een korte **beschrijving** voor het token op, bijvoorbeeld 'Demo ACR Tasks'
1. Selecteer bereiken voor ACR om toegang te krijgen tot de opslagplaats. Voor toegang tot een openbare opslagplaats zoals in deze zelfstudie, schakelt u onder **repo** de opties **repo:status** and **public_repo** in

   ![Schermafbeelding van de pagina Persoonlijk toegangstoken genereren in GitHub][build-task-01-new-token]

   > [!NOTE]
   > Als u een PAT wilt genereren om toegang te krijgen tot een *privéopslagplaats*, selecteert u het bereik voor volledige controle van de **opslagplaats**.

1. Selecteer de knop **Token genereren** (u wordt mogelijk gevraagd om uw wachtwoord te bevestigen)
1. Kopieer en bewaar het gegenereerde token in een **beveiligde locatie** (u gebruikt dit token bij het definiëren van een taak in de volgende sectie)

   ![Schermafbeelding van het gegeneerde persoonlijke toegangstoken in GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
