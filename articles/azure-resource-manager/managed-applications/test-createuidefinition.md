---
title: Het definitiebestand van de gebruikersinterface testen
description: Beschrijft hoe u de gebruikerservaring voor het maken van uw door Azure beheerde toepassing kunt testen via de portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478893"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Uw portalinterface testen voor Azure Managed Applications

Nadat [u de createUiDefinition.jsvoor uw](create-uidefinition-overview.md) beheerde toepassing hebt aanmaken, moet u de gebruikerservaring testen. Om het testen te vereenvoudigen, gebruikt u een sandbox-omgeving die uw bestand in de portal laadt. U hoeft uw beheerde toepassing niet daadwerkelijk te implementeren. De sandbox geeft uw gebruikersinterface weer in de huidige portal met volledig scherm. U kunt ook een script gebruiken om de interface te testen. Beide benaderingen worden in dit artikel weergegeven. De sandbox is de aanbevolen manier om een voorbeeld van de interface te bekijken.

## <a name="prerequisites"></a>Vereisten

* Een **createUiDefinition.jsin het** bestand. Als u dit bestand niet hebt, kopieert u het [voorbeeldbestand](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json).

* Een Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="use-sandbox"></a>Sandbox gebruiken

1. Open de [Sandbox voor gebruikersinterfacedefinitie maken.](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)

   ![Sandbox tonen](./media/test-createuidefinition/show-sandbox.png)

1. Vervang de lege definitie door de inhoud van uw createUiDefinition.jsin het bestand. Selecteer **Preview.**

   ![Preview selecteren](./media/test-createuidefinition/select-preview.png)

1. Het formulier dat u hebt gemaakt, wordt weergegeven. U kunt de gebruikerservaring stapsgewijs door nemen en de waarden invullen.

   ![Formulier tonen](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als uw formulier niet wordt weergegeven nadat u **Voorbeeld hebt geselecteerd,** is er mogelijk een syntaxisfout opgetreden. Zoek de rode indicator op de rechter schuifbalk en navigeer ernaar.

![Syntaxisfout tonen](./media/test-createuidefinition/show-syntax-error.png)

Als uw formulier niet wordt weergegeven en u in plaats daarvan een pictogram van een cloud met een foutmelding ziet, heeft uw formulier een foutmelding, zoals een ontbrekende eigenschap. Open het webvenster Ontwikkelhulpprogramma's in uw browser. In **de console** worden belangrijke berichten over uw interface weergegeven.

![Fout weergeven](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Testscript gebruiken

Als u uw interface in de portal wilt testen, kopieert u een van de volgende scripts naar uw lokale computer:

* [PowerShell-script voor side-load - Az-module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-script voor side-load - Azure-module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-script voor side-load](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Als u het interfacebestand in de portal wilt zien, moet u het gedownloade script uitvoeren. Het script maakt een opslagaccount in uw Azure-abonnement en uploadt uw createUiDefinition.jsnaar het opslagaccount. Het opslagaccount wordt gemaakt wanneer u het script voor het eerst hebt uitgevoerd of als het opslagaccount is verwijderd. Als het opslagaccount al in uw Azure-abonnement bestaat, wordt het door het script opnieuw gebruikt. Het script opent de portal en laadt het bestand uit het opslagaccount.

Geef een locatie op voor het opslagaccount en geef de map op die uw createUiDefinition.jsin het bestand.

Gebruik voor PowerShell:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Gebruik voor Azure CLI:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Als uw createUiDefinition.jsin het bestand zich in dezelfde map als het script en u het opslagaccount al hebt gemaakt, hoeft u deze parameters niet op te geven.

Gebruik voor PowerShell:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Gebruik voor Azure CLI:

```bash
./sideload-createuidef.sh
```

Met het script wordt een nieuw tabblad in uw browser geopend. De portal wordt weergegeven met uw interface voor het maken van de beheerde toepassing.

Geef waarden op voor de velden. Wanneer u klaar bent, ziet u de waarden die worden doorgegeven aan de sjabloon die u kunt vinden in de console voor ontwikkelhulpprogramma's van uw browser.

![Waarden tonen](./media/test-createuidefinition/show-json.png)

U kunt deze waarden gebruiken als parameterbestand voor het testen van uw implementatiesjabloon.

Als de portal vast komt te zitten op het overzichtsscherm, is er mogelijk een fout in de uitvoersectie. Mogelijk hebt u bijvoorbeeld verwezen naar een besturingselement dat niet bestaat. Als een parameter in de uitvoer leeg is, verwijst de parameter mogelijk naar een eigenschap die niet bestaat. De verwijzing naar het besturingselement is bijvoorbeeld geldig, maar de verwijzing naar de eigenschap is niet geldig.

## <a name="test-your-solution-files"></a>Uw oplossingsbestanden testen

Nu u hebt gecontroleerd of uw portalinterface werkt zoals verwacht, is het tijd om te controleren of uw createUiDefinition-bestand correct is geïntegreerd met uw mainTemplate.json-file. U kunt een validatiescripttest uitvoeren om de inhoud van uw oplossingsbestanden te testen, inclusief het createUiDefinition-bestand. Het script valideert de JSON-syntaxis, controleert op regex-expressies voor tekstvelden en zorgt ervoor dat de uitvoerwaarden van de portalinterface overeenkomen met de parameters van uw sjabloon. Zie Statische validatiecontroles uitvoeren voor sjablonen voor meer informatie over het uitvoeren [van dit script.](https://aka.ms/arm-ttk)

## <a name="next-steps"></a>Volgende stappen

Nadat u uw portalinterface hebt valideren, leert u hoe u uw [door Azure beheerde toepassing beschikbaar maakt in Marketplace.](../../marketplace/create-new-azure-apps-offer.md)
