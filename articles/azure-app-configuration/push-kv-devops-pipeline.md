---
title: Instellingen pushen naar app-configuratie met Azure-pijp lijnen
description: Meer informatie over het gebruik van Azure-pijp lijnen om sleutel waarden naar een app-configuratie archief te pushen
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: lcozzens
ms.openlocfilehash: 936be917d5b494c9d71386440c711ef6c29ffb45
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183495"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Instellingen pushen naar app-configuratie met Azure-pijp lijnen

Met de [Push taak configuratie-Azure-app](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) worden sleutel waarden van een configuratie bestand naar uw app-configuratie archief gepusht. Met deze taak wordt de volledige cirkel functionaliteit binnen de pijp lijn ingeschakeld, omdat u nu instellingen van de app-configuratie opslag en Push-instellingen kunt ophalen naar de app-configuratie opslag.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Bron van de app-configuratie: Maak een gratis versie in de [Azure Portal](https://portal.azure.com).
- Azure DevOps-project: [Maak er een gratis](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure-app-configuratie push taak: down load gratis vanuit [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push).

## <a name="create-a-service-connection"></a>Een service verbinding maken

Met een service verbinding kunt u vanuit uw Azure DevOps-project toegang krijgen tot resources in uw Azure-abonnement.

1. Ga in azure DevOps naar het project met de doel pijplijn en open de **project instellingen** linksonder.
1. Onder **pijp lijnen** selecteert u **service verbindingen** en selecteert u in de rechter bovenhoek **nieuwe service verbinding** .
1. Selecteer **Azure Resource Manager**.
1. Selecteer **Service-Principal (automatisch)**.
1. Vul uw abonnement en resource in. Geef een naam op voor uw service verbinding.

Nu uw service verbinding is gemaakt, zoekt u de naam van de service-principal die hieraan is toegewezen. In de volgende stap voegt u een nieuwe roltoewijzing aan deze service-principal toe.

1. Ga naar de **project instellingen**  >  **service verbindingen**.
1. Selecteer de service verbinding die u hebt gemaakt in de vorige sectie.
1. Selecteer **Service-Principal beheren**.
1. Let op de **weer gegeven weergave naam** .

## <a name="add-role-assignment"></a>Roltoewijzing toevoegen

Wijs de juiste toewijzingen van de functie app-configuratie toe aan de referenties die in de taak worden gebruikt, zodat de taak toegang heeft tot de app-configuratie opslag.

1. Navigeer naar de configuratie Store van uw doel-app. 
1. Selecteer aan de linkerkant **toegangs beheer (IAM)**.
1. Selecteer aan de bovenkant **+** **roltoewijzing** toevoegen en kiezen.
1. Selecteer onder **rol** de **eigenaar van de app-configuratie gegevens**. Met deze rol kan de taak lezen uit en schrijven naar de app-configuratie opslag. 
1. Selecteer de service-principal die is gekoppeld aan de service verbinding die u hebt gemaakt in de vorige sectie.
  
## <a name="use-in-builds"></a>Gebruiken in builds

Deze sectie bevat informatie over het gebruik van de Azure-app configuratie push taak in een Azure DevOps build-pijp lijn.

1. Ga naar de pagina voor het bouwen van de pijp lijn door te klikken op **pijp** lijnen  >  **pijp lijnen**. Documentatie voor Build-pijp lijnen vindt u [hier](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2&view=azure-devops).
      - Als u een nieuwe build-pijp lijn maakt, selecteert u de optie **assistent weer geven** aan de rechter kant van de pijp lijn en zoekt u naar de **Azure-app push** taak voor de configuratie.
      - Als u een bestaande build-pijp lijn gebruikt, navigeert u naar het tabblad **taken** bij het bewerken van de pijp lijn en zoekt u naar de push taak voor de **Azure-app-configuratie** .
2. Configureer de vereiste para meters voor de taak om de sleutel waarden van het configuratie bestand naar de app-configuratie opslag te pushen. De para meter **pad naar configuratie bestand** begint bij de hoofdmap van de bestands opslagplaats.
3. Een build opslaan en in de wachtrij plaatsen. In het build-logboek worden de fouten weer gegeven die zijn opgetreden tijdens de uitvoering van de taak.

## <a name="use-in-releases"></a>In releases gebruiken

Deze sectie bevat informatie over het gebruik van de Azure-app configuratie push taak in een Azure DevOps-release pijplijn.

1. Navigeer naar de pagina release pijplijn door **pijp lijnen** te selecteren  >  **Releases**. Documentatie voor release pijplijnen vindt u [hier](/azure/devops/pipelines/release?view=azure-devops).
1. Kies een bestaande release pijplijn. Als u er nog geen hebt, selecteert u **+ Nieuw** om een nieuw item te maken.
1. Selecteer de knop **bewerken** in de rechter bovenhoek om de release pijplijn te bewerken.
1. Kies het **stadium** om de taak toe te voegen. Meer informatie over de stadia vindt u [hier](/azure/devops/pipelines/release/environments?view=azure-devops).
1. Selecteer **+** voor die taak en voeg vervolgens de taak **configuratie push Azure-app** op het tabblad **implementeren** toe.
1. Configureer de benodigde para meters in de taak om uw sleutel waarden van het configuratie bestand naar uw app-configuratie archief te pushen. Uitleg van de para meters zijn beschikbaar in de sectie **para meters** hieronder en in knop info naast elke para meter.
1. Een release opslaan en in de wachtrij plaatsen. In het release logboek worden eventuele fouten weer gegeven die zijn opgetreden tijdens de uitvoering van de taak.

## <a name="parameters"></a>Parameters

De volgende para meters worden gebruikt door de taak app-configuratie push:

- **Azure-abonnement**: een vervolg keuzelijst met uw beschik bare Azure-service verbindingen. Als u de lijst met beschik bare Azure-service verbindingen wilt bijwerken en vernieuwen, klikt u op de knop **Azure-abonnement vernieuwen** rechts van het tekstvak.
- **App-configuratie naam**: een vervolg keuzelijst waarmee uw beschik bare configuratie archieven worden geladen onder het geselecteerde abonnement. Als u de lijst met beschik bare configuratie archieven wilt bijwerken en vernieuwen, klikt u op de knop **app-configuratie naam vernieuwen** rechts van het tekstvak.
- **Pad naar configuratie bestand**: het pad naar het configuratie bestand. U kunt door uw build-artefact bladeren om een configuratie bestand te selecteren. ( `...` knop rechts van het tekstvak).
- **Scheidings teken**: het scheidings teken dat wordt gebruikt voor het samen voegen van JSON-en yml-bestanden.
- **Diepte**: de diepte waarmee de JSON-en yml-bestanden worden afgevlakt.
- **Voor voegsel**: een teken reeks die wordt toegevoegd aan het begin van elke sleutel die wordt gepusht naar de app-configuratie opslag.
- **Label**: een teken reeks die wordt toegevoegd aan elke sleutel waarde als het label in de app-configuratie opslag.
- **Inhouds type**: een teken reeks die wordt toegevoegd aan elke sleutel waarde als het inhouds type in de app-configuratie opslag.
- **Tags**: een JSON-object in de indeling van `{"tag1":"val1", "tag2":"val2"}` , waarmee Tags worden gedefinieerd die worden toegevoegd aan elke sleutel waarde die wordt gepusht naar de app-configuratie opslag.
- **Verwijder alle andere Key-Values in de Store met het opgegeven voor voegsel en label: de** standaard waarde is **uitgeschakeld**.
  - **Ingeschakeld**: Hiermee verwijdert u alle sleutel waarden in de app-configuratie opslag die overeenkomen met het opgegeven voor voegsel en label voordat u nieuwe sleutel waarden uit het configuratie bestand pusht.
  - **Uitgeschakeld**: Hiermee worden alle sleutel waarden van het configuratie bestand naar de app-configuratie opslag gepusht en blijven alle andere in de app-configuratie Store intact.

Voer de pijp lijn uit na het invullen van de vereiste para meters. Alle sleutel waarden in het opgegeven configuratie bestand worden geüpload naar de app-configuratie.

## <a name="troubleshooting"></a>Problemen oplossen

Als er een onverwachte fout optreedt, kunnen de logboeken voor fout opsporing worden ingeschakeld door de pijplijn variabele `system.debug` in te stellen op `true` .

## <a name="faq"></a>Veelgestelde vragen

**Hoe kan ik meerdere configuratie bestanden uploaden?**

Maak meerdere exemplaren van de push taak voor de configuratie van Azure-app in dezelfde pijp lijn om meerdere configuratie bestanden naar de app-configuratie opslag te pushen.

**Waarom ontvang ik een 409-fout bij het pushen van sleutel waarden naar mijn configuratie archief?**

Er treedt een 409-conflict op als de taak een sleutel waarde probeert te verwijderen of te overschrijven die is vergrendeld in de app-configuratie opslag.