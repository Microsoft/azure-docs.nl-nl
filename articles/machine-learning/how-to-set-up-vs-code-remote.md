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
ms.date: 11/16/2020
ms.openlocfilehash: 3f3794ed4748eda44e863ff4627dedc0a34691af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98070096"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Verbinding maken met een Azure Machine Learning Compute-instantie in Visual Studio code (preview)

In dit artikel leert u hoe u met Visual Studio code verbinding kunt maken met een Azure Machine Learning Reken exemplaar.

Een [Azure machine learning Compute-exemplaar](concept-compute-instance.md) is een volledig beheerd werk station in de Cloud voor gegevens wetenschappers en biedt beheer-en Enter prise-gereedheids mogelijkheden voor IT-beheerders.

Er zijn twee manieren waarop u verbinding kunt maken met een reken instantie vanuit Visual Studio code:

* Externe Jupyter Notebook server. Met deze optie kunt u een reken instantie instellen als een externe Jupyter Notebook server.
* [Visual Studio code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview). Met Visual Studio code Remote Development kunt u een container, een externe computer of het Windows-subsysteem voor Linux (WSL) gebruiken als een ontwikkel omgeving met volledige functionaliteit.

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

## <a name="configure-compute-instance-remote-development"></a>Externe ontwikkeling van reken instantie configureren

Voor een volledig functionele externe ontwikkel ervaring hebt u enkele vereisten nodig:

* [Visual Studio code remote SSH-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* Reken instantie met SSH-functionaliteit. [Zie de hand leiding Create a Compute instance](how-to-create-manage-compute-instance.md)voor meer informatie.

> [!NOTE]
> Op Windows-platforms moet u [een met OpenSSH compatibele SSH-client installeren](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) als deze nog niet aanwezig is. PuTTy wordt niet ondersteund in Windows omdat de SSH-opdracht zich in het pad moet bevinden.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>De IP-en SSH-poort voor uw reken instantie ophalen

1. Ga naar de Azure Machine Learning Studio op https://ml.azure.com/ .
2. Selecteer uw [werk ruimte](concept-workspace.md).
1. Klik op het tabblad **Compute instances** .
1. Klik in de kolom **toepassings-URI** op de **SSH** -koppeling van het reken exemplaar dat u wilt gebruiken als een externe compute. 
1. Noteer het IP-adres en de SSH-poort in het dialoog venster. 
1. Sla uw persoonlijke sleutel op in de map ~/.SSH/op de lokale computer. Open bijvoorbeeld een editor voor een nieuw bestand en plak de sleutel in: 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   De persoonlijke sleutel ziet er ongeveer als volgt uit:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Wijzig de machtigingen voor het bestand om er zeker van te zijn dat alleen u het bestand kunt lezen.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Exemplaar als host toevoegen

Open het bestand `~/.ssh/config` (Linux) of `C:\Users<username>.ssh\config` (Windows) in een editor en voeg een nieuwe vermelding toe die vergelijkbaar is met de onderstaande inhoud:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Hier vindt u enkele details over de velden:

|Veld|Description|
|----|---------|
|Host|Een wille keurige steno gebruiken voor het reken exemplaar |
|HostName|Dit is het IP-adres van het reken exemplaar |
|Poort|Dit is de poort die wordt weer gegeven in het bovenstaande SSH-dialoog venster |
|Gebruiker|Dit moet worden `azureuser` |
|IdentityFile|Moet verwijzen naar het bestand waarin u de persoonlijke sleutel hebt opgeslagen |

Nu moet u uw reken exemplaar SSHen met behulp van de steno die u hierboven hebt gebruikt `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>VERSUS code verbinden met het exemplaar

1. Klik op het pictogram externe-SSH van de activiteiten balk van Visual Studio om uw SSH-configuraties weer te geven.

1. Klik met de rechter muisknop op de SSH-hostconfiguratie die u zojuist hebt gemaakt.

1. Selecteer **verbinding maken met host in het huidige venster**. 

Vanaf hier kunt u zich helemaal op het reken proces bevinden en nu bewerken, fouten opsporen, Git gebruiken, uitbrei dingen, enzovoort, net als bij uw lokale Visual Studio-code.

## <a name="next-steps"></a>Volgende stappen

Nu u Visual Studio code Remote hebt ingesteld, kunt u een reken instantie gebruiken als externe Compute van Visual Studio code om [uw code interactief te debuggen](how-to-debug-visual-studio-code.md).

[Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.
