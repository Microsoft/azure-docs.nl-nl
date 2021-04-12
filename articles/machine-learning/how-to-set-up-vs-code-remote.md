---
title: Verbinding maken met reken instantie in Visual Studio code (preview)
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u verbinding maakt met een Azure Machine Learning Reken instantie in Visual Studio code om interactieve Jupyter Notebook en werk belastingen voor externe ontwikkeling uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 04/08/2021
ms.openlocfilehash: 14f0d15d48193267c224f3497c24651ca3249b0b
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028579"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Verbinding maken met een Azure Machine Learning Compute-instantie in Visual Studio code (preview)

In dit artikel leert u hoe u met Visual Studio code verbinding kunt maken met een Azure Machine Learning Reken exemplaar.

Een [Azure machine learning Compute-exemplaar](concept-compute-instance.md) is een volledig beheerd werk station in de Cloud voor gegevens wetenschappers en biedt beheer-en Enter prise-gereedheids mogelijkheden voor IT-beheerders.

Er zijn twee manieren waarop u verbinding kunt maken met een reken instantie vanuit Visual Studio code:

* Externe Compute-instantie. Deze optie biedt u een volledig functionele ontwikkel omgeving voor het maken van uw machine learning-projecten.
* Externe Jupyter Notebook server. Met deze optie kunt u een reken instantie instellen als een externe Jupyter Notebook server.

## <a name="configure-a-remote-compute-instance"></a>Een externe Compute-instantie configureren

Als u een extern Compute-exemplaar voor ontwikkeling wilt configureren, hebt u enkele vereisten nodig.

* Visual Studio code-extensie Azure Machine Learning. Zie de [Azure machine learning Visual Studio code extension Setup Guide (Engelstalig](tutorial-setup-vscode-extension.md)) voor meer informatie.
* Azure Machine Learning werk ruimte. [Gebruik de Azure machine learning Visual Studio code-extensie om een nieuwe werk ruimte te maken](how-to-manage-resources-vscode.md#create-a-workspace) als u er nog geen hebt.
* Azure Machine Learning Compute-instantie. [Gebruik de Azure machine learning Visual Studio code-extensie om een nieuw reken exemplaar te maken](how-to-manage-resources-vscode.md#create-compute-instance) als u er nog geen hebt.

Verbinding maken met uw externe Compute-instantie:

# <a name="vs-code"></a>[VS-code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning extensie

1. In VS code opent u de extensie Azure Machine Learning.
1. Vouw het knoop punt **reken instanties** in uw uitbrei ding uit.
1. Klik met de rechter muisknop op het reken exemplaar waarmee u verbinding wilt maken en selecteer **verbinding maken met Compute instance**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Verbinding maken met reken exemplaar Visual Studio code Azure ML extension" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Opdracht palet

1. Open in VS code het opdracht palet door **> opdracht venster weer geven** te selecteren.
1. Voer in het tekstvak **Azure ml: verbinding maken met reken instantie** in.
1. Selecteer uw abonnement.
1. Selecteer uw werk ruimte.
1. Selecteer uw reken instantie of maak een nieuwe.

# <a name="studio"></a>[Studio](#tab/studio)

Ga naar [ml.Azure.com](https://ml.azure.com)

> [!IMPORTANT]
> Als u verbinding wilt maken met uw externe Compute-exemplaar vanuit Visual Studio code, moet u ervoor zorgen dat het account waarmee u bent aangemeld in Azure Machine Learning Studio hetzelfde is als de naam die u in Visual Studio code gebruikt.

### <a name="compute"></a>Compute

1. Selecteer het tabblad **Compute**
1. Selecteer in de kolom *toepassings* -URI **VS code** voor het reken exemplaar waarmee u verbinding wilt maken.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Verbinding maken met reken instantie versus code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notebook

1. Het tabblad **notebook** selecteren
1. Selecteer op het tabblad *notebook* het bestand dat u wilt bewerken.
1. Selecteer **Editors > bewerken in VS code (preview)**.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Verbinding maken met reken instantie versus code Azure ML notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

---

Er wordt een nieuw venster gestart voor uw externe Compute-exemplaar. Wanneer u probeert verbinding te maken met een extern Compute-exemplaar, worden de volgende taken uitgevoerd:

1. Autorisatie. Er zijn enkele controles uitgevoerd om ervoor te zorgen dat de gebruiker die een verbinding probeert te maken, gemachtigd is om het reken exemplaar te gebruiken.
1. De VS code-externe server wordt geïnstalleerd op het reken exemplaar.
1. Er wordt een WebSocket-verbinding tot stand gebracht voor realtime-interactie.

Zodra de verbinding tot stand is gebracht, wordt deze persistent gemaakt. Er wordt een token uitgegeven aan het begin van de sessie, die automatisch wordt vernieuwd om de verbinding met uw reken exemplaar te onderhouden.

Nadat u verbinding hebt gemaakt met uw externe Compute-exemplaar, gebruikt u de editor voor het volgende:

* [Bestanden maken en beheren op uw externe reken instantie of bestands share](https://code.visualstudio.com/docs/editor/codebasics).
* Gebruik de [geïntegreerde VS code-Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) om [opdrachten en toepassingen uit te voeren op uw externe Compute-exemplaar](how-to-access-terminal.md).
* [Fouten opsporen in uw scripts en toepassingen](https://code.visualstudio.com/Docs/editor/debugging)
* [VS code gebruiken voor het beheren van uw Git-opslag plaatsen](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Reken exemplaar als externe notebook server configureren

Als u een reken instantie wilt configureren als een externe Jupyter Notebook server, hebt u enkele vereisten nodig:

* Visual Studio code-extensie Azure Machine Learning. Zie de [Azure machine learning Visual Studio code extension Setup Guide (Engelstalig](tutorial-setup-vscode-extension.md)) voor meer informatie.
* Azure Machine Learning werk ruimte. [Gebruik de Azure machine learning Visual Studio code-extensie om een nieuwe werk ruimte te maken](how-to-manage-resources-vscode.md#create-a-workspace) als u er nog geen hebt.

Verbinding maken met een reken instantie:

1. Open een Jupyter Notebook in Visual Studio code.
1. Wanneer de geïntegreerde laptop ervaring wordt geladen, selecteert u **Jupyter server**.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning externe Jupyter Notebook server-vervolg keuzelijst starten](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    U kunt ook het opdracht palet gebruiken:

    1. Open het opdrachtenpalet in door **Beeld > Opdrachtenpalet** te selecteren in het menu.
    1. Voer in het tekstvak in `Azure ML: Connect to Compute instance Jupyter server` .

1. Kies `Azure ML Compute Instances` uit de lijst met Jupyter-server opties.
1. Selecteer uw abonnement in de lijst met abonnementen. Als u uw standaard Azure Machine Learning-werk ruimte eerder hebt geconfigureerd, wordt deze stap overgeslagen.
1. Selecteer uw werk ruimte.
1. Selecteer uw reken instantie in de lijst. Als u er nog geen hebt, selecteert u **nieuwe Azure ml Compute-exemplaar maken** en volgt u de prompts om er een te maken.
1. Als u de wijzigingen wilt door voeren, moet u Visual Studio code opnieuw laden.
1. Een Jupyter Notebook openen en een cel uitvoeren.

> [!IMPORTANT]
> U **moet** een cel uitvoeren om de verbinding tot stand te brengen.

Op dit moment kunt u door gaan met het uitvoeren van cellen in uw Jupyter Notebook.

> [!TIP]
> U kunt ook werken met python-script bestanden (. py) met Jupyter-achtige code cellen. Zie [Visual Studio code python Interactive documentation](https://code.visualstudio.com/docs/python/jupyter-support-py)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nu u Visual Studio code Remote hebt ingesteld, kunt u een reken instantie gebruiken als externe Compute van Visual Studio code om [uw code interactief te debuggen](how-to-debug-visual-studio-code.md).

[Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.
