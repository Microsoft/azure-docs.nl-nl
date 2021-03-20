---
title: 'Zelfstudie: Azure Policy implementeren als code met GitHub'
description: In deze zelfstudie implementeert u een Azure Policy als Codewerkstroom met export, GitHub-acties en GitHub-werkstromen
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92325849"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Zelfstudie: Azure Policy implementeren als code met GitHub

Een Azure Policy als Codewerkstroom maakt het mogelijk om uw beleidsdefinities en -toewijzingen te beheren als code, de levenscyclus van het bijwerken van deze definities te bepalen en de validatie van de nalevingsresultaten te automatiseren. In deze zelfstudie leert u Azure Policy-functies gebruiken met GitHub om een levenscyclusproces te bouwen. Hierbij gaat het onder andere om de volgende taken:

> [!div class="checklist"]
> - Beleidsdefinities en -toewijzingen exporteren naar GitHub
> - Beleidsobjecten die zijn bijgewerkt in GitHub naar Azure pushen
> - Een nalevingsscan activeren vanuit de GitHub-actie

Als u een beleid wilt toewijzen voor het identificeren van de huidige nalevingsstatus van uw bestaande bronnen, dan geven de quickstart-artikelen over dit ontwerp uitleg.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
- Bekijk [Een Azure Policy ontwerpen als Codewerkstroom](../concepts/policy-as-code.md) om een goed beeld te krijgen van de ontwerppatronen die in deze zelfstudie worden gebruikt.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Azure Policy-objecten exporteren uit de Azure Portal

Voer de volgende stappen uit om een beleidsdefinitie te exporteren uit Azure Portal:

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

1. Selecteer **Definities** aan de linkerkant van de Azure Policy-pagina.

1. Gebruik de knop **Definities exporteren** of selecteer het beletselteken in de rij van een beleidsdefinitie en selecteer vervolgens **Definitie exporteren**.

1. Selecteer de knop **Aanmelden bij GitHub**. Als u nog niet hebt geverifieerd met GitHub om Azure Policy te autoriseren voor het exporteren van de resource, bekijkt u de toegang tot de [GitHub-actie](https://github.com/features/actions) in het nieuwe venster dat wordt geopend en selecteert u **AzureGitHubActions autoriseren** om door te gaan met het exportproces. Zodra dit is voltooid, wordt het nieuwe venster automatisch gesloten.

1. Stel op het tabblad **Basisprincipes** de volgende opties in en selecteer vervolgens het tabblad **Beleid** of de knop **Volgende: Beleid** onderaan de pagina.

   - **Filter voor opslagplaats**: Ingesteld op _Mijn opslagplaatsen_ om alleen opslagplaatsen weer te geven waarvan u de eigenaar bent, of _Alle opslagplaatsen_ om alles weer te geven waaraan u de GitHub-actie toegang hebt verleend.
   - **Opslagplaats**: Stel in op de opslagplaats waarnaar u de Azure Policy-resources wilt exporteren.
   - **Vertakking**: Stel de vertakking in de opslagplaats in. Het gebruik van een andere vertakking dan de standaard is een goede manier om uw updates te valideren voordat u de broncode verder samenvoegt.
   - **Map**: De _map op het hoofdniveau_ om de Azure Policy-resources naar te exporteren. Submappen onder deze map worden gemaakt op basis van de resources die worden geëxporteerd.

1. Stel op het tabblad **Beleid** het bereik in op zoeken door het beletselteken te selecteren en een combinatie van beheergroepen, abonnementen of resourcegroepen te kiezen.
   
1. Gebruik de knop **Beleidsdefinitie(s) toevoegen** om het bereik te doorzoeken op zoek naar welke objecten moeten worden geëxporteerd. Selecteer in het venster aan de zijkant dat wordt geopend elk object dat u wilt exporteren. Filter de selectie door middel van het zoekvak of het type. Wanneer u alle objecten hebt geselecteerd die u wilt exporteren, gebruikt u de knop **Toevoegen** onderaan de pagina.

1. Selecteer voor elk geselecteerd object de gewenste exportopties, zoals _Alleen definitie_ of _Definitie en toewijzing(en)_ voor een beleidsdefinitie. Selecteer vervolgens het tabblad **Beoordelen + exporteren** of de knop **Volgende: Beoordelen + exporteren** onderaan de pagina.

   > [!NOTE]
   > Als de optie _Definitie en toewijzing(en)_ wordt gekozen, worden alleen beleidstoewijzingen geëxporteerd binnen het bereik dat is ingesteld door het filter wanneer de beleidsdefinitie wordt toegevoegd.

1. Controleer op het tabblad **Beoordelen + exporteren** of de informatie overeenkomt en gebruik vervolgens de knop **Exporteren** aan de onderkant van de pagina.

1. Controleer uw GitHub-opslagplaats, vertakking en _map op het hoofdniveau_ om te zien of de geselecteerde resources nu zijn geëxporteerd naar uw broncodebeheer.

De Azure Policy-resources worden geëxporteerd naar de volgende structuur in de geselecteerde GitHub-opslagplaats en _map op het hoofdniveau_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Beleidsobjecten die zijn bijgewerkt in GitHub naar Azure pushen

1. Wanneer beleidsobjecten worden geëxporteerd, wordt er ook een [GitHub-werkstroom](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)bestand met de naam `.github/workflows/manage-azure-policy-<randomLetters>.yml` gemaakt zodat u aan de slag kunt.

   > [!NOTE]
   > Het GitHub-werkstroombestand wordt gemaakt telkens wanneer exporteren wordt gebruikt. Elk exemplaar van het bestand is specifiek voor de opties tijdens die exportactie.

1. Dit werkstroombestand maakt gebruik van de actie [Azure Policy beheren](https://github.com/marketplace/actions/manage-azure-policy) om wijzigingen aan de geëxporteerde beleidsobjecten in de GitHub-opslagplaats terug te pushen naar Azure Policy. De actie beschouwt en synchroniseert standaard alleen de bestanden die verschillen van die van de bestaande bestanden in Azure. U kunt ook de parameter `assignments` gebruiken in de actie om alleen wijzigingen te synchroniseren die zijn uitgevoerd op specifieke toewijzingsbestanden. Deze parameter kan worden gebruikt om beleidstoewijzingen alleen voor een specifieke omgeving toe te passen. Zie voor meer informatie de [Leesmij van Azure Policy-opslagplaats beheren](https://github.com/Azure/manage-azure-policy).

1. Standaard moet de werkstroom handmatig worden geactiveerd. Als u dit wilt doen, gebruikt u de **Acties** in GitHub, selecteert u de werkstroom `manage-azure-policy-<randomLetters>`, selecteert u **Werkstroom uitvoeren** en vervolgens nogmaals **Werkstroom uitvoeren**.

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Schermopname van het tabblad Actie, werkstroom en Werkstroomknoppen uitvoeren in de GitHub-web-interface.":::

   > [!NOTE]
   > Het werkstroombestand moet in de standaard vertakking staan om te worden gedetecteerd en moet handmatig worden uitgevoerd.

1. De werkstroom synchroniseert de wijzigingen die zijn aangebracht aan beleidsobjecten met Azure en geeft u de status in de logboeken.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Schermopname van de werkstroom in actie en aan logboek toegevoegde gegevens in de logboeken.":::

1. De werkstroom voegt ook details toe aan Azure Policy-objecten `properties.metadata` die u kunt bijhouden.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Schermopname van de Azure Policy-definitie in Azure Portal bijgewerkt met metagegevens die specifiek zijn voor de GitHub-actie.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Nalevingsscans activeren met behulp van GitHub-actie

Met de [actie Azure Policy-nalevingsscan](https://github.com/marketplace/actions/azure-policy-compliance-scan) kunt u een nalevingsevaluatiescan op aanvraag activeren van uw [GitHub-werkstroom](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) op basis van een of meer resources, resourcegroepen of abonnementen, en het pad van de werkstroom aanpassen aan de hand van de nalevingsstatus van deze resources. U kunt de werkstroom ook zo configureren dat deze op een gepland tijdstip wordt uitgevoerd om de meest recente nalevingsstatus op een geschikt tijdstip te verkrijgen. Deze GitHub-actie kan eventueel ook een rapport genereren over de nalevingsstatus van gescande resources voor verdere analyse of voor archivering.

In het volgende voorbeeld wordt een nalevingsscan voor een abonnement uitgevoerd. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Beoordelen

In deze zelfstudie hebt u de volgende taken uitgevoerd:

> [!div class="checklist"]
> - U hebt beleidsdefinities en -toewijzingen naar GitHub geëxporteerd
> - U hebt beleidsobjecten die zijn bijgewerkt in GitHub naar Azure gepusht
> - U hebt een nalevingsscan geactiveerd vanuit de GitHub-actie

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel voor meer informatie over de structuur van beleidsdefinities:

> [!div class="nextstepaction"]
> [Structuur van Azure Policy-definities](../concepts/definition-structure.md)