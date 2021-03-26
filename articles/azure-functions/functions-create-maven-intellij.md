---
title: Een Java-functie maken in Azure Functions met behulp van IntelliJ
description: Meer informatie over hoe u IntelliJ kunt gebruiken om een eenvoudige HTTP-geactiveerde Java-functie te maken, die u vervolgens publiceert om te worden uitgevoerd in een serverloze omgeving in Azure.
author: yucwan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: yucwan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 45fb62b446e6b589dc0cb9287a8aebe7f4e699b1
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543636"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>Uw eerste Java-functie maken in azure met behulp van IntelliJ

In dit artikel wordt het volgende beschreven:
- Een Java-activerings functie voor HTTP in een IntelliJ-idee project maken.
- Stappen voor het testen en opsporen van fouten in het project in de Integrated Development Environment (IDE) op uw eigen computer.
- Instructies voor het implementeren van het functie project op Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Als u Java-functies wilt maken en publiceren naar Azure met behulp van IntelliJ, installeert u de volgende software:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
+ Een door [Azure ondersteunde Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support) voor Java 8
+ Een [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition of Community Edition geïnstalleerd
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Nieuwste [functie kern hulpprogramma's](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installatie en aanmelden

1. Selecteer in het dialoogvenster Instellingen/voorkeuren van IntelliJ (CTRL+ALT+S) **Invoegtoepassingen**. Ga vervolgens naar de **Azure-toolkit voor IntelliJ** in de **Marketplace** en klik op **Installeren**. Klik na de installatie op **Opnieuw opstarten** om de invoegtoepassing te activeren. 

    ![Azure-toolkit voor IntelliJ-invoegtoepassing in Marketplace][marketplace]

2. Open in de zijbalk **Azure Explorer** in uw Azure-account en klik op het pictogram **Aanmelden bij Azure** in de balk boven (of vanuit het IDEA-menu **Hulpprogramma’s/Azure/Aanmelden bij Azure**).
    ![De opdracht Aanmelden bij Azure in IntelliJ][intellij-azure-login]

3. Selecteer **Apparaataanmelding** in het venster **Aanmelden bij Azure** en klik op **Aanmelden** ([andere aanmeldingsopties](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Het venster Aanmelden bij Azure met apparaataanmelding geselecteerd][intellij-azure-popup]

4. Klik op **Kopiëren en openen** in het dialoogvenster **Azure-apparaataanmelding**.

   ![Het dialoogvenster Azure-apparaataanmelding][intellij-azure-copycode]

5. Plak uw apparaatcode (die is gekopieerd toen u in de vorige stap op **Kopiëren en openen** klikte) in de browser en klik op **Volgende**.

   ![De apparaataanmeldingsbrowser][intellij-azure-link-ms-account]

6. Selecteer in het dialoog venster **abonnementen selecteren** de abonnementen die u wilt gebruiken en klik vervolgens op **selecteren**.

   ![Het dialoogvenster Abonnementen selecteren][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Uw lokale project maken

In deze sectie gebruikt u Azure-toolkit voor IntelliJ om een lokaal Azure Functions-project te maken. Verderop in dit artikel publiceert u de functiecode in Azure. 

1. Open IntelliJ Welkom dialoog venster, selecteer *Nieuw project maken* om een nieuwe project wizard te openen, selecteer *Azure functions*.

    ![Functie project maken](media/functions-create-first-java-intellij/create-functions-project.png)

1. Selecteer *http-trigger*, klik op *volgende* en volg de wizard om alle configuraties op de volgende pagina's te door lopen. Bevestig de project locatie en klik op *volt ooien*. Het nieuwe project wordt vervolgens met Intellj-idee geopend.

    ![Functie project volt ooien maken](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>Het project lokaal uitvoeren

1. Ga naar `src/main/java/org/example/functions/HttpTriggerFunction.java` om de gegenereerde code te zien. Naast regel *17* ziet u dat er een knop groen *uitvoeren* wordt weer gegeven, klikt u erop en selecteert u *' Azure-function-examen... '.* u zult zien dat uw functie-app lokaal wordt uitgevoerd met een paar Logboeken.

    ![Lokaal uitvoerings project](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Uitvoer van lokaal project uitvoeren](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. U kunt de functie proberen door het afgedrukte eind punt te openen vanuit de browser, bijvoorbeeld `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Test resultaat van lokale uitvoerings functie](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Het logboek wordt ook in uw idee afgedrukt. u kunt nu de functie-app stoppen door te klikken op de knop *stoppen* .

    ![Test logboek voor de functie voor lokale uitvoeringen](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>Lokaal fouten opsporen in het project

1. Als u de functie code in uw project lokaal wilt opsporen, selecteert u de knop *fout opsporing* op de werk balk. Als u de werk balk niet ziet, schakelt u deze in door op de  >    >  **werk balk** vormgeving weer geven te klikken.

    ![Knop voor de lokale app fout opsporing](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Klik op regel *20* van het bestand `src/main/java/org/example/functions/HttpTriggerFunction.java` om een onderbrekings punt toe te voegen en toegang tot het eind punt te krijgen. `http://localhost:7071/api/HttpTrigger-Java?name=Azure` u vindt het onderbrekings punt. u kunt meer probleemoplossings functies, zoals *stap*, *kijken* en *evaluatie*, proberen. Stop de foutopsporingssessie door te klikken op de knop stoppen.

    ![Lokale app-afbreek functie voor fout opsporing](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Uw project naar Azure implementeren

1. Klik met de rechter muisknop op uw project in IntelliJ project Verkenner, selecteer *Azure-> implementeren naar Azure functions*

    ![Project implementeren in azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Als u nog geen functie-app hebt, klikt u op *+* de *functie* regel. Typ de naam van de functie-app en kies het juiste platform. hier kunt u gewoon de standaard waarde accepteren. Klik op *OK* en de nieuwe functie-app die u zojuist hebt gemaakt, wordt automatisch geselecteerd. Klik op *uitvoeren* om uw functies te implementeren.

    ![Functie-app maken in azure](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

    ![Functie-app implementeren in azure-logboek](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>Functie-apps uit idee beheren

1. U kunt uw functie-apps met *Azure Explorer* in uw idee beheren. klik op *functie-app* om hier al uw functie-apps weer te geven.

    ![Functie-apps in Verkenner weer geven](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Klik om een van de functie-apps te selecteren en klik met de rechter muisknop, selecteer *Eigenschappen weer geven* om de detail pagina te openen. 

    ![Eigenschappen van de functie-app weer geven](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Klik met de rechter muisknop op de *http trigger-Java-functie-* app en selecteer *trigger functie*. u ziet dat de browser wordt geopend met de trigger-URL.

    ![Scherm afbeelding toont een browser met de U R L.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Meer functies aan het project toevoegen

1. Klik met de rechter muisknop op het pakket *org. voor beeld. functions* en Select *New-> Azure function-klasse*. 

    ![Functies toevoegen aan het project item](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Vul de klassenaam *HttpTest* in en selecteer *http trigger* in de wizard functie klasse maken, klik op *OK* om te maken. u kunt op deze manier nieuwe functies maken, zoals u dat wilt.

    ![Scherm afbeelding toont het dialoog venster functie klasse maken.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Functies toevoegen aan de project uitvoer](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Functies opschonen

1. Functies verwijderen in azure Verkenner
      
      ![Scherm afbeelding toont verwijderen geselecteerd in een context menu.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-project met een door HTTP geactiveerde functie gemaakt, uitgevoerd op uw lokale computer en geïmplementeerd in Azure. Breid uw functie nu uit door...

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij-uitvoer binding toevoegen](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
