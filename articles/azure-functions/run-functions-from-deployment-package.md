---
title: Uw Azure Functions uitvoeren vanuit een pakket
description: Laat de Azure Functions runtime uw functies uitvoeren door een implementatie pakket bestand te koppelen dat de project bestanden van de functie-app bevat.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: aad6991d0ddd5c439d03e41adec63837a21db87b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581589"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uw Azure Functions uitvoeren vanuit een pakket bestand

In azure kunt u uw functies rechtstreeks uitvoeren vanuit een implementatie pakket bestand in uw functie-app. De andere optie is het implementeren van uw bestanden in de `d:\home\site\wwwroot` map van uw functie-app.

In dit artikel worden de voor delen beschreven van het uitvoeren van uw functies vanuit een pakket. Ook wordt uitgelegd hoe u deze functie inschakelt in uw functie-app.

## <a name="benefits-of-running-from-a-package-file"></a>Voor delen van het uitvoeren vanuit een pakket bestand
  
Er zijn verschillende voor delen voor het uitvoeren van een pakket bestand:

+ Hiermee vermindert u het risico dat problemen met het kopiëren van bestanden worden vergrendeld.
+ Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
+ U kunt zeker zijn van de bestanden die in uw app worden uitgevoerd.
+ Verbetert de prestaties van [Azure Resource Manager implementaties](functions-infrastructure-as-code.md).
+ Kan koude begin tijden verminderen, met name voor Java script-functies met grote NPM-pakket structuren.

Zie [deze aankondiging](https://github.com/Azure/app-service-announcements/issues/84)voor meer informatie.

## <a name="enabling-functions-to-run-from-a-package"></a>Functies inschakelen om uit te voeren vanuit een pakket

Als u wilt dat uw functie-app kan worden uitgevoerd vanuit een pakket, voegt u alleen een `WEBSITE_RUN_FROM_PACKAGE` instelling toe aan de instellingen van uw functie-app. De `WEBSITE_RUN_FROM_PACKAGE` instelling kan een van de volgende waarden hebben:

| Waarde  | Beschrijving  |
|---------|---------|
| **`1`**  | Aanbevolen voor functie-apps die worden uitgevoerd in Windows. Voer uit vanuit een pakket bestand in de `d:\home\data\SitePackages` map van uw functie-app. Als niet wordt [geïmplementeerd met zip Deploy](#integration-with-zip-deployment), moet voor deze optie ook een bestand met de naam worden opgegeven `packagename.txt` . Dit bestand bevat alleen de naam van het pakket bestand in de map, zonder spaties. |
|**`<URL>`**  | Locatie van een specifiek pakket bestand dat u wilt uitvoeren. Bij het gebruik van Blob Storage moet u een persoonlijke container met een [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) gebruiken om de runtime van de functies in te scha kelen voor toegang tot het pakket. U kunt de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om pakket bestanden te uploaden naar uw Blob Storage-account. Wanneer u een URL opgeeft, moet u ook [Triggers synchroniseren](functions-deployment-technologies.md#trigger-syncing) nadat u een bijgewerkt pakket hebt gepubliceerd. |

> [!CAUTION]
> Bij het uitvoeren van een functie-app in Windows levert de optie externe URL slechterere prestaties bij koude start. Wanneer u de functie-app in Windows implementeert, moet u instellen `WEBSITE_RUN_FROM_PACKAGE` op `1` en publiceren met zip-implementatie.

Hieronder ziet u een functie-app die is geconfigureerd om te worden uitgevoerd vanuit een zip-bestand dat wordt gehost in Azure Blob Storage:

![App-instelling WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Momenteel worden alleen zip-pakket bestanden ondersteund.

## <a name="integration-with-zip-deployment"></a>Integratie met zip-implementatie

Een [zip-implementatie][Zip deployment for Azure Functions] is een functie van Azure app service waarmee u uw functie-app-project kunt implementeren in de `wwwroot` Directory. Het project wordt verpakt als een zip-implementatie bestand. Dezelfde Api's kunnen worden gebruikt voor het implementeren van uw pakket naar de `d:\home\data\SitePackages` map. Met de `WEBSITE_RUN_FROM_PACKAGE` app-instellings waarde van `1` worden de zip-implementatie-api's uw pakket naar de `d:\home\data\SitePackages` map gekopieerd in plaats van de bestanden uit te pakken naar `d:\home\site\wwwroot` . Het bestand wordt ook gemaakt `packagename.txt` . Na het opnieuw opstarten wordt het pakket gekoppeld `wwwroot` als een alleen-lezen bestands systeem. Zie voor meer informatie over de implementatie van zip-implementatie [voor Azure functions](deployment-zip-push.md).

> [!NOTE]
> Wanneer een implementatie plaatsvindt, wordt de functie-app opnieuw gestart. Voordat de computer opnieuw wordt opgestart, is het mogelijk om alle bestaande functies uit te voeren of te time-out. Zie [implementatie gedrag](functions-deployment-technologies.md#deployment-behaviors)voor meer informatie.

## <a name="adding-the-website_run_from_package-setting"></a>De instelling WEBSITE_RUN_FROM_PACKAGE toevoegen

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Problemen oplossen

- Uitvoeren vanuit pakket maakt `wwwroot` alleen-lezen, dus er wordt een fout bericht weer gegeven wanneer u bestanden naar deze map schrijft.
- Tar-en gzip-indelingen worden niet ondersteund.
- Het ZIP-bestand kan Maxi maal 1 GB groot zijn.
- Deze functie is niet samen met de lokale cache.
- Gebruik de lokale zip-optie (= 1) voor verbeterde prestaties van koud start `WEBSITE_RUN_FROM_PACKAGE` .
- Uitvoeren vanuit pakket is niet compatibel met de optie voor het aanpassen van de implementatie ( `SCM_DO_BUILD_DURING_DEPLOYMENT=true` ) de build-stap wordt genegeerd tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
