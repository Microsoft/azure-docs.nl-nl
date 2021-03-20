---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78201946"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Binding-extensies registreren

Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. Voer de volgende [dotnet add package](/dotnet/core/tools/dotnet-add-package)-opdracht in het terminalvenster uit om het Storage-extensiepakket toe te voegen aan uw project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Nu kunt u de Storage-uitvoerbinding toevoegen aan uw project.  
::: zone-end  