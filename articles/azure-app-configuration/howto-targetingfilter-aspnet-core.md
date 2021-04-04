---
title: Gefaseerde implementatie van functies voor doelgroepen inschakelen
titleSuffix: Azure App Configuration
description: Meer informatie over het inschakelen van de gefaseerde implementatie van functies voor doel groepen
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929681"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>Gefaseerde implementatie van functies voor doelgroepen inschakelen

Met functie vlaggen kunt u de functionaliteit van uw toepassing dynamisch activeren of deactiveren. Functie filters bepalen de status van een functie vlag elke keer dat deze wordt geëvalueerd. De `Microsoft.FeatureManagement` bibliotheek bevat `TargetingFilter` , waarmee een functie vlag wordt ingeschakeld voor een opgegeven lijst met gebruikers en groepen, of voor een opgegeven percentage gebruikers. `TargetingFilter` is ' Plak '. Dit betekent dat zodra een afzonderlijke gebruiker een functie heeft ontvangen, deze functie blijft zien voor alle toekomstige aanvragen. U kunt gebruiken `TargetingFilter` om een functie in te scha kelen voor een specifiek account tijdens een demo, zodat u nieuwe functies geleidelijk kunt implementeren voor gebruikers in verschillende groepen of ' belsignalen ' en nog veel meer.

In dit artikel leert u hoe u een nieuwe functie in een ASP.NET Core-webtoepassing implementeert voor opgegeven gebruikers en groepen, met behulp `TargetingFilter` van Azure-app-configuratie.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Een webtoepassing maken met functie vlaggen en verificatie

Als u functies wilt implementeren op basis van gebruikers en groepen, hebt u een webtoepassing nodig waarmee gebruikers zich kunnen aanmelden.

1. Maak een webtoepassing die wordt geverifieerd aan de hand van een lokale data base met behulp van de volgende opdracht:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Bouw en voer uit en selecteer vervolgens de koppeling **registreren** in de rechter bovenhoek om een nieuw gebruikers account te maken. Gebruik een e-mail adres van `test@contoso.com` . Selecteer in het bevestigings scherm voor het **registreren** de optie **Klik hier om uw account te bevestigen**.

1. Volg de instructies in [Quick Start: Voeg functie vlaggen toe aan een ASP.net core-app](./quickstart-feature-flag-aspnet-core.md) om een functie vlag toe te voegen aan uw nieuwe webtoepassing.

1. De functie vlag in app-configuratie in-of uitschakelen. Controleer of deze actie de zicht baarheid van het **bèta** -item op de navigatie balk bepaalt.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>De code van de webtoepassing bijwerken voor het gebruik van TargetingFilter

Op dit moment kunt u de functie vlag gebruiken om de functie in of uit te scha kelen `Beta` voor alle gebruikers. Als u de functie vlag voor sommige gebruikers wilt inschakelen en deze voor anderen wilt uitschakelen, moet u de code bijwerken om te gebruiken `TargetingFilter` . In dit voor beeld gebruikt u het e-mail adres van de aangemelde gebruiker als de gebruikers-ID en het gedeelte domein naam van het e-mail adres als de groep. U voegt de gebruiker en groep toe aan de `TargetingContext` . De `TargetingFilter` gebruikt deze context om de status van de functie vlag voor elke aanvraag te bepalen.

1. Update naar de nieuwste versie van het `Microsoft.FeatureManagement.AspNetCore` pakket.

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. Voeg een bestand *TestTargetingContextAccessor. cs* toe:

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. Voeg in *Startup. cs* een verwijzing toe aan de naam ruimte *micro soft. FeatureManagement. FeatureFilters* :

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Werk de *ConfigureServices* -methode bij om te registreren `TargetingFilter` , na het aanroepen van `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Werk de methode *ConfigureServices* bij om de `TestTargetingContextAccessor` gemaakte in de vorige stap toe te voegen aan de service verzameling. De *TargetingFilter* gebruikt deze om de doel context te bepalen wanneer de functie vlag wordt geëvalueerd.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

De volledige *ConfigureServices* -methode ziet er als volgt uit:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>De functie vlag voor het gebruik van TargetingFilter bijwerken

1. Ga in het Azure Portal naar de configuratie Store van uw app en selecteer **functie beheer**.

1. Selecteer het context menu voor de vlag van de *bèta* functie die u hebt gemaakt in de Quick Start. Selecteer **Bewerken**.

    > [!div class="mx-imgBorder"]
    > ![Bèta-functie vlag bewerken](./media/edit-beta-feature-flag.png)

1. Schakel in het scherm **bewerken** het selectie vakje **functie vlag inschakelen** in als dit nog niet is geselecteerd. Schakel vervolgens het selectie vakje **functie filter gebruiken** in.

1. Selecteer het keuze rondje voor de **doel** groep.

1. Selecteer de volgende opties:

    - **Standaard percentage**: 0
    - **Groepen**: Voer een **naam** in van _contoso.com_ en een **percentage** van _50_
    - **Gebruikers**: `test@contoso.com`

    Het scherm onderdeel filter ziet er als volgt uit:

    > [!div class="mx-imgBorder"]
    > ![Vlag voor voorwaardelijke functie](./media/feature-flag-filter-enabled.png)

    Deze instellingen resulteren in het volgende gedrag:

    - De functie vlag is altijd ingeschakeld voor gebruiker `test@contoso.com` , omdat `test@contoso.com` deze wordt weer gegeven in de sectie _gebruikers_ .
    - De functie vlag is ingeschakeld voor 50% van andere gebruikers in de groep _contoso.com_ , omdat _contoso.com_ wordt vermeld in de sectie _groepen_ met een _percentage_ van _50_.
    - De functie is altijd uitgeschakeld voor alle andere gebruikers, omdat het _standaard percentage_ is ingesteld op _0_.

1. Selecteer **Toep assen** om deze instellingen op te slaan en terug te keren naar het scherm **functie beheer** .

1. Het **functie filter** voor de functie vlag wordt nu weer gegeven als *doel*. Deze status geeft aan dat de functie vlag per aanvraag wordt in-of uitgeschakeld, op basis van de criteria die worden afgedwongen door het *doel* onderdeel filter.

## <a name="targetingfilter-in-action"></a>TargetingFilter in actie

Als u de effecten van deze functie vlag wilt zien, bouwt u de toepassing en voert u deze uit. In eerste instantie wordt het *bèta* -item niet weer gegeven op de werk balk, omdat de optie _standaard percentage_ is ingesteld op 0.

Meld u nu aan als `test@contoso.com` met het wacht woord dat u hebt ingesteld bij het registreren. Het *bèta* -item wordt nu weer gegeven op de werk balk, omdat het `test@contoso.com` is opgegeven als een doel gebruiker.

In de volgende video ziet u dit gedrag in actie.

> [!div class="mx-imgBorder"]
> ![TargetingFilter in actie](./media/feature-flags-targetingfilter.gif)

U kunt aanvullende gebruikers met `@contoso.com` e-mail adressen maken om het gedrag van de groeps instellingen te bekijken. met 50% van deze gebruikers wordt het *bèta* -item weer geven. Het *bèta* -item wordt niet weer geven in het andere 50%.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Overzicht van functie beheer](./concept-feature-management.md)