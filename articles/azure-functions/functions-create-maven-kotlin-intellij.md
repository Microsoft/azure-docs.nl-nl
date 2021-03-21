---
title: Een Kotlin-functie in Azure Functions maken met behulp van IntelliJ
description: Ontdek het gebruik van IntelliJ om een eenvoudige, door HTTP geactiveerde Kotlin-functie te maken, die u vervolgens publiceert om te worden uitgevoerd in een serverloze omgeving in Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f02643ee28d76d4f90206a1aa2879b4672da2a38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179439"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Uw eerste Kotlin-functie maken in Azure met behulp van IntelliJ

In dit artikel leest u hoe u een door HTTP geactiveerde Java-functie maakt in een IntelliJ IDEA-project, hoe u het project uitvoert en debugt in de IDE (Integrated Development Environment) en vervolgens het functieproject implementeert in een functie-app in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Installeer de volgende software om Kotlin-functies in Azure te maken en publiceren met behulp van IntelliJ:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), versie 8
- [Apache Maven](https://maven.apache.org) versie 3.0 of hoger
- [IntelliJ-idee](https://www.jetbrains.com/idea/download), Community of Ultimate-versies met Maven
- [Azure-CLI](/cli/azure)
- [Versie 2.x](functions-run-local.md#v2) van de Azure Functions Core Tools. Het is een lokale ontwikkelingsomgeving voor het schrijven, uitvoeren en debuggen van Azure Functions.

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om de stappen in dit artikel te voltooien.

## <a name="create-a-function-project"></a>Een functieproject maken

1. Selecteer **Nieuw project maken** in IntelliJ IDEA.  
1. Selecteer in het venster **Nieuw project** **Maven** in het linkerdeelvenster.
1. Selecteer het selectievakje **Maken van archetype**, en selecteer vervolgens **Archetype toevoegen** voor het [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Voltooi in het venster **Archtetype toevoegen** de velden als volgt:
    - _GroupId:_ com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _Version_: Gebruik de nieuwste versie van [de centrale opslagplaats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Een Maven-project maken van archetype in IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecteer **OK**, en selecteer vervolgens **Volgende**.
1. Voer uw gegevens in voor het huidige project en selecteer **Voltooien**.

Maven maakt de projectbestanden in een nieuwe map met dezelfde naam als de waarde _ArtifactId_. De gegenereerde code van het project is een eenvoudige, [door HTTP getriggerde](./functions-bindings-http-webhook.md) functie die de hoofdtekst van de activerende HTTP-aanvraag weergeeft.

## <a name="run-project-locally-in-the-ide"></a>Project lokaal uitvoeren in de IDE

> [!NOTE]
> Als u het project lokaal wilt uitvoeren en debuggen, controleert u of [Azure Functions Core Tools, versie 2](functions-run-local.md#v2) is geïnstalleerd.

1. Wijzigingen handmatig importeren of [automatisch importeren](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Op de werkbalk **Maven-projecten**.
1. Vouw **Levenscyclus** uit en open **pakket**. De oplossing is gebouwd en verpakt in een nieuw gemaakte doelmap.
1. Vouw **Invoegtoepassingen** > **azure-functions** uit en open **azure-functions:run** uit om de lokale runtime van Azure Functions te starten.  
  ![Maven-werkbalk voor Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Sluit het dialoogvenster uitvoeren wanneer u klaar bent met het testen van de functie. Er kan slechts één functiehost actief zijn en lokaal worden uitgevoerd.

## <a name="debug-the-project-in-intellij"></a>Het project debuggen in IntelliJ

1. Als u de functiehost in de foutopsporingsmodus wilt starten, voegt u **-DenableDebug** als argument toe wanneer u de functie uitvoert. U kunt de configuratie in [maven-doelen](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) wijzigen of de volgende opdracht uitvoeren in een terminalvenster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Deze opdracht zorgt ervoor dat de functiehost een poort voor foutopsporing opent op 5005.

1. Selecteer in het menu **Uitvoeren** **Configuraties bewerken**.
1. Selecteer **'+'** om een **Externe** toe te voegen.
1. Voltooi de velden _Naam_ en _Instellingen_ en selecteer vervolgens **OK** om de configuratie op te slaan.
1. Na de installatie selecteert u **Fouten opsporen < Naam van de externe configuratie >** of drukt u op SHIFT + F9 op het toetsenbord om de foutopsporing te starten.

   ![Project debuggen in IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Wanneer u klaar bent, stopt u de foutopsporing en het actieve proces. Er kan slechts één functiehost actief zijn en lokaal worden uitgevoerd.

## <a name="deploy-the-project-to-azure"></a>Het project implementeren in Azure

1. Voordat u uw project kunt implementeren in een functie-app in Azure, moet u zich [aanmelden met behulp van de Azure CLI](/cli/azure/authenticate-azure-cli).

   ``` azurecli
   az login
   ```

1. Implementeer de code in een nieuwe functie-app met behulp van de Maven-target `azure-functions:deploy`. U kunt ook de optie **Azure-functions:deploy** in het venster Maven-projecten selecteren.

   ```
   mvn azure-functions:deploy
   ```

1. Zoek de URL voor de HTTP-triggerfunctie in de Azure CLI-uitvoer nadat de functie-app is geïmplementeerd.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Volgende stappen

Nu u uw eerste Kotlin-functie-app naar Azure hebt geïmplementeerd, neemt u de [Azure Functions Java-handleiding voor ontwikkelaars](functions-reference-java.md) door voor meer informatie over het ontwikkelen van Java- en Kotlin-functies.
- Voeg extra functies verschillende triggers toe aan uw project met de Maven-target `azure-functions:add`.
