---
title: Voor beeld van Azure Security Bench Mark Foundation blauw punt implementeren
description: Implementeer stappen voor het voor beeld van de Azure Security Bench Mark Foundation-blauw druk, inclusief details van de artefact parameter.
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: 84c157d696dc8ababe1f252136672ea600e604af
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633951"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Het voor beeld van Azure Security Bench Mark Foundation blauw punt implementeren

Voor het implementeren van het voor beeld van Azure Security Bench Mark Foundation blauw druk moeten de volgende stappen worden uitgevoerd:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het voor beeld van **Azure Security Bench Mark Foundation** blauw onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van Azure Security Bench Mark Foundation.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van de Azure Security Bench Mark Foundation-blauw druk.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **wijzigings notities** op, zoals ' eerste versie gepubliceerd vanuit het Azure Security Bench Mark Foundation-voor beeld. ' Selecteer vervolgens **Publiceren** onder aan de pagina.

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
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken.
     - Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren.
       Zie [Beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Blauwdrukdefinitieversie**: Kies een **gepubliceerde** versie van uw kopie van het blauwdrukvoorbeeld.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergrendelen van de blauwdruk voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Kies de standaardoptie voor de _door het systeem toegewezen_ beheerde identiteit of de optie voor de _door de gebruiker toegewezen identiteit_.

   - Blauwdrukparameters

     De parameters die in deze sectie worden gedefinieerd, worden door veel van de artefacten in de definitie van de blauwdruk gebruikt om consistentie te bieden.
    
     - Voor **voegsel voor resources en resource groepen**: deze teken reeks wordt gebruikt als voor voegsel voor de namen van alle resources en resources
     - **Naaf naam**: naam voor de hub
     - **Bewaar periode logboek (dagen)**: het aantal dagen dat Logboeken worden bewaard; bij het invoeren van ' 0 ' blijven logboeken voor onbepaalde tijd behouden
     - **Hub implementeren**: Voer ' waar ' of ' onwaar ' in om op te geven of de toewijzing de hub-onderdelen van de architectuur implementeert
     - **Locatie van hub**: locatie voor de resource groep hub
     - **Doel-IP-adressen**: doel-IP-adressen voor uitgaande verbindingen; een door komma's gescheiden lijst met IP-adressen of IP-bereik voorvoegsels
     - **Network Watcher naam**: naam voor de Network Watcher resource
     - **Network Watcher naam van resource groep**: naam voor de Network Watcher resource groep
     - **DDoS-beveiliging inschakelen**: Voer ' waar ' of ' onwaar ' in om op te geven of DDoS Protection is ingeschakeld in het virtuele netwerk

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

## <a name="artifact-parameters-table"></a>Tabel met artefactparameters

De volgende tabel bevat een lijst met de para meters van de blauw druk:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|Hub-resource groep|Resourcegroep|Naam van de resourcegroep|Vergrendeld: het voor voegsel wordt toegevoegd aan de naam van de hub|
|Hub-resource groep|Resourcegroep|Resourcegroeplocatie|Vergrendeld: gebruikt een hub-locatie|
|Sjabloon voor Azure Firewall|Resource Manager-sjabloon|Privé-IP-adres Azure Firewall||
|Azure Log Analytics en diagnostische sjabloon|Resource Manager-sjabloon|Locatie van Log Analytics werkruimte|De locatie waar Log Analytics werk ruimte is gemaakt; uitvoeren `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` in azure PowersShell om beschik bare regio's weer te geven|
|Azure Log Analytics en diagnostische sjabloon|Resource Manager-sjabloon|Azure Automation account-ID (optioneel)|Resource-ID van Automation-account; wordt gebruikt om een gekoppelde service te maken tussen Log Analytics en een Automation-account|
|Sjabloon voor Azure-netwerk beveiligings groep|Resource Manager-sjabloon|NSG-stroom logboeken inschakelen|Voer ' waar ' of ' onwaar ' in om NSG-stroom Logboeken in of uit te scha kelen|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsel van virtueel netwerk|Virtueel netwerk adres voorvoegsel voor hub-virtueel netwerk|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsel van het subnet van de firewall|Adres voorvoegsel van het subnet van de firewall voor het virtuele hub-netwerk|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsel van Bastion-subnet|Adres voorvoegsel van het Bastion-subnet voor het virtuele hub-netwerk|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsel van Gateway-subnet|Adres voorvoegsel van Gateway-subnet voor hub virtueel netwerk|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsel van het management-subnet|Adres voorvoegsel van het management-subnet voor het virtuele hub-netwerk|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsel van het subnet van het Jump box|Adres voorvoegsel van het subnet van het Jump box voor het virtuele hub-netwerk|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres namen van subnet (optioneel)|Matrix van namen van subnetten die moeten worden geïmplementeerd in het virtuele hub-netwerk; bijvoorbeeld ' subnet1 ', ' subnet2 '|
|Azure Virtual Network hub-sjabloon|Resource Manager-sjabloon|Adres voorvoegsels (optioneel)|Matrix met IP-adres voorvoegsels voor optionele subnetten voor het virtuele hub-netwerk; bijvoorbeeld "10.0.7.0/24", "10.0.8.0/24"|
|Spoke-resource groep|Resourcegroep|Naam van de resourcegroep|Vergrendeld-voor voegsel koppelen aan spoke-naam|
|Spoke-resource groep|Resourcegroep|Resourcegroeplocatie|Vergrendeld: gebruikt een hub-locatie|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Spoke implementeren|Voer ' waar ' of ' onwaar ' in om op te geven of met de toewijzing de spoke-onderdelen van de architectuur worden geïmplementeerd|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|ID van hub-abonnement|Abonnement-ID waar hub is geïmplementeerd; de standaard waarde is het abonnement waarbij de definitie van de blauw druk zich bevindt|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Spoke-naam|Naam van de spoke|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Voorvoegsel van adres voor virtueel netwerk|Adres voorvoegsel voor spoke-virtueel netwerk Virtual Network|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Adres voorvoegsel van het subnet|Adres voorvoegsel van het subnet voor een spoke-virtueel netwerk|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Adres namen van subnet (optioneel)|Matrix van namen van subnetten die moeten worden geïmplementeerd in het spoke-virtuele netwerk; bijvoorbeeld ' subnet1 ', ' subnet2 '|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Adres voorvoegsels (optioneel)|Matrix met IP-adres voorvoegsels voor optionele subnetten voor het spoke-virtuele netwerk; bijvoorbeeld "10.0.7.0/24", "10.0.8.0/24"|
|Virtual Network spoke-sjabloon voor Azure|Resource Manager-sjabloon|Spoke implementeren|Voer ' waar ' of ' onwaar ' in om op te geven of met de toewijzing de spoke-onderdelen van de architectuur worden geïmplementeerd|
|Azure Network Watcher-sjabloon|Resource Manager-sjabloon|Network Watcher locatie|Als Network Watcher al is ingeschakeld, **moet** deze parameter waarde overeenkomen met de locatie van de bestaande Network Watcher resource groep.|
|Azure Network Watcher-sjabloon|Resource Manager-sjabloon|Locatie van de resource groep Network Watcher|Als Network Watcher al is ingeschakeld, **moet** deze parameter waarde overeenkomen met de naam van de bestaande Network Watcher resource groep.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het voor beeld van Azure Security Bench Mark Foundation Blue hebt bekeken, gaat u naar het volgende artikel voor meer informatie over de architectuur:

> [!div class="nextstepaction"]
> [Azure Security Bench Mark Foundation-blauw druk-overzicht](./index.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
