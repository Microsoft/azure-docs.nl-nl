---
title: Fouten vaststellen met Azure AD Connected service (Visual Studio)
description: Er is een incompatibel verificatie type gedetecteerd door de met Active Directory verbonden service
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88114469"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Fouten diagnosticeren met de Azure Active Directory Connected-service

Tijdens het detecteren van de vorige verificatie code heeft de Azure Active Directory verbonden service een incompatibel verificatie type gedetecteerd.

Als u de vorige verificatie code in een project correct wilt detecteren, moet het project opnieuw worden opgebouwd. Als u deze fout ziet en u geen eerdere verificatie code in uw project hebt, bouwt u opnieuw op en probeert u het opnieuw.

## <a name="project-types"></a>Project typen

De verbonden service controleert het type project dat u ontwikkelt zodat het de juiste verificatie logica kan injecteren in het project. Als er een controller is die van `ApiController` in het project is afgeleid, wordt het project beschouwd als een WebAPI-project. Als er alleen controllers zijn die zijn afgeleid van `MVC.Controller` in het project, wordt het project beschouwd als een MVC-project. De verbonden service biedt geen ondersteuning voor een ander project type.

## <a name="compatible-authentication-code"></a>Compatibele verificatie code

De verbonden service controleert ook op verificatie-instellingen die eerder zijn geconfigureerd of compatibel zijn met de service. Als alle instellingen aanwezig zijn, wordt het beschouwd als een nieuwe aankomend geval en de verbonden service wordt weer gegeven met de instellingen.  Als slechts een deel van de instellingen aanwezig is, wordt dit beschouwd als een fout melding.

In een MVC-project controleert de verbonden service op een van de volgende instellingen, wat het resultaat is van het vorige gebruik van de service:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

De verbonden service controleert ook op een van de volgende instellingen in een web-API-project, wat het resultaat is van het vorige gebruik van de service:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>Incompatibele verificatie code

Ten slotte probeert de verbonden service versies van verificatie code te detecteren die zijn geconfigureerd met eerdere versies van Visual Studio. Als u deze fout hebt ontvangen, betekent dit dat het project een incompatibel verificatie type bevat. De verbonden service detecteert de volgende verificatie typen uit eerdere versies van Visual Studio:

* Windows-verificatie
* Afzonderlijke gebruikers accounts
* Organisatie-accounts

Als u Windows-verificatie in een MVC-project wilt detecteren, zoekt de verbonden het `authentication` element in uw `web.config` bestand.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Als u Windows-verificatie in een web-API-project wilt detecteren, zoekt de verbonden service naar het `IISExpressWindowsAuthentication` element in het bestand van het project `.csproj` :

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Voor het detecteren van verificatie van afzonderlijke gebruikers accounts zoekt de verbonden service naar het pakket element in uw `packages.config` bestand.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Voor het detecteren van een oude vorm van verificatie van organisatie-accounts zoekt de verbonden service naar het volgende-element in `web.config` :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Als u het verificatie type wilt wijzigen, verwijdert u het incompatibele verificatie type en probeert u de verbonden service opnieuw toe te voegen.

Zie [verificatie scenario's voor Azure AD](./authentication-vs-authorization.md)voor meer informatie.