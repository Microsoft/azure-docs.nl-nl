---
title: OpenSSL voor Linux configureren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van OpenSSL voor Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577441"
---
# <a name="configure-openssl-for-linux"></a>OpenSSL voor Linux configureren

Wanneer u een Speech SDK-versie vóór 1.9.0 gebruikt, wordt [openssl](https://www.openssl.org) dynamisch geconfigureerd voor de versie van het hostsysteem. In latere versies van de Speech SDK is OpenSSL (versie [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statisch gekoppeld aan de kern bibliotheek van de spraak-SDK.

Controleer of de OpenSSL-certificaten in uw systeem zijn geïnstalleerd om verbinding te garanderen. Een opdracht uitvoeren:
```bash
openssl version -d
```

De uitvoer op systemen op basis van Ubuntu/Debian moet zijn:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Controleer of er een `certs` submap onder OPENSSLDIR is. In het bovenstaande voor beeld zou het zijn `/usr/lib/ssl/certs` .

* Als `/usr/lib/ssl/certs` dat het geval is en een groot aantal afzonderlijke certificaat bestanden (met `.crt` of `.pem` uitbrei ding) bevat, is er geen verdere actie nodig.

* Als OPENSSLDIR iets anders is dan `/usr/lib/ssl` en/of er één certificaat bundel bestand is in plaats van meerdere afzonderlijke bestanden, moet u een geschikte SSL-omgevings variabele instellen om aan te geven waar de certificaten kunnen worden gevonden.

## <a name="examples"></a>Voorbeelden

- OPENSSLDIR is `/opt/ssl` . Er is een `certs` submap met veel `.crt` of `.pem` bestanden.
Stel de omgevings variabele `SSL_CERT_DIR` in op om aan te wijzen `/opt/ssl/certs` voordat een programma wordt uitgevoerd dat gebruikmaakt van de spraak-SDK. Bijvoorbeeld:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR is `/etc/pki/tls` (zoals op RHEL/CentOS gebaseerde systemen). Er is `certs` bijvoorbeeld een submap met een certificaat bundel bestand `ca-bundle.crt` .
Stel de omgevings variabele `SSL_CERT_FILE` zodanig in dat deze naar dat bestand verwijst voordat u een programma uitvoert dat gebruikmaakt van de spraak-SDK. Bijvoorbeeld:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Intrekkings controles voor certificaten
Wanneer u verbinding maakt met de spraak service, controleert de Speech SDK of het TLS-certificaat dat wordt gebruikt door de speech-service niet is ingetrokken. Om deze controle uit te voeren, moet de spraak-SDK toegang hebben tot de CRL-distributie punten voor certificerings instanties die door Azure worden gebruikt. In [dit document](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)vindt u een lijst met mogelijke locaties voor het downloaden van de CRL. Als een certificaat is ingetrokken of de CRL niet kan worden gedownload, breekt de spraak-SDK de verbinding af en wordt de geannuleerde gebeurtenis verhoogd.

In het geval dat het netwerk waarvan de spraak-SDK wordt gebruikt, is geconfigureerd op een manier die geen toegang tot de locaties voor het downloaden van de CRL toestaat, kan de CRL-controle worden uitgeschakeld of ingesteld op niet mislukt als de CRL niet kan worden opgehaald. Deze configuratie wordt uitgevoerd via het configuratie object dat wordt gebruikt voor het maken van een Recognizer-object.

Als u wilt door gaan met de verbinding wanneer een CRL niet kan worden opgehaald, stelt u de eigenschap in OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Wanneer deze optie is ingesteld op ' True ', wordt een poging gedaan om de CRL op te halen. als het ophalen is geslaagd, wordt het certificaat gecontroleerd op intrekking als het ophalen mislukt, kan de verbinding worden voortgezet.

Als u de controle van certificaat intrekking volledig wilt uitschakelen, stelt u de eigenschap OPENSSL_DISABLE_CRL_CHECK in op ' True '.
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Het is ook een goed idee dat er voor sommige distributies van Linux geen omgevings variabele TMP of TMPDIR is gedefinieerd. Dit zorgt ervoor dat de spraak-SDK elke keer de certificaatintrekkingslijst (CRL) downloadt, in plaats van de CRL in de cache te plaatsen op de schijf voor hergebruik totdat deze verloopt. Als u de initiële verbindings prestaties wilt verbeteren, kunt u [een omgevings variabele met de naam tmpdir maken en deze instellen op het pad van de gekozen tijdelijke map.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Info over de Speech-SDK](speech-sdk.md)
