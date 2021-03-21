---
title: Het blauwdrukvoorbeeld voor PCI-DSS v3.2.1 implementeren
description: Implementeer stappen voor het blauwdrukvoorbeeld voor Payment Card Industry Data Security Standard v3.2.1, inclusief de parametergegevens voor blauwdrukartefacten.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 37e39231ee7395b951f69035cd98f2e14028ba1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98045050"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>Het blauwdrukvoorbeeld voor PCI-DSS v3.2.1 implementeren

Als u het PCI-DSS v3.2.1-blauwdrukvoorbeeld van Azure Blueprints wilt implementeren, moet u de volgende stappen uitvoeren:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek onder _Andere voorbeelden_ het blauwdrukvoorbeeld **PCI-DSS v3.2.1** en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw exemplaar van het PCI-DSS v3.2.1-blauwdrukvoorbeeld.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. Het exemplaar van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar door die aanpassing is het mogelijk dat het exemplaar niet meer voldoet aan de PCI-DSS v3.2.1-standaard.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals Eerste gepubliceerde versie op basis van het PCI-DSS v3.2.1-blauwdrukvoorbeeld. Selecteer vervolgens **Publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **Gepubliceerd**, kan het worden toegewezen aan een abonnement binnen de beheergroep waarin de kopie is opgeslagen. Dit is de stap waarin parameters worden opgegeven om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw kopie van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement waarvoor de ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf voor u ingevuld op basis van de naam van de blauwdruk.
       Wijzig de naam als dat nodig is of gebruik de opgegeven naam.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Blauwdrukdefinitieversie**: Kies een **gepubliceerde** versie van uw kopie van het blauwdrukvoorbeeld.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergrendelen van de blauwdruk voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de optie voor de standaard door het _systeem toegewezen_ beheerde identiteit staan.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

## <a name="artifact-parameters-table"></a>Tabel met artefactparameters

In de volgende tabel ziet u een lijst met de blauwdrukartefactparameters:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|PCI v3.2.1:2018|Beleidstoewijzing|Lijst met resourcetypen | Controleer diagnostische instelling voor geselecteerde resourcetypen Standaardwaarde is: alle resources zijn geselecteerd| 
|Toegestane locaties|Beleidstoewijzing|Lijst met toegestane locaties|Lijst met toegestane locaties van datacentra waarin resources kunnen worden geïmplementeerd. Deze lijst kan worden aangepast aan gewenste Azure-locaties overal ter wereld. Selecteer de locaties die u wilt toestaan.| 
|Toegestane locaties voor resourcegroepen|Beleidstoewijzing |Toegestane locatie |Met dit beleid kunt u de locaties beperken waarin uw organisatie resourcegroepen kan maken. Dit beleid wordt gebruikt om uw geografische nalevingsvereisten af te dwingen.| 
|Controle op SQL-servers implementeren|Beleidstoewijzing|Retentieperiode in dagen|Gegevensretentie in aantal dagen. De standaardwaarde is 180, maar PCI vereist 365.| 
|Controle op SQL-servers implementeren|Beleidstoewijzing|Naam resourcegroep voor opslagaccount|De controle schrijft databasegebeurtenissen naar een auditlogboek in uw Azure Storage-account (er wordt een opslagaccount aangemaakt in elke regio waarin een SQL-server wordt gemaakt die wordt gedeeld door alle servers in die regio).| 

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het PCI-DSS v3.2.1-blauwdrukvoorbeeld hebt bekeken, raadpleegt u de volgende artikelen voor informatie over het overzicht en de toewijzing van besturingselementen:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1-blauwdruk - Overzicht](./index.md)
> [PCI-DSS v3.2.1-blauwdruk - Toewijzen van beheeropties](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
