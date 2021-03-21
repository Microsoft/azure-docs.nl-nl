---
title: 'Zelfstudie: Blauwdrukvoorbeeld voor een nieuwe omgeving'
description: In deze zelfstudie gebruikt u een blauwdrukvoorbeeld om een blauwdrukdefinitie te maken waarmee twee resourcegroepen worden ingesteld, en wordt voor elk hiervan een roltoewijzing geconfigureerd.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915550"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Zelfstudie: Een omgeving maken op basis van een blauwdrukvoorbeeld

Voorbeeldblauwdrukken bieden voorbeelden van wat u kunt doen met Azure Blueprints. Elk hiervan is een voorbeeld met een specifieke intentie of een specifiek doel, maar maakt zelf geen volledige omgeving. Ze zijn bedoeld als uitgangspunt voor verkennen met behulp van Azure Blueprints, en bevatten verschillende combinaties van opgenomen artefacten, ontwerpen en parameters.

In de volgende zelfstudie wordt het blauwdrukvoorbeeld **Resourcegroepen met RBAC** gebruikt om verschillende aspecten van de Azure Blueprints-service te presenteren. De volgende stappen komen aan bod:

> [!div class="checklist"]
> - Een nieuwe blauwdrukdefinitie maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement
> - Geïmplementeerde resources voor de toewijzing inspecteren
> - De toewijzing van de blauwdruk ongedaan maken om vergrendelingen te verwijderen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-blueprint-definition-from-sample"></a>Een blauwdrukdefinitie maken op basis van het voorbeeld

Implementeer eerst het blauwdrukvoorbeeld. Bij het importeren wordt een nieuwe blauwdruk in uw omgeving gemaakt op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Ga naar het blauwdrukvoorbeeld **Resourcegroepen met RBAC** onder _Andere voorbeelden_ en selecteer dit.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het blauwdrukvoorbeeld. Voor deze zelfstudie gebruiken we de naam _two-rgs-with-role-assignments_.
   - **Definitielocatie**: Gebruik het beletselteken en selecteer de beheergroep of het abonnement waarin u de kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. In dit voorbeeld worden twee resourcegroepen gedefinieerd, met de weergavenamen _ProdRG_ en _PreProdRG_. De definitieve naam en locatie van elke resourcegroep worden ingesteld tijdens de blauwdruktoewijzing. Aan de resourcegroep _ProdRG_ is de rol _Inzender_ toegewezen, en aan de resourcegroep _PreProdRG_ zijn de rollen _Eigenaar_ en _Lezers_ toegewezen. De rollen die zijn toegewezen in de definitie zijn statisch, maar de gebruiker, app of groep waaraan de rol is toegewezen, wordt ingesteld tijdens de blauwdruktoewijzing.

1. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

Met deze stap wordt een kopie van de blauwdrukdefinitie gemaakt in de geselecteerde beheergroep of het geselecteerde abonnement. De opgeslagen blauwdrukdefinitie wordt beheerd zoals elke nieuw gemaakte blauwdruk. U kunt het voorbeeld zo vaak als nodig opslaan in de beheergroep of het abonnement. Elke kopie moet echter een unieke naam hebben.

Zodra de portalmelding **De blauwdrukdefinitie is opgeslagen** is verschenen, gaat u naar de volgende stap.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften. Voor deze zelfstudie brengen we geen wijzigingen aan.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdruk _two-rgs-with-role-assignments_ te zoeken, en selecteer deze.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. In het nieuwe deelvenster aan de rechterkant geeft u _1.0_ op als **Versie** voor de kopie van het blauwdrukvoorbeeld. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals ‘Eerste gepubliceerde versie op basis van het blauwdrukvoorbeeld Resourcegroepen met RBAC'. Selecteer vervolgens **Publiceren** onder aan de pagina.

Met deze stap kunt u de blauwdruk toewijzen aan een abonnement. Na de publicatie kunnen nog steeds wijzigingen worden aangebracht. Voor extra wijzigingen moet u publiceren met een nieuwe waarde voor **Versie** om de verschillen tussen versies van dezelfde blauwdrukdefinitie bij te houden.

Zodra de portalmelding **De blauwdrukdefinitie is gepubliceerd** is verschenen, gaat u naar de volgende stap.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **Gepubliceerd**, kan het worden toegewezen aan een abonnement binnen de beheergroep waarin de kopie is opgeslagen. Dit is de stap waarin parameters worden opgegeven om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om de blauwdruk _two-rgs-with-role-assignments_ te zoeken, en selecteer deze.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw kopie van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement waarvoor de ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf voor u ingevuld op basis van de naam van de blauwdrukdefinitie.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
       Voor deze zelfstudie selecteert u _US - oost 2_.
     - **Blauwdrukdefinitieversie**: Kies de **gepubliceerde** versie _1.0_ van uw kopie van de voorbeeldblauwdrukdefinitie.

   - Toewijzing vergrendelen

     Selecteer blauwdrukvergrendelingsmodus _Alleen-lezen_. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de standaardoptie _Systeem toegewezen_ staan. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Stel voor elk artefact de parameterwaarde in op wat is gedefinieerd in de kolom **Waarde**. Selecteer uw Azure-gebruikersaccount voor `{Your ID}`.

     |Naam van het artefact|Type artefact|Parameternaam|Waarde|Beschrijving|
     |-|-|-|-|-|
     |Resourcegroep ProdRG|Resourcegroep|Naam|ProductionRG|Hiermee definieert u de naam van de eerste resourcegroep.|
     |Resourcegroep ProdRG|Resourcegroep|Locatie|VS - west 2|Hiermee stelt u de locatie van de eerste resourcegroep in.|
     |Inzender|Roltoewijzing|Gebruiker of groep|{Uw id}|Hiermee definieert u aan welke gebruiker of groep de roltoewijzing _Inzender_ moet worden verleend in de eerste resourcegroep.|
     |Resourcegroep PreProdRG|Resourcegroep|Naam|PreProductionRG|Hiermee definieert u de naam van de tweede resourcegroep.|
     |Resourcegroep PreProdRG|Resourcegroep|Locatie|VS - west|Hiermee stelt u de locatie van de tweede resourcegroep in.|
     |Eigenaar|Roltoewijzing|Gebruiker of groep|{Uw id}|Hiermee definieert u aan welke gebruiker of groep de roltoewijzing _Eigenaar_ moet worden verleend in de tweede resourcegroep.|
     |Lezers|Roltoewijzing|Gebruiker of groep|{Uw id}|Hiermee definieert u aan welke gebruiker of groep de roltoewijzing _Lezers_ moet worden verleend in de tweede resourcegroep.|

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina.

Met deze stap worden de gedefinieerde resources geïmplementeerd en wordt de geselecteerde **Vergrendelingstoewijzing** geconfigureerd. Het kan dertig minuten duren om blauwdrukvergrendelingen toe te passen.

Zodra de portalmelding **De blauwdrukdefinitie is toegewezen** is verschenen, gaat u naar de volgende stap.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Resources inspecteren die door de toewijzing zijn geïmplementeerd

Met de blauwdruktoewijzing worden de artefacten gemaakt en bijgehouden die zijn gedefinieerd in de blauwdrukdefinitie. We kunnen de status van de resources bekijken op de pagina van de blauwdruktoewijzing of direct in de resources.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer aan de linkerkant de pagina **Toegewezen blauwdrukken**. Gebruik de filters om de blauwdruktoewijzing _Assignment-two-rgs-with-role-assignments_ te zoeken, en selecteer deze.

   Op deze pagina ziet u dat de toewijzing is geslaagd en kunt u de lijst met gemaakte resources bekijken samen met de bijbehorende blauwdrukvergrendelingsstatus. Als de toewijzing wordt bijgewerkt, bevat de vervolgkeuzelijst **Toewijzingsbewerking** details over de implementatie van elke definitieversie. U kunt elke vermelde resource selecteren. De eigenschappenpagina van de desbetreffende resource wordt dan geopend.

1. Selecteer de resourcegroep **ProductionRG**.

   U ziet dat de naam van de resourcegroep **ProductionRG** is, en niet de weergavenaam van artefact _ProdRG_. Deze naam komt overeen met de waarde die is ingesteld tijdens de blauwdruktoewijzing.

1. Selecteer aan de linkerkant de pagina **Toegangsbeheer (IAM)** , en selecteer vervolgens het tabblad **Roltoewijzingen**.

   Hier ziet u dat aan uw account de rol _Inzender_ is verleend voor het bereik van _Deze resource_. De blauwdruktoewijzing _Assignment-two-rgs-with-role-assignments_ heeft de rol _Eigenaar_ omdat deze is gebruikt om de resourcegroep te maken. Deze machtigingen worden ook gebruikt voor het beheren van resources met geconfigureerde blauwdrukvergrendelingen.

1. Selecteer via de breadcrumb van Azure Portal **Assignment-two-rgs-with-role-assignments** om één pagina terug te gaan, en selecteer vervolgens de resourcegroep **PreProductionRG**.

1. Selecteer aan de linkerkant de pagina **Toegangsbeheer (IAM)** , en selecteer vervolgens het tabblad **Roltoewijzingen**.

   Hier ziet u dat aan uw account zowel de rol _Eigenaar_ als de rol _Lezer_ is verleend, beide voor het bereik van _Deze resource_. De blauwdruktoewijzing heeft ook de rol _Eigenaar_, zoals de eerste resourcegroep.

1. Selecteer het tabblad **Weigeringstoewijzingen**.

   Door de blauwdruktoewijzing is een [weigeringstoewijzing](../../../role-based-access-control/deny-assignments.md) gemaakt op de geïmplementeerde resourcegroep om de blauwdrukvergrendelingsmodus _Alleen-lezen_ af te dwingen. Met de weigeringstoewijzing wordt voorkomen dat iemand met de juiste rechten voor het tabblad _Roltoewijzingen_ bepaalde acties uit kan voeren. De weigeringstoewijzing heeft gevolgen voor _Alle principals_.

1. Selecteer de weigeringstoewijzing en selecteer vervolgens aan de linkerkant de pagina **Geweigerde machtigingen**.

   De weiger toewijzing voor komt dat alle bewerkingen met de configuratie **\* *_ en _*** van de actie worden uitgevoerd, maar biedt Lees toegang door **\* /Read** uit te **sluiten.**

1. Selecteer via de breadcrumb van Azure Portal de optie **PreProductionRG - Toegangsbeheer (IAM)** . Selecteer vervolgens aan de linkerkant de pagina **Overzicht** en vervolgens de knop **Resourcegroep verwijderen**. Voer de naam _PreProductionRG_ in om de verwijdering te bevestigen, en selecteer vervolgens onderaan het deelvenster de optie **Verwijderen**.

   De portalmelding **Verwijderen van resourcegroep PreProductionRG is mislukt** wordt weergegeven. De fout geeft aan dat hoewel het account is gemachtigd voor het verwijderen van de resourcegroep, de toegang via de blauwdruktoewijzing wordt geweigerd. De blauwdrukvergrendelingsmodus _Alleen-lezen_ is namelijk tijdens het toewijzen van de blauwdruk geselecteerd. Dankzij de blauwdrukvergrendeling wordt voorkomen dat met een account met toestemming, ook _Eigenaar_, de resource kan worden verwijderd. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

Deze stappen laten zien dat de resources zijn gemaakt zoals gedefinieerd en dat de blauwdrukvergrendelingen ongewenste verwijdering hebben voorkomen, zelfs van een account met toestemming.

## <a name="unassign-the-blueprint"></a>De toewijzing van de blauwdruk ongedaan maken

De laatste stap is het verwijderen van de toewijzing van de blauwdruk en de resources die ermee zijn geïmplementeerd.
Als u de toewijzing verwijdert, worden de geïmplementeerde artefacten niet verwijderd.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer aan de linkerkant de pagina **Toegewezen blauwdrukken**. Gebruik de filters om de blauwdruktoewijzing _Assignment-two-rgs-with-role-assignments_ te zoeken, en selecteer deze.

1. Selecteer bovenaan de pagina de knop **Toewijzing blauwdruk intrekken**. Lees de waarschuwing in het bevestigingsvenster, en selecteer vervolgens **OK**.

   Nu de blauwdruktoewijzing is verwijderd, worden de blauwdrukvergrendelingen ook verwijderd. De gemaakte resources kunnen opnieuw worden verwijderd via een account met machtigingen.

1. Selecteer **Resourcegroepen** in het Azure-menu, en selecteer vervolgens **ProductionRG**.

1. Selecteer aan de linkerkant de pagina **Toegangsbeheer (IAM)** , en selecteer vervolgens het tabblad **Roltoewijzingen**.

De beveiliging voor elke resourcegroep heeft nog steeds de geïmplementeerde roltoewijzingen, maar de blauwdruktoewijzing druk heeft geen toegang als _Eigenaar_ meer.

Nadat de portalmelding **De blauwdruktoewijzing is verwijderd** is verschenen, gaat u naar de volgende stap.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelfstudie, verwijdert u de volgende resources:

- Resourcegroep _ProductionRG_
- Resourcegroep _PreProductionRG_
- Blauwdrukdefinitie _two-rgs-with-role-assignments_

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een nieuwe blauwdruk kunt maken uit een voorbeelddefinitie. Ga verder met het artikel over de levenscyclus van blauwdrukken voor meer informatie over Azure Blueprints.

> [!div class="nextstepaction"]
> [Meer informatie over de levenscyclus van een blauwdruk](../concepts/lifecycle.md)