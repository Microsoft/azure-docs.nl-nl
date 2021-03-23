---
title: Voor beeld van Nieuw-Zeeland met beperkte blauw druk
description: Overzicht van het voor beeld van het Nieuw-Zeeland met beperkte blauw druk. Met dit blauwdrukvoorbeeld kunnen klanten specifieke beheeropties bekijken.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803927"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Voor beeld van Nieuw-Zeeland met beperkte blauw druk

Het nieuwe blauw druk-voor beeld met een Nieuw-Zeelands spoorweg biedt beheer rails met behulp van [Azure Policy](../../policy/overview.md) die u helpen bij het beoordelen van specifieke [hand matige](https://www.nzism.gcsb.govt.nz/) controles van Nieuw-Zeeland-gegevens Deze blauw druk helpt klanten bij het implementeren van een kern verzameling beleids regels voor elke architectuur die door Azure wordt geïmplementeerd en die besturings elementen moet implementeren voor Nieuw-Zeeland ISM beperkt.

## <a name="control-mapping"></a>Toewijzing van beheeropties

De [toewijzing van het Azure Policy besturings element](../../policy/samples/new-zealand-ism.md) bevat details over beleids definities die zijn opgenomen in deze blauw druk en hoe deze beleids definities worden toegewezen aan de **besturings elementen** in de nieuwe hand leiding Information Security-land instellingen. Wanneer resources worden toegewezen aan een architectuur, worden deze op niet-naleving van toegewezen beleidsregels geëvalueerd door Azure Policy. Zie [Azure Policy](../../policy/overview.md) voor meer informatie.

## <a name="deploy"></a>Implementeren

De volgende stappen moeten worden uitgevoerd voor het implementeren van het Azure-blauw drukken voor het nieuwe blauw druk-Zeeland:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

### <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het **nieuwe** blauw druk-voor beeld van het Nieuw-Zeeland met _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van het nieuwe blauw druk-Zeeland met de extensie.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die in het blauwdrukvoorbeeld zijn opgenomen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

### <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van de uitlijning met Nieuw-Zeeland ISM-restrictie besturings elementen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **wijzigings notities** op, zoals ' de eerste versie die is gepubliceerd vanuit het nieuwe voor beeld van het Nieuw-Zeeland, ISM, beperkt. ' Selecteer vervolgens **Publiceren** onder aan de pagina.

### <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **Gepubliceerd**, kan het worden toegewezen aan een abonnement binnen de beheergroep waarin de kopie is opgeslagen. Dit is de stap waarin parameters worden opgegeven om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw kopie van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement waarvoor de ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf voor u ingevuld op basis van de naam van de blauwdruk.
       Wijzig de naam als dat nodig is of gebruik de opgegeven naam.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Blauwdrukdefinitieversie**: Kies een **gepubliceerde** versie van uw kopie van het blauwdrukvoorbeeld.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergrendelen van de blauwdruk voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de optie voor de standaard door het _systeem toegewezen_ beheerde identiteit staan.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

### <a name="artifact-parameters-table"></a>Tabel met artefactparameters

In de volgende tabel ziet u een lijst met de blauwdrukartefactparameters:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Lijst met gebruikers die moeten worden opgenomen in de groep beheerders voor Windows-VM's|Een lijst met gebruikers die moeten worden opgenomen in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Lijst met gebruikers die moeten worden uitgesloten van de Windows VM-Beheerders groep|Een lijst met gebruikers die moeten worden uitgesloten in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Lijst met gebruikers die de groep beheerders voor Windows-VM's alleen mag bevatten|Een door punt komma's gescheiden lijst met alle verwachte leden van de lokale groep Administrators; Bijvoorbeeld: beheerder; myUser1; myUser2|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Id van de Log Analytics-werkruimte voor rapportage van de VM-agent|Id (GUID) van de Log Analytics-werkruimte waarin de VM-agents moeten rapporteren|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Web Application firewall (WAF) moet zijn ingeschakeld voor de Azure front-deur service|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: aanbevelingen voor adaptieve netwerk beveiliging moeten worden toegepast op Internet gerichte virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Schijfversleuteling moet worden toegepast op virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor functie-apps|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: Web Application firewall (WAF) moet de opgegeven modus gebruiken voor Application Gateway|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Vereiste WAF-modus voor Application Gateway|De preventie-of detectie modus moet zijn ingeschakeld op de Application Gateway-Service|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Transparent Data Encryption in SQL-databases moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: de evaluatie van beveiligings problemen moet worden ingeschakeld op het SQL-beheerde exemplaar|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Optioneel: een lijst met aangepaste VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan de scopes in de galerie voor beleid: Deploy-configure dependency agent to worden ingeschakeld op virtuele Windows-machines|Ga voor meer informatie over gast configuratie naar [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: alleen beveiligde verbindingen met uw Azure-cache voor redis moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van beleid: controleren of Windows-machines een van de opgegeven leden in de groep Administrators ontbreken|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Optioneel: een lijst met aangepaste VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan de installatie kopieën in de galerie voor het volgende beleid: [Preview]: Log Analytics agent moet zijn ingeschakeld voor weer gegeven installatie kopieën van virtuele machines|Ga voor meer informatie over gast configuratie naar [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Optioneel: een lijst met aangepaste VM-installatie kopieën die een ondersteund Linux-besturings systeem hebben om toe te voegen aan de installatie kopieën in de galerie voor het volgende beleid: [Preview]: Log Analytics agent moet zijn ingeschakeld voor weer gegeven installatie kopieën van virtuele machines|Ga voor meer informatie over gast configuratie naar [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: opslag accounts moeten netwerk toegang beperken|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Optioneel: een lijst met aangepaste VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan het bereik extra voor de installatie kopieën in de galerie voor het volgende beleid: Deploy-configure dependency agent to ingeschakeld op Windows virtual machine Scale sets|Ga voor meer informatie over gast configuratie naar [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van beleid: Windows-machines met extra accounts in de groep Administrators controleren|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: Web Application firewall (WAF) moet de opgegeven modus gebruiken voor de Azure front-deur service|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Vereiste WAF-modus voor Azure front-deur service|De preventie of detectie modus moet zijn ingeschakeld op de Azure front-deur service|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: besturings elementen voor adaptieve toepassingen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: [Preview]: open bare toegang tot opslag account moet niet worden toegestaan|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: er moet een oplossing voor de evaluatie van beveiligings problemen worden ingeschakeld op uw virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Web Application firewall (WAF) moet zijn ingeschakeld voor Application Gateway|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw webtoepassingen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van beleid: Windows-webservers controleren die geen protocollen voor beveiligde communicatie gebruiken|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Minimale TLS-versie voor Windows-webservers|Windows-webservers met lagere TLS-versies worden beoordeeld als niet-compatibel|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Optioneel: een lijst met aangepaste VM-installatie kopieën die een ondersteund Linux-besturings systeem hebben om toe te voegen aan het bereik extra voor de installatie kopieën in de galerie voor het beleid: Log Analytics agent moet worden ingeschakeld in virtuele-machine schaal sets voor de vermelde installatie kopieën van virtuele machines|Ga voor meer informatie over gast configuratie naar [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Optioneel: een lijst met aangepaste VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben om toe te voegen aan de scopes in de galerie voor beleid: Log Analytics agent moet worden ingeschakeld in virtuele-machine schaal sets voor installatie kopieën van virtuele machines|Ga voor meer informatie over gast configuratie naar [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Bij het evalueren van beleid onder andere Arc-connected servers: controleren van Windows-machines met de opgegeven leden in de groep Administrators|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Afgeschafte accounts moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Functie-app mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: Azure-abonnementen moeten een logboek profiel voor het activiteiten logboek hebben|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Lijst met resourcetypen waarvoor diagnostische logboeken moeten zijn ingeschakeld||
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Er moeten systeemupdates op uw computers worden geïnstalleerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Microsoft IaaSAntimalware-uitbreiding moet zijn geïmplementeerd op Windows-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Webtoepassing mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Azure DDoS Protection standaard moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Advanced Data Security moet zijn ingeschakeld op uw SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: geavanceerde gegevens beveiliging moet zijn ingeschakeld op een SQL-beheerd exemplaar|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Ontbrekende Endpoint Protection bewaken in Azure Security Center|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: activiteiten logboek moet gedurende ten minste één jaar worden bewaard|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: beheer poorten van virtuele machines moeten worden beveiligd met Just-in-time-netwerk toegangs beheer|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: API-app mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: controleren van Windows-computers waarop Windows Defender exploit Guard niet is ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van beleid: controleren van Windows-computers waarop Windows Defender exploit Guard niet is ingeschakeld|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Nalevings status om te rapporteren voor Windows-computers waarop Windows Defender exploit Guard niet beschikbaar is|Windows Defender exploit Guard is alleen beschikbaar vanaf Windows 10/Windows Server met update 1709. Als u deze waarde instelt op ' niet-compatibel ', worden computers weer gegeven met oudere versies waarvoor Windows Defender exploit Guard niet beschikbaar is (zoals Windows Server 2012 R2) als niet-compatibel. Als u deze waarde instelt op compatibel, worden deze computers als compatibel weer gegeven.|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor API-apps|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Linux-machines controleren die externe verbindingen toestaan van accounts zonder wacht woorden|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van beleid: Linux-machines controleren die externe verbindingen toestaan van accounts zonder wacht woorden|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Windows-computers moeten voldoen aan de vereisten voor beveiligings instellingen-account beleid|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Wachtwoord geschiedenis voor lokale Windows-VM-accounts afdwingen|Hiermee worden de limieten voor het opnieuw gebruiken van wacht woorden opgegeven: hoe vaak er een nieuw wacht woord moet worden gemaakt voor een gebruikers account voordat het wacht woord kan worden herhaald|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen tijdens het evalueren van beleid: Windows-computers moeten voldoen aan de vereisten voor beveiligings instellingen-account beleid|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Maximale wachtwoord duur voor lokale Windows-accounts voor virtuele machines|Hiermee geeft u het maximum aantal dagen op dat kan verstrijken voordat een wacht woord voor een gebruikers account moet worden gewijzigd. de notatie van de waarde bestaat uit twee gehele getallen, gescheiden door een komma, waarbij een inclusief bereik wordt geretourneerd|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Minimale wachtwoord duur voor lokale Windows VM-accounts|Hiermee geeft u het minimum aantal dagen op dat moet verstrijken voordat een wacht woord voor een gebruikers account kan worden gewijzigd|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Minimale wachtwoord lengte voor lokale Windows VM-accounts|Hiermee geeft u het minimum aantal tekens op dat een wacht woord voor een gebruikers account mag bevatten|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Wacht woord moet voldoen aan complexiteits vereisten voor lokale Windows-accounts voor VM'S|Hiermee geeft u op of een wacht woord voor een gebruikers account complex moet zijn. indien nodig mag een complex wacht woord geen deel van de account naam van de gebruiker of de volledige naam bevatten. moet ten minste zes tekens lang zijn. een combi natie van hoofd letters, kleine letters, cijfers en niet-alfabetische tekens bevatten|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor het beleid: Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Linux-machines met accounts zonder wacht woorden controleren|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van beleid: Linux-machines met accounts zonder wacht woorden controleren|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per met de boog verbonden computer|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect van het beleid: [Preview]: alle Internet verkeer moet worden gerouteerd via uw geïmplementeerde Azure Firewall|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Nieuw-Zeeland ISM beperkt|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in uw SQL-databases moeten worden opgelost|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).