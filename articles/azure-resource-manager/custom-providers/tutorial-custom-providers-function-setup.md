---
title: Azure Functions instellen
description: In deze zelfstudie wordt uitgelegd hoe u een Azure-functie-app kunt maken en instellen om te werken met aangepaste Azure-providers
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737317"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure Functions instellen voor aangepaste Azure-providers

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers kunt u werkstromen in Azure wijzigen. In deze zelfstudie wordt beschreven hoe u een Azure-functie-app instelt om te werken als een aangepast providereindpunt.

## <a name="create-the-azure-function-app"></a>De Azure-functie-app maken

> [!NOTE]
> In deze zelfstudie maakt u een eenvoudig service-eindpunt dat gebruikmaakt van een Azure-functie-app. Een aangepaste provider kan echter elk openbaar toegankelijk eindpunt gebruiken. Alternatieven zijn Azure Logic Apps, Azure API Management en de Web Apps-functie van Azure App Service.

Als u deze zelfstudie wilt starten, volgt u eerst de zelfstudie [Uw eerste Azure-functie-app te maken in de Azure Portal](../../azure-functions/functions-get-started.md). In deze zelfstudie maakt u een .NET core-webhookfunctie die kan worden gewijzigd in de Azure Portal. Het is ook de basis van de huidige zelfstudie.

## <a name="install-azure-table-storage-bindings"></a>Azure Table Storage-bindingen installeren

Azure Table Storage-bindingen installeren:

1. Ga naar het tabblad **Integreren** voor de HttpTrigger.
1. Selecteer **+ Nieuwe invoer**.
1. Selecteer **Azure Table Storage**.
1. Installeer de extensie Microsoft.Azure.WebJobs.Extensions.Storage als deze nog niet is geïnstalleerd.
1. In het vak **Tabelparameternaam** voert u **tableStorage** in.
1. In het vak **Tabelnaam** voert u **myCustomResources** in.
1. Selecteer **Opslaan** om de bijgewerkte invoerparameter op te slaan.

![Overzicht van aangepaste provider waarin tabelbindingen worden weergegeven](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP-methoden bijwerken

De Azure-functie zo instellen dat de RESTful-aanvraagmethoden van de aangepaste provider worden opgenomen:

1. Ga naar het tabblad **Integreren** voor de HttpTrigger.
1. Onder **Geselecteerde HTTP-methoden** selecteert u **GET**, **POST**, **DELETE** en **PUT**.

![Overzicht van de aangepaste provider met HTTP-methoden](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet-pakketten toevoegen

> [!NOTE]
> Als uw C#-projectbestand ontbreekt in de projectmap, kunt u het handmatig toevoegen. Of het wordt weergegeven nadat de extensie Microsoft.Azure.WebJobs.Extensions.Storage is geïnstalleerd op de functie-app.

Werk vervolgens het C#-projectbestand bij zodat dit handige NuGet-bibliotheken bevat. Met deze bibliotheken is het gemakkelijker om binnenkomende aanvragen van aangepaste providers te parseren. Volg de stappen om [extensies toe te voegen vanuit de portal](../../azure-functions/functions-bindings-register.md) en werk het C#-projectbestand bij zodat het de volgende pakketverwijzingen bevat:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Het volgende XML-element is een voorbeeld C#-projectbestand:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een Azure-functie-app in om te werken als een aangepast Azure-providereindpunt.

Voor meer informatie over het ontwerpen van een RESTful aangepast providereindpunt raadpleegt u [Zelfstudie: Een RESTful aangepast providereindpunt ontwerpen](./tutorial-custom-providers-function-authoring.md).