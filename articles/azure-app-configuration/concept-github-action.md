---
title: Uw GitHub-opslag plaats synchroniseren met de app-configuratie
description: Gebruik GitHub-acties om uw app-configuratie-exemplaar automatisch bij te werken wanneer u uw GitHub-opslag plaats bijwerkt.
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 588efd692119c9e2831e16c1ce26c2759898a1e5
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607361"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Uw GitHub-opslag plaats synchroniseren met de app-configuratie

Teams die hun bestaande broncode beheer procedures willen blijven gebruiken, kunnen GitHub-acties gebruiken om de GitHub-opslag plaats automatisch te synchroniseren met de app-configuratie opslag. Hierdoor kunt u wijzigingen aanbrengen in de configuratie bestanden zoals u dat normaal zou doen, terwijl u de voor delen van de app-configuratie krijgt, zoals: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Gecentraliseerde configuratie buiten uw code <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Het bijwerken van de configuratie zonder uw volledige app opnieuw te implementeren <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integratie met Services als Azure App Service en functions. 

Een GitHub actions- [werk stroom](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) definieert een geautomatiseerd proces in een github-opslag plaats. Met de actie voor het *synchroniseren van Azure-app-configuratie* worden updates voor een app-configuratie-exemplaar geactiveerd wanneer er wijzigingen in de bron opslagplaats worden aangebracht. Er wordt een YAML-bestand (. yml) gebruikt dat is gevonden in het `/.github/workflows/` pad van uw opslag plaats om de stappen en para meters te definiëren. U kunt configuratie-updates activeren tijdens het pushen, reviseren of vertakking van app-configuratie bestanden, net als bij app-code.

De GitHub- [documentatie](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions) biedt uitgebreide weer gave van github-werk stromen en-acties. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-acties inschakelen in uw opslag plaats
Als u deze GitHub actie wilt gaan gebruiken, gaat u naar uw opslag plaats en selecteert u het tabblad **acties** . Selecteer **nieuwe werk stroom** en **Stel zelf een werk stroom** in. Ga ten slotte naar de Marketplace voor ' Azure-app-configuratie synchronisatie '.
> [!div class="mx-imgBorder"]
> ![Selecteer het tabblad actie](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecteer de synchronisatie actie voor de configuratie van de app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Configuratie bestanden na een push synchroniseren
Met deze actie worden Azure-app configuratie bestanden gesynchroniseerd wanneer een wijziging naar wordt gepusht `appsettings.json` . Wanneer een ontwikkelaar een wijziging doorstuurt `appsettings.json` , wordt het app-configuratie-exemplaar met de nieuwe waarden bijgewerkt met de synchronisatie actie van de app-configuratie.

In de eerste sectie van deze werk stroom wordt aangegeven dat de actie wordt geactiveerd *op* een *Push* die `appsettings.json` naar de *hoofd* vertakking bevat. De tweede sectie bevat een lijst met de taken die worden uitgevoerd zodra de actie wordt geactiveerd. Met deze actie worden de relevante bestanden gecontroleerd en wordt het app-configuratie-exemplaar bijgewerkt met behulp van de connection string opgeslagen als een geheim in de opslag plaats.  Zie [het artikel van github](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets) over het maken en gebruiken van versleutelde geheimen voor meer informatie over het gebruik van geheimen in github.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Strikte synchronisatie gebruiken
Standaard wordt met de actie GitHub niet de strikte modus ingeschakeld, wat betekent dat de synchronisatie alleen sleutel waarden van het configuratie bestand toevoegt aan het app-configuratie-exemplaar (er worden geen sleutel-waardeparen verwijderd). Als u strikte modus inschakelt, worden sleutel-waardeparen die zich niet in het configuratie bestand bevinden, verwijderd uit het app-configuratie-exemplaar, zodat het overeenkomt met het configuratie bestand. Als u synchroniseert vanuit meerdere bronnen of Azure Key Vault met app-configuratie, wilt u verschillende voor voegsels of labels gebruiken met strikte synchronisatie om te voor komen dat configuratie-instellingen van andere bestanden worden gewist (Zie voor beelden hieronder). 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Meerdere bestanden in één actie synchroniseren 

Als uw configuratie zich in meerdere bestanden bevindt, kunt u het onderstaande patroon gebruiken om een synchronisatie te activeren wanneer een bestand wordt gewijzigd. Dit patroon maakt gebruik van de Globs-bibliotheek https://www.npmjs.com/package/glob . Houd er rekening mee dat als de naam van het configuratie bestand een komma bevat, u een back slash kunt gebruiken om de komma te escapen. 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Synchroniseren op voor voegsel of label
Als u voor voegsels of labels opgeeft in uw synchronisatie actie, wordt alleen die specifieke set gesynchroniseerd. Dit is belang rijk voor het gebruik van strikte synchronisatie met meerdere bestanden. Afhankelijk van hoe de configuratie is ingesteld, kan een voor voegsel of een label aan elk bestand worden gekoppeld, waarna elk voor voegsel of label afzonderlijk kan worden gesynchroniseerd, zodat er niets wordt overschreven. Doorgaans worden voor voegsels gebruikt voor verschillende toepassingen of services en labels worden gebruikt voor verschillende omgevingen. 

Synchroniseren op voor voegsel: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Synchroniseren op label: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Een dynamisch label gebruiken bij synchronisatie
Met de volgende actie wordt een dynamisch label voor elke synchronisatie ingevoegd, zodat elke synchronisatie uniek kan worden geïdentificeerd en het mogelijk is dat code wijzigingen worden toegewezen aan configuratie wijzigingen.

In de eerste sectie van deze werk stroom wordt aangegeven dat de actie wordt geactiveerd *op* een *Push* die `appsettings.json` naar de *hoofd* vertakking bevat. In het tweede gedeelte wordt een taak uitgevoerd waarmee een uniek label voor de configuratie-update wordt gemaakt op basis van de commit-hash. De taak werkt vervolgens het app-configuratie-exemplaar bij met de nieuwe waarden en het unieke label voor deze update.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Azure Key Vault gebruiken met actie GitHub
Ontwikkel aars die Azure Key Vault gebruiken met AppConfiguration, moeten twee afzonderlijke bestanden gebruiken, meestal een appsettings.jsop en een secretreferences.jsop. De secretreferences.jsin bevat de URL naar het sleutel kluis geheim.

{"mySecret": "{ \" URI \" : \" https://myKeyVault.vault.azure.net/secrets/mySecret "} "}

De actie GitHub kan vervolgens worden geconfigureerd om een strikte synchronisatie uit te voeren op de appsettings.jsop, gevolgd door een niet-strikte synchronisatie op secretreferences.jsop. In het volgende voor beeld wordt een synchronisatie geactiveerd wanneer een van de bestanden wordt bijgewerkt:

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Maximale diepte gebruiken om de GitHub-actie te beperken
Het standaard gedrag voor geneste JSON-kenmerken is het samen voegen van het hele object.  De JSON hieronder definieert deze sleutel/waarde-paar:

| Sleutel | Waarde |
| --- | --- |
| Object: binnenste: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Als het geneste object de waarde is die naar het configuratie-exemplaar moet worden gepusht, kunt u de *diepte* waarde gebruiken om het afvlakken met de juiste diepte te stoppen. 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Met het bovenstaande voor beeld wordt nu het volgende sleutel/waarde-paar als resultaat gegeven:

| Sleutel | Waarde |
| --- | --- |
| Object: binnenste | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Informatie over actie-invoer
Invoer parameters geven gegevens op die worden gebruikt door de actie tijdens runtime.  De volgende tabel bevat de invoer parameters die worden geaccepteerd door de synchronisatie van de app-configuratie en de verwachte waarden voor elke.  Zie de [documentatie](https://docs.github.com/en/free-pro-team@latest/actions/creating-actions/metadata-syntax-for-github-actions#inputs)van github voor meer informatie over actie-invoer voor github-acties.

> [!Note]
> Invoer-Id's zijn niet hoofdletter gevoelig.


| Invoer naam | Vereist? | Waarde |
|----|----|----|
| configurationFile | Ja | Relatief pad naar het configuratie bestand in de opslag plaats.  Globs-patronen worden ondersteund en kunnen meerdere bestanden bevatten. |
| indeling | Ja | Bestands indeling van het configuratie bestand.  Geldige indelingen zijn: JSON, YAML, eigenschappen. |
| connectionString | Ja | Verbindings reeks voor het app-configuratie-exemplaar. De connection string moet worden opgeslagen als een geheim in de GitHub-opslag plaats en alleen de geheime naam moet in de werk stroom worden gebruikt. |
| scheiding | Ja | Scheidings teken dat wordt gebruikt bij het afvlakken van het configuratie bestand naar sleutel-waardeparen.  Geldige waarden zijn:. , ; : - _ __ / |
| beleids | Nee | Het voor voegsel dat moet worden toegevoegd aan het begin van de sleutels. |
| label | Nee | Label dat wordt gebruikt bij het instellen van sleutel-waardeparen. Als u geen waarde opgeeft, wordt een NULL-label gebruikt. |
| beperking | Nee | Een Booleaanse waarde die bepaalt of de strikte modus is ingeschakeld. De standaardwaarde is false. |
| diepga | Nee | Maximale diepte voor het afvlakken van het configuratie bestand.  Depth moet een positief getal zijn.  De standaard waarde heeft geen maximale diepte. |
| tags | Nee | Hiermee geeft u de tag die is ingesteld op sleutel-waardeparen.  De verwachte indeling is een stringified vorm van een JSON-object van de volgende vorm: {[propertyname: String]: String;} Elke eigenschaps naam-waarde wordt een tag. |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de GitHub-actie voor het configureren van de app-configuratie en hoe deze kan worden gebruikt voor het automatiseren van updates voor uw app-configuratie-exemplaar. Ga door naar het volgende [artikel](./concept-app-configuration-event.md)voor meer informatie over de manier waarop Azure-app configuratie reageert op wijzigingen in sleutel-waardeparen.
