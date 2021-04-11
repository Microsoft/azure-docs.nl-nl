---
title: 'Quick Start: een ASP.NET-Web-app implementeren'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door uw eerste ASP.NET-app te implementeren.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 7f538f5accb533b01c5ea685e424c70bfeb44f00
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058205"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Quick Start: een ASP.NET-Web-app implementeren

In deze Quick Start leert u hoe u uw eerste ASP.NET-Web-App maakt en implementeert om [Azure app service](overview.md). App Service ondersteunt verschillende versies van .NET-Apps en biedt een uiterst schaal bare webhostingservice met self-patch functie. ASP.NET Web apps zijn platform voor meerdere platforms en kunnen worden gehost op Linux of Windows. Als u klaar bent, beschikt u over een Azure-resourcegroep die bestaat uit een App Service-hostingabonnement en een Azure Service-app met een geïmplementeerde webtoepassing.

> [!TIP]
> .NET Core 3,1 is de huidige LTS-versie (lange termijn ondersteuning) van .NET. Zie [.net-ondersteunings beleid](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

:::zone target="docs" pivot="development-environment-vs"

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> met de **ASP.NET- en webontwikkelworkload**.

    Als u Visual Studio 2019 al hebt geïnstalleerd:

    - Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
    - Voeg de workload toe door **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** te selecteren.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- De extensie voor <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Azure-Hulpprogram ma's</a> .

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installeer de nieuwste versie van .NET Core 3,1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installeer de nieuwste versie van .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Installeer het .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> Visual Studio code is meerdere platformen, maar .NET Framework niet. Als u .NET Framework apps ontwikkelt met Visual Studio code, kunt u overwegen een Windows-machine te gebruiken om te voldoen aan de afhankelijkheden van de build.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet)
- De <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>.
- De .NET-SDK (inclusief runtime en CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installeer de nieuwste versie van .NET Core 3,1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installeer de nieuwste versie van .NET 5,0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installeer de nieuwste versie van .net 5,0 SDK. </a> en <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> het .NET Framework 4,8 Developer Pack. </a>

> [!NOTE]
> De [.net cli](/dotnet/core/tools) is voor meerdere platforms, maar .NET Framework niet. Als u .NET Framework-apps ontwikkelt met de .NET CLI, kunt u overwegen een Windows-machine te gebruiken om te voldoen aan de build-afhankelijkheden.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. In **een nieuw project maken**, zoek en kies **ASP.net Web core-app** en selecteer vervolgens **volgende**.
1. Geef in **uw nieuwe project een** naam op voor de toepassing _MyFirstAzureWebApp_ en selecteer **volgende**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="ASP.NET Core 3,1-Web-App configureren" border="true":::

1. Selecteer **.net Core 3,1 (ondersteuning voor lange termijn)**.
1. Zorg ervoor dat het **verificatie type** is ingesteld op **geen**. Selecteer **Maken**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio: Selecteer .NET Core 3,1 en geen voor het verificatie type." border="true":::

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio-.NET Core 3,1 lokaal zoeken" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. In **een nieuw project maken**, zoek en kies **ASP.net Web core-app** en selecteer vervolgens **volgende**.
1. Geef in **uw nieuwe project een** naam op voor de toepassing _MyFirstAzureWebApp_ en selecteer **volgende**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio: ASP.NET 5,0-Web-App configureren." border="true":::

1. Selecteer **.net Core 5,0 (actueel)**.
1. Zorg ervoor dat het **verificatie type** is ingesteld op **geen**. Selecteer **Maken**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio: aanvullende informatie bij het selecteren van .NET Core 5,0." border="true":::

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio-ASP.NET Core 5,0 lokaal uitgevoerd." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. In **een nieuw project maken**, zoek en kies **ASP.net Web Application (.NET Framework)** en selecteer vervolgens **volgende**.
1. Geef in **uw nieuwe project een** naam voor de toepassing _MyFirstAzureWebApp_ en selecteer vervolgens **maken**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio-ASP.NET Framework 4,8 Web-App configureren." border="true":::

1. Selecteer de **MVC** -sjabloon.
1. Zorg ervoor dat **verificatie** is ingesteld op **geen verificatie**. Selecteer **Maken**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio: Selecteer de MVC-sjabloon." border="true":::

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio-ASP.NET Framework 4,8 wordt lokaal uitgevoerd." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Maak een nieuwe map met de naam _MyFirstAzureWebApp_ en open deze in Visual Studio code. Open het <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminal</a> venster en maak een nieuwe .net-Web-app met behulp van de [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) opdracht.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> De `--target-framework-override` vlag is een vrije-tekst vervanging van de doel-Framework-moniker (tfm) voor het project en biedt *geen garantie* dat de ondersteunende sjabloon bestaat of zich compileert. U kunt alleen .NET Framework-apps bouwen en uitvoeren in Windows.

---

Voer vanuit de **Terminal** in Visual Studio code de toepassing lokaal uit met behulp van de [`dotnet run`](/dotnet/core/tools/dotnet-run) opdracht.

```dotnetcli
dotnet run
```

Open een webbrowser en navigeer naar de app op `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet de sjabloon ASP.NET Core 3,1 Web-app die wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio code: voer .NET Core 3,1 in de browser lokaal uit." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet de sjabloon ASP.NET Core 5,0 Web-app die wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio code: .NET 5,0 in de browser lokaal uitvoeren." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet dat de Web-App van de sjabloon ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio code: .NET 4,8 in de browser lokaal uitvoeren." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Open een Terminal venster op uw computer naar een werkmap. Maak een nieuwe .NET-Web-app met behulp van de [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) opdracht en wijzig vervolgens de mappen in de zojuist gemaakte app.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> De `--target-framework-override` vlag is een vrije-tekst vervanging van de doel-Framework-moniker (tfm) voor het project en biedt *geen garantie* dat de ondersteunende sjabloon bestaat of zich compileert. U kunt alleen .NET Framework-apps bouwen in Windows.

---

Vanuit dezelfde terminal sessie voert u de toepassing lokaal uit met behulp van de [`dotnet run`](/dotnet/core/tools/dotnet-run) opdracht.

```dotnetcli
dotnet run
```

Open een webbrowser en navigeer naar de app op `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet de sjabloon ASP.NET Core 3,1 Web-app die wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio code-ASP.NET Core 3,1 in de lokale browser." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet de sjabloon ASP.NET Core 5,0 Web-app die wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio code-ASP.NET Core 5,0 in de lokale browser." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet dat de Web-App van de sjabloon ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio code-ASP.NET Framework 4,8 in de lokale browser." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Uw web-app publiceren

Als u de web-app wilt publiceren, moet u eerst een nieuwe App Service maken en configureren waarin u de app kunt publiceren.

Als onderdeel van het instellen van de App Service maakt u het volgende:

- Een nieuwe [resource groep](../azure-resource-manager/management/overview.md#terminology) die alle Azure-resources voor de service bevat.
- Een nieuw [Hostingabonnement](overview-hosting-plans.md) dat de locatie, grootte en functies opgeeft van de webserverfarm die als host fungeert voor de app.

Volg deze stappen om de App Service te maken en uw web-app te publiceren:

:::zone target="docs" pivot="development-environment-vs"

1. Klik in **Solution Explorer** met de rechter muisknop op het project **MyFirstAzureWebApp** en selecteer **publiceren**.
1. Selecteer in **publiceren** de optie **Azure** en vervolgens **volgende**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio: de web-app en het doel-Azure publiceren." border="true":::

1. Uw opties zijn afhankelijk van het feit of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt gekoppeld aan een Azure-account. Selecteer **Een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio: Selecteer aanmelden bij Azure dialoog venster.":::

1. Kies het **specifieke doel**, hetzij **Azure app service (Linux)** of **Azure app service (Windows)**.

    > [!IMPORTANT]
    > Bij het richten op ASP.NET Framework 4,8 maakt u gebruik van **Azure app service (Windows)**.

1. Selecteer aan de rechter kant van **app service exemplaren** **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Dialoog venster Visual Studio-nieuwe App Service-app.":::

1. Accepteer bij **Abonnement** het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.
1. Selecteer in **Resourcegroep** de optie **Nieuw**. Voer bij **Naam van nieuwe resourcegroep** in: *myResourceGroup*. Selecteer vervolgens **OK**.
1. Selecteer bij **Hostingabonnement** de optie **Nieuw**.
1. Voer in het dialoogvenster **Hostingabonnement: Nieuw hostingabonnement maken** de waarden in die zijn opgegeven in de volgende tabel:

    | Instelling          | Voorgestelde waarde          | Beschrijving                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Hostingabonnement** | *MyFirstAzureWebAppPlan* | De naam van het App Service-plan.                                         |
    | **Locatie**     | *Europa -west*            | Het datacenter waar de web-app wordt gehost.                           |
    | **Grootte**         | *Gratis*                   | De [prijscategorie][app-service-pricing-tier] bepaalt de hosting-functies. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Nieuw hostingabonnement maken":::

1. Voer bij **Naam** een unieke app-naam in die alleen deze geldige tekens bevat: `a-z`, `A-Z`, `0-9` en `-`. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app-name>.azurewebsites.net`, waarbij `<app-name>` de naam van uw app is.
1. Selecteer **Maken** om de Azure-resources te maken.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio: een dialoog venster voor het maken van app-resources.":::

   Zodra de wizard is voltooid, worden de Azure-resources gemaakt en bent u klaar om te publiceren.

1. Klik op **Voltooien** om de wizard te sluiten.
1. Selecteer op de pagina **publiceren** de optie **publiceren**. Visual Studio maakt, verpakt en publiceert de app naar Azure. Daarna wordt de app gestart in de standaardbrowser.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de web-app ASP.NET Core 3,1 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio-ASP.NET Core 3,1 Web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    U ziet dat de web-app ASP.NET Core 5,0 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio-ASP.NET Core 5,0 Web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet dat de web-app ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio-ASP.NET Framework 4,8 Web-app in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Uw web-app implementeren met behulp van de Visual Studio Azure Tools Extension:

1. Open in Visual Studio code het [**opdracht palet**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>CTRL</kbd> + <kbd>+ SHIFT</kbd> + <kbd>P</kbd>.
1. Zoek en selecteer ' Azure App Service: implementeren naar web-app '.
1. Reageer op de prompts als volgt:

    - Selecteer *MyFirstAzureWebApp* als de map die u wilt implementeren.
    - Selecteer **Configuratie toevoegen** wanneer u hierom wordt gevraagd.
    - Meld u aan bij uw bestaande Azure-account als u hierom wordt gevraagd.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio code: Meld u aan bij Azure." border="true":::

    - Selecteer uw **abonnement**.
    - Selecteer **nieuwe web-app maken... Geavanceerd**.
    - Voor **het invoeren van een wereld wijd unieke naam** gebruikt u een naam die uniek is voor alle Azure (*geldige tekens zijn `a-z` , `0-9` en `-`*). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
    - Selecteer **nieuwe resource groep maken** en geef een naam op zoals `myResourceGroup` .
    - Wanneer u wordt gevraagd om **een runtime stack te selecteren**:
      - Voor *.net core 3,1* selecteert u **.net Core 3,1 (LTS)**
      - Voor *.net 5,0* selecteert u **.net 5**
      - Selecteer **ASP.net v 4.8** voor *.NET Framework 4,8*
    - Selecteer een besturings systeem (Windows of Linux).
        - Voor *.NET Framework 4,8* wordt Windows impliciet geselecteerd.
    - Selecteer **een nieuw app service plan maken**, geef een naam op en selecteer de **gratis** [prijs categorie][app-service-pricing-tier]F1.
    - Selecteer **overs laan voor nu** voor de Application Insights resource.
    - Selecteer een locatie bij u in de buurt.

1. Wanneer het publiceren is voltooid, selecteert u **Bladeren website** in de melding en selecteert u **openen** wanneer u hierom wordt gevraagd.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de web-app ASP.NET Core 3,1 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio code-ASP.NET Core 3,1-Web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    U ziet dat de web-app ASP.NET Core 5,0 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio code-ASP.NET Core 5,0-Web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet dat de web-app ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio code-ASP.NET Framework 4,8 Web-app in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Implementeer de code in uw lokale *MyFirstAzureWebApp* -map met behulp van de [`az webapp up`](/cli/azure/webapp#az_webapp_up) opdracht:

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Als de `az` opdracht niet wordt herkend, moet u ervoor zorgen dat de Azure cli is geïnstalleerd, zoals beschreven in [vereisten](#prerequisites).
- Vervang `<app-name>` door een naam die in de volledige Azure-omgeving uniek is (*geldige tekens zijn `a-z`, `0-9` en `-`* ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
- `--sku F1`Met het argument maakt u de web-app op de [prijs categorie][app-service-pricing-tier] **gratis** . Laat dit argument weg om een snellere Premium-laag te gebruiken, waarmee u kosten per uur in rekening worden gebracht.
- Vervang door `<os>` ofwel `linux` of `windows` . U moet gebruiken `windows` bij het richten op *ASP.NET Framework 4,8*.
- U kunt eventueel het argument `--location <location-name>` toevoegen, waarbij `<location-name>` een beschikbare Azure-regio is. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de [`az account list-locations`](/cli/azure/appservice#az-appservice-list-locations)-opdracht uit te voeren.

Het volledig uitvoeren van de opdracht kan even duren. Tijdens het uitvoeren van worden berichten over het maken van de resource groep, het App Service plan en de hosting-app, het configureren van logboek registratie en het uitvoeren van een ZIP-implementatie. Vervolgens wordt een bericht met de URL van de app uitgevoerd:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Open een webbrowser en navigeer naar de URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet dat de web-app ASP.NET Core 3,1 wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI-ASP.NET Core 3,1-Web-app in Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet dat de web-app ASP.NET Core 5,0 wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI-ASP.NET Core 5,0-Web-app in Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet dat de web-app ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI-ASP.NET Framework 4,8-Web-app in Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Volg deze stappen om uw web-app bij te werken en opnieuw te implementeren:

:::zone target="docs" pivot="development-environment-vs"

1. Open in **Solution Explorer** onder uw project *index. cshtml*.
1. Vervang het eerste `<div>` element door de volgende code:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Sla uw wijzigingen op.

1. Als u opnieuw wilt implementeren naar Azure, klikt u met de rechter muisknop op het **MyFirstAzureWebApp** -project in **Solution Explorer** en selecteert u **publiceren**.
1. Selecteer op de samenvattingspagina **Publiceren** de optie **Publiceren**.

    Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de bijgewerkte ASP.NET Core 3,1 Web-app wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio-ASP.NET Core 3,1-Web-app in azure bijgewerkt.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    U ziet dat de bijgewerkte ASP.NET Core 5,0 Web-app wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio-ASP.NET Core 5,0-Web-app in azure bijgewerkt.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet dat de bijgewerkte web-app ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio-bijgewerkt ASP.NET Framework 4,8 Web-app in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Open *Index.cshtml*.
1. Vervang het eerste `<div>` element door de volgende code:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Sla uw wijzigingen op.

1. Open de Visual Studio-code balk aan de **zijkant**, selecteer het pictogram van **Azure** om de opties uit te vouwen.
1. Vouw in het knoop punt **app service** uw abonnement uit en klik met de rechter muisknop op het **MyFirstAzureWebApp**.
1. Selecteer de **Web-app voor implementeren...**.
1. Selecteer **implementeren** wanneer u hierom wordt gevraagd.
1. Wanneer het publiceren is voltooid, selecteert u **Bladeren website** in de melding en selecteert u **openen** wanneer u hierom wordt gevraagd.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de bijgewerkte ASP.NET Core 3,1 Web-app wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio code-bijgewerkte ASP.NET Core 3,1-Web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    U ziet dat de bijgewerkte ASP.NET Core 5,0 Web-app wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio code-bijgewerkte ASP.NET Core 5,0-Web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet dat de bijgewerkte web-app ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio code-bijgewerkt ASP.NET Framework 4,8 Web-app in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Open in de lokale map het bestand *index. cshtml* . Het eerste `<div>` element vervangen:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Sla de wijzigingen op en implementeer de app opnieuw met de opdracht `az webapp up`:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3,1 is kruislings platform, op basis van uw vorige implementatie, vervangen door `<os>` ofwel `linux` of `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5,0 is kruislings platform, op basis van uw vorige implementatie, vervangen door `<os>` ofwel `linux` of `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4,8 heeft Framework-afhankelijkheden en moet worden gehost in Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Als u geïnteresseerd bent in het hosten van uw .NET-apps op Linux, kunt u overwegen [om te migreren van ASP.NET Framework naar ASP.net core](/aspnet/core/migration/proper-to-2x).

---

Met deze opdracht worden waarden gebruikt die lokaal in de cache worden opgeslagen in het bestand *.azure/config*, met inbegrip van de app-naam, de resourcegroep en het App Service-plan.

Als de implementatie is voltooid, gaat u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en klikt u op Vernieuwen.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet dat de bijgewerkte ASP.NET Core 3,1 Web-app wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI-ASP.NET Core 3,1-Web-app in azure bijgewerkt.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet dat de bijgewerkte ASP.NET Core 5,0 Web-app wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI-ASP.NET Core 5,0-Web-app in azure bijgewerkt.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet dat de bijgewerkte web-app ASP.NET Framework 4,8 wordt weer gegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI-ASP.NET Framework 4,8 Web-app in azure bijgewerkt.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u de web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure portal: Selecteer App Services optie.":::

Selecteer op de pagina **App Services** de naam van uw web-app.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal-App Services pagina met een voor beeld-web-app geselecteerd.":::

De **Overzichtspagina** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een meer pagina's voor het configureren van uw app.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Overzichts pagina voor App Service van Azure Portal.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een ASP.NET-Web-App gemaakt en geïmplementeerd op Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

> [!div class="nextstepaction"]
> [Zelf studie: app ASP.NET Core met SQL database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 3,1-app configureren](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

> [!div class="nextstepaction"]
> [Zelf studie: app ASP.NET Core met SQL database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [ASP.NET Core 5,0-app configureren](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Ga naar het volgende artikel voor meer informatie over het maken van een .NET Framework-app en het verbinden met een SQL Database:

> [!div class="nextstepaction"]
> [Zelf studie: ASP.NET-app met SQL database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET Framework-app configureren](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
