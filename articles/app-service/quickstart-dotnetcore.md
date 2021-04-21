---
title: 'Quickstart: Een ASP.NET-web-app implementeren'
description: Meer informatie over het uitvoeren van web-apps in Azure App Service door uw eerste app ASP.NET implementeren.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762457"
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

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Quickstart: Een ASP.NET-web-app implementeren

In deze quickstart leert u hoe u uw eerste web-app maakt en ASP.NET implementeert in [Azure App Service](overview.md). App Service ondersteunt verschillende versies van .NET-apps en biedt een uiterst schaalbare webhostingservice met self-patching. ASP.NET web-apps zijn platformoverschrijdend en kunnen worden gehost op Linux of Windows. Als u klaar bent, beschikt u over een Azure-resourcegroep die bestaat uit een App Service-hostingabonnement en een Azure Service-app met een geïmplementeerde webtoepassing.

> [!TIP]
> .NET Core 3.1 is de huidige LTS-release (long-term support) van .NET. Zie [.NET-ondersteuningsbeleid voor meer informatie.](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)

## <a name="prerequisites"></a>Vereisten

:::zone target="docs" pivot="development-environment-vs"

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> met de **ASP.NET- en webontwikkelworkload**.

    Als u al hebt geïnstalleerd Visual Studio 2019:

    - Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
    - Voeg de workload toe door **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** te selecteren.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet)
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- De <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">extensie Azure Tools.</a>

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installeer de nieuwste .NET Core 3.1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installeer de nieuwste .NET 5.0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Installeer het .NET Framework 4.8 Developer Pack. </a>

> [!NOTE]
> Visual Studio Code is echter platformoverschrijdend; .NET Framework is dat niet. Als u apps ontwikkelt .NET Framework met Visual Studio Code, kunt u een Windows-computer gebruiken om te voldoen aan de build-afhankelijkheden.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet)
- De <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>.
- De .NET SDK (inclusief runtime en CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Installeer de nieuwste .NET Core 3.1 SDK. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installeer de nieuwste .NET 5.0 SDK. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Installeer de nieuwste .NET 5.0 SDK. </a> en <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> het .NET Framework 4.8 Developer Pack. </a>

> [!NOTE]
> De [.NET CLI](/dotnet/core/tools) is echter platformoverschrijdend, maar .NET Framework is dat niet. Als u apps ontwikkelt .NET Framework de .NET CLI, kunt u overwegen om een Windows-computer te gebruiken om te voldoen aan de build-afhankelijkheden.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Zoek **in Een nieuw project maken** de optie ASP.NET Web Core **App** en selecteer **vervolgens Volgende.**
1. In **Uw nieuwe project configureren** noemt u de toepassing _MyFirstAzureWebApp_ en selecteert u **vervolgens Volgende.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Een ASP.NET Core 3.1-web-app configureren" border="true":::

1. Selecteer **.NET Core 3.1 (langetermijnondersteuning).**
1. Zorg ervoor **dat Verificatietype** is ingesteld op **Geen.** Selecteer **Maken**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio: selecteer .NET Core 3.1 en Geen als verificatietype." border="true":::

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio - .NET Core 3.1 lokaal bladeren" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Zoek **in Een nieuw project maken** de optie ASP.NET Web Core **App** en selecteer **vervolgens Volgende.**
1. In **Uw nieuwe project configureren** noemt u de toepassing _MyFirstAzureWebApp_ en selecteert u **vervolgens Volgende.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio: configureer ASP.NET 5.0-web-app." border="true":::

1. Selecteer **.NET Core 5.0 (huidig).**
1. Zorg ervoor **dat Verificatietype** is ingesteld op **Geen.** Selecteer **Maken**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio: aanvullende informatie bij het selecteren van .NET Core 5.0." border="true":::

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio: ASP.NET Core 5.0 lokaal uitgevoerd." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Zoek **in Een nieuw project** maken de optie ASP.NET **WebToepassing (.NET Framework)** en selecteer **vervolgens Volgende.**
1. In **Uw nieuwe project configureren** noemt u de toepassing _MyFirstAzureWebApp_ en selecteert u **vervolgens Maken.**

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio: configureer ASP.NET Framework 4.8-web-app." border="true":::

1. Selecteer de **MVC-sjabloon.**
1. Zorg ervoor **dat Verificatie** is ingesteld op **Geen verificatie.** Selecteer **Maken**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio: selecteer de MVC-sjabloon." border="true":::

1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio: ASP.NET Framework 4.8 wordt lokaal uitgevoerd." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Maak een nieuwe map met _de naam MyFirstAzureWebApp_ en open deze in Visual Studio Code. Open het <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">terminalvenster</a> en maak een nieuwe .NET-web-app met behulp van de [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) opdracht .

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
> De vlag is een vrije tekstvervanging van de doel framework `--target-framework-override` moniker  (TFM) voor het project en biedt geen garantie dat de ondersteunende sjabloon bestaat of compileert. U kunt alleen apps bouwen en uitvoeren .NET Framework Windows.

---

Voer vanuit **de Terminal** in Visual Studio Code de toepassing lokaal uit met behulp van de [`dotnet run`](/dotnet/core/tools/dotnet-run) opdracht .

```dotnetcli
dotnet run
```

Open een webbrowser en navigeer naar de app op `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet de sjabloon ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code: voer .NET Core 3.1 lokaal uit in de browser." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet de sjabloon ASP.NET Core 5.0-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code: voer .NET 5.0 lokaal uit in de browser." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet de sjabloon ASP.NET Framework 4.8-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code: voer .NET 4.8 lokaal uit in de browser." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Open een terminalvenster op uw computer naar een werkmap. Maak een nieuwe .NET-web-app met behulp van [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) de opdracht en wijzig vervolgens de directories in de zojuist gemaakte app.

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
> De vlag is een vrije tekstvervanging van de doel framework `--target-framework-override` moniker  (TFM) voor het project en biedt geen garantie dat de ondersteunende sjabloon bestaat of compileert. U kunt alleen apps .NET Framework Windows bouwen.

---

Voer vanuit dezelfde terminalsessie de toepassing lokaal uit met behulp van de [`dotnet run`](/dotnet/core/tools/dotnet-run) opdracht .

```dotnetcli
dotnet run
```

Open een webbrowser en navigeer naar de app op `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet de sjabloon ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code- ASP.NET Core 3.1 in de lokale browser." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet de sjabloon ASP.NET Core 5.0-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code: ASP.NET Core 5.0 in de lokale browser." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet de sjabloon ASP.NET Framework 4.8-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code - ASP.NET Framework 4.8 in de lokale browser." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Uw web-app publiceren

Als u de web-app wilt publiceren, moet u eerst een nieuwe App Service maken en configureren waarin u de app kunt publiceren.

Als onderdeel van het instellen van de App Service maakt u het volgende:

- Een nieuwe [resource groep](../azure-resource-manager/management/overview.md#terminology) die alle Azure-resources voor de service bevat.
- Een nieuw [Hostingabonnement](overview-hosting-plans.md) dat de locatie, grootte en functies opgeeft van de webserverfarm die als host fungeert voor de app.

Volg deze stappen om de App Service te maken en uw web-app te publiceren:

:::zone target="docs" pivot="development-environment-vs"

1. Klik **Solution Explorer** met de rechtermuisknop op het project **MyFirstAzureWebApp** en selecteer **Publiceren.**
1. Selecteer **in** Publiceren **de optie Azure** en vervolgens **Volgende.**

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio: publiceer de web-app en doel-Azure." border="true":::

1. Uw opties zijn afhankelijk van het feit of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt gekoppeld aan een Azure-account. Selecteer **Een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio: selecteer het dialoogvenster Aanmelden bij Azure.":::

1. Kies het **specifieke doel**, **Azure App Service (Linux)** **of Azure App Service (Windows)**.

    > [!IMPORTANT]
    > Bij het ASP.NET Framework 4.8 gebruikt u **Azure App Service (Windows)**.

1. Selecteer rechts van **App Service instanties.** **+**

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio: dialoogvenster Nieuwe App Service app.":::

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

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio: dialoogvenster App-resources maken.":::

   Zodra de wizard is voltooid, worden de Azure-resources gemaakt en bent u klaar om te publiceren.

1. Klik op **Voltooien** om de wizard te sluiten.
1. Selecteer publiceren **op de** **pagina Publiceren.** Visual Studio maakt, verpakt en publiceert de app naar Azure. Daarna wordt de app gestart in de standaardbrowser.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio- ASP.NET Core 3.1-web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    U ziet dat de ASP.NET Core 5.0-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio: ASP.NET Core 5.0-web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    De web-app ASP.NET Framework 4.8 wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio- ASP.NET Framework 4.8-web-app in Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Uw web-app implementeren met behulp van Visual Studio Azure Tools-extensie:

1. Open in Visual Studio Code het [**opdrachtenpalet**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd>.
1. Zoek en selecteer 'Azure App Service: Implementeren naar web-app'.
1. U kunt als volgt op de prompts reageren:

    - Selecteer *MyFirstAzureWebApp* als de map die u wilt implementeren.
    - Selecteer **Configuratie toevoegen wanneer** u hier om wordt gevraagd.
    - Meld u aan bij uw bestaande Azure-account als u hier om wordt gevraagd.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code: meld u aan bij Azure." border="true":::

    - Selecteer uw **abonnement**.
    - Selecteer **Nieuwe web-app maken... Geavanceerd.**
    - Gebruik **voor Enter a globally unique name** een naam die uniek is in Heel Azure ( geldige tekens zijn ,*`a-z` `0-9` en `-`*). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
    - Selecteer **Nieuwe resourcegroep maken en** geef een naam op, zoals `myResourceGroup` .
    - Wanneer u wordt gevraagd een **runtimestack te selecteren:**
      - Selecteer *voor .NET Core 3.1* **.NET Core 3.1 (LTS)**
      - Voor *.NET 5.0 selecteert* **u .NET 5**
      - Selecteer *.NET Framework 4.8* ASP.NET **V4.8**
    - Selecteer een besturingssysteem (Windows of Linux).
        - Voor *.NET Framework versie 4.8* wordt Windows impliciet geselecteerd.
    - Selecteer **Een nieuw abonnement App Service maken,** geef een naam op en selecteer de **prijscategorie F1** [Gratis.][app-service-pricing-tier]
    - Selecteer **Nu overslaan voor** de Application Insights resource.
    - Selecteer een locatie bij u in de buurt.

1. Wanneer het publiceren is voltooid, **selecteert u Bladeren** door website in de melding en **selecteert u Openen wanneer** u hier om wordt gevraagd.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code- ASP.NET Core 3.1-web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    U ziet dat de ASP.NET Core 5.0-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code: ASP.NET Core 5.0-web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet dat de ASP.NET Framework 4.8-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code- ASP.NET Framework 4.8-web-app in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Implementeer de code in uw lokale *MyFirstAzureWebApp-map* met behulp van de [`az webapp up`](/cli/azure/webapp#az_webapp_up) opdracht :

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Als de opdracht niet wordt herkend, moet u ervoor zorgen dat de Azure CLI is geïnstalleerd zoals `az` beschreven in [Vereisten.](#prerequisites)
- Vervang `<app-name>` door een naam die in de volledige Azure-omgeving uniek is (*geldige tekens zijn `a-z`, `0-9` en `-`* ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.
- Met `--sku F1` het argument maakt u de web-app in de **prijscategorie** [Gratis.][app-service-pricing-tier] Laat dit argument weg om een snellere Premium-laag te gebruiken, waarmee u kosten per uur in rekening worden gebracht.
- Vervang `<os>` door of `linux` `windows` . U moet gebruiken `windows` wanneer u zich richt op ASP.NET Framework *4.8.*
- U kunt eventueel het argument `--location <location-name>` toevoegen, waarbij `<location-name>` een beschikbare Azure-regio is. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations)-opdracht uit te voeren.

Het volledig uitvoeren van de opdracht kan even duren. Tijdens de uitvoering worden berichten verstrekt over het maken van de resourcegroep, het App Service-abonnement en het hosten van de app, het configureren van logboekregistratie en het uitvoeren van zip-implementatie. Vervolgens wordt een bericht met de URL van de app uitgevoerd:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Open een webbrowser en navigeer naar de URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet dat de ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI: ASP.NET Core 3.1-web-app in Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet dat de ASP.NET Core 5.0-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI: ASP.NET Core 5.0-web-app in Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

De web-app ASP.NET Framework 4.8 wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI: ASP.NET Framework 4.8-web-app in Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

Volg deze stappen om uw web-app bij te werken en opnieuw te implementeren:

:::zone target="docs" pivot="development-environment-vs"

1. Open **Solution Explorer**, onder uw project, *Index.cshtml*.
1. Vervang het eerste `<div>` element door de volgende code:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Sla uw wijzigingen op.

1. Als u opnieuw wilt implementeren naar Azure, klikt u met de rechtermuisknop op het project **MyFirstAzureWebApp** in **Solution Explorer** selecteert u **Publiceren.**
1. Selecteer op de samenvattingspagina **Publiceren** de optie **Publiceren**.

    Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de bijgewerkte ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio- Bijgewerkte ASP.NET Core 3.1-web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Op de pagina ziet u de bijgewerkte ASP.NET Core 5.0-web-app.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio- Bijgewerkte ASP.NET Core 5.0-web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet dat de bijgewerkte ASP.NET Framework 4.8-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio- Bijgewerkte ASP.NET Framework 4.8-web-app in Azure.":::

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

1. Open de Visual Studio Code **Side Bar** en selecteer het **Azure-pictogram** om de opties uit te vouwen.
1. Vouw onder **het knooppunt APP SERVICE** uw abonnement uit en klik met de rechtermuisknop op **MyFirstAzureWebApp.**
1. Selecteer implementeren **naar web-app...**.
1. Selecteer **Implementeren wanneer** u hier om wordt gevraagd.
1. Wanneer het publiceren is voltooid, **selecteert u Bladeren** door website in de melding en **selecteert u Openen wanneer** u hier om wordt gevraagd.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    U ziet dat de bijgewerkte ASP.NET Core 3.1-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code: bijgewerkte ASP.NET Core 3.1-web-app in Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Op de pagina ziet u de bijgewerkte ASP.NET Core 5.0-web-app.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code: bijgewerkte ASP.NET Core 5.0-web-app in Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    U ziet de bijgewerkte ASP.NET Framework 4.8-web-app wordt weergegeven op de pagina.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code: bijgewerkte ASP.NET Framework 4.8-web-app in Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Open het bestand *Index.cshtml* in de lokale map. Vervang het eerste `<div>` element:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Sla de wijzigingen op en implementeer de app opnieuw met de opdracht `az webapp up`:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

ASP.NET Core 3.1 is platformoverschrijdend, op basis van uw vorige implementatie vervangen `<os>` door `linux` of `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

ASP.NET Core 5.0 is platformoverschrijdend, op basis van uw vorige implementatie vervangen `<os>` door `linux` of `windows` .

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

ASP.NET Framework 4.8 heeft frameworkafhankelijkheden en moet worden gehost in Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Als u geïnteresseerd bent in het hosten van uw .NET-apps op Linux, kunt u overwegen om te migreren van [ASP.NET Framework naar ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Met deze opdracht worden waarden gebruikt die lokaal in de cache worden opgeslagen in het bestand *.azure/config*, met inbegrip van de app-naam, de resourcegroep en het App Service-plan.

Als de implementatie is voltooid, gaat u terug naar het browservenster dat is geopend in de stap **Bladeren naar de app** en klikt u op Vernieuwen.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

U ziet de bijgewerkte ASP.NET Core 3.1-web-app weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI: bijgewerkt ASP.NET Core 3.1-web-app in Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

U ziet de bijgewerkte ASP.NET Core 5.0-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI: bijgewerkt ASP.NET Core 5.0-web-app in Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

U ziet de bijgewerkte ASP.NET Framework 4.8-web-app wordt weergegeven op de pagina.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI: bijgewerkt ASP.NET Framework 4.8-web-app in Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>De Azure-app beheren

Als u de web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u **App Services**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Azure Portal: selecteer App Services optie.":::

Selecteer op de pagina **App Services** de naam van uw web-app.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Azure Portal: App Services pagina met een voorbeeldweb-app geselecteerd.":::

De **Overzichtspagina** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een meer pagina's voor het configureren van uw app.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Azure Portal: App Service overzichtspagina.":::

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

In deze quickstart hebt u een web-app gemaakt en ASP.NET geïmplementeerd voor Azure App Service.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

> [!div class="nextstepaction"]
> [Zelfstudie: ASP.NET Core-app met SQL database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Een ASP.NET Core 3.1-app configureren](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

> [!div class="nextstepaction"]
> [Zelfstudie: ASP.NET Core-app met SQL database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Een ASP.NET Core 5.0-app configureren](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Naar het volgende artikel gaan voor meer informatie over het maken van een .NET Framework-app en het verbinden ervan met een SQL Database:

> [!div class="nextstepaction"]
> [Zelfstudie: ASP.NET app met SQL database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Een ASP.NET Framework-app configureren](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
