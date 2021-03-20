---
title: Een aangepaste NuGet-feed gebruiken
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Gebruik een aangepaste NuGet-feed voor toegang tot en gebruik van NuGet-pakketten in een Azure dev-ruimte.
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960215"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Een aangepaste NuGet-feed gebruiken met Azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Een NuGet-feed biedt een handige manier om pakket bronnen in een project op te neemt. Azure dev Spaces moeten toegang hebben tot deze feed om afhankelijkheden goed te kunnen installeren in de docker-container.

## <a name="set-up-a-nuget-feed"></a>Een NuGet-feed instellen

Voeg een [pakket verwijzing](/nuget/consume-packages/package-references-in-project-files) toe voor uw afhankelijkheid in het `*.csproj` bestand onder het `PackageReference` knoop punt. Bijvoorbeeld:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Maak een [NuGet.Config](/nuget/reference/nuget-config-file) -bestand in de projectmap en stel de `packageSources` secties en in `packageSourceCredentials` voor uw NuGet-feed. De `packageSources` sectie bevat de feed-URL, die toegankelijk moet zijn vanuit uw AKS-cluster. De `packageSourceCredentials` zijn de referenties voor toegang tot de feed. Bijvoorbeeld:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Werk uw Dockerfiles bij om het `NuGet.Config` bestand naar de installatie kopie te kopiëren. Bijvoorbeeld:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> In Windows, `NuGet.Config` , `Nuget.Config` , en `nuget.config` werkt als geldige bestands namen. In Linux is alleen `NuGet.Config` een geldige bestands naam voor dit bestand. Omdat Azure dev Spaces docker en Linux gebruiken, moet dit bestand een naam hebben `NuGet.Config` . U kunt de naamgeving hand matig of door uit te voeren `dotnet restore --configfile nuget.config` .


Als u Git gebruikt, moet u de referenties voor uw NuGet-feed niet in versie beheer hebben. Voeg het `NuGet.Config` `.gitignore` voor uw project toe zodat het `NuGet.Config` bestand niet wordt toegevoegd aan versie beheer. Voor Azure dev Spaces is dit bestand vereist tijdens het proces voor het bouwen van de container installatie kopie, maar dit houdt rekening met de regels die zijn gedefinieerd in `.gitignore` en `.dockerignore` tijdens de synchronisatie. Als u de standaard instelling wilt wijzigen en Azure-ontwikkel ruimten wilt toestaan om het bestand te synchroniseren, moet u `NuGet.Config` het `azds.yaml` bestand bijwerken:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Als u geen gebruik maakt van Git, kunt u deze stap overs Laan.

De volgende keer dat u `azds up` `F5` Visual Studio code of Visual Studio uitvoert of aanraakt, wordt het bestand gesynchroniseerd met Azure dev Spaces `NuGet.Config` om pakket afhankelijkheden te installeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [NuGet en hoe deze werkt](/nuget/what-is-nuget).
