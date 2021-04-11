---
title: App beheren en bewaken met Azure Spring Boot Actuator
description: Meer informatie over het beheren en bewaken van de app met een Spring boot-klep.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878015"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>App beheren en bewaken met Azure Spring Boot Actuator

**Dit artikel is van toepassing op:** ✔️ Java

Nadat u het nieuwe binaire bestand hebt geïmplementeerd voor uw app, kunt u de functionaliteit controleren en informatie over uw actieve toepassing bekijken. In dit artikel wordt uitgelegd hoe u toegang krijgt tot de API vanuit een test eindpunt dat is opgenomen in azure lente Cloud en de functies voor productie gereed voor uw app beschikbaar maakt.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Spring boot 2. x-toepassing hebt die met succes kan worden geïmplementeerd en opgestart in azure lente-Cloud service.  Zie [Quick Start: een bestaande Azure lente-Cloud toepassing starten met behulp van de Azure Portal](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>App verifiëren via een test eindpunt
1. Ga naar het **toepassings dashboard** en klik op uw app om de overzichts pagina van de app in te voeren.

1. In het deel venster **overzicht** ziet u **test-eind punten**.  Open dit eind punt vanaf de opdracht regel of browser en Bekijk het API-antwoord.

1. Let op de **eind punt** -URI die wordt gebruikt in de volgende sectie.

>[!TIP]
> * Als de app een front-end-pagina retourneert en verwijst naar andere bestanden via een relatief pad, controleert u of het eind punt van de test eindigt met een slash (/). Dit zorgt ervoor dat het CSS-bestand correct wordt geladen.
> * Als u uw API bekijkt vanuit een browser en uw browser vereist dat u aanmeldings referenties opgeeft om de pagina weer te geven, gebruikt u [URL decoderen](https://www.urldecoder.org/) om het eind punt te decoderen. Het decoderen van de URL retourneert een URL in de vorm "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/ \<app-name> / \<deployment-name> ".  Gebruik dit formulier om toegang te krijgen tot uw eind punt.

## <a name="add-actuator-dependency"></a>Klep afhankelijkheid toevoegen

Als u de klep wilt toevoegen aan een Maven-project, voegt u de afhankelijkheid ' starter ' toe:

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Compileer het nieuwe binaire bestand en implementeer dit voor uw app.

## <a name="enable-production-ready-features"></a>Productie klare functies inschakelen
Met Actuator-eind punten kunt u uw toepassing bewaken en ermee werken. Er wordt standaard een Spring boot-toepassing `health` `info` weer gegeven en eind punten om informatie over wille keurige toepassingen en status te tonen.

Als u de configuratie en configureer bare omgeving wilt observeren, moeten `env` `configgrops` ook eind punten worden ingeschakeld.

1. Ga naar het deel venster app- **overzicht** , klik op **configuratie** in het menu instelling en ga naar de pagina configuratie van **omgevings variabelen** .
1. Voeg de volgende eigenschappen toe, zoals in het formulier sleutel: waarde. In deze omgeving wordt het eind punt ' env ', ' status ', ' info ' van de lente-klep geopend.

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Klik op de knop **Opslaan** , uw toepassing wordt automatisch opnieuw opgestart en de nieuwe omgevings variabelen worden geladen.

U kunt nu teruggaan naar het app-overzichts deel venster en wachten tot de inrichtings status is gewijzigd in voltooid.  Er is meer dan een actief exemplaar.

> [!Note] 
> Zodra u de app beschikbaar maakt voor openbaar, worden deze Actuator-eind punten ook beschikbaar gemaakt voor openbaar. U kunt alle eind punten verbergen door de omgevings variabelen te verwijderen en in te `management.endpoints.web.exposure.include` stellen `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Het Actuator-eind punt weer geven om toepassings gegevens weer te geven
1. U kunt nu toegang krijgen tot de URL `"<test-endpoint>/actuator/"` om alle eind punten weer te geven die worden weer gegeven met een Spring boot-klep.
1. Access `"<test-endpoint>/actuator/env"` -URL kunt u actieve profielen zien die worden gebruikt door de app en alle omgevings variabelen die zijn geladen.
1. Als u wilt zoeken in een specifieke omgeving, kunt u toegang krijgen tot de URL  `"<test-endpoint>/actuator/env/{toMatch}"` om deze weer te geven.

Als u alle ingebouwde eind punten wilt weer geven, raadpleegt u [eind punten zichtbaar](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints) maken

## <a name="next-steps"></a>Volgende stappen

* [Informatie over metrische gegevens voor Azure lente-Cloud](spring-cloud-concept-metrics.md)
* [Informatie over appstatus in Azure Spring Cloud](spring-cloud-concept-app-status.md)

