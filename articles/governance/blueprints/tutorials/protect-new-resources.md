---
title: 'Zelfstudie: Nieuwe resources beveiligen met vergrendelingen'
description: In deze zelfstudie gebruikt u de opties voor resourcevergrendeling Alleen-lezen en Niet verwijderen in Azure Blueprints om nieuwe geïmplementeerde resources te beveiligen.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: c671d641982ba833b54586c1b33979a97747396b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915404"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>Zelfstudie: Nieuwe resources beveiligen met resourcevergrendelingen in Azure Blueprints

Met [resourcevergrendelingen](../concepts/resource-locking.md) in Azure Blueprints kunt u nieuwe geïmplementeerde resources beveiligen tegen onrechtmatige wijzigingen, ook door accounts met de rol van _Eigenaar_. U kunt deze beveiliging toevoegen in de blauwdrukkdefinities van resources die zijn gemaakt met een artefact van een Azure Resource Manager-sjabloon (ARM-sjabloon).

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> - Een blauwdrukdefinitie maken
> - De definitie van uw blauwdruk markeren als **Gepubliceerd**
> - De blauwdrukdefinitie toewijzen aan een bestaand abonnement
> - De nieuwe resourcegroep inspecteren
> - De toewijzing van de blauwdruk ongedaan maken om vergrendelingen te verwijderen

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-a-blueprint-definition"></a>Een blauwdrukdefinitie maken

Maak eerst de blauwdrukdefinitie.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer aan de linkerkant van de pagina **Aan de slag**, onder **Een blauwdruk maken**, de optie **Maken**.

1. Zoek het blauwdrukvoorbeeld **Lege blauwdruk** boven aan de pagina. Selecteer **Beginnen met een lege blauwdruk**.

1. Voer deze informatie in op het tabblad **Algemeen**:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het blauwdrukvoorbeeld. Voor deze zelfstudie gebruiken we de naam **locked-storageaccount**.
   - **Beschrijving van blauwdruk**: Voeg een beschrijving voor de blauwdrukdefinitie toe. Gebruik **Voor het testen van resourcevergrendeling van blauwdrukken op geïmplementeerde resources**.
   - **Definitielocatie**: Selecteer de knop met het beletselteken (...) en selecteer vervolgens de beheergroep of het abonnement waar u de blauwdrukdefinitie wilt opslaan.

1. Selecteer het tabblad **Artefacten** boven aan de pagina of selecteer **Volgende: Artefacten** onder aan de pagina.

1. Een resourcegroep toevoegen op abonnementsniveau:
   1. Selecteer de rij **Artefact toevoegen** onder **Abonnement**.
   1. Selecteer **Resourcegroep** onder **Type artefact**.
   1. Stel **Weergavenaam van artefact** in op **RGtoLock**.
   1. Laat de vakjes **Naam resourcegroep** en **Locatie** leeg, maar zorg dat het selectievakje voor elke eigenschap is geselecteerd, zodat het **dynamische parameters** zijn.
   1. Selecteer **Toevoegen** om dit artefact aan de blauwdruk toe te voegen.

1. Een sjabloon onder de resourcegroep toevoegen:
   1. Selecteer de rij **Artefact toevoegen** onder de vermelding **RGtoLock**.
   1. Selecteer **Azure Resource Manager-sjabloon** onder **Type artefact**, stel **Weergavenaam van artefact** in op **StorageAccount** en laat de optie **Beschrijving** leeg.
   1. Op het tabblad **Sjabloon** plakt u de volgende ARM-sjabloon in het editorvak. Nadat u de sjabloon hebt geplakt, selecteert u **Toevoegen** om het artefact aan de blauwdruk toe te voegen.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

1. Selecteer **Concept opslaan** onder aan de pagina.

Met deze stap maakt u de blauwdrukdefinitie in de geselecteerde beheergroep of het geselecteerde abonnement.

Nadat de portalmelding **De blauwdrukdefinitie is opgeslagen** is verschenen, gaat u naar de volgende stap.

## <a name="publish-the-blueprint-definition"></a>De blauwdrukdefinitie publiceren

Uw blauwdrukdefinitie is nu in uw omgeving gemaakt. De definitie is gemaakt in de modus **Concept** en moet worden gepubliceerd voordat deze kan worden toegewezen en geïmplementeerd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdrukdefinitie **locked-storageaccount** te vinden en selecteer deze.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Voer in het nieuwe deelvenster aan de rechter kant **1.0** in als **Versie**. Deze eigenschap is handig als u later een wijziging aanbrengt. Voer **Notities wijzigen** in, bijvoorbeeld **Eerste versie die is gepubliceerd voor het vergrendelen van op blauwdrukken geïmplementeerde resources**. Selecteer vervolgens **Publiceren** onder aan de pagina.

Met deze stap kunt u de blauwdruk toewijzen aan een abonnement. Wanneer de blauwdrukdefinitie is gepubliceerd, kunt u er nog steeds wijzigingen in aanbrengen. Als u wijzigingen aanbrengt, moet u de definitie publiceren met een nieuwe versiewaarde om de verschillen tussen versies van dezelfde blauwdrukdefinitie bij te houden.

Nadat de portalmelding **De blauwdrukdefinitie is gepubliceerd** is verschenen, gaat u naar de volgende stap.

## <a name="assign-the-blueprint-definition"></a>De blauwdrukdefinitie toewijzen

Nadat de blauwdrukdefinitie is gepubliceerd, kunt u deze toewijzen aan een abonnement binnen de beheergroep waar u deze hebt opgeslagen. In deze stap geeft u parameters op om elke implementatie van de blauwdrukdefinitie uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdrukdefinitie **locked-storageaccount** te vinden en selecteer deze.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - **Basisinstellingen**

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw blauwdrukdefinitie hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement, waarvoor de door u ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf ingevuld op basis van de blauwdrukdefinitie. Deze toewijzing moet het vergrendelen van de nieuwe resourcegroep representeren. Wijzig daarom de toewijzingsnaam in **assignment-locked-storageaccount-TestingBPLocks**.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
       Voor deze zelfstudie selecteert u **US - oost 2**.
     - **Blauwdrukdefinitieversie**: Selecteer gepubliceerde versie **1.0** van de blauwdrukdefinitie.

   - **Vergrendelingstoewijzing**

     Selecteer blauwdrukvergrendelingsmodus **Alleen-lezen**. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - **Beheerde identiteit**

     Gebruik de standaardoptie: **Door het systeem toegewezen**. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

   - **Artefactparameters**

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder ze zijn gedefinieerd. Deze parameters zijn [dynamische parameters](../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens het toewijzen van de blauwdruk. Stel voor elk artefact de parameterwaarde in op wat er in de kolom **Waarde** staat.

     |Naam van het artefact|Type artefact|Parameternaam|Waarde|Beschrijving|
     |-|-|-|-|-|
     |Resourcegroep RGtoLock|Resourcegroep|Naam|TestingBPLocks|Hiermee wordt de naam van de nieuwe resourcegroep gedefinieerd waarop blauwdrukvergrendelingen moeten worden toegepast.|
     |Resourcegroep RGtoLock|Resourcegroep|Locatie|VS - west 2|Hiermee wordt de locatie van de nieuwe resourcegroep gedefinieerd waarop blauwdrukvergrendelingen moeten worden toegepast.|
     |StorageAccount|Resource Manager-sjabloon|storageAccountType (StorageAccount)|Standard_GRS|De opslag-SKU. De standaardwaarde is _Standard_LRS_.|

1. Nadat u alle parameters hebt ingevoerd, selecteert u **Toewijzen** onder aan de pagina.

Met deze stap worden de gedefinieerde resources geïmplementeerd en wordt de geselecteerde **Vergrendelingstoewijzing** geconfigureerd. Het kan dertig minuten duren om blauwdrukvergrendelingen toe te passen.

Nadat de portalmelding **De blauwdrukdefinitie is toegewezen** is verschenen, gaat u naar de volgende stap.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Resources inspecteren die door de toewijzing zijn geïmplementeerd

Door de toewijzing wordt de resourcegroep _TestingBPLocks_ gemaakt en tevens het door het ARM-sjabloonartefact geïmplementeerde opslagaccount. De nieuwe resourcegroep en de geselecteerde vergrendelingsstatus worden weergegeven op de pagina met toewijzingsdetails.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer aan de linkerkant de pagina **Toegewezen blauwdrukken**. Gebruik de filters om de blauwdruktoewijzing **assignment-locked-storageaccount-TestingBPLocks** te zoeken en te selecteren.

   Op deze pagina ziet u dat de toewijzing is geslaagd en dat de resources zijn geïmplementeerd met de nieuwe blauwdrukvergrendelingsstatus. Als de toewijzing wordt bijgewerkt, bevat de vervolgkeuzelijst **Toewijzingsbewerking** details over de implementatie van elke definitieversie. U kunt de resourcegroep selecteren om de pagina met eigenschappen te openen.

1. Selecteer de resourcegroep **TestingBPLocks**.

1. Selecteer aan de linkerkant de pagina **Toegangsbeheer (IAM)** . Selecteer vervolgens het tabblad **Roltoewijzingen**.

   U ziet dat blauwdruktoewijzing _assignment-locked-storageaccount-TestingBPLocks_ de rol _Eigenaar_ heeft. De toewijzing heeft deze rol omdat deze rol is gebruikt om de resourcegroep te implementeren en vergrendelen.

1. Selecteer het tabblad **Weigeringstoewijzingen**.

   Door de blauwdruktoewijzing is een [weigeringstoewijzing](../../../role-based-access-control/deny-assignments.md) gemaakt op de geïmplementeerde resourcegroep om de blauwdrukvergrendelingsmodus **Alleen-lezen** af te dwingen. Met de weigeringstoewijzing wordt voorkomen dat iemand met de juiste rechten voor het tabblad **Roltoewijzingen** bepaalde acties uit kan voeren. De weigeringstoewijzing heeft gevolgen voor _Alle principals_.

   Zie [Resourcevergrendeling van blauwdrukken](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment) voor meer informatie over het uitsluiten van een principal vanwege een weigeringstoewijzing.

1. Selecteer de weigeringstoewijzing en selecteer vervolgens aan de linkerkant de pagina **Geweigerde machtigingen**.

   De weigering-toewijzing voor komt dat alle bewerkingen met de configuratie **\* *_ en _*** van de actie worden uitgevoerd, maar biedt wel lees toegang door **\* /Read** uit te **sluiten.**

1. Selecteer via de breadcrumb in Azure Portal de optie **TestingBPLocks - Toegangsbeheer (IAM)** . Selecteer vervolgens aan de linkerkant de pagina **Overzicht** en vervolgens de knop **Resourcegroep verwijderen**. Voer de naam **TestingBPLocks** in om de verwijdering te bevestigen en selecteer vervolgens **Verwijderen** onder aan het deelvenster.

   De portalmelding **Verwijderen van resourcegroep TestingBPLocks is mislukt** verschijnt. De fout geeft aan dat hoewel uw account is gemachtigd voor het verwijderen van de resourcegroep, de toegang door de blauwdruktoewijzing wordt geweigerd. De blauwdrukvergrendelingsmodus **Alleen-lezen** is namelijk tijdens het toewijzen van de blauwdruk geselecteerd. Dankzij de blauwdrukvergrendeling wordt voorkomen dat met een account met toestemming, ook _Eigenaar_, de resource kan worden verwijderd. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

Deze stappen laten zien dat de geïmplementeerde resources nu zijn beveiligd met blauwdrukvergrendelingen die ongewenste verwijdering voorkomen, zelfs met een account dat is gemachtigd om de resources te verwijderen.

## <a name="unassign-the-blueprint"></a>De toewijzing van de blauwdruk ongedaan maken

In de laatste stap wordt de toewijzing van de blauwdrukdefinitie verwijderd. Als u de toewijzing verwijdert, worden de gekoppelde artefacten niet verwijderd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer aan de linkerkant de pagina **Toegewezen blauwdrukken**. Gebruik de filters om de blauwdruktoewijzing **assignment-locked-storageaccount-TestingBPLocks** te zoeken en te selecteren.

1. Selecteer boven aan de pagina **Toewijzing blauwdruk ongedaan maken**. Lees de waarschuwing in het bevestigingsvenster en selecteer **OK**.

   Wanneer de blauwdruktoewijzing wordt verwijderd, worden de blauwdrukvergrendelingen ook verwijderd. De resources kunnen opnieuw worden verwijderd door middel van een account met de juiste machtigingen.

1. Selecteer **Rsource groepen** in het Azure-menu en selecteer vervolgens **TestingBPLocks**.

1. Selecteer aan de linkerkant de pagina **Toegangsbeheer (IAM)** en selecteer vervolgens het tabblad **Roltoewijzingen**.

De beveiliging van de resourcegroep laat zien dat de blauwdruktoewijzing niet meer over de rol _Eigenaar_ beschikt.

Nadat de portalmelding **De blauwdruktoewijzing is verwijderd** is verschenen, gaat u naar de volgende stap.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelfstudie, verwijdert u de volgende resources:

- Resourcegroep _TestingBPLocks_
- Blauwdrukdefinitie _locked-storageaccount_

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u nieuwe resources kunt beveiligen die zijn geïmplementeerd met Azure Blueprints. Ga verder met het artikel over de levenscyclus van blauwdrukken voor meer informatie over Azure Blueprints.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van een blauwdruk](../concepts/lifecycle.md)