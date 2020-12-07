---
title: Ethereum Block Chain-connector gebruiken met Azure Logic Apps-Azure Block Chain-Service
description: Gebruik de Ethereum Block Chain-connector met Azure Logic Apps om slimme contract functies te activeren en te reageren op slimme contract gebeurtenissen.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 411337908553e58c252a0ed1a42d17f76195c720
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763788"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>De Ethereum Block Chain-connector gebruiken met Azure Logic Apps

Gebruik de [Ethereum Block Chain-connector](/connectors/blockchainethereum/) met [Azure Logic apps](../../logic-apps/index.yml) om slimme contract acties uit te voeren en te reageren op slimme contract gebeurtenissen. In dit artikel wordt uitgelegd hoe u de Ethereum Block Chain-connector kunt gebruiken om Block Chain informatie naar een andere service te verzenden of om een Block Chain-functie aan te roepen. Stel bijvoorbeeld dat u een REST-gebaseerde micro service wilt maken die gegevens uit een Block Chain-boek houding retourneert. Door gebruik te maken van een logische app, kunt u HTTP-aanvragen accepteren die query's uitvoeren op gegevens die zijn opgeslagen in een Block Chain-groot boek.

## <a name="prerequisites"></a>Vereisten

- Voltooi de optionele [Snelstartgids: gebruik Visual Studio code om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md). De Snelstartgids helpt u bij het installeren van de [Azure Blockchain Development Kit voor Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) en het instellen van uw blockchain-ontwikkelomgeving.
- Als u niet bekend bent met Azure Logic Apps, kunt u overwegen om de Microsoft Learn modules [Inleiding tot Azure Logic apps te](/learn/modules/intro-to-logic-apps/) bekijken en [een API aan te roepen vanuit een Logic apps werk stroom met behulp van een aangepaste connector](/learn/modules/logic-apps-and-custom-connectors/).

## <a name="create-a-logic-app"></a>Een logische app maken

Azure Logic Apps helpt u bij het plannen en automatiseren van bedrijfsprocessen en werkstromen wanneer u systemen en services moet integreren. Eerst maakt u een logica die gebruikmaakt van de Ethereum Block Chain-connector.

1. Selecteer in [Azure Portal](https://portal.azure.com)**Een resource maken** > **Integratie** > **Logische app**.
1. Geef onder **logische app maken** Details op over waar u uw logische app maakt. Selecteer **Maken** als u klaar bent.

    Zie [Automatische werkstromen maken met Azure Logic Apps](../../logic-apps/quickstart-create-first-logic-app-workflow.md) voor meer informatie over het maken van logische apps.

1. Nadat Azure uw app heeft geïmplementeerd, selecteert u de resource van de logische app.
1. Selecteer in de Logic Apps Designer onder **Sjablonen** **Lege logische app**.

Elke logische app moet beginnen met een trigger, die wordt geactiveerd wanneer er een bepaalde gebeurtenis plaatsvindt of wanneer er aan een bepaalde voorwaarde is voldaan. Telkens wanneer de trigger wordt geactiveerd, maakt de Logic Apps-engine een exemplaar van een logische app dat wordt gestart en de werkstroom uitvoert.

De Ethereum Block Chain-connector heeft één trigger en verschillende acties. Welke trigger of actie u gebruikt, is afhankelijk van uw scenario. Volg de sectie in dit artikel die het beste overeenkomt met uw scenario.

Als uw werk stroom:

* Hiermee wordt geactiveerd wanneer een gebeurtenis op de Block Chain plaatsvindt, [de gebeurtenis trigger gebruiken](#use-the-event-trigger).
* Query's uitvoeren of een slim contract implementeren, [acties gebruiken](#use-actions).
* Volgt een veelvoorkomend scenario, [Genereer een werk stroom met behulp van de Developer Kit](#generate-a-workflow).

## <a name="use-the-event-trigger"></a>De gebeurtenis trigger gebruiken

Gebruik Ethereum Block Chain-gebeurtenis triggers wanneer u wilt dat een logische app wordt uitgevoerd nadat een info contract gebeurtenis plaatsvindt. U wilt bijvoorbeeld een e-mail verzenden wanneer een slimme contract functie wordt aangeroepen.

1. Selecteer in de Logic Apps Designer de Ethereum Block Chain-connector.
1. Selecteer op het tabblad **Triggers** **Wanneer een info contract gebeurtenis optreedt**.
1. Wijzig of [Maak een API-verbinding met de](#create-an-api-connection) Azure Block Chain-service.
1. Voer de details in van het slimme contract dat u wilt controleren op gebeurtenissen.

    ![Logic Apps Designer met eigenschappen van gebeurtenis trigger](./media/ethereum-logic-app/event-properties.png)

    | Eigenschap | Beschrijving |
    |----------|-------------|
    | **Contract-ABI** | Met de ABI (contract Application Binary Interface) worden de Smart contract interfaces gedefinieerd. Zie [Get the contract Abi](#get-the-contract-abi)voor meer informatie. |
    | **Info contract adres** | Het contract adres is het doel adres van het slimme contract op de Ethereum Block chain. Zie [het contract adres ophalen](#get-the-contract-address)voor meer informatie. |
    | **Gebeurtenis naam** | Selecteer een slimme contract gebeurtenis om te controleren. De logische app wordt geactiveerd door de gebeurtenis. |
    | **Interval** en **frequentie** | Selecteer hoe vaak u wilt controleren op de gebeurtenis. |

1. Selecteer **Opslaan**.

Als u uw logische app wilt volt ooien, kunt u een nieuwe stap toevoegen waarmee een actie wordt uitgevoerd op basis van de gebeurtenis trigger Ethereum Block chain. U kunt bijvoorbeeld een e-mail bericht verzenden.

## <a name="use-actions"></a>Acties gebruiken

Gebruik de Ethereum Block Chain-acties wanneer u wilt dat een logische app een actie uitvoert op het block chaine groot boek. Stel dat u een REST-gebaseerde micro service wilt maken die een functie van een slim contract aanroept wanneer een HTTP-aanvraag wordt gedaan bij een logische app.

Voor connector acties is een trigger vereist. U kunt een Ethereum Block Chain-connector actie gebruiken als de volgende stap na een trigger, zoals een HTTP-aanvraag trigger voor een micro service.

1. Selecteer in de ontwerp functie voor Logic Apps de optie **nieuwe stap** na een trigger.
1. Selecteer de Ethereum Block Chain-connector.
1. Selecteer een van de beschik bare acties op het tabblad **acties** .

    ![Logic Apps Designer met acties-eigenschappen](./media/ethereum-logic-app/action-properties.png)

1. Wijzig of [Maak een API-verbinding met de](#create-an-api-connection) Azure Block Chain-service.
1. Afhankelijk van de actie die u hebt gekozen, geeft u de volgende details op over de functie van het slimme contract.

    | Eigenschap | Beschrijving |
    |----------|-------------|
    | **Contract-ABI** | In de ABI van het contract worden de slimme contractinterfaces gedefinieerd. Zie [Get the contract Abi](#get-the-contract-abi)voor meer informatie. |
    | **Byte code van contract** | De gecompileerde byte code van het Smart contract. Zie [Get the contract byte code](#get-the-contract-bytecode)(Engelstalig) voor meer informatie. |
    | **Info contract adres** | Het contract adres is het doel adres van het slimme contract op de Ethereum Block chain. Zie [het contract adres ophalen](#get-the-contract-address)voor meer informatie. |
    | **Naam van de functie voor slimme contracten** | Selecteer de naam van de functie voor de actie. De lijst wordt ingevuld op basis van de details in het contract ABI. |

    Na het selecteren van een naam van een slimme contract functie ziet u mogelijk de vereiste velden voor functie parameters. Voer de waarden of dynamische inhoud in die vereist zijn voor uw scenario.

U kunt nu uw logische app gebruiken. Wanneer de logische app-gebeurtenis wordt geactiveerd, wordt de actie Ethereum Block Chain uitgevoerd. Bijvoorbeeld, een HTTP-aanvraag trigger voert een Ethereum Block Chain actie uit om een Smart contract status waarde op te vragen. Deze query resulteert in een HTTP-antwoord dat de waarde retourneert.

## <a name="generate-a-workflow"></a>Een werk stroom genereren

De Azure Block Chain Development Kit voor Ethereum Visual Studio code extension kan werk stromen voor logische apps genereren voor veelvoorkomende scenario's. Er zijn vier scenario's beschikbaar:

* Gegevens publiceren naar een Azure SQL Database-exemplaar
* Gebeurtenis publiceren naar een exemplaar van Azure Event Grid of Azure Service Bus
* Rapport publicatie
* Micro service op REST basis

 De Azure Block Chain Development Kit maakt gebruik van truffle om Block Chain-ontwikkeling te vereenvoudigen. Als u een logische app op basis van een slim contract wilt genereren, hebt u een Truffle-oplossing nodig voor het slimme contract. U hebt ook een verbinding met uw Azure Block Chain Service consortium-netwerk nodig. Zie [Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium Network Quick](connect-vscode.md)start voor meer informatie.

Met de volgende stappen wordt bijvoorbeeld een op REST gebaseerde micro service Logic-app gegenereerd op basis van het Smart-contract **HelloBlockchain** Quick Start:

1. Vouw in de Visual Studio code Explorer-zijbalk de map **contracten** in uw oplossing uit.
1. Klik met de rechter muisknop op **HelloBlockchain. Sol** en selecteer micro **Services voor slimme contracten genereren** in het menu.

    ![Venster Visual Studio code met de selectie micro Services voor slimme contracten genereren](./media/ethereum-logic-app/generate-logic-app.png)

1. Selecteer in het opdracht palet **Logic app**.
1. Voer het **contract adres** in. Zie [het contract adres ophalen](#get-the-contract-address)voor meer informatie.
1. Selecteer het Azure-abonnement en de resource groep voor de logische app.

    De configuratie van de logische app en de code bestanden worden gegenereerd in de **generatedLogicApp** -map.

1. Bekijk de **generatedLogicApp/HelloBlockchain-** map. Er is een JSON-bestand van de logische app voor elke functie, gebeurtenis en eigenschap van een slim contract.
1. Open het bestand **generatedLogicApp/HelloBlockchain/service/property.RequestMessage.logicapp.js** en kopieer de inhoud.

    ![JSON-bestand met te kopiëren code](./media/ethereum-logic-app/requestmessage.png)

1. Selecteer in uw logische app de **code weergave Logic app**. Vervang de bestaande JSON door de gegenereerde JSON van de logische app.

    ![Code weergave van logische apps met nieuwe vervangen app code](./media/ethereum-logic-app/code-view.png)

1. Selecteer **Designer** om over te scha kelen naar de ontwerp weergave.
1. De logische app bevat de basis stappen voor het scenario. U moet echter de configuratie gegevens voor de Ethereum Block Chain-connector bijwerken.
1. Selecteer de stap **verbindingen** en wijzig of [Maak een API-verbinding](#create-an-api-connection) met de Azure Block Chain-service.

    ![Ontwerp weergave met de selectie verbindingen](./media/ethereum-logic-app/microservice-logic-app.png)

1. U kunt nu uw logische app gebruiken. Als u de micro service op REST basis wilt testen, geeft u een HTTP POST-aanvraag voor de URL van de logische app-aanvraag. Kopieer de **http post-URL-** inhoud van de stap **Wanneer een HTTP-aanvraag is ontvangen** .

    ![Deel venster Logic Apps Designer met de HTTP POST-URL](./media/ethereum-logic-app/post-url.png)

1. Gebruik krul om een HTTP POST-aanvraag te maken. Vervang de tekst van de tijdelijke aanduiding door *\<HTTP POST URL\>* de URL uit de vorige stap.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    De krul opdracht retourneert een reactie van de logische app. In dit geval is het antwoord de uitvoer van de **RequestMessage** -functie van het slimme contract.

    ![Code-uitvoer van de RequestMessage-functie voor slimme contracten](./media/ethereum-logic-app/curl.png)

Zie de [pagina Azure Block Chain Development Kit voor Ethereum wiki](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)voor meer informatie over het gebruik van de Development Kit.

## <a name="create-an-api-connection"></a>Een API-verbinding maken

Er is een API-verbinding met een Block Chain vereist voor de Ethereum Block Chain-connector. U kunt de API-connector gebruiken voor meerdere Logic apps. Sommige eigenschappen zijn vereist en andere zijn afhankelijk van uw scenario.

> [!IMPORTANT]
> Een persoonlijke sleutel of account adres en wacht woord zijn vereist voor het maken van trans acties in een Block chain. Er is slechts één vorm van verificatie nodig. U hoeft niet zowel de persoonlijke sleutel als de account gegevens op te geven. Voor het uitvoeren van een query op contracten is geen trans actie vereist. Als u acties gebruikt waarmee de contract status wordt opgevraagd, zijn de persoonlijke sleutel of het account adres en het wacht woord niet vereist.

Om u te helpen bij het instellen van een verbinding met een Azure Block Chain service-lid, heeft de volgende lijst mogelijk eigenschappen die u mogelijk nodig hebt, afhankelijk van uw scenario.

| Eigenschap | Beschrijving |
|----------|-------------|
|**Verbindings naam** | De naam van de API-verbinding. Vereist. |
|**RPC-eind punt Ethereum** | Het HTTP-adres van het knoop punt van de Azure Block Chain Service-trans actie. Vereist. Zie [het RPC-eind punt ophalen](#get-the-rpc-endpoint)voor meer informatie. |
|**Persoonlijke sleutel** | Persoonlijke sleutel van het Ethereum-account. De persoonlijke sleutel of het account adres en het wacht woord zijn vereist voor trans acties. Zie [de persoonlijke sleutel ophalen](#get-the-private-key)voor meer informatie. |
|**Account adres** | Adres van lid van Azure Block Chain Service-account. De persoonlijke sleutel of het account adres en het wacht woord zijn vereist voor trans acties. Zie [het account adres ophalen](#get-the-account-address)voor meer informatie. |
|**Account wachtwoord** | Het wacht woord voor het account wordt ingesteld wanneer u het lid maakt. Zie [Ethereum-account](consortium.md#ethereum-account)voor meer informatie over het opnieuw instellen van het wacht woord.|

## <a name="get-the-rpc-endpoint"></a>Het RPC-eind punt ophalen

Het RPC-eindpunt adres van de Azure Block Chain-service is vereist om verbinding te maken met een Block chain-netwerk. U kunt het eindpunt adres ophalen met behulp van de Azure Block Chain Development Kit voor Ethereum of de Azure Portal.

**De Development Kit gebruiken:**

1. Klik onder **Azure Block Chain-Service** in Visual Studio code met de rechter muisknop op het consortium.
1. Selecteer het **RPC-eindpunt adres kopiëren**.

    ![Deel venster Visual Studio code met het consortium met de selectie voor het kopiëren van RPC-eind punten](./media/ethereum-logic-app/devkit-rpc.png)

    Het RPC-eind punt wordt gekopieerd naar het klem bord.

**Als u de Azure Portal wilt gebruiken:**

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar het lid van Azure Block Chain Service. Selecteer **Transactieknooppunten** en de koppeling voor het standaardtransactieknooppunt.

    ![Pagina transactie knooppunten met de (standaard knooppunt) selectie](./media/ethereum-logic-app/transaction-nodes.png)

1. Selecteer **Connection strings**  >  **toegangs sleutels** voor verbindings reeksen.
1. Kopieer het eindpunt adres van **https (toegangs sleutel 1)** of **https (toegangs sleutel 2)**.

    ![Azure Portal met de connection string toegangs sleutels](./media/ethereum-logic-app/connection-string.png)

    Het RPC-eind punt is de HTTPS-URL, die het adres en de toegangs sleutel bevat van het trans actie-knoop punt van uw Azure Block Chain service-lid.

## <a name="get-the-private-key"></a>De persoonlijke sleutel ophalen

U kunt de persoonlijke sleutel van het Ethereum-account gebruiken om te verifiëren bij het verzenden van een trans actie naar de Block chain. De open bare en persoonlijke sleutels van uw Ethereum-account worden gegenereerd op basis van een webinstructie van 12 woorden. De Azure Block Chain Development Kit voor Ethereum genereert een instructie wanneer u verbinding maakt met een Azure Block Chain Service consortium-lid. U kunt het eindpunt adres ophalen met de uitbrei ding Development Kit.

1. Open in Visual Studio code het opdracht palet (F1).
1. Selecteer **Block chain: persoonlijke sleutel ophalen**.
1. Selecteer de door u opgeslagen instructie wanneer u verbinding maakt met het consortium.

    ![Opdracht palet met een optie voor het selecteren van de instructie](./media/ethereum-logic-app/private-key.png)

    De persoonlijke sleutel wordt gekopieerd naar het klem bord.

## <a name="get-the-account-address"></a>Het account adres ophalen

U kunt de leden account en het wacht woord gebruiken om te verifiëren wanneer u een trans actie verzendt naar de Block chain. Het wacht woord wordt ingesteld wanneer u het lid maakt.

1. Ga in het Azure Portal naar de overzichts pagina van de Azure Block Chain-service.
1. Kopieer het adres van het **lid-account** .

    ![Overzichts pagina met het adres van het lid-account](./media/ethereum-logic-app/member-account.png)

Zie [Ethereum-account](consortium.md#ethereum-account)voor meer informatie over het account adres en het wacht woord.

## <a name="get-the-contract-abi"></a>De ABI van het contract ophalen

In de ABI van het contract worden de slimme contractinterfaces gedefinieerd. Hierin wordt beschreven hoe u met het slimme contract communiceert. U kunt de contract ABI ophalen met behulp van de Azure Block Chain Development Kit voor Ethereum. U kunt dit bestand ook ophalen uit het meta gegevensbestand voor contracten dat is gemaakt door de volheid-compiler.

**De Development Kit gebruiken:**

Als u de Development Kit of Truffle hebt gebruikt om uw slimme contract te maken, kunt u de extensie gebruiken om de contract ABI naar het klem bord te kopiëren.

1. Vouw in het deelvenster Visual Studio Code-verkenner de map **Build/contracten** van uw Solidity-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met metagegevens van het contract. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Selecteer **ABI van het contract kopiëren**.

    ![Visual Studio Code-deelvenster met de selectie ABI van het contract kopiëren](./media/ethereum-logic-app/abi-devkit.png)

    De ABI van het contract wordt gekopieerd naar het klembord.

**Het meta gegevensbestand voor het contract gebruiken:**

1. Open het meta gegevensbestand van de opdracht in de map **Build/contract** van het volheid-project. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Zoek de sectie **Abi** in het JSON-bestand.
1. Kopieer de JSON-matrix **Abi** .

    ![ABI-code in het meta gegevens bestand van het contract](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>De byte code van het contract ophalen

De byte code van het contract is het gecompileerde slimme contract dat door de virtuele Ethereum-machine wordt uitgevoerd. U kunt de byte code van het contract ophalen met behulp van de Azure Block Chain Development Kit voor Ethereum. U kunt deze ook ophalen uit de volheid-compiler.

**De Development Kit gebruiken:**

Als u de Development Kit of Truffle hebt gebruikt om uw slimme contract te maken, kunt u de extensie gebruiken om de byte code van het contract te kopiëren naar het klem bord.

1. Vouw in het deelvenster Visual Studio Code-verkenner de map **Build/contracten** van uw Solidity-project uit.
1. Klik met de rechter muisknop op het JSON-bestand met metagegevens van het contract. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Selecteer de **byte code van het Kopieer contract**.

    ![Deel venster Visual Studio met de byte code ring van het Kopieer contract](./media/ethereum-logic-app/bytecode-devkit.png)

    De byte code van het contract wordt gekopieerd naar het klem bord.

**Het meta gegevensbestand voor het contract gebruiken:**

1. Open het meta gegevensbestand van de opdracht in de map **Build/contract** van het volheid-project. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Zoek het **byte code** -element in het JSON-bestand.
1. Kopieer de waarde van de **byte code** .

    ![Venster Visual Studio code met byte code in de meta gegevens](./media/ethereum-logic-app/bytecode-metadata.png)

**De volheid compiler gebruiken:**

Gebruik de opdracht `solc --bin <smart contract>.sol` om de byte code van het contract te genereren.

## <a name="get-the-contract-address"></a>Het contract adres ophalen

Het contract adres is het doel adres van het slimme contract op de Ethereum Block chain. U kunt dit adres gebruiken om een trans actie-of query status van een slim contract te verzenden. U kunt het contract adres ophalen uit de Truffle-migratie of het meta gegevens bestand van het contract.

**De Truffle migrate output gebruiken:**

Truffle geeft het contract adres na de implementatie van het slimme contract weer. Kopieer het **contract adres** uit de uitvoer.

![Truffle-migratie-uitvoer met het contract adres in Visual Studio code](./media/ethereum-logic-app/contract-address-truffle.png)

**Het meta gegevensbestand voor het contract gebruiken:**

1. Open het meta gegevensbestand van de opdracht in de map **Build/contract** van het volheid-project. De bestandsnaam is de naam van het slimme contract gevolgd door de extensie **. json**.
1. Zoek de sectie **netwerken** in het JSON-bestand.
1. Particuliere netwerken worden aangeduid met een netwerk-ID met gehele getallen. Zoek de adres waarde binnen de sectie netwerk.
1. Kopieer de **adres** waarde.

![Meta gegevens met de adres waarde in Visual Studio code](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk de algemene scenario's in de video [met Logic apps](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true).
