---
title: Het definitie bestand van de gebruikers interface testen
description: Hierin wordt beschreven hoe u de gebruikers ervaring kunt testen voor het maken van uw door Azure beheerde toepassing via de portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: f76d3b81c2d5425f7bb91c5c86a79faa097794e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434997"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Uw portal-interface voor Azure Managed Applications testen

Nadat u [de createUiDefinition.jshebt gemaakt](create-uidefinition-overview.md) voor uw beheerde toepassing, moet u de gebruikers ervaring testen. Gebruik een sandbox-omgeving om het bestand in de portal te laden om het testen te vereenvoudigen. U hoeft uw beheerde toepassing niet echt te implementeren. De sandbox geeft uw gebruikers interface weer in de huidige, volledige scherm Portal-ervaring. U kunt ook een script gebruiken om de interface te testen. Beide benaderingen worden weer gegeven in dit artikel. De sandbox is de aanbevolen manier om een voor beeld van de interface te bekijken.

## <a name="prerequisites"></a>Vereisten

* Een **createUiDefinition.jsin** het bestand. Als u dit bestand niet hebt, kopieert u het [voorbeeld bestand](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json).

* Een Azure-abonnement. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="use-sandbox"></a>Sandbox gebruiken

1. Open de [sandbox-definitie voor de gebruikers interface maken](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Sandbox weer geven](./media/test-createuidefinition/show-sandbox.png)

1. Vervang de lege definitie door de inhoud van uw createUiDefinition.jsin het bestand. Selecteer **voor beeld**.

   ![Voor beeld selecteren](./media/test-createuidefinition/select-preview.png)

1. Het formulier dat u hebt gemaakt, wordt weer gegeven. U kunt de gebruikers ervaring stapsgewijs door lopen en de waarden invullen.

   ![Formulier weer geven](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als uw formulier niet wordt weer gegeven nadat u de **Preview** hebt geselecteerd, hebt u mogelijk een syntaxis fout. Zoek naar de rode indicator op de rechter schuif balk en navigeer ernaar.

![Syntaxis fout weer geven](./media/test-createuidefinition/show-syntax-error.png)

Als uw formulier niet wordt weer gegeven, en u in plaats daarvan een pictogram van een Cloud met afscheuren ziet, bevat uw formulier een fout, zoals een ontbrekende eigenschap. Open de Web-Ontwikkelhulpprogramma's in uw browser. In de- **console** worden belang rijke berichten over uw interface weer gegeven.

![Fout weergeven](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Test script gebruiken

Als u uw interface in de portal wilt testen, kopieert u een van de volgende scripts naar uw lokale computer:

* [Power shell side-load script-AZ-module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Power shell side-load script-Azure-module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script voor het laden van Azure CLI-scripts](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Als u het interface bestand in de portal wilt bekijken, voert u het gedownloade script uit. Met het script maakt u een opslag account in uw Azure-abonnement en uploadt u uw createUiDefinition.jsnaar het opslag account. Het opslag account wordt gemaakt wanneer u het script voor het eerst uitvoert of als het opslag account is verwijderd. Als het opslag account al in uw Azure-abonnement bestaat, wordt het door het script opnieuw gebruikt. Het script opent de portal en laadt uw bestand vanuit het opslag account.

Geef een locatie op voor het opslag account en geef de map op die uw createUiDefinition.jsbevat voor het bestand.

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

Als uw createUiDefinition.jsvoor het bestand zich in dezelfde map bevindt als het script en u het opslag account al hebt gemaakt, hoeft u deze para meters niet op te geven.

Gebruik voor PowerShell:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Gebruik voor Azure CLI:

```bash
./sideload-createuidef.sh
```

Het script opent een nieuw tabblad in uw browser. De portal wordt weer gegeven met uw interface voor het maken van de beheerde toepassing.

Geef waarden op voor de velden. Wanneer u klaar bent, ziet u de waarden die worden door gegeven aan de sjabloon die u kunt vinden in de console ontwikkel hulpprogramma's van uw browser.

![Waarden weer geven](./media/test-createuidefinition/show-json.png)

U kunt deze waarden gebruiken als het parameter bestand voor het testen van uw implementatie sjabloon.

Als de portal vastloopt op het scherm samen vatting, is er mogelijk een fout in de uitvoer sectie. U kunt bijvoorbeeld verwijzen naar een besturings element dat niet bestaat. Als een para meter in de uitvoer leeg is, kan de para meter verwijzen naar een eigenschap die niet bestaat. De verwijzing naar het besturings element is bijvoorbeeld geldig, maar de verwijzing naar de eigenschap is niet geldig.

## <a name="test-your-solution-files"></a>Uw oplossings bestanden testen

Nu u hebt gecontroleerd of uw portal-interface werkt zoals verwacht, is het tijd om te controleren of uw createUiDefinition-bestand correct is geïntegreerd met uw mainTemplate.jsvoor het bestand. U kunt een validatie script test uitvoeren om de inhoud van uw oplossings bestanden te testen, met inbegrip van het createUiDefinition-bestand. Het script valideert de JSON-syntaxis, controleert op regex-expressies in tekst velden en zorgt ervoor dat de uitvoer waarden van de portal-interface overeenkomen met de para meters van uw sjabloon. Zie voor meer informatie over het uitvoeren van dit script [statische validatie controles uitvoeren voor sjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/test).

## <a name="next-steps"></a>Volgende stappen

Nadat u uw portal interface hebt gevalideerd, leert u hoe u uw door [Azure beheerde toepassing beschikbaar maakt op Marketplace](../../marketplace/create-new-azure-apps-offer.md).