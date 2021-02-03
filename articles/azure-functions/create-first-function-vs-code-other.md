---
title: Een functie in Go of Rust maken met behulp van Visual Studio Code - Azure Functions
description: Leer hoe u een Go-functie maakt als een aangepaste Azure Functies-handler en publiceer vervolgens het lokale project naar serverloze hosting in Azure Functions met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 12/4/2020
ms.openlocfilehash: 4f2e0b30c4bf5e6c4629fc63f3125e5ddda70ad2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493652"
---
# <a name="quickstart-create-a-go-or-rust-function-in-azure-using-visual-studio-code"></a>Quickstart: Een Go- of Rust-functie maken in Azure met behulp van Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

In dit artikel gebruikt u Visual Studio Code om een [aangepaste handler](functions-custom-handlers.md)-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions.

Aangepaste handlers kunnen worden gebruikt om functies in elke taal of runtime te maken door een HTTP-serverproces uit te voeren. Dit artikel biedt ondersteuning voor zowel [Go](create-first-function-vs-code-other.md?tabs=go) als [Rust](create-first-function-vs-code-other.md?tabs=rust).

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

## <a name="configure-your-environment"></a>Uw omgeving configureren

Voordat u aan de slag kunt gaan, moet u beschikken over de volgende vereisten:

# <a name="go"></a>[Go](#tab/go)

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code.

+ De [Azure Functions Core Tools](./functions-run-local.md#v2), versie 3.x. Gebruik de opdracht `func --version` om te controleren of deze versie correct is geïnstalleerd.

+ [Go](https://golang.org/doc/install) (nieuwste versie wordt aanbevolen). Gebruik de opdracht `go version` om uw versie te controleren.

# <a name="rust"></a>[Rust](#tab/rust)

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code.

+ De [Azure Functions Core Tools](./functions-run-local.md#v2), versie 3.x. Gebruik de opdracht `func --version` om te controleren of deze versie correct is geïnstalleerd.

+ Rust-hulpprogrammaketen met [rustup](https://www.rust-lang.org/tools/install). Gebruik de opdracht `rustc --version` om uw versie te controleren.

---

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure Functions-project voor aangepaste handlers te maken. Verderop in dit artikel publiceert u de functiecode in Azure.

1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: Functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de werkruimte van uw project en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen waren bedoeld om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een taal voor uw functieproject**: Kies `Custom`.

    + **Selecteer een sjabloon voor de eerste functie van uw project**: Kies `HTTP trigger`.

    + **Geef een functienaam op**: Typ `HttpExample`.

    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen. Zie [Autorisatiesleutels](functions-bindings-http-webhook-trigger.md#authorization-keys) voor meer informatie over autorisatieniveau.

    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

1. Met behulp van deze informatie wordt met Visual Studio Code een Azure Functions-project gegenereerd met een HTTP-triggerfunctie. U kunt de lokale projectbestanden weergeven in de Explorer. Zie [Gegenereerde projectbestanden](functions-develop-vs-code.md#generated-project-files) voor meer informatie over bestanden die worden gemaakt. 

## <a name="create-and-build-your-function"></a>Uw functie maken en compileren

Het bestand *function.json* in de map *HttpExample* declareert een HTTP-triggerfunctie. U voltooit de functie door een handler toe te voegen en deze te compileren in een uitvoerbaar bestand.

# <a name="go"></a>[Go](#tab/go)

1. Druk op <kbd>Ctrl+N</kbd> (<kbd>Cmd+N</kbd> in macOS) om een nieuw bestand te maken. Sla het bestand op als *handler.go* in de hoofdmap van de functie-app (in dezelfde map als *host.json*).

1. Voeg de volgende code toe aan *handler.go* en sla het bestand op. Dit is uw aangepaste handler in Go.

    ```go
    package main

    import (
        "fmt"
        "log"
        "net/http"
        "os"
    )

    func helloHandler(w http.ResponseWriter, r *http.Request) {
        message := "This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.\n"
        name := r.URL.Query().Get("name")
        if name != "" {
            message = fmt.Sprintf("Hello, %s. This HTTP triggered function executed successfully.\n", name)
        }
        fmt.Fprint(w, message)
    }

    func main() {
        listenAddr := ":8080"
        if val, ok := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT"); ok {
            listenAddr = ":" + val
        }
        http.HandleFunc("/api/HttpExample", helloHandler)
        log.Printf("About to listen on %s. Go to https://127.0.0.1%s/", listenAddr, listenAddr)
        log.Fatal(http.ListenAndServe(listenAddr, nil))
    }
    ```

1. Druk op <kbd>Ctrl+Shift+`</kbd> of selecteer *Nieuwe terminal* in het menu *Terminal* om een nieuwe geïntegreerde terminal te openen in Visual Studio Code.

1. Compileer uw aangepaste handler met de volgende opdracht. Een uitvoerbaar bestand met de naam `handler` (`handler.exe` in Windows) wordt uitgevoerd in de hoofdmap van de functie-app.

    ```bash
    go build handler.go
    ```

    ![VS code - Aangepaste handler in Go compileren](./media/functions-create-first-function-vs-code/functions-vscode-go.png)

# <a name="rust"></a>[Rust](#tab/rust)

1. Druk op <kbd>Ctrl+Shift+`</kbd> of selecteer *Nieuwe terminal* in het menu *Terminal* om een nieuwe geïntegreerde terminal te openen in Visual Studio Code.

1. Initialiseer een Rust-project met de naam `handler` in de hoofdmap van de functie-app (dezelfde map als *host.json*).

    ```bash
    cargo init --name handler
    ```

1. Voeg in *Cargo.toml* de volgende afhankelijkheden toe die nodig zijn om deze quickstart te voltooien. In het voorbeeld wordt het webserverframework [warp](https://docs.rs/warp/) gebruikt.

    ```toml
    [dependencies]
    warp = "0.2"
    tokio = { version = "0.2", features = ["full"] }
    ```

1. Voeg de volgende code toe aan *src/main.rs* en sla het bestand op. Dit is uw aangepaste handler in Rust.

    ```rust
    use std::collections::HashMap;
    use std::env;
    use std::net::Ipv4Addr;
    use warp::{http::Response, Filter};

    #[tokio::main]
    async fn main() {
        let example1 = warp::get()
            .and(warp::path("api"))
            .and(warp::path("HttpExample"))
            .and(warp::query::<HashMap<String, String>>())
            .map(|p: HashMap<String, String>| match p.get("name") {
                Some(name) => Response::builder().body(format!("Hello, {}. This HTTP triggered function executed successfully.", name)),
                None => Response::builder().body(String::from("This HTTP triggered function executed successfully. Pass a name in the query string for a personalized response.")),
            });

        let port_key = "FUNCTIONS_CUSTOMHANDLER_PORT";
        let port: u16 = match env::var(port_key) {
            Ok(val) => val.parse().expect("Custom Handler port is not a number!"),
            Err(_) => 3000,
        };

        warp::serve(example1).run((Ipv4Addr::UNSPECIFIED, port)).await
    }
    ```

1. Compileer een binair bestand voor uw aangepaste handler. Een uitvoerbaar bestand met de naam `handler` (`handler.exe` in Windows) wordt uitgevoerd in de hoofdmap van de functie-app.

    ```bash
    cargo build --release
    cp target/release/handler .
    ```

    ![VS Code - Aangepaste handler in Rust compileren](./media/functions-create-first-function-vs-code/functions-vscode-rust.png)

---

## <a name="configure-your-function-app"></a>Uw functie-app configureren

De functiehost moet worden geconfigureerd om het binaire bestand van uw aangepaste handler uit te voeren wanneer deze wordt gestart.

1. Open *host.json*.

1. Stel in de sectie `customHandler.description` de waarde van `defaultExecutablePath` in op `handler` (in Windows moet u deze waarde instellen op `handler.exe`).

1. Voeg in de sectie `customHandler` een eigenschap toe met de naam `enableForwardingHttpRequest` en stel de waarde ervan in op `true`. Deze maakt het programmeren voor functies die uitsluitend bestaan uit een HTTP-trigger eenvoudiger, omdat u een typische HTTP-aanvraag kunt gebruiken in plaats van de aangepaste handler [request payload](functions-custom-handlers.md#request-payload).

1. Controleer of de sectie `customHandler` eruitziet als in dit voorbeeld. Sla het bestand op.

    ```
    "customHandler": {
      "description": {
        "defaultExecutablePath": "handler",
        "workingDirectory": "",
        "arguments": []
      },
      "enableForwardingHttpRequest": true
    }
    ```

De functie-app is geconfigureerd om het uitvoerbare bestand van de aangepaste handler te starten.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

U kunt dit project uitvoeren op uw lokale ontwikkelcomputer voordat u naar Azure publiceert.

1. Start de functie-app in de geïntegreerde terminal met behulp van Azure Functions Core Tools.

    ```bash
    func start
    ```

1. Als Core Tools wordt uitgevoerd, navigeert u naar de volgende URL om een GET-aanvraag uit te voeren die de querytekenreeks `?name=Functions` bevat.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Er wordt een antwoord geretourneerd die er in een browser als volgt uitziet:

    ![Browser - voorbeelduitvoer van localhost](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal**.

    ![Taakhost starten - VS Code-terminaluitvoer](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op <kbd>Ctrl+C</kbd> om Core Tools te stoppen.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure met behulp van Visual Studio Code.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="compile-the-custom-handler-for-azure"></a>De aangepaste handler voor Azure compileren

In deze sectie publiceert u uw project naar Azure in een functie-app met Linux. In de meeste gevallen moet u uw binaire bestand opnieuw compileren en de configuratie aanpassen aan het doelplatform voordat u het naar Azure publiceert.

# <a name="go"></a>[Go](#tab/go)

1. Compileer de handler in Linux/x64 in de geïntegreerde terminal. Er wordt een binair bestand met de naam `handler` gemaakt in de hoofdmap van de functie-app.

    # <a name="macos"></a>[MacOS](#tab/macos)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="linux"></a>[Linux](#tab/linux)

    ```bash
    GOOS=linux GOARCH=amd64 go build handler.go
    ```

    # <a name="windows"></a>[Windows](#tab/windows)
    ```cmd
    set GOOS=linux
    set GOARCH=amd64
    go build hello.go
    ```

    Wijzig de `defaultExecutablePath` in *host.json* van `handler.exe` in `handler`. Dit geeft de functie-app opdracht om het binaire Linux-bestand uit te voeren.
    
    ---

# <a name="rust"></a>[Rust](#tab/rust)

1. Maak een bestand in *.cargo/config*. Voeg de volgende inhoud toe aan het bestand en sla het op.

    ```
    [target.x86_64-unknown-linux-musl]
    linker = "rust-lld"
    ```

1. Compileer de handler in Linux/x64 in de geïntegreerde terminal. Er wordt een binair bestand met de naam `handler` gemaakt. Ga naar de hoofdmap van de functie-app.

    ```bash
    rustup target add x86_64-unknown-linux-musl
    cargo build --release --target=x86_64-unknown-linux-musl
    cp target/x86_64-unknown-linux-musl/release/handler .
    ```

1. Als u Windows gebruikt, wijzigt u de `defaultExecutablePath` in *host.json* van `handler.exe` in `handler`. Dit geeft de functie-app opdracht om het binaire Linux-bestand uit te voeren.

1. Voeg de volgende regel toe aan het bestand *.funcignore*:

    ```
    target
    ```

    Hiermee wordt voorkomen dat de inhoud van de *doelmap* wordt gepubliceerd.

---

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement en implementeert u vervolgens uw code. 

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 


1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    ![Uw project naar Azure publiceren](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer map**: Kies een map in uw werkruimte of blader naar een map die de functie-app bevat. Dit wordt niet weergegeven als u al een geldige functie-app hebt geopend.

    + **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Dit ziet u niet als u maar één abonnement hebt.

    + **Selecteer functie-app in Azure**: Kies `+ Create new Function App (advanced)`. 
    
        > [!IMPORTANT]
        > Met de optie `advanced` kunt u het specifieke besturingssysteem kiezen waarop de functie-app wordt uitgevoerd in Azure. In dit geval is dat Linux.

        ![VS code - Selecteer Geavanceerd > Nieuwe functie-app maken](./media/functions-create-first-function-vs-code/functions-vscode-create-azure-advanced.png)

    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions.

    + **Een runtimestack selecteren**: Kies `Custom Handler`.

    + **Een besturingssysteem selecteren**: Kies `Linux`.

    + **Een hostingplan selecteren**: Kies `Consumption`.

    + **Een resourcegroep selecteren**: Kies `+ Create new resource group`. Voer een naam in voor de resourcegroep. Deze naam moet uniek zijn binnen uw Azure-abonnement. U kunt de naam gebruiken die wordt voorgesteld in de prompt.

    + **Een opslagaccount selecteren**: Kies `+ Create new storage account`. Deze naam moet wereldwijd uniek zijn in Azure. U kunt de naam gebruiken die wordt voorgesteld in de prompt.

    + **Een Application Insights-resource selecteren**: Kies `+ Create Application Insights resource`. Deze naam moet wereldwijd uniek zijn in Azure. U kunt de naam gebruiken die wordt voorgesteld in de prompt.

    + **Selecteer een locatie voor nieuwe resources**: Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. De uitbrei ding toont de status van afzonderlijke resources, aangezien deze worden gemaakt in Azure in het systeemvak.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Melding van het maken van Azure-resources":::

1. Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 

4. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw weer te geven.

    ![Volledige melding maken](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over aangepaste Azure Functions-handlers](functions-custom-handlers.md)
