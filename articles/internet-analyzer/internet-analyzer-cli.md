---
title: Een Internet Analyzer-test maken met behulp van CLI | Microsoft Docs
description: In dit artikel leert u hoe u uw eerste Internet Analyzer-test kunt maken met behulp van Azure CLI.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 4e200440bd37d3593d5fc096b3dd79eff25dc1d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877223"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Een Internet Analyzer-test maken met behulp van CLI (Preview)

Er zijn twee manieren om een Internet Analyzer-resource te maken: met behulp van de [Azure Portal](internet-analyzer-create-test-portal.md) of met behulp van CLI. Dit gedeelte helpt u bij het maken van een nieuwe Azure Internet Analyzer-resource met onze CLI-ervaring. 


> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

De openbare preview is beschikbaar voor gebruik wereldwijd; gegevensopslag is tijdens de preview echter beperkt tot *VS - west 2*.

## <a name="object-model"></a>Objectmodel
De Internet Analyzer CLI maakt de volgende typen resources beschikbaar:
* **Tests**: een test vergelijkt de prestaties van de eindgebruiker van twee interneteindpunten (A en B) in de loop van de tijd.
* **Profielen**: tests worden gemaakt onder een Internet Analyzer-profiel. Met profielen kunnen gerelateerde testen worden gegroepeerd; één profiel kan een of meer tests bevatten.
* **Vooraf geconfigureerde eindpunten**: we hebben eindpunten ingesteld met verschillende configuraties (regio's, versnellingstechnologieën, enzovoort). U kunt alle vooraf geconfigureerde eindpunten in uw tests gebruiken.
* **Scorecards**: een scorecard biedt snelle en zinvolle samenvattingen van meetresultaten. Raadpleeg [Uw scorecard interpreteren](internet-analyzer-scorecard.md).
* **Tijdreeks**: een tijdreeks laat zien hoe metrische gegevens in de loop van de tijd veranderen.

## <a name="profile-and-test-creation"></a>Maken van profiel en test
1. Krijg toegang tot Internet Analyzer-preview door de instructies in **Hoe kan ik deelnemen aan de preview?** van de [Veelgestelde vragen over Azure Internet Analyzer](internet-analyzer-faq.md) te volgen.
2. [Installeer de Azure CLI](/cli/azure/install-azure-cli).
3. Voer de opdracht `login` uit om een CLI-sessie te starten:
    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.
    Als dat niet het geval is, opent u een browserpagina op https://aka.ms/devicelogin en voert u de autorisatiecode in die wordt weergegeven in uw terminal.

4. Meldt u zich in de browser aan met uw accountreferenties.

5. Selecteer uw abonnements-id waaraan toegang tot de openbare preview van Internet Analyzer is verleend.

    Nadat u zich hebt aangemeld, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnementsgegevens met `isDefault: true` zijn het abonnement dat momenteel is geactiveerd na aanmelden. Als u een ander abonnement wilt selecteren, gebruikt u de opdracht [az account set](/cli/azure/account#az_account_set) met de abonnements-id waarnaar moet worden overgeschakeld. Zie [Meerdere Azure-abonnementen gebruiken](/cli/azure/manage-azure-subscriptions-azure-cli) voor meer informatie over het selecteren van abonnementen.

    Er zijn ook manieren om u niet-interactief aan te melden. Deze manieren worden uitvoerig behandeld in [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli).

6. **[Optioneel]** Een nieuwe Azure-resourcegroep maken:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installeer de Azure CLI Internet Analyzer-extensie:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Een nieuw Internet Analyzer-profiel maken:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Vermeld alle vooraf geconfigureerde eindpunten die beschikbaar zijn voor het zojuist gemaakte profiel:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Maak een nieuwe test onder het zojuist gemaakte InternetAnalyzer-profiel:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    In de bovenstaande opdracht wordt ervan uitgegaan dat zowel `www.contoso.com` als `www.microsoft.com` als host fungeren voor de afbeelding van één pixel ([trans.gif](https://fpc.msedge.net/apc/trans.gif)) onder aangepaste paden. Als een objectpad niet expliciet wordt opgegeven, gebruikt Internet Analyzer standaard `/apc/trans.gif` als het objectpad. Dit is de locatie waar de vooraf geconfigureerde eindpunten de afbeelding met één pixel hosten. Houd er ook rekening mee dat het schema (https/http) niet hoeft te worden opgegeven. Internet Analyzer ondersteunt alleen HTTPS-eindpunten, dus HTTPS wordt aangenomen.

11. De nieuwe test moet worden weergegeven onder het Internet Analyzer-profiel:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Voorbeelduitvoer:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Als u metingen wilt genereren, moet het JavaScript-bestand waarnaar wordt verwezen door de **scriptFileUri** van de test worden ingesloten in uw webtoepassing. Specifieke instructies vindt u op de pagina [Internet Analyzer-client insluiten](internet-analyzer-embed-client.md).

13. U kunt de voortgang van de test controleren door de waarde van de 'status' bij te houden:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. U kunt de verzamelde resultaten van de test controleren door er tijdseries of scorecards voor te genereren:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Volgende stappen

* Blader door de [Internet Analyzer CLI-referentie](/cli/azure/internet-analyzer) voor een volledige lijst met ondersteunde opdrachten en gebruiksvoorbeelden.
* Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md).
* Meer informatie over het insluiten van de [Internet Analyzer-client](internet-analyzer-embed-client.md) en het maken van een [aangepast eindpunt](internet-analyzer-custom-endpoint.md).