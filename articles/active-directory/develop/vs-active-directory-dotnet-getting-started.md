---
title: Aan de slag met Azure AD in .NET MVC-projecten | Azure
description: Aan de slag met Azure Active Directory in .NET MVC-projecten na het maken van verbinding met of het maken van een Azure AD met behulp van Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165461"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Aan de slag met Azure Active Directory (ASP.NET MVC-projecten)

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-dotnet-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-dotnet-what-happened.md)

In dit artikel vindt u aanvullende richt lijnen nadat u Active Directory hebt toegevoegd aan een ASP.NET MVC-project via de opdracht **project > Connected Services** van Visual Studio. Als u de service nog niet aan uw project hebt toegevoegd, kunt u dit op elk gewenst moment doen.

Zie [Wat is er gebeurd met mijn MVC-project?](vs-active-directory-dotnet-what-happened.md) voor de wijzigingen die zijn aangebracht in het project bij het toevoegen van de verbonden service.

## <a name="requiring-authentication-to-access-controllers"></a>Verificatie vereisen voor toegang tot controllers

Alle controllers in uw project zijn voorzien van het `[Authorize]` kenmerk. Voor dit kenmerk moet de gebruiker worden geverifieerd voordat ze toegang krijgen tot deze controllers. Als u wilt toestaan dat de controller anoniem wordt geopend, verwijdert u dit kenmerk van de controller. Als u de machtigingen op een meer gedetailleerd niveau wilt instellen, past u het kenmerk toe op elke methode waarvoor autorisatie is vereist in plaats van het toe te passen op de klasse controller.

## <a name="adding-signin--signout-controls"></a>Besturings elementen voor aanmelden/afmelden toevoegen

Als u de besturings elementen voor aanmelden/afmelden wilt toevoegen aan uw weer gave, kunt u de `_LoginPartial.cshtml` gedeeltelijke weer gave gebruiken om de functionaliteit toe te voegen aan een van uw weer gaven. Hier volgt een voor beeld van de functionaliteit die is toegevoegd aan de standaard `_Layout.cshtml` weergave. (Let op het laatste element in de div met Class-navigatie balk-samen vouwen):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>Volgende stappen

- [Verificatie scenario's voor Azure Active Directory](./authentication-vs-authorization.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)