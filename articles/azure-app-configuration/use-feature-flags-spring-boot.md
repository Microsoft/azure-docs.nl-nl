---
title: Zelfstudie voor het gebruik van functievlaggen in een Spring Boot-app - Azure App Configuration | Microsoft Docs
description: In deze zelfstudie leert u hoe u functievlaggen kunt implementeren in Spring Boot-apps.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0dcf85add4e1c9f1d701c4ce4a122c8d6b31382a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077827"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Zelfstudie: Functievlaggen gebruiken in een Spring Boot-app

De Spring Boot Core Feature Management-bibliotheken bieden ondersteuning voor het implementeren van functievlaggen in een Spring Boot-toepassing. Deze bibliotheken bieden u de mogelijkheid om functievlaggen declaratief toe te voegen aan uw code.

De Feature Management-bibliotheken beheren ook de levenscyclus van functievlaggen achter de schermen. Bijvoorbeeld: de bibliotheken worden vernieuwd en slaan vlagstatussen op in een cache of garanderen dat een vlagstatus onveranderbaar is tijdens het aanroepen van een aanvraag. Daarnaast biedt de Spring Boot-bibliotheek integraties, waaronder MVC-controlleracties, routes en middleware.

De [quickstart Functievlaggen toevoegen aan een Spring boot-app](./quickstart-feature-flag-spring-boot.md) bevat verschillende manieren om functievlaggen toe te voegen in een Spring Boot-toepassing. In deze zelfstudie worden deze methoden uitgebreid beschreven.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voeg functievlaggen toe aan de belangrijkste onderdelen van uw toepassing om de beschikbaarheid van functies te bepalen.
> * Integreer met App Configuration wanneer u deze gebruikt voor het beheren van functievlaggen.

## <a name="set-up-feature-management"></a>Functiebeheer instellen

De Spring Boot-functiebeheerder `FeatureManager` krijgt functievlaggen van het systeemeigen configuratiesysteem van het Framework. Als gevolg hiervan kunt u de functievlaggen van uw toepassing definiëren met behulp van een configuratiebron die Spring Boot ondersteunt, met inbegrip van het lokale *bootstrap.yml*-bestand of omgevingsvariabelen. `FeatureManager` maakt gebruik van afhankelijkheidsinjectie. U kunt de functiebeheerservices registreren met behulp van standaard conventies:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

U wordt aangeraden de functievlaggen buiten de toepassing te houden en deze afzonderlijk te beheren. Als u dit doet, kunt u de status van de vlaggen op elk gewenst moment wijzigen. Deze wijzigingen worden direct doorgevoerd in de toepassing. App Configuration biedt een centrale locatie voor het organiseren en beheren van al uw functievlaggen via een toegewezen portal-gebruikersinterface. App Configuration levert ook rechtstreeks de vlaggen aan uw toepassing via de clientbibliotheken voor Spring Boot.

De eenvoudigste manier om uw Spring Boot-toepassing te verbinden met App Configuration is via de configuratieprovider:

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Declaratie van functievlaggen

Elke functievlag bestaat uit twee delen: een naam en een lijst met een of meer filters die worden gebruikt om te evalueren of de status van een functie *aan* is (dat wil zeggen, wanneer de waarde ervan `True` is). Een filter definieert een use-case voor wanneer een functie moet worden ingeschakeld.

Wanneer een functievlag meerdere filters heeft, wordt de filterlijst in de juiste volgorde doorgelopen, totdat een van de filters bepaalt dat de functie moet worden ingeschakeld. Op dat moment is de functievlag *aan* en worden alle resterende filterresultaten overgeslagen. Als geen enkel filter aangeeft dat de functie moet worden ingeschakeld, is de functievlag *uit*.

De functiebeheerder ondersteunt *application.yml* als een configuratiebron voor functievlaggen. In het volgende voorbeeld ziet u hoe u functievlaggen kunt instellen in een YAML-bestand:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Standaard wordt het gedeelte `feature-management` van dit YML-document gebruikt voor instellingen voor functievlaggen. Het vorige voorbeeld toont drie functievlaggen waarvoor de filters zijn gedefinieerd in de eigenschap `EnabledFor`:

* `feature-a` is *aan*.
* `feature-b` is *uit*.
* Met `feature-c` geeft u een filter op met de naam `Percentage` met een eigenschap `parameters`. `Percentage` is een configureerbaar filter. In dit voorbeeld geeft `Percentage` een kans van 50 procent dat de vlag `feature-c` *aan* is.

## <a name="feature-flag-checks"></a>Controles van functievlaggen

Het basispatroon van functiebeheer is om eerst te controleren of een functievlag is ingesteld op *aan*. Als dit het geval is, voert het functiebeheer de acties uit die de functie bevat. Bijvoorbeeld:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Afhankelijkheidsinjectie

In Spring Boot krijgt u toegang tot de functiebeheerder `FeatureManager` via afhankelijkheidsinjectie:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Controller-acties

In MVC-controllers gebruikt u het kenmerk `@FeatureGate` om te controleren of een bepaalde actie is ingeschakeld. Voor de volgende actie `Index` moet `feature-a` *aan* zijn voordat deze kan worden uitgevoerd:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Wanneer een MVC-controller of -actie wordt geblokkeerd omdat de vlag voor het controleren van de functie *uit* is, wordt een geregistreerde `IDisabledFeaturesHandler`-interface aangeroepen. De standaard `IDisabledFeaturesHandler`-interface retourneert een 404-statuscode naar de client zonder hoofdtekst van de reactie.

## <a name="mvc-filters"></a>MVC-filters

U kunt MVC-filters zo instellen dat ze worden geactiveerd op basis van de status van een functievlag. Met de volgende code wordt een MVC-filter toegevoegd met de naam `FeatureFlagFilter`. Dit filter wordt alleen in de MVC-pijplijn geactiveerd als `feature-a` is ingeschakeld.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Routes

U kunt functievlaggen gebruiken om routes om te leiden. Met de volgende code wordt een gebruiker omgeleid als `feature-a` is ingeschakeld:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u functievlaggen kunt implementeren in een Spring Boot-toepassing met behulp van de `spring-cloud-azure-feature-management-web`-bibliotheken. Raadpleeg de volgende bronnen voor meer informatie over de ondersteuning van functiebeheer in Spring Boot en App Configuration:

* [Voorbeeldcode voor de Spring Boot-functievlag](./quickstart-feature-flag-spring-boot.md)
* [Functievlaggen beheren](./manage-feature-flags.md)