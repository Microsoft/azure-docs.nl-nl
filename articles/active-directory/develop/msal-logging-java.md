---
title: Logboek registratie van fouten en uitzonde ringen in MSAL voor Java
titleSuffix: Microsoft identity platform
description: Meer informatie over het vastleggen van fouten en uitzonde ringen in MSAL voor Java
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954915"
---
# <a name="logging-in-msal-for-java"></a>Logboekregistratie in MSAL voor Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-java-logging"></a>MSAL voor Java-logboek registratie

Met MSAL voor Java kunt u de bibliotheek voor logboek registratie gebruiken die u al gebruikt voor uw app, zolang deze compatibel is met SLF4J. MSAL voor Java maakt gebruik van de [eenvoudige logboek registratie facade voor Java](http://www.slf4j.org/) (SLF4J) als eenvoudige gevel of abstractie voor verschillende logging-frameworks, zoals [Java. util. logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [logback](http://logback.qos.ch/) en [Log4j](https://logging.apache.org/log4j/2.x/). Met SLF4J kan de gebruiker de gewenste logboek registratie raamwerk tijdens de implementatie laten aansluiten.

Als u bijvoorbeeld logback als het Framework voor logboek registratie in uw toepassing wilt gebruiken, voegt u de afhankelijkheid logback toe aan het maven pom-bestand voor uw toepassing:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Voeg vervolgens het logback-configuratie bestand toe:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J maakt tijdens de implementatie automatisch verbinding met logback. MSAL-logboeken worden naar de-console geschreven.

Zie de [SLF4J-hand leiding](http://www.slf4j.org/manual.html)voor instructies over het maken van een binding met andere frameworks voor logboek registratie.

### <a name="personal-and-organization-information"></a>Persoonlijke gegevens en organisatie-informatie

MSAL-logboek registratie legt geen persoonlijke of bedrijfs gegevens vast. In het volgende voor beeld is logboek registratie persoonlijk of organisatie gegevens standaard uitgeschakeld:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Schakel persoonlijke en organisatorische gegevens registratie in door in te stellen `logPii()` op de opbouw functie voor client toepassingen. Als u persoonlijke of bedrijfs gegevens logboek registratie inschakelt, moet uw app verantwoordelijk zijn voor het veilig verwerken van uiterst gevoelige gegevens en het voldoen aan wettelijke vereisten.

In het volgende voor beeld wordt logboek registratie van persoonlijk of organisatie gegevens ingeschakeld:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [micro soft Identity platform-code voorbeelden](sample-v2-code.md)voor meer code voorbeelden.