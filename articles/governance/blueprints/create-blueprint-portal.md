---
title: 'Quickstart: Een blauwdruk maken in de portal'
description: In deze quickstart gebruikt u Azure Blueprints om artefacten te maken, te definiëren en te implementeren via Azure Portal.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 75a4e9deca4859ac8b9f302fcb62ecc1d12dd8c9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918327"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Een blauwdruk definiëren en toewijzen in de portal

Als u leert hoe u blauwdrukken maakt en toewijst, kunt u algemene patronen definiëren voor de ontwikkeling van herbruikbare en snel implementeerbare configuraties op basis van ARM-sjablonen (Azure Resource Manager), beleid, beveiliging en meer. In deze zelfstudie leert u hoe u Azure Blueprints gebruikt om algemene taken uit te voeren met betrekking tot het maken, publiceren en toewijzen van een blauwdruk binnen uw organisatie. Hierbij gaat het onder andere om de volgende taken:

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-a-blueprint"></a>Een blauwdruk maken

De eerste stap bij het definiëren van een standaardpatroon voor naleving bestaat uit het samenstellen van een blauwdruk uit de beschikbare resources. In dit voorbeeld maakt u een blauwdruk met de naam **MyBlueprint** om de rol- en beleidstoewijzingen voor het abonnement te configureren. Vervolgens voegt u een nieuwe resourcegroep toe en maakt u een Resource Manager-sjabloon en roltoewijzing voor de nieuwe resourcegroep.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant en selecteer de knop **+ Blauwdruk maken** aan de bovenkant van de pagina.

   U kunt op de pagina **Aan de slag** ook **Maken** selecteren om rechtstreeks naar het maken van een blauwdruk te gaan.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="Schermopname van de knop Blauwdruk maken op de pagina Blauwdrukdefinities." border="false":::

1. Selecteer bovenaan de pagina op de kaart met ingebouwde blauwdrukken de optie **Beginnen met een lege blauwdruk**.

1. Geef bij **Naam van blauwdruk** een naam op, zoals **MyBlueprint**. (Gebruik maximaal 48 letters en cijfers, maar geen spaties of speciale tekens.) Laat **Beschrijving van blauwdruk** voor nu leeg.

1. Selecteer in het vak **Definitielocatie** het beletselteken aan de rechterkant, selecteer de [beheergroep](../management-groups/overview.md) of het abonnement waarin u de blauwdruk wilt opslaan en klik op **Selecteren**.

1. Controleer of de gegevens correct zijn. De velden **Naam van blauwdruk** en **Definitielocatie** kunnen achteraf niet worden gewijzigd. Klik op **Volgende: artefacten** onderaan de pagina of op het tabblad **Artefacten** bovenaan de pagina.

1. Een roltoewijzing toevoegen op abonnementsniveau:

   1. Selecteer de rij **+ Artefact toevoegen** onder **Abonnement**. Het venster **Artefact toevoegen** wordt geopend aan de rechterkant van de browser.

   1. Selecteer **Roltoewijzing** voor **Type artefact**.

   1. Selecteer bij **Inzender** de optie **Rol**. Laat het selectievakje **Gebruik, app of groep toevoegen** ingeschakeld, wat betekent dat het een dynamische parameter is.

   1. Selecteer **Toevoegen** om dit artefact toe te voegen aan de blauwdruk.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="Schermopname van de opties voor het artefact Roltoewijzing voor het toevoegen aan een blauwdrukdefinitie." border="false":::

   > [!NOTE]
   > De meeste artefacten ondersteunen parameters. Een parameter waaraan een waarde wordt toegewezen tijdens het maken van een blauwdruk, is een _statische parameter_. Als de parameter wordt toegewezen tijdens het toewijzen van de blauwdruk, is de parameter een _dynamische parameter_. Zie [Blauwdrukparameters](./concepts/parameters.md) voor meer informatie.

1. Een beleidstoewijzing toevoegen op abonnementsniveau:

   1. Selecteer de rij **+ Artefact toevoegen** onder het artefact voor roltoewijzing.

   1. Selecteer **Beleidstoewijzing** als **Type artefact**.

   1. Wijzig **Type** in **Ingebouwd**. Voer bij **Zoeken** een **tag** in.

   1. Wijzig de focus van **Zoeken**, zodat wordt gefilterd. Selecteer **Tag met bijbehorende standaardwaarde toevoegen aan resourcegroepen**.

   1. Selecteer **Toevoegen** om dit artefact toe te voegen aan de blauwdruk.

1. Selecteer de rij van de beleidstoewijzing **Tag met bijbehorende standaardwaarde toevoegen aan resourcegroepen**.

1. Het venster waarin u parameters voor het artefact kunt opgeven als onderdeel van de blauwdrukdefinitie wordt geopend. Hier kunt u de parameters voor alle toewijzingen instellen (statische parameters) op basis van deze blauwdruk in plaats van tijdens de toewijzing (dynamische parameters). In dit voorbeeld worden dynamische parameters gebruikt tijdens de blauwdruktoewijzing. Laat daarom de standaardwaarden staan en klik op **Annuleren**.

1. Een resourcegroep toevoegen op abonnementsniveau:

   1. Selecteer de rij **+ Artefact toevoegen** onder **Abonnement**.

   1. Selecteer **Resourcegroep** voor **Type artefact**.

   1. Laat de vakken **Weergavenaam van artefact**, **Resourcegroepnaam** en **Locatie** leeg, maar zorg dat het selectievakje voor elke eigenschap is ingeschakeld zodat het dynamische parameters zijn.

   1. Selecteer **Toevoegen** om dit artefact toe te voegen aan de blauwdruk.

1. Een sjabloon onder de resourcegroep toevoegen:

   1. Selecteer de rij **+ Artefact toevoegen** onder de vermelding **ResourceGroup**.

   1. Selecteer **Azure Resource Manager-sjabloon** als **Type artefact**, stel **Weergavenaam van artefact** in op **StorageAccount** en laat de eigenschap **Beschrijving** leeg.

   1. Plak op het tabblad **Sjabloon** de volgende ARM-sjabloon in het editorvak. Nadat u de sjabloon hebt geplakt, selecteert u het tabblad **Parameters**. U ziet dat de sjabloonparameters **storageAccountType** en **location** zijn gedetecteerd. Elke parameter is automatisch gedetecteerd en gevuld, maar geconfigureerd als een dynamische parameter.

      > [!IMPORTANT]
      > Als u de sjabloon importeert, controleert u of het bestand alleen JSON is en geen HTML-code bevat. Wanneer u verwijst naar een URL op GitHub, zorg er dan voor dat u **RAW** hebt geselecteerd, zodat u het zuivere JSON-bestand ophaalt en weergeeft op GitHub en niet het bestand met HTML. Er treedt een fout op als de geïmporteerde sjabloon geen zuivere JSON is.

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
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
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

   1. Wanneer u het selectievakje **storageAccountType** uitschakelt, ziet u dat de vervolgkeuzelijst alleen waarden bevat die in de ARM-sjabloon onder **allowedValues** voorkomen. Schakel het selectievakje weer in om de parameters terug te zetten op een dynamische parameter.

   1. Selecteer **Toevoegen** om dit artefact toe te voegen aan de blauwdruk.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="Schermopname van de opties voor het artefact Resource Manager-sjabloon voor het toevoegen aan een definitie van de blauwdruk." border="false":::

1. De voltooide blauwdruk moet er nu ongeveer als volgt uitzien. U ziet dat voor elk artefact **_x_ van _y_ parameters zijn ingevuld** in de kolom **Parameters**. De dynamische parameters worden ingesteld tijdens elke toewijzing van de blauwdruk.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="Schermopname van een voltooide blauwdrukdefinitie voor elk type artefact." border="false":::

1. Nu alle geplande artefacten zijn toegevoegd, selecteert u onderaan de pagina **Concept opslaan**.

## <a name="edit-a-blueprint"></a>Een blauwdruk bewerken

U hebt geen beschrijving opgegeven of roltoewijzing aan de nieuwe resourcegroep toegevoegd in [Een blauwdruk maken](#create-a-blueprint). U kunt beide problemen oplossen door de volgende stappen uit te voeren:

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de blauwdruk die u eerder hebt gemaakt en kies **Blauwdruk bewerken**.

1. Geef in **Beschrijving van blauwdruk** informatie op over de blauwdruk en de artefacten waaruit deze bestaat. Voer in dat geval iets in als: 'Deze blauwdruk stelt het tagbeleid en de roltoewijzing op abonnement in, maakt een ResourceGroup en implementeert een resourcesjabloon en wijst een rol toe aan die resourcegroep.'

1. Klik op **Volgende: artefacten** onderaan de pagina of op het tabblad **Artefacten** bovenaan de pagina.

1. Een roltoewijzing toevoegen onder een resourcegroep:

   1. Selecteer de rij **+ Artefact toevoegen**, direct onder de vermelding **ResourceGroup**.

   1. Selecteer **Roltoewijzing** voor **Type artefact**.

   1. Selecteer onder **Rol** de optie **Eigenaar** en schakel het selectievakje onder het vak **Gebruiker, app of groep toevoegen** uit.

   1. Zoek en selecteer de gebruiker, app of groep die u wilt toevoegen. Dit artefact maakt gebruik van een statische parameter die in elke toewijzing van deze blauwdruk is ingesteld.

   1. Selecteer **Toevoegen** om dit artefact toe te voegen aan de blauwdruk.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="Schermopname van de opties voor het artefact van de tweede roltoewijzing voor het toevoegen aan een definitie van de blauwdruk." border="false":::

1. De voltooide blauwdruk moet er nu ongeveer als volgt uitzien. Voor de zojuist toegevoegde roltoewijzing wordt **1 van 1 parameters ingevuld** weergegeven. Dat wil zeggen dat het een statische parameter is.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="Schermopname van de tweede voltooide blauwdrukdefinitie met het artefact van de aanvullende roltoewijzing." border="false":::

1. Selecteer **Concept opslaan** nu de definitie is bijgewerkt.

## <a name="publish-a-blueprint"></a>Een blauwdruk publiceren

Nu alle geplande artefacten aan de blauwdruk zijn toegevoegd, kunt u deze gaan publiceren.
Als de blauwdruk wordt gepubliceerd, kan deze worden toegewezen aan een abonnement.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de blauwdruk die u eerder hebt gemaakt en selecteer **Blauwdruk publiceren**.

1. Geef in het venster dat wordt geopend een **Versie** op (letters, cijfers en afbreekstreepjes met een maximale lengte van 20 tekens), bijvoorbeeld **v1**. U kunt eventueel tekst in **Notities wijzigen** invoeren, bijvoorbeeld **Eerst publiceren**.

1. Selecteer **Publiceren** onderaan de pagina.

## <a name="assign-a-blueprint"></a>Een blauwdruk toewijzen

Zodra een blauwdruk is gepubliceerd, kan deze worden toegewezen aan een abonnement. Wijs de blauwdruk die u hebt gemaakt toe aan een van de abonnementen in uw beheergroephiërarchie. Als de blauwdruk is opgeslagen in een abonnement, kan deze alleen aan dat abonnement worden toegewezen.

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de blauwdruk die u eerder hebt gemaakt (of klik op het beletselteken) en kies **Blauwdruk toewijzen**.

1. Op de pagina **Blauwdruk toewijzen** selecteert u in de vervolgkeuzelijst **Abonnement** de abonnementen waarin u deze blauwdruk wilt implementeren.

   Als er ondersteunde Enterprise-aanbiedingen beschikbaar zijn via [Azure Billing](../../cost-management-billing/index.yml), wordt de koppeling **Nieuw** onder het vak **Abonnement** geactiveerd. Volg deze stappen:

   1. Klik op de koppeling **Nieuw** om een nieuw abonnement te maken in plaats van een bestaand abonnement te selecteren.

   1. Geef een **weergavenaam** voor het nieuwe abonnement op.

   1. Selecteer de beschikbare **aanbieding** in de vervolgkeuzelijst.

   1. Gebruik het beletselteken om de [beheergroep](../management-groups/overview.md) te selecteren waaronder u het abonnement wilt maken.

   1. Selecteer **Maken** onderaan de pagina.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="Schermopname van het venster Abonnement maken en de opties voor het nieuwe abonnement." border="false":::

      > [!IMPORTANT]
      > Het nieuwe abonnement wordt onmiddellijk gemaakt nadat u **Maken** hebt geselecteerd.

   > [!NOTE]
   > Er wordt voor elk abonnement dat u selecteert, een toewijzing gemaakt. U kunt een abonnementstoewijzing achteraf wijzigen zonder dat de overige geselecteerde abonnementen worden gewijzigd.

1. Geef bij **Naam van toewijzing** een unieke naam op voor deze toewijzing.

1. Selecteer voor **Locatie** een regio waarin u de beheerde identiteit en het implementatieobject voor het abonnement wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

1. Laat de vervolgkeuzelijst **Blauwdrukdefinitieversie** van **Gepubliceerde** versies staan op de vermelding **v1**. (De standaardinstelling is de nieuwste gepubliceerde versie.)

1. Laat **Toewijzing vergrendelen** staan op de standaardwaarde **Niet vergrendelen**. Zie voor meer informatie [Blueprints resource locking](./concepts/resource-locking.md) (Resourcevergrendeling in Blueprints).

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="Schermopname van de opties voor Toewijzing vergrendelen en Beheerde identiteit voor de toewijzing van de blauwdruk." border="false":::

1. Laat onder **Beheerde identiteit** de standaardinstelling **Door het systeem toegewezen** staan.

1. Voor de roltoewijzing op abonnementsniveau **[gebruikersgroep of toepassingsnaam]: Inzender** selecteert u een gebruiker, app of groep.

1. Voor de beleidstoewijzing op abonnementsniveau stelt u **Tagnaam** in op **CostCenter** en **Tagwaarde** op **ContosoIT**.

1. Geef voor **ResourceGroup** de **Naam** **StorageAccount** op en selecteer voor **Locatie** **VS - oost 2** in de vervolgkeuzelijst.

   > [!NOTE]
   > Elk artefact dat u tijdens de blauwdrukdefinitie hebt toegevoegd aan de resourcegroep, springt in, zodat het op één lijn staat met de resourcegroep of het object waarmee u het implementeert.
   > Artefacten die geen parameters hebben of waarvoor tijdens de toewijzing geen parameters zijn gedefinieerd, worden alleen weergegeven voor contextuele informatie.

1. Selecteer op de ARM-sjabloon **StorageAccount** de optie **Standard_GRS** voor de parameter **storageAccountType**.

1. Lees het informatievak onderaan de pagina en selecteer **Toewijzen**.

## <a name="track-deployment-of-a-blueprint"></a>Implementatie van een blauwdruk bijhouden

Wanneer een blauwdruk is toegewezen aan een of meer abonnementen, gebeuren er twee dingen:

- De blauwdruk wordt voor elke abonnement toegevoegd aan de pagina **Toegewezen blauwdrukken**.
- De implementatie van de artefacten die zijn gedefinieerd door de blauwdruk begint.

Nu de blauwdruk is toegewezen aan een abonnement, kunt u de voortgang van de implementatie controleren:

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant.

1. Klik in de lijst met blauwdrukken met de rechtermuisknop op de blauwdruk die u eerder hebt toegewezen en selecteer **Toewijzingsdetails weergeven**.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="Schermopname van het contextmenu van de blauwdruktoewijzing waarin de optie Details van de toewijzing weergeven is geselecteerd." border="false":::

1. Controleer op de pagina **Blauwdruktoewijzing** of alle artefacten zijn geïmplementeerd en of er tijdens de implementatie geen fouten zijn opgetreden. Als er fouten zijn opgetreden, raadpleegt u [Blauwdrukproblemen oplossen](./troubleshoot/general.md) voor stappen om te bepalen wat er is misgegaan.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="unassign-a-blueprint"></a>De toewijzing van een blauwdruk ongedaan maken

Als u een blauwdruktoewijzing niet langer nodig hebt, verwijdert u deze uit een abonnement. De blauwdruk is mogelijk vervangen door een nieuwere blauwdruk met bijgewerkte patronen, beleidsregels en ontwerpen. Wanneer een blauwdruk wordt verwijderd, blijven de artefacten die als onderdeel van die blauwdruk zijn toegewezen, achter. Voer de volgende stappen uit als u een blauwdruktoewijzing wilt verwijderen:

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant.

1. Selecteer in de lijst met blauwdrukken de blauwdruk waarvoor u de toewijzing wilt opheffen. Selecteer bovenaan de pagina vervolgens de knop **Toewijzing blauwdruk intrekken**.

1. Lees het bevestigingsbericht en selecteer **OK**.

### <a name="delete-a-blueprint"></a>Een blauwdruk verwijderen

1. Selecteer **Blauwdrukdefinities** op de pagina aan de linkerkant.

1. Klik met de rechtermuisknop op de blauwdruk die u wilt verwijderen en selecteer **Blauwdruk verwijderen**. Selecteer vervolgens **Ja** in het bevestigingsvenster.

> [!NOTE]
> Door een blauwdruk met deze methode te verwijderen, verwijdert u ook alle gepubliceerde versies van de geselecteerde blauwdruk.
> Als u één versie wilt verwijderen, opent u de blauwdruk en selecteert u achtereenvolgens het tabblad **Gepubliceerde versies**, de versie die u wilt verwijderen en de optie **Deze versie verwijderen**. Bovendien kunt u een blauwdruk pas verwijderen als u alle blauwdruktoewijzingen van die blauwdrukdefinitie hebt gewijzigd.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een blauwdruk gemaakt, toegewezen en verwijderd met Azure Portal. Ga verder met het artikel over de levenscyclus van blauwdrukken voor meer informatie over Azure Blueprints.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van een blauwdruk](./concepts/lifecycle.md)
