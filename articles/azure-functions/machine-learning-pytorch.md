---
title: Een PyTorch-model implementeren als een Azure Functions-toepassing
description: Gebruik een vooraf getraind ResNet 18 Neural-netwerk van PyTorch met Azure Functions om 1 van 1000 ImageNet-labels toe te wijzen aan een installatiekopie.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 8891c29e5d8d06df6292d06ec06e5e57fb9880e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422838"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Zelfstudie: Een vooraf getraind afbeeldingsclassificatie model implementeren in Azure Functions met PyTorch

In dit artikel leert y hoe u Python, PyTorch en Azure Functions gebruikt om een vooraf getraind model te laden om een afbeelding te classificeren op basis van de inhoud ervan. Omdat u lokaal werkt en geen Azure-resources in de cloud maakt, zijn er geen kosten om deze zelfstudie te voltooien.

> [!div class="checklist"]
> * Initialiseer een lokale omgeving voor het ontwikkelen van Azure Functions in Python.
> * Importeer een vooraf getraind machine learning-model van PyTorch in een functie-app.
> * Bouw een serverloze HTTP API om een afbeelding te classificeren als een van 1000 [klassen](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) van ImageNet.
> * Gebruik de API van een web-app.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Python 3.7.4 of hoger](https://www.python.org/downloads/release/python-374/). (Python 3.8.x en Python 3.6.x zijn ook geverifieerd met Azure Functions.)
- De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Een code-editor zoals [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Controle van vereisten

1. Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 2.7.1846 of hoger zijn.
1. Voer `python --version` (Linux/MacOS) of `py --version` (Windows) uit om uw Python-versierapporten 3.7.x te controleren.

## <a name="clone-the-tutorial-repository"></a>De zelfstudie-opslagplaats klonen

1. Kloon in een terminal- of opdrachtvenster de volgende opslagplaats met Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navigeer naar de map en bekijk de inhoud ervan.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* is de werkmap voor de zelfstudie.
    - *end* is het uiteindelijke resultaat en de volledige implementatie voor uw referentie.
    - *resources* bevatten het machine learning-model en de helper-bibliotheken.
    - *frontend* is een website die de functie-app aanroept.

## <a name="create-and-activate-a-python-virtual-environment"></a>Een virtuele Python-omgeving maken en activeren

Ga naar de *start*-map en voer de volgende opdrachten uit om een virtuele omgeving met de naam `.venv` te maken en te activeren.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Als Python het venv-pakket niet heeft geïnstalleerd in uw Linux-distributie, voert u de volgende opdracht uit:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

U voert alle volgende opdrachten uit in deze geactiveerde virtuele omgeving. (om de virtuele omgeving te sluiten, voert u `deactivate` uit.)


## <a name="create-a-local-functions-project"></a>Een lokaal Functions-project maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject met een individuele standaardfunctie met de naam `classify` die een HTTP-eindpunt biedt. U kunt meer specifieke code toevoegen in een latere sectie.

1. Gebruik in de map *start* de Azure Functions Core Tools om een Python-functie-app te initialiseren:

    ```
    func init --worker-runtime python
    ```

    Na initialisatie bevat de map *start* verschillende bestanden voor het project,waaronder configuratiebestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.

    > [!TIP]
    > Omdat een functieproject is gekoppeld aan een specifieke runtime, moeten alle functies in het project met dezelfde taal worden geschreven.

1. Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie. Maak met `func new` een submap met de naam van de functie die een codebestand bevat dat geschikt is voor de taal van het project en een configuratiebestand met de naam *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Met deze opdracht maakt u een map die overeenkomt met de naam van de functie, *classify*. In die map zijn twee bestanden: *\_\_init\_\_.py*, die de functiecode bevat en *function.json-* , waarmee de trigger van de functie en de bijbehorende invoer- en uitvoerbindingen worden beschreven. Zie [De inhoud van het bestand controleren](./create-first-function-cli-python.md#optional-examine-the-file-contents) in de quickstart voor Python voor meer informatie over de inhoud van deze bestanden.


## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Start de functie door de lokale Azure Functions runtime host te starten in de map *start*:

    ```
    func start
    ```

1. Zodra het eindpunt `classify` wordt weergegeven in de uitvoer, gaat u naar de URL, ```http://localhost:7071/api/classify?name=Azure```. Het bericht "Hello Azure!" moet worden weergegeven in de uitvoer.

1. Gebruik **Ctrl**-**C** om de host te stoppen.


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Het PyTorch-model importeren en helpercode toevoegen

Als u de functie `classify` wilt wijzigen om een afbeelding te classificeren op basis van de inhoud, gebruikt u een vooraf getraind [ResNet](https://arxiv.org/abs/1512.03385)-model. Met het vooraf getrainde model, dat afkomstig is van [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), wordt een afbeelding in 1 van 1000 [ImageNet-klassen](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a) geclassificeerd. U voegt vervolgens een helpercode en afhankelijkheden toe aan uw project.

1. Voer in de map *start* de volgende opdracht uit om de voorspellingscode en labels te kopiëren naar de map *classify*.

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Controleer of de map *classify* de bestanden bevat met de naam *predict.py* en *labels.txt*. Als dat niet het geval is, controleert u of u de opdracht hebt uitgevoerd in de map *start*.

1. Open *start/requirements.txt* in een teksteditor en voeg de afhankelijkheden toe die vereist zijn voor de helpercode. Deze zien er als volgt uit:

    ```txt
    azure-functions
    requests
    -f https://download.pytorch.org/whl/torch_stable.html
    torch==1.5.0+cpu
    torchvision==0.6.0+cpu
    ```

1. Sla *requirements.txt* op en voer vervolgens de volgende opdracht uit vanuit de map *start* om de afhankelijkheden te installeren.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

De installatie kan enkele minuten duren. Intussen u kunt doorgaan met het wijzigen van de functie in de volgende sectie.
> [!TIP]
> >In Windows kan de fout optreden "Kan pakketten niet installeren vanwege een EnvironmentError: [errno 2] Geen bestand of map met die naam:" gevolgd door een lange padnaam naar een bestand zoals *sharded_mutable_dense_hashtable. cpython-37.pyc*. Deze fout treedt meestal op omdat de diepte van het mappad te lang wordt. In dit geval moet u de registersleutel `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` instellen op `1` om lange paden in te schakelen. U kunt ook controleren waar uw Python-interpreter is geïnstalleerd. Als deze locatie een lang pad heeft, moet u proberen het opnieuw te installeren in een map met een korter pad.

## <a name="update-the-function-to-run-predictions"></a>De functie bijwerken voor het uitvoeren van voorspellingen

1. Open *classify/\_\_init\_\_.py* in een teksteditor en voeg de volgende regels toe na de bestaande `import` instructies om de standaard JSON-bibliotheek en de helpers *predict* te importeren:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Vervang de volledige inhoud van de functie `main` door de volgende code:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Deze functie ontvangt een afbeeldings-URL in een queryreeks parameter met de naam `img`. Vervolgens wordt `predict_image_from_url` aangeroepen vanuit de helperbibliotheek om de afbeelding te downloaden en classificeren met behulp van het PyTorch-model. De functie retourneert vervolgens een HTTP-antwoord met de resultaten.

    > [!IMPORTANT]
    > Omdat dit HTTP-eindpunt wordt aangeroepen door een webpagina die wordt gehost op een ander domein, bevat het antwoord een `Access-Control-Allow-Origin`-header om te voldoen aan de vereisten voor CORS (Cross-Origin Resource Sharing) van de browser.
    >
    > Wijzig in een productietoepassing `*` naar de specifieke oorsprong van de webpagina voor extra beveiliging.

1. Sla de wijzigingen op en controleer of de installatie van afhankelijkheden is voltooid, start de lokale functiehost opnieuw met `func start`. Zorg ervoor dat u de host uitvoert in de map *starten* met de virtuele omgeving geactiveerd. Anders wordt de host gestart, maar worden er fouten weergeven bij het aanroepen van de functie.

    ```
    func start
    ```

1. Open in een browser de volgende URL om de functie aan te roepen met de URL van een afbeelding van een Berner sennenhond en te controleren of de geretourneerde JSON de afbeelding als een Berner sennenhond heeft geclassificeerd.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. Zorg ervoor dat de host actief blijft omdat u deze in de volgende stap gebruikt.

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Voer de frontend van de lokale web-app uit om de functie te testen

Als u het aanroepen van het functie-eindpunt vanuit een andere web-app wilt testen, is er een eenvoudige app in de map *frontend* van de opslagplaats.

1. Open een nieuwe terminal of opdrachtprompt en activeer de virtuele omgeving (zoals eerder beschreven onder [Een virtuele Python-omgeving maken en activeren](#create-and-activate-a-python-virtual-environment)).

1. Ga naar de map *frontend* van de opslagplaats.

1. Een HTTP-server starten met Python:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Ga in een browser naar `localhost:8000` en voer een van de volgende foto-URL's in het tekstvak in of gebruik de URL van een openbaar toegankelijke afbeelding.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Selecteer **Verzenden** om het functie-eindpunt aan te roepen voor het classificeren van de afbeelding.

    ![Schermopname van voltooid project](media/machine-learning-pytorch/screenshot.png)

    Als de browser een fout meldt bij het verzenden van de afbeeldings-URL, controleert u de terminal waarin u de functie-app uitvoert. Als er een foutbericht wordt weergegeven als "Geen module gevonden 'PIL'", hebt u de functie-app mogelijk gestart in de map *start* zonder eerst de virtuele omgeving te activeren die u eerder hebt gemaakt. Als u nog steeds fouten ziet, voert u `pip install -r requirements.txt` opnieuw uit met de virtuele omgeving geactiveerd en zoekt u naar fouten.

## <a name="clean-up-resources"></a>Resources opschonen

Omdat deze hele zelfstudie lokaal op uw computer wordt uitgevoerd, zijn er geen Azure-resources of -services om op te schonen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een HTTP API-eindpunt kunt bouwen en aanpassen met Azure Functions om afbeeldingen te classificeren met behulp van een PyTorch-model. U hebt ook geleerd hoe u de API aanroept vanuit een web-app. U kunt de technieken in deze zelfstudie gebruiken om meer en minder complexe API's te ontwikkelen, die alle worden uitgevoerd op het serverloze rekenmodel dat wordt geleverd door Azure Functions.

Zie ook:

- [De functie implementeren in Azure met Visual Studio](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Ontwikkelaarshandleiding voor Azure Functions Python](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [De functie implementeren in Azure Functions met behulp van de Azure CLI-handleiding](./functions-run-local.md#publish)
