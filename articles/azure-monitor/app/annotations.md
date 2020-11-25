---
title: Release aantekeningen voor Application Insights | Microsoft Docs
description: Voeg implementatie-of build markeringen toe aan uw grafieken met metrische gegevens Verkenner in Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 58f6603687838713fafbf4cd5cc3f100e22b7401
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993717"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Aantekeningen op metrische grafieken in Application Insights

Aantekeningen laten zien waar u een nieuwe build of andere belang rijke gebeurtenissen hebt geïmplementeerd. Aantekeningen maken het eenvoudig om te zien of uw wijzigingen van invloed zijn op de prestaties van uw toepassing. Ze kunnen automatisch worden gemaakt door het build-systeem van [Azure pipelines](/azure/devops/pipelines/tasks/) . U kunt ook aantekeningen maken om elke gewenste gebeurtenis te markeren door ze te maken vanuit Power shell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Release annotaties with Azure pipelines build

Release aantekeningen zijn een functie van de Azure pipelines in de cloud van Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>De aantekeningen uitbreiding installeren (één keer)

Als u release aantekeningen wilt maken, moet u een van de vele Azure DevOps-uitbrei dingen installeren die beschikbaar zijn in de Visual Studio Marketplace.

1. Meld u aan bij uw [Azure DevOps](https://azure.microsoft.com/services/devops/) -project.
   
1. Selecteer op de pagina [uitbrei ding aantekeningen](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) voor de release van Visual Studio Marketplace de Azure DevOps-organisatie en selecteer vervolgens **installeren** om de extensie toe te voegen aan uw Azure DevOps-organisatie.
   
   ![Selecteer een Azure DevOps-organisatie en selecteer vervolgens installeren.](./media/annotations/1-install.png)
   
U hoeft de uitbrei ding slechts eenmaal te installeren voor uw Azure DevOps-organisatie. U kunt nu release aantekeningen configureren voor elk project in uw organisatie.

### <a name="configure-release-annotations"></a>Release aantekeningen configureren

Maak een afzonderlijke API-sleutel voor elk van de release sjablonen van Azure pipelines.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en open de Application Insights resource waarmee uw toepassing wordt bewaakt. Of als u er geen hebt, [maakt u een nieuwe Application Insights resource](./app-insights-overview.md).
   
1. Open het tabblad **API-toegang** en kopieer de **Application Insights-id**.
   
   ![Kopieer de toepassings-ID onder API-toegang.](./media/annotations/2-app-id.png)

1. Open of maak in een afzonderlijk browser venster de release sjabloon waarmee de implementaties van uw Azure-pijp lijnen worden beheerd.
   
1. Selecteer **taak toevoegen** en selecteer vervolgens de taak **aantekening van de Application Insights release** in het menu.
   
   ![Selecteer taak toevoegen en selecteer aantekening van Application Insights versie.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > De taak release annotatie ondersteunt momenteel alleen op Windows gebaseerde agents. het kan niet worden uitgevoerd op Linux-, macOS-of andere typen agents.
   
1. Plak onder **toepassings-id** de Application Insights-id die u hebt gekopieerd via het tabblad **API-toegang** .
   
   ![De Application Insights-ID plakken](./media/annotations/4-paste-app-id.png)
   
1. Klik in het venster Application Insights **API-toegang** op **API-sleutel maken**. 
   
   ![Op het tabblad API-toegang selecteert u API-sleutel maken.](./media/annotations/5-create-api-key.png)
   
1. Typ een beschrijving in het venster **API-sleutel maken** , selecteer **aantekeningen schrijven** en selecteer vervolgens **sleutel genereren**. Kopieer de nieuwe sleutel.
   
   ![Typ een beschrijving in het venster API-sleutel maken, selecteer aantekeningen schrijven en selecteer vervolgens sleutel genereren.](./media/annotations/6-create-api-key.png)
   
1. Selecteer in het venster release-sjabloon, op het tabblad **variabelen** , de optie **toevoegen** om een definitie van een variabele te maken voor de nieuwe API-sleutel.

1. Plak onder **naam**, Enter `ApiKey` en onder **waarde** de API-sleutel die u hebt gekopieerd op het tabblad **API-toegang** .
   
   ![Ga naar het tabblad Azure DevOps-variabelen, selecteer toevoegen, noem de variabele ApiKey en plak de API-sleutel onder waarde.](./media/annotations/7-paste-api-key.png)
   
1. Selecteer **Opslaan** in het venster hoofd release sjabloon om de sjabloon op te slaan.


   > [!NOTE]
   > Limieten voor API-sleutels worden beschreven in de documentatie voor de [rest API frequentie limieten](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Aantekeningen weer geven


   > [!NOTE]
   > Release aantekeningen zijn momenteel niet beschikbaar in het deel venster metrieken van Application Insights

Wanneer u nu de release sjabloon gebruikt om een nieuwe release te implementeren, wordt er een aantekening naar Application Insights verzonden. De aantekeningen kunnen worden weer gegeven op de volgende locaties:

Het deel venster gebruik waarin u ook hand matig release aantekeningen kunt maken:

![Scherm afbeelding van een staaf diagram met het aantal bezochte bezoekers gedurende een periode van de gebruiker. Release-aantekeningen worden weer gegeven als groen vinkjes boven de grafiek met het tijdstip waarop een release heeft plaatsgevonden](./media/annotations/usage-pane.png)

In elke werkmap query op basis van een logboek waarbij de visualisatie tijd op de x-as weergeeft.

![Scherm afbeelding van het deel venster werkmappen met een query op basis van een tijd reeks-logboeken met annotaties](./media/annotations/workbooks-annotations.png)

Ga naar **Geavanceerde instellingen** en selecteer **aantekeningen weer geven** om aantekeningen in uw werkmap in te scha kelen.

![Scherm opname van het menu Geavanceerde instellingen met de woorden Toon aantekeningen gemarkeerd met een vinkje naast de instelling om deze in te scha kelen.](./media/annotations/workbook-show-annotations.png)

Selecteer een markering voor aantekeningen om details over de release te openen, met inbegrip van de aanvrager, de bron beheer vertakking, de release pijplijn en de omgeving.

## <a name="create-custom-annotations-from-powershell"></a>Aangepaste aantekeningen maken vanuit Power shell
U kunt het Power shell-script [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) van github gebruiken om aantekeningen te maken op basis van elk gewenst proces, zonder Azure DevOps te gebruiken. 

1. Maak een lokale kopie van [CreateReleaseAnnotation.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Volg de stappen in de voor gaande procedure om uw Application Insights-ID op te halen en een API-sleutel te maken op het tabblad Application Insights **API-toegang** .
   
1. Roep het Power shell-script aan met de volgende code en vervang de tijdelijke aanduidingen met punt haken door uw waarden. De `-releaseProperties` zijn optioneel. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

U kunt het script wijzigen, bijvoorbeeld om aantekeningen voor het verleden te maken.

## <a name="next-steps"></a>Volgende stappen

* [Werkitems maken](./diagnostic-search.md#create-work-item)
* [Automatisering met PowerShell](./powershell.md)

