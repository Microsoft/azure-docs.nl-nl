---
title: 'Zelfstudie: een Jupyter Notebook maken en uitvoeren - Azure Notebooks Preview'
description: Leer hoe u in Azure Notebooks Preview een Jupyter Notebook kunt maken en uitvoeren dat het proces van lineaire regressie in de datawetenschap demonstreert.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: 47e9a07c7b8abffc06bfd30a792af46ba04adf5c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844468"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Zelfstudie: een Jupyter Notebook maken en uitvoeren met Python

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

In deze zelfstudie wordt u begeleid bij het gebruik van Azure Notebooks om een compleet Jupyter Notebook te maken waarmee eenvoudige lineaire regressie wordt gedemonstreerd. In de loop van deze zelfstudie maakt u kennis met de gebruikersinterface van Jupyter Notebooks voor het maken van verschillende cellen, het uitvoeren van cellen en het presenteren van het notebook als een diavoorstelling.

Het voltooide notebook is te vinden op [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Deze zelfstudie begint echter met een nieuw project en een leeg notebook, zodat u het maken ervan stap voor stap kunt ervaren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een project-notebook maken met enkele voorbeeldgegevens
> * De notebook-interface gebruiken om diverse typen cellen te maken
> * Het notitieblok uitvoeren
> * Het notebook opslaan
> * Fouten opsporen in het notebook in Visual Studio Code

## <a name="create-the-project"></a>Het project maken

1. Ga naar [Azure Notebooks](https://notebooks.azure.com) en meld u aan. (Zie [Quickstart: aanmelden bij Azure Notebooks](quickstart-sign-in-azure-notebooks.md)) voor meer informatie.

1. Selecteer op de openbare profielpagina **Mijn projecten** bovenaan de pagina:

    ![De koppeling Mijn projecten bovenaan in het browservenster](media/quickstarts/my-projects-link.png)

1. Selecteer op de pagina **Mijn projecten** de optie **+ Nieuw project** (sneltoets: n). Als het browservenster smal is, wordt de knop mogelijk alleen weergegeven als **+** :

    ![De opdracht Nieuw project op de pagina Mijn projecten](media/quickstarts/new-project-command.png)

1. Voer in de pop-up **Nieuw project maken** die wordt weergegeven de volgende gegevens in of stel deze in, en selecteer **Maken**:

   - **Projectnaam**: Voorbeeld van lineaire regressie: Cricket Chirps
   - **Project-ID**: linear-regression-example
   - **Openbaar project**: (uitgeschakeld)
   - **Een README.md maken** : (uitgeschakeld)

1. Na enkele ogenblikken gaat Azure Notebooks naar het nieuwe project.

## <a name="create-the-data-file"></a>Het gegevensbestand maken

Het lineaire-regressiemodel dat u in het notebook maakt, haalt gegevens uit het bestand *cricket_chirps.csv* in uw project. U kunt dit bestand maken door het te kopiëren uit [GitHub - Azure Notebooks Samples](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), of door de gegevens rechtstreeks in te voeren. In de volgende secties worden beide benaderingen beschreven.

### <a name="upload-the-data-file"></a>Het gegevensbestand uploaden

1. Selecteer op het projectdashboard in Azure Notebooks **Uploaden** > **Van URL**
1. Voer in de pop-up de volgende URL in bij **Bestands-URL** en *cricket_chirps.csv* bij **Bestandsnaam** en selecteer **Gereed**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Het bestand *cricket_chirps.csv* zou nu in de bestandslijst van uw project moeten staan:

    ![Pas gemaakt CSV-bestand in de projectbestandslijst](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Een nieuw bestand maken

1. Selecteer op het projectdashboard in Azure Notebooks **+ Nieuw** > **Leeg bestand**
1. Er wordt een veld weergegeven in de bestandslijst van het project. Voer *cricket_chirps.csv* in en druk op Enter.
1. Klik met de rechtermuisknop op *cricket_chirps.csv* en selecteer **Bestand bewerken**.
1. Voer in de editor die daarna wordt weergegeven de volgende gegevens in:

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Selecteer **Bestand opslaan** om het bestand op te slaan en terug te gaan naar het projectdashboard.

## <a name="install-project-level-packages"></a>Pakketten op projectniveau installeren

Binnen een notebook kunt u altijd opdrachten zoals `!pip install` in een codecel gebruiken om vereiste pakketten te installeren. Deze opdrachten worden echter elke keer dat u de codecellen van het notebook uitvoert, uitgevoerd, en kunnen veel tijd in beslag nemen. Daarom kunt u in plaats daarvan pakketten op projectniveau installeren met behulp van een `requirements.txt`-bestand.

1. Gebruik het proces dat wordt beschreven in [Een nieuw bestand maken](#create-a-file-from-scratch) om een bestand met de naam `requirements.txt` te maken met de volgende inhoud:

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    U kunt desgewenst ook een `requirements.txt`-bestand uploaden vanaf uw lokale computer, zoals beschreven op [Het gegevensbestand uploaden](#upload-the-data-file).

1. Selecteer **Projectinstellingen** op het projectdashboard.
1. Selecteer het tabblad **Omgeving** in de pop-up die wordt weergegeven en selecteer **+Toevoegen**.
1. Kies in de eerste vervolgkeuzelijst (de bewerking) onder **Stappen voor het instellen van de omgeving** het bestand **Requirements.txt**.
1. Kies in de tweede vervolgkeuzelijst (de bestandsnaam) *requirements.txt* (het bestand dat u hebt gemaakt).
1. Kies in de derde vervolgkeuzelijst (de Python-versie) **Python versie 3.6**.
1. Selecteer **Opslaan**.

![Het bestand requirements.txt opgeven op het tabblad Omgeving in de Projectinstellingen](media/tutorial/tutorial-requirements-txt.png)

Na deze installatiestap wordt elk notebook dat u in het project uitvoert, uitgevoerd in een omgeving waar die pakketten zijn geïnstalleerd.

## <a name="create-and-run-a-notebook"></a>Een notebook maken en uitvoeren

Nu het gegevensbestand gereed is en de projectomgeving is ingesteld, kunt u het notebook maken en openen.

1. Selecteer op het projectdashboard **+ Nieuw** > **Notebook**.
1. Voer in de pop-up *Linear Regression Example - Cricket Chirps.ipynb* in bij **Itemnaam**, kies **Python 3.6** als de taal en selecteer **Nieuw**.
1. Wanneer het nieuwe notebook wordt weergegeven in de bestandslijst, selecteert u het om het notebook te starten. Er wordt automatisch een nieuw browsertabblad geopend.
1. Omdat u het bestand *requirements.txt* in de omgevingsinstellingen hebt, ziet u het bericht 'Wacht tot uw container is voorbereid'. U kunt **OK** selecteren om het bericht te sluiten en door te gaan met werken in het notebook, maar u kunt geen codecellen uitvoeren voordat de omgeving volledig is ingesteld.
1. Het notebook opent standaard in de Jupyter-interface met één lege codecel.

    [![Aanvankelijke weergave van een nieuw notebook in Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Rondleiding door de notebook-interface

Terwijl het notebook wordt uitgevoerd kunt u code- en Markdown-cellen toevoegen, deze cellen uitvoeren en de werking van het notebook beheren. Maar het is de moeite waard om eerst een paar minuten de tijd te nemen om kennis te maken met de interface. Selecteer voor de volledige documentatie de menuopdracht **Help** > **Notebook Help**.

Bovenaan het venster ziet u de volgende items:

(A) De naam van uw notebook, die u kunt bewerken door erop te klikken.
(B) Knoppen om naar het project waarin het notebook zich bevindt en uw projectdashboard te gaan, die nieuwe tabbladen openen in uw browser.
(C) Een menu met opdrachten voor het werken met het notebook.
(D) Een werkbalk met snelkoppelingen voor veelvoorkomende bewerkingen.
(E) Het bewerkingscanvas met cellen.
(F) Indicator die aangeeft of het notebook wordt vertrouwd (standaard is **Niet vertrouwd**).
(G) De kernel die wordt gebruikt om het notebook uit te voeren, met een activiteitsindicator.

[![Primaire gebieden van de Jupyter-gebruikersinterface](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter biedt een ingebouwde rondleiding door de primaire elementen van de gebruikersinterface. Start de rondleiding door de opdracht **Help** > **User Interface Tour** te selecteren en door de pop-ups te klikken.

De groepen menuopdrachten zijn als volgt:

| Menu | Beschrijving |
| --- | --- |
| File | Opdrachten voor het beheren van het notebook-bestand, waaronder opdrachten voor het maken en kopiëren van notebooks, het weergeven van een afdrukvoorbeeld en het downloaden van het notebook in diverse indelingen. |
| Edit | Gebruikelijke opdrachten voor het knippen, kopiëren en plakken van cellen, het zoeken en vervangen van waarden, het beheren van celbijlagen en het invoegen van afbeeldingen.  |
| Weergave | Opdrachten voor het beheren van de zichtbaarheid van verschillende onderdelen van de Jupyter-gebruikersinterface. |
| Invoegen | Opdrachten voor het invoegen van een nieuwe cel boven of onder de huidige cel. U gebruikt deze opdrachten veel bij het maken van een notebook. |
| Cell | Met de verschillende **Run**-opdrachten voert u een of meer cellen uit in verschillende combinaties. De **Cell Type**-opdrachten wijzigen het type van een cel in **Code**, **Markdown** en **Raw NBConvert** (tekst zonder opmaak). De opdrachten **Current Outputs** en **All Outputs** bepalen hoe de uitvoer van uitgevoerde code wordt getoond, en bevatten een opdracht om alle uitvoer te wissen. |
| Kernel | Opdrachten om te beheren hoe code in de kernel wordt uitgevoerd, samen met **Change kernel** om de taal of Python-versie te wijzigen die gebruikt wordt om het notebook uit te voeren. |
| Gegevens | Opdrachten voor het uploaden en downloaden van bestanden van het project of de sessie. Zie [Werken met projectgegevensbestanden](work-with-project-data-files.md) |
| Widgets | Opdrachten voor het beheren van [Jupyter-widgets](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), die extra mogelijkheden bieden voor visualisatie, toewijzing en plotten.|
| Help | Opdrachten die help en documentatie bieden voor de Jupyter-interface. |

De meeste opdrachten op de werkbalk hebben overeenkomstige menuopdrachten. Een uitzondering is **Enter/Edit RISE Slideshow**; deze opdracht wordt besproken in [Notebooks delen en presenteren](present-jupyter-notebooks-slideshow.md).

U gebruikt een aantal van deze opdrachten wanneer u het notebook vult in de volgende secties.

## <a name="create-a-markdown-cell"></a>Een Markdown-cel maken

1. Klik in de eerste lege cel die wordt weergegeven op het notebookcanvas. Standaard heeft een cel het type **Code**, wat betekent dat de cel bedoeld is om uitvoerbare code te bevatten voor de geselecteerde kernel (Python, R of F#). Het huidige type wordt weergegeven in de typevervolgkeuzelijst op de werkbalk:

    ![Vervolgkeuzelijst met celtypen op de werkbalk](media/tutorial/tutorial-cell-type-drop-down.png)

1. Wijzig het celtype in **Markdown** met behulp van de vervolgkeuzelijst op de werkbalk, of gebruik de menuopdracht **Cell** > **Cell Type** > **Markdown**:

    ![De menuopdracht Cell Type](media/tutorial/tutorial-cell-type-menu.png)

1. Klik in de cel om te beginnen met bewerken en voer de volgende Markdown in:

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Om de Markdown om te zetten in HTML voor de browser selecteert u de opdracht **Run** op de werkbalk of gebruikt u de opdracht **Cell** > **Run Cells**. De Markdown-code voor opmaak en koppelingen worden nu weergegeven zoals verwacht in een browser.

1. Wanneer u de laatste cel in het notebook uitvoert, maakt Jupyter automatisch een nieuwe cel onder degene die u hebt uitgevoerd. Plaats meer Markdown in deze cel door de stappen in deze sectie te herhalen met de volgende Markdown:

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Dubbelklik in de weergegeven cel om de Markdown te bewerken. Voer de cel uit om de HTML weer te genereren nadat u wijzigingen hebt aangebracht.

## <a name="create-a-code-cell-with-commands"></a>Een codecel met opdrachten maken

Zoals uitgelegd in de bovenstaande Markdown-cel, kunt u rechtstreeks opdrachten opnemen in het notebook. U kunt opdrachten gebruiken om pakketten te installeren, curl of wget uit te voeren om gegevens op te halen, of wat u maar wilt. Jupyter-notebooks worden in feite uitgevoerd op een virtuele Linux-machine, zodat u beschikt over de volledige opdrachtenset van Linux.

1. Voer in de codecel die werd weergegeven nadat u **Run** hebt gebruikt in de vorige Markdown-cel de volgende opdrachten in. Als er geen nieuwe cel wordt weergegeven, maakt u er een met **Insert** > **Insert Cell Below**, of gebruikt u de knop **+** op de werkbalk.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Maak voordat u de cel uitvoert een nieuwe cel met de knop **+** op de werkbalk, stel deze in op Markdown en voer de volgende uitleg in:

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Selecteer de opdracht **Cell** > **Run All** om alle cellen in het notebook uit te voeren. U ziet dat de Markdown-cellen worden weergegeven als HTML en dat de opdracht wordt uitgevoerd in de kernel, en de kernel-indicator zoals in de Markdown zelf wordt beschreven:

    ![Bezet-indicator voor de notebook-kernel](media/tutorial/tutorial-kernel-busy.png)

1. Het kost ook enige tijd om alle `pip install`-opdrachten uit te voeren, en omdat u deze pakketten al in de projectomgeving hebt geïnstalleerd (en omdat ze ook standaard in Azure Notebooks zijn opgenomen), ziet u veel berichten die luiden: 'Vereiste reeds voldaan'. Het kan afleiden om al die uitvoer te zien, dus selecteer die cel (met één klik) en gebruik **Cell** > **Cell Outputs** > **Toggle** om de uitvoer te verbergen. U kunt ook de opdracht **Clear** in hetzelfde submenu gebruiken om de uitvoer helemaal te verwijderen.

    De opdracht **Toggle** verbergt alleen de meest recente uitvoer van de cel, en als u de cel uitvoert, wordt de uitvoer weer weergegeven.

1. Omdat de pakketten in de projectomgeving zijn geïnstalleerd, gebruikt u `#` om de `! pip install`-opdrachten uit te commentariëren. Op deze manier kunnen ze in het notebook blijven staan als instructiemateriaal, maar kosten geen tijd om uit te voeren en produceren geen overbodige uitvoer. In dit geval worden doordat de uitgecommentarieerde opdrachten in het notebook blijven staan, ook de afhankelijkheden van het notebook gedocumenteerd.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>De resterende cellen maken

Om de rest van het notebook te vullen, maakt u vervolgens een reeks Markdown- en codecellen. Maak voor elke hieronder genoemde cel eerst de nieuwe cel, stel het type in en plak de inhoud erin.

U kunt wachten met uitvoeren van het notebook tot nadat u alle cellen hebt gemaakt, maar het is interessant om elke cel uit te voeren zodra u deze hebt gemaakt. Niet alle cellen geven uitvoer weer; als u geen foutmeldingen ziet, kunt u ervan uitgaan dat de cel normaal is uitgevoerd.

Elke codecel is afhankelijk van de code die in eerdere cellen is uitgevoerd, en als u één van de cellen niet uitvoert, kunnen latere cellen fouten produceren. Als u merkt dat u een cel vergeten bent uit te voeren, kunt u proberen de opdracht **Cell** > **Run All Above** uit te voeren om alle cellen boven de huidige cel uit te voeren voordat u de huidige cel uitvoert.

Als u onverwachte resultaten ziet (wat waarschijnlijk zo is!) controleert u of elke cel correct op 'Code' of 'Markdown' is ingesteld. U krijgt bijvoorbeeld gewoonlijk een 'ongeldige syntaxis'-fout als u Markdown hebt ingevoerd in een codecel.

1. Markdown-cel:

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Codecel; als deze wordt uitgevoerd, wordt de inhoud van de tabel weergegeven als uitvoer. U kunt de uitvoer onderdrukken door de `print`-instructies uit te commentariëren.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > U kunt waarschuwingen zien van deze code met 'numpy.dtype size changed'; deze waarschuwingen kunnen veilig worden genegeerd.

1. Markdown-cel:

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Code-cel; deze cel heeft geen uitvoer wanneer deze wordt uitgevoerd.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown-cel:

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Codecel; wanneer deze cel wordt uitgevoerd, wordt de uitvoer `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)` weergegeven.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown-cel:

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Codecel; wanneer deze cel wordt uitgevoerd, wordt uitvoer als `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` weergegeven.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown-cel:

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown-cel:

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Codecel; bij het uitvoeren van deze cel wordt een grafisch diagram gegenereerd. Als u het diagram de eerste keer niet ziet (en in plaats daarvan 'Figure size 640x480 with 1 Axes' ziet), voer de cel dan opnieuw uit.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![De uitvoer van de matplotlib-code](media/tutorial/tutorial-plot-output.png)

1. Markdown-cel:

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Uitvoer wissen en alle cellen opnieuw uitvoeren

Nadat u alle stappen in de vorige sectie hebt uitgevoerd om het gehele notebook te vullen, hebt u een stuk werkende code gemaakt in de context van een volledige zelfstudie over lineaire regressie. Deze directe combinatie van code en tekst is een van de grote voordelen van notebooks!

Probeer het hele notebook nu opnieuw uit te voeren:

1. Wis alle sessiegegevens van de kernel en alle sessie-uitvoer door **Kernel** > **Restart & Clear Output** te selecteren. Het is altijd goed om deze opdracht uit te voeren nadat u een notebook hebt voltooid, om u ervan te verzekeren dat u geen vreemde afhankelijkheden tussen codecellen hebt gecreëerd.

1. Voer het notebook opnieuw uit met **Cell** > **Run All**. U ziet dat de kernel-indicator is ingevuld terwijl de code wordt uitgevoerd.

    Als u code hebt waarvan de uitvoering te lang duurt of die vastloopt, kunt u de kernel stoppen door de opdracht **Kernel** > **Interrupt** te gebruiken.

1. Blader door het notebook om de resultaten te bestuderen. (Als het diagram weer niet wordt weergegeven, voert u die cel opnieuw uit.)

## <a name="save-halt-and-close-the-notebook"></a>Het notebook opslaan, stoppen en sluiten

Wanneer u bezig bent een notebook te bewerken, kunt u de huidige staat ervan opslaan met de opdracht **File** > **Save and Checkpoint**, of met de knop Save op de werkbalk. Met een 'checkpoint' (controlepunt) maakt u een momentopname waarnaar u op elk gewenst moment tijdens de sessie kunt terugkeren. Met controlepunten kunt u een reeks experimentele wijzigingen aanbrengen, en als die niet werken, kunt u gewoon teruggaan naar het controlepunt met behulp van de opdracht **File** > **Revert to Checkpoint**. Een alternatieve methode is extra cellen te maken en code die u niet wilt uitvoeren uit te commentariëren. Beide manieren werken.

U kunt ook wanneer u maar wilt de opdracht **File** > **Make a Copy** gebruiken om een kopie te maken van de huidige staat van het notebook in een nieuw bestand in uw project. Deze kopie wordt automatisch in een nieuw browsertabblad geopend.

Gebruik wanneer u klaar bent met een notebook de opdracht **File** > **Close and halt** om het notebook te sluiten en de kernel af te sluiten waar het werd uitgevoerd. Azure Notebooks sluit vervolgens automatisch het browsertabblad.

## <a name="debug-notebooks-using-visual-studio-code"></a>Fouten in notebooks opsporen met behulp van Visual Studio Code

Als de codecellen in uw notebook niet werken zoals verwacht, kan uw code fouten of andere defecten bevatten. Maar een Jupyter-omgeving bevat gewoonlijk geen foutopsporingsmogelijkheden, behalve het opnemen van `print`-instructies om de waarde van variabelen weer te geven.

Gelukkig kunt u het *.ipynb*-bestand van het notebook downloaden en het openen in Visual Studio Code met behulp van de Python-extensie. De extensie importeert een notebook rechtstreeks als één codebestand, en bewaart uw Markdown-cellen in commentaar. Nadat u het notebook hebt geïmporteerd, kunt u de foutopsporing van Visual Studio Code gebruiken om uw code stap voor stap te doorlopen, onderbrekingspunten in te stellen, de status te onderzoeken, enzovoort. Nadat u de code hebt gecorrigeerd, exporteert u het *.ipynb*-bestand uit Visual Studio Code en uploadt u het weer in Azure Notebooks.

Zie voor meer informatie [Debug a Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) (Fouten opsporen in een Jupyter Notebook) in de documentatie van Visual Studio Code.

Zie ook [Visual Studio Code - Jupyter support](https://code.visualstudio.com/docs/python/jupyter-support) (Visual Studio Code - Ondersteuning van Jupyter) voor meer functies van Visual Studio Code voor Jupyter-notebooks.

## <a name="next-steps"></a>Volgende stappen

- [Voorbeelden van notebooks verkennen](azure-notebooks-samples.md)

Artikelen met procedures:

- [Projecten maken en klonen](create-clone-jupyter-notebooks.md)
- [Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Pakketten vanuit een notebook installeren](install-packages-jupyter-notebook.md)
- [Een diavoorstelling presenteren](present-jupyter-notebooks-slideshow.md)
- [Werken met gegevensbestanden](work-with-project-data-files.md)
- [Toegang tot gegevensbronnen](access-data-resources-jupyter-notebooks.md)
- [Azure Machine Learning gebruiken](../machine-learning/samples-notebooks.md)