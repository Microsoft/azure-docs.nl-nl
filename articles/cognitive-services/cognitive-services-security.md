---
title: Azure Cognitive Services-beveiliging
titleSuffix: Azure Cognitive Services
description: Meer informatie over de verschillende beveiligings overwegingen voor het gebruik van Cognitive Services.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 66de81387fe27bd2a81ebcfeab7d86aac7031ff1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425291"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services-beveiliging

De beveiliging moet worden beschouwd als een topprioriteit bij het ontwikkelen van alle toepassingen. Met het begin van kunst matige intelligentie-toepassingen is de beveiliging nog belang rijker. In dit artikel worden verschillende aspecten van Azure Cognitive Services beveiliging beschreven, zoals het gebruik van trans port Layer Security, authenticatie, het veilig configureren van gevoelige gegevens en Klanten-lockbox voor toegang tot klant gegevens.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Alle Cognitive Services-eind punten die worden weer gegeven via HTTP afdwingen TLS 1,2. Met een afgedwongen beveiligings protocol moeten gebruikers die proberen een Cognitive Services eind punt aan te roepen, voldoen aan de volgende richt lijnen:

* Het besturings systeem van de client moet TLS 1,2 ondersteunen
* De taal (en het platform) waarmee de HTTP-aanroep moet TLS 1,2 opgeven als onderdeel van de aanvraag
  * Afhankelijk van de taal en het platform wordt het opgeven van TLS impliciet of expliciet uitgevoerd

Neem voor .NET-gebruikers rekening met de <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Aanbevolen procedures voor Transport Layer Security </a>.

## <a name="authentication"></a>Verificatie

Bij het bespreken van verificatie zijn er verschillende veelvoorkomende problemen. Verificatie en autorisatie worden vaak voor elkaar verward. De identiteit is ook een belang rijk onderdeel van de beveiliging. Een identiteit is een verzameling gegevens over een <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">Principal </a>. Id-providers (IdP) bieden identiteiten aan verificatie services. Verificatie is het verifiëren van de identiteit van een gebruiker. Autorisatie is de specificatie van toegangs rechten en bevoegdheden voor bronnen voor een bepaalde identiteit. Enkele van de Cognitive Services-aanbiedingen, waaronder Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Azure RBAC kan worden gebruikt om een aantal van de ceremonie die betrokken is bij het hand matig beheren van principals, te vereenvoudigen. Zie [toegangs beheer op basis van rollen voor Azure-resources](../role-based-access-control/overview.md)voor meer informatie.

Zie <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">aanvragen verifiëren voor Azure Cognitive Services</a>voor meer informatie over verificatie met abonnements sleutels, toegangs tokens en Azure Active Directory (Aad).

## <a name="environment-variables-and-application-configuration"></a>Omgevings variabelen en toepassings configuratie

Omgevings variabelen zijn naam/waarde-paren, die zijn opgeslagen in een specifieke omgeving. Een veiligere alternatief voor het gebruik van hardcoded waarden voor gevoelige gegevens is het gebruik van omgevings variabelen. Hardcoded waarden zijn onveilig en moeten worden vermeden.

> [!CAUTION]
> Gebruik **geen** hardcoded waarden voor gevoelige gegevens, maar dit is een belang rijk beveiligings probleem.

> [!NOTE]
> Hoewel omgevings variabelen worden opgeslagen als tekst zonder opmaak, zijn ze geïsoleerd voor een omgeving. Als er is geknoeid met een omgeving, is dat ook de variabelen van de omgeving.

### <a name="set-environment-variable"></a>Omgevings variabele instellen

Als u omgevings variabelen wilt instellen, gebruikt u een van de volgende opdrachten: waarbij de de `ENVIRONMENT_VARIABLE_KEY` benoemde sleutel is en `value` de waarde is die is opgeslagen in de omgevings variabele.

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Maak een permanente omgevings variabele en wijs deze toe, op basis van de waarde.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Lees de omgevings variabele in een nieuw exemplaar van de **opdracht prompt**.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Maak een permanente omgevings variabele en wijs deze toe, op basis van de waarde.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Lees de omgevings variabele in een nieuw exemplaar van **Windows Power shell**.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Maak een permanente omgevings variabele en wijs deze toe, op basis van de waarde.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

In een nieuw exemplaar van de **bash** leest u de omgevings variabele.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Nadat u een omgevings variabele hebt ingesteld, start u de Integrated Development Environment (IDE) opnieuw om er zeker van te zijn dat nieuwe, toegevoegde omgevings variabelen beschikbaar zijn.

### <a name="get-environment-variable"></a>Omgevings variabele ophalen

Als u een omgevings variabele wilt ophalen, moet deze in het geheugen worden gelezen. Afhankelijk van de taal die u gebruikt, moet u rekening houden met de volgende code fragmenten. Deze code fragmenten laten zien hoe u de omgevings variabele kunt ophalen `ENVIRONMENT_VARIABLE_KEY` en toewijzen aan een variabele met de naam `value` .

# <a name="c"></a>[C#](#tab/csharp)

Zie <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` </a>voor meer informatie.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Zie <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` </a>voor meer informatie.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Zie <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` </a>voor meer informatie.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Zie <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` </a>voor meer informatie.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Zie <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` </a>voor meer informatie.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

Zie <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` </a>voor meer informatie.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Klanten-lockbox

[Klanten-lockbox voor Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) biedt klanten een interface om aanvragen voor toegang tot klant gegevens te controleren en goed te keuren of af te wijzen. Dit wordt gebruikt in gevallen waarin een Microsoft-engineer toegang heeft tot klant gegevens tijdens een ondersteuningsaanvraag. Zie [klanten-lockbox](../security/fundamentals/customer-lockbox-overview.md)voor informatie over hoe klanten-lockbox aanvragen worden gestart, bijgehouden en opgeslagen voor latere beoordelingen en controles. 

Klanten-lockbox is beschikbaar voor deze cognitieve service:

* Translator

Voor de volgende services heeft micro soft-technici geen toegang tot klant gegevens in de E0-laag: 

* Taalbegrip
* Face
* Content Moderator
* Personalizer

> [!IMPORTANT]
> Voor **formulier herkenner** hebben micro soft-technici geen toegang tot klant gegevens in resources die zijn gemaakt na 10 juli 2020.

Als u de mogelijkheid wilt aanvragen om de E0-SKU te gebruiken, vult u dit [aanvraag formulier](https://aka.ms/cogsvc-cmk)in en verzendt u dit. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van de E0 SKU met LUIS, moet u een nieuwe resource maken op basis van de Azure Portal en E0 selecteren als prijs categorie. Gebruikers kunnen geen upgrade uitvoeren van de F0 naar de nieuwe E0-SKU.

De spraak service biedt momenteel geen ondersteuning voor Klanten-lockbox. Klant gegevens kunnen echter worden opgeslagen met behulp van uw eigen opslag (BYOS), zodat u vergelijk bare gegevens besturings elementen kunt behalen voor Klanten-lockbox. Houd er wel bij dat de gegevens van de speech-service blijven en worden verwerkt in de regio waarin de spraak bron is gemaakt. Dit geldt voor alle gegevens in rust en gegevens die onderweg zijn. Wanneer u aanpassings functies gebruikt, zoals Custom Speech en aangepaste spraak, worden alle klant gegevens overgebracht, opgeslagen en verwerkt in dezelfde regio waar uw BYOS (indien gebruikt) en de speech service-bron zich bevinden.

> [!IMPORTANT]
> Micro soft gebruikt **geen** klant gegevens om de spraak modellen te verbeteren. Als eindpunt logboek registratie is uitgeschakeld en er geen aanpassingen worden gebruikt, worden er geen klant gegevens opgeslagen. 

## <a name="next-steps"></a>Volgende stappen

* De verschillende [Cognitive Services](./what-are-cognitive-services.md) verkennen
* Meer informatie over [Cognitive Services virtuele netwerken](cognitive-services-virtual-networks.md)