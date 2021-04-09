---
title: Settingsfromo-app-configuratie verzamelen met Azure-pijp lijnen
description: Meer informatie over het gebruik van Azure-pijp lijnen voor het ophalen van sleutel waarden naar een app-configuratie archief
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c01984f6a359c0fd1f5d06d26d97d4a84973f57
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056742"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Instellingen voor app-configuratie ophalen met Azure-pijp lijnen

Met de [Azure-app configuratie](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) taak worden sleutel waarden uit uw app-configuratie archief opgehaald en ingesteld als Azure-pijplijn variabelen, die kunnen worden gebruikt door volgende taken. Deze taak vormt een aanvulling op de [Azure-app configuratie push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) taak waarmee sleutel waarden van een configuratie bestand worden gepusht naar de app-configuratie opslag. Zie [Push-instellingen naar app-configuratie met Azure-pijp lijnen](push-kv-devops-pipeline.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- App-configuratie archief: Maak er gratis een in de [Azure Portal](https://portal.azure.com).
- Azure DevOps-project: [Maak er een gratis](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure-app configuratie taak: down load gratis vanuit [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Een service verbinding maken

Met een [service verbinding](/azure/devops/pipelines/library/service-endpoints) kunt u vanuit uw Azure DevOps-project toegang krijgen tot resources in uw Azure-abonnement.

1. Ga in azure DevOps naar het project met de doel pijplijn en open de **project instellingen** linksonder.
1. Onder **pijp lijnen** selecteert u **service verbindingen**.
1. Als u geen bestaande service verbindingen hebt, klikt u op de knop **service verbinding maken** in het midden van het scherm. Anders klikt u in de rechter bovenhoek van de pagina op **nieuwe service verbinding** .
1. Selecteer **Azure Resource Manager**.
![Scherm afbeelding toont het selecteren van Azure Resource Manager in de vervolg keuzelijst nieuwe service verbinding.](./media/new-service-connection.png)
1. Selecteer in het dialoog venster **verificatie methode** **Service-Principal (automatisch)**.
    > [!NOTE]
    > **Beheerde identiteits** verificatie wordt momenteel niet ondersteund voor de app-configuratie taak.
1. Vul uw abonnement en resource in. Geef een naam op voor uw service verbinding.

Nu uw service verbinding is gemaakt, zoekt u de naam van de service-principal die hieraan is toegewezen. In de volgende stap voegt u een nieuwe roltoewijzing aan deze service-principal toe.

1. Ga naar de **project instellingen**  >  **service verbindingen**.
1. Selecteer de service verbinding die u hebt gemaakt in de vorige sectie.
1. Selecteer **Service-Principal beheren**.
1. Let op de **weer gegeven weergave naam** .

## <a name="add-role-assignment"></a>Roltoewijzing toevoegen

Wijs de juiste app-configuratie functie toe aan de service verbinding die wordt gebruikt binnen de taak, zodat de taak toegang heeft tot de app-configuratie opslag.

1. Navigeer naar de configuratie Store van uw doel-app. Voor een overzicht van het instellen van een app-configuratie archief raadpleegt u [een app-configuratie archief maken](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) in een van de Quick starts voor de configuratie van Azure-app.
1. Selecteer aan de linkerkant **toegangs beheer (IAM)**.
1. Klik aan de rechter kant op de knop roltoewijzingen **toevoegen** .
![Scherm afbeelding toont de knop roltoewijzingen toevoegen. ](./media/add-role-assignment-button.png) ..
1. Onder **rol** selecteert u **app Configuration Data Reader**. Met deze rol kan de taak uit het configuratie archief van de app worden gelezen. 
1. Selecteer de service-principal die is gekoppeld aan de service verbinding die u hebt gemaakt in de vorige sectie.
![Scherm afbeelding toont het dialoog venster functie toewijzing toevoegen.](./media/add-role-assignment-reader.png)

> [!NOTE]
> Om Azure Key Vault verwijzingen in de app-configuratie op te lossen, moet aan de service verbinding ook machtigingen worden verleend om geheimen te lezen in de Azure-sleutel kluizen waarnaar wordt verwezen.
  
## <a name="use-in-builds"></a>Gebruiken in builds

Deze sectie bevat informatie over het gebruik van de Azure-app configuratie taak in een Azure DevOps build-pijp lijn.

1. Ga naar de pagina voor het bouwen van de pijp lijn door te klikken op **pijp** lijnen  >  **pijp lijnen**. Zie  [uw eerste pijp lijn maken](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)voor informatie over het bouwen van pijp lijnen.
      - Als u een nieuwe build-pijp lijn maakt, selecteert u in de laatste stap van het proces op het tabblad **controle** de optie **assistent weer geven** aan de rechter kant van de pijp lijn.
      ![Scherm afbeelding toont de knop assistent weer geven voor een nieuwe pijp lijn.](./media/new-pipeline-show-assistant.png)
      - Als u een bestaande build-pijp lijn gebruikt, klikt u op de knop **bewerken** in de rechter bovenhoek.
      ![Scherm afbeelding toont de knop bewerken voor een bestaande pijp lijn.](./media/existing-pipeline-show-assistant.png)
1. Zoek naar de **Azure-app configuratie** taak.
![Scherm afbeelding toont het dialoog venster taak toevoegen met Azure-app configuratie in het zoekvak.](./media/add-azure-app-configuration-task.png)
1. Configureer de vereiste para meters voor de taak om de sleutel waarden uit het app-configuratie archief op te halen. Beschrijvingen van de para meters zijn beschikbaar in de sectie **para meters** hieronder en in knop info naast elke para meter.
      - Stel de para meter van het **Azure-abonnement** in op de naam van de service verbinding die u in een vorige stap hebt gemaakt.
      - Stel de **naam** van de app-configuratie in op de resource naam van de app-configuratie opslag.
      - Behoud de standaard waarden voor de overige para meters.
![Scherm afbeelding toont de para meters van de app-configuratie taak.](./media/azure-app-configuration-parameters.png)
1. Een build opslaan en in de wachtrij plaatsen. In het build-logboek worden de fouten weer gegeven die zijn opgetreden tijdens de uitvoering van de taak.

## <a name="use-in-releases"></a>In releases gebruiken

Deze sectie bevat informatie over het gebruik van de Azure-app configuratie taak in een Azure DevOps release-pijp lijn.

1. Navigeer naar de pagina release pijplijn door **pijp lijnen** te selecteren  >  . Zie [release pipelines](/azure/devops/pipelines/release)(Engelstalig) voor informatie over de release pijplijn.
1. Kies een bestaande release pijplijn. Als u er nog geen hebt, klikt u op **nieuwe pijp lijn** om een nieuwe te maken.
1. Selecteer de knop **bewerken** in de rechter bovenhoek om de release pijplijn te bewerken.
1. Kies in de vervolg keuzelijst **taken** het **stadium** waaraan u de taak wilt toevoegen. Meer informatie over de stadia vindt u [hier](/azure/devops/pipelines/release/environments).
![Scherm afbeelding toont de geselecteerde fase in de vervolg keuzelijst taken.](./media/pipeline-stage-tasks.png)
1. Klik op **+** volgende bij de taak waaraan u een nieuwe taak wilt toevoegen.
![In de scherm afbeelding wordt de plus knop naast de taak weer gegeven.](./media/add-task-to-job.png)
1. Zoek naar de **Azure-app configuratie** taak.
![Scherm afbeelding toont het dialoog venster taak toevoegen met Azure-app configuratie in het zoekvak.](./media/add-azure-app-configuration-task.png)
1. Configureer de vereiste para meters in de taak om uw sleutel waarden uit uw app-configuratie archief op te halen. Beschrijvingen van de para meters zijn beschikbaar in de sectie **para meters** hieronder en in knop info naast elke para meter.
      - Stel de para meter van het **Azure-abonnement** in op de naam van de service verbinding die u in een vorige stap hebt gemaakt.
      - Stel de **naam** van de app-configuratie in op de resource naam van de app-configuratie opslag.
      - Behoud de standaard waarden voor de overige para meters.
1. Een release opslaan en in de wachtrij plaatsen. In het release logboek worden eventuele fouten weer gegeven die zijn opgetreden tijdens de uitvoering van de taak.

## <a name="parameters"></a>Parameters

De volgende para meters worden gebruikt door de configuratie taak Azure-app:

- **Azure-abonnement**: een vervolg keuzelijst met uw beschik bare Azure-service verbindingen. Als u de lijst met beschik bare Azure-service verbindingen wilt bijwerken en vernieuwen, klikt u op de knop **Azure-abonnement vernieuwen** rechts van het tekstvak.
- **App-configuratie naam**: een vervolg keuzelijst waarmee uw beschik bare configuratie archieven worden geladen onder het geselecteerde abonnement. Als u de lijst met beschik bare configuratie archieven wilt bijwerken en vernieuwen, klikt u op de knop **app-configuratie naam vernieuwen** rechts van het tekstvak.
- **Sleutel filter**: het filter kan worden gebruikt om te selecteren welke sleutel waarden worden aangevraagd vanuit Azure-app configuratie. Met de waarde * worden alle sleutel waarden geselecteerd. Zie waarden van de [query sleutel](concept-key-value.md#query-key-values)voor meer informatie.
- **Label**: Hiermee geeft u op welk label moet worden gebruikt bij het selecteren van sleutel waarden uit de app-configuratie opslag. Als er geen label wordt gegeven, worden de sleutel waarden met het label no opgehaald. De volgende tekens zijn niet toegestaan:, *.
- **Voor voegsel van trim sleutel**: Hiermee geeft u een of meer voor voegsels op die moeten worden bijgesneden van de app-configuratie sleutels voordat u deze instelt als variabelen. Meerdere voor voegsels kunnen worden gescheiden door een nieuwe-regel teken.

## <a name="use-key-values-in-subsequent-tasks"></a>Sleutel waarden in volgende taken gebruiken

De sleutel waarden die worden opgehaald uit de app-configuratie, worden ingesteld als pijplijn variabelen, die toegankelijk zijn als omgevings variabelen. De sleutel van de omgevings variabele is de sleutel van de sleutel waarde die wordt opgehaald uit de app-configuratie nadat het voor voegsel is getrimd, indien opgegeven.

Als een volgende taak bijvoorbeeld een Power shell-script uitvoert, kan dit een sleutel waarde gebruiken met de sleutel ' myBuildSetting ' als volgt:
```powershell
echo "$env:myBuildSetting"
```
En de waarde wordt afgedrukt op de-console.

> [!NOTE]
> Azure Key Vault verwijzingen in de app-configuratie worden omgezet en ingesteld als [geheime variabelen](/azure/devops/pipelines/process/variables#secret-variables). In azure-pijp lijnen worden geheime variabelen uit het logboek gemaskeerd. Ze worden niet door gegeven aan taken als omgevings variabelen en moeten in plaats daarvan worden door gegeven als invoer. 

## <a name="troubleshooting"></a>Problemen oplossen

Als er een onverwachte fout optreedt, kunnen de logboeken voor fout opsporing worden ingeschakeld door de pijplijn variabele `system.debug` in te stellen op `true` .

## <a name="faq"></a>Veelgestelde vragen

**Wilt u de configuratie van meerdere sleutels en labels Hoe kan ik opstellen?**

Het kan zijn dat de configuratie uit meerdere labels moet bestaan, bijvoorbeeld standaard en dev. Meerdere app-configuratie taken kunnen in één pijp lijn worden gebruikt voor het implementeren van dit scenario. De sleutel waarden die door een taak in een latere stap worden opgehaald, vervangen alle waarden uit de vorige stappen. In het bovenstaande voor beeld kan een taak worden gebruikt om sleutel waarden met het standaard label te selecteren, terwijl een tweede taak sleutel waarden kan selecteren met het dev label. De sleutels met het dev label overschrijven dezelfde sleutels met het standaard label.
