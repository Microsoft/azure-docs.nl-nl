---
title: 'Azure AD Connect: meer informatie over declaratief inrichten | Microsoft Docs'
description: Hierin wordt het declaratieve inrichtings configuratie model in Azure AD Connect uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91313617"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect Sync: uitleg over declaratieve inrichting
In dit onderwerp wordt het configuratie model in Azure AD Connect uitgelegd. Het model wordt declaratieve inrichting genoemd en stelt u in staat om een configuratie wijziging met gemak te maken. Veel dingen die in dit onderwerp worden beschreven, zijn Geavanceerd en zijn niet vereist voor de meeste klanten scenario's.

## <a name="overview"></a>Overzicht
Met declaratieve inrichting worden objecten verwerkt die afkomstig zijn van een door een bron verbonden Directory en wordt bepaald hoe het object en de kenmerken van een bron naar een doel moeten worden getransformeerd. Een object wordt verwerkt in een synchronisatie pijplijn en de pijp lijn is hetzelfde voor binnenkomende en uitgaande regels. Een regel voor inkomend verkeer is van een connector ruimte op de tekst en een uitgaande regel is van de omgekeerde naar een connector ruimte.

![Diagram waarin een voor beeld van een synchronisatie pijplijn wordt weer gegeven.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

De pijp lijn heeft verschillende modules. Elke account is verantwoordelijk voor één concept in object synchronisatie.

![Diagram waarin de modules in de pijp lijn worden weer gegeven.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Bron, het bron object
* [Bereik](#scope), zoekt alle synchronisatie regels die binnen het bereik vallen
* [Samen voegen](#join)Hiermee wordt de relatie tussen de connector ruimte en de omgekeerde
* Transformeren, berekent hoe kenmerken moeten worden getransformeerd en flow
* [Prioriteit](#precedence), worden conflicterende kenmerk bijdragen opgelost
* Doel, het doel object

## <a name="scope"></a>Bereik
De bereik module evalueert een object en bepaalt de regels die binnen het bereik vallen en moeten worden opgenomen in de verwerking. Afhankelijk van de kenmerken waarden van het object, worden verschillende synchronisatie regels geëvalueerd die binnen het bereik vallen. Een uitgeschakelde gebruiker zonder Exchange-postvak heeft bijvoorbeeld andere regels dan een ingeschakelde gebruiker met een postvak.  
![Diagram waarin de bereik module voor een object wordt weer gegeven.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Het bereik is gedefinieerd als groepen en componenten. De componenten bevinden zich in een groep. Een logische en wordt gebruikt tussen alle componenten in een groep. Bijvoorbeeld (afdeling = IT en land = Denemarken). Een logische of wordt gebruikt tussen groepen.

![Bereik](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Het bereik in deze afbeelding moet worden gelezen als (afdeling = IT en land = Denemarken) of (land = Zweden). Als óf groep 1 ofwel groep 2 als waar wordt geëvalueerd, ligt de regel binnen het bereik.

De bereik module ondersteunt de volgende bewerkingen.

| Bewerking | Beschrijving |
| --- | --- |
| GELIJK AAN, NOTEQUAL |Een teken reeks waarmee wordt geëvalueerd als de waarde gelijk is aan de waarde in het kenmerk. Voor kenmerken met meerdere waarden raadpleegt u ISIN en ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Een teken reeks waarmee wordt geëvalueerd als de waarde lager is dan de waarde in het kenmerk. |
| BEVAT, NOTCONTAINS |Een teken reeks die moet worden geëvalueerd en die als waarde kan worden gevonden ergens binnen de waarde in het kenmerk. |
| STARTSWITH, NOTSTARTSWITH |Een teken reeks waarmee wordt geëvalueerd als de waarde zich in het begin van de waarde in het kenmerk bevindt. |
| ENDSWITH, NOTENDSWITH |Een teken reeks waarmee wordt geëvalueerd als de waarde zich in het einde van de waarde in het kenmerk bevindt. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Een teken reeks waarmee wordt geëvalueerd als waarde groter is dan de waarde in het kenmerk. |
| ISNULL, ISNOTNULL |Evalueert of het kenmerk van het object ontbreekt. Als het kenmerk niet aanwezig is en daarom null is, ligt de regel binnen het bereik. |
| ISIN, ISNOTIN |Evalueert of de waarde aanwezig is in het gedefinieerde kenmerk. Deze bewerking is de meerwaardige variatie van gelijk en NOTEQUAL. Het kenmerk moet een kenmerk met meerdere waarden zijn en als de waarde kan worden gevonden in een van de kenmerk waarden, ligt de regel binnen het bereik. |
| ISBITSET, ISNOTBITSET |Evalueert of een bepaalde bit is ingesteld. Kan bijvoorbeeld worden gebruikt om de bits in userAccountControl te evalueren om te controleren of een gebruiker is ingeschakeld of uitgeschakeld. |
| ISMEMBEROF, ISNOTMEMBEROF |De waarde moet een DN bevatten voor een groep in de ruimte van de connector. Als het object lid is van de groep die is opgegeven, ligt de regel binnen het bereik. |

## <a name="join"></a>Deelnemen
De samenvoegings module in de synchronisatie pijplijn is verantwoordelijk voor het vinden van de relatie tussen het object in de bron en een object in het doel. In een regel voor binnenkomende verbindingen zou deze relatie een object zijn in een connector ruimte waarmee een relatie wordt gezocht naar een object in de tekst.  
![Samen voegen tussen CS en MV](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Het doel is om te zien of er al een object is in de tekst die is gemaakt door een andere connector. In een account-bron-forest moet de gebruiker van het account forest bijvoorbeeld worden gekoppeld aan de gebruiker uit de bron-forest.

Samen voegingen worden voornamelijk gebruikt voor regels voor binnenkomende verbindingen voor het samen voegen van connector-ruimte objecten aan hetzelfde omgekeerd object.

De samen voegingen worden gedefinieerd als een of meer groepen. Binnen een groep bevindt u componenten. Een logische en wordt gebruikt tussen alle componenten in een groep. Een logische of wordt gebruikt tussen groepen. De groepen worden van boven naar beneden verwerkt in de volg orde. Wanneer één groep precies één overeenkomst heeft gevonden met een object in het doel, worden er geen andere regels voor samen voegen geëvalueerd. Als er geen of meer dan één object wordt gevonden, gaat de verwerking door naar de volgende groep regels. Daarom moeten de regels worden gemaakt in de volg orde van de meest expliciete eerste en meer fuzzy aan het einde.  
![Koppelings definitie](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
De samen voegingen in deze afbeelding worden van boven naar beneden verwerkt. Eerst ziet de synchronisatie pijplijn of er een overeenkomst is voor werk nemers. Als dat niet het geval is, ziet de tweede regel of de account naam kan worden gebruikt om de objecten samen te voegen. Als dat niet het geval is, is de derde en de laatste regel een beter benadering met de naam van de gebruiker.

Als alle regels voor samen voegen zijn geëvalueerd en er niet precies één overeenkomst is, wordt het **koppelings type** op de pagina **Beschrijving** gebruikt. Als deze optie is ingesteld op **inrichten**, wordt een nieuw object in het doel gemaakt.  
![Scherm afbeelding met de vervolg keuzelijst ' koppelings type ' geopend.](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Een object mag slechts één synchronisatie regel hebben met de regels voor samen voegen in het bereik. Als er meerdere synchronisatie regels zijn waarbij een samen voeging is gedefinieerd, treedt er een fout op. De prioriteit wordt niet gebruikt om deelname conflicten op te lossen. Een object moet een joinlijn hebben in het bereik voor kenmerken die moeten worden verzonden met dezelfde inkomende/uitgaande richting. Als u kenmerken zowel binnenkomend als uitgaand naar hetzelfde object wilt stroomren, moet u zowel een binnenkomende als een uitgaande synchronisatie regel met samen voegen hebben.

Uitgaande samen voeging heeft een speciaal gedrag wanneer het probeert een object in te richten op een doel connector ruimte. Het kenmerk DN wordt gebruikt om eerst een reverse-koppeling te proberen. Als er al een object in de doel-connector ruimte is met dezelfde DN, worden de objecten toegevoegd.

De samenvoegings module wordt slechts eenmaal geëvalueerd wanneer een nieuwe synchronisatie regel binnen het bereik valt. Wanneer een object is toegevoegd, wordt het niet verwijderd, zelfs als niet meer aan de samenvoeg criteria wordt voldaan. Als u een object wilt loskoppelen, moet de synchronisatie regel die aan de objecten is gekoppeld, buiten het bereik vallen.

### <a name="metaverse-delete"></a>Omgekeerde verwijdering
Een omgekeerd object blijft zolang er één synchronisatie regel is in het bereik met het **koppelings type** ingesteld op **inrichten** of **StickyJoin**. Een StickyJoin wordt gebruikt wanneer een connector geen nieuw object mag inrichten voor het omgekeerde, maar wanneer het is gekoppeld, moet het worden verwijderd uit de bron voordat het omgekeerde object wordt verwijderd.

Wanneer een omgekeerd object wordt verwijderd, worden alle objecten die zijn gekoppeld aan een uitgaande synchronisatie regel gemarkeerd voor **inrichten** , gemarkeerd voor verwijdering.

## <a name="transformations"></a>Transformaties
De trans formaties worden gebruikt om te definiëren hoe kenmerken van de bron naar het doel moeten stromen. De stromen kunnen een van de volgende **stroom typen** hebben: direct, constant of expressie. Een directe stroom, geeft een kenmerk waarde als-is zonder aanvullende trans formaties. Een constante waarde stelt de opgegeven waarde in. Een expressie maakt gebruik van de declaratieve inrichtings expressie taal om te zien hoe de trans formatie moet zijn. De Details voor de expressie taal vindt u in het onderwerp [uitleg over declaratieve inrichtings expressie](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

![Inrichten of lid worden](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Met het selectie vakje **eenmaal Toep assen** definieert u dat het kenmerk alleen moet worden ingesteld wanneer het object voor het eerst wordt gemaakt. Deze configuratie kan bijvoorbeeld worden gebruikt om een eerste wacht woord in te stellen voor een nieuw gebruikers object.

### <a name="merging-attribute-values"></a>Kenmerk waarden worden samengevoegd
In de kenmerk stromen is er een instelling om te bepalen of kenmerken met meerdere waarden moeten worden samengevoegd uit verschillende connectors. De standaard waarde is **Update**, wat aangeeft dat de synchronisatie regel met de hoogste prioriteit moet worden gewonnen.

![Scherm afbeelding met de sectie ' trans formaties toevoegen ' met de vervolg keuzelijst ' typen samen voegen ' geopend.](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Er is ook **samen voegen** en **MergeCaseInsensitive**. Met deze opties kunt u waarden uit verschillende bronnen samen voegen. Het kan bijvoorbeeld worden gebruikt om het kenmerk lid of proxyAddresses uit verschillende forests samen te voegen. Wanneer u deze optie gebruikt, moeten alle synchronisatie regels in het bereik voor een object hetzelfde type samen voegen gebruiken. U kunt de **Update** niet definiëren vanuit de ene connector en **samen voegen** van een andere. Als u probeert, treedt er een fout op.

Het verschil tussen **Merge** en **MergeCaseInsensitive** is het verwerken van dubbele kenmerk waarden. De synchronisatie-engine zorgt ervoor dat er geen dubbele waarden worden ingevoegd in het doel kenmerk. Met **MergeCaseInsensitive** zijn dubbele waarden met alleen een verschil in het geval niet aanwezig. U zou bijvoorbeeld niet zowel " SMTP:bob@contoso.com " als " smtp:bob@contoso.com in het doel kenmerk moeten zien. **Samen voegen** heeft alleen betrekking op de exacte waarden en meerdere waarden, waarbij er alleen een verschil is in het geval kan aanwezig zijn.

De optie **vervangen** is hetzelfde als **Update**, maar wordt niet gebruikt.

### <a name="control-the-attribute-flow-process"></a>Het kenmerk stroom proces beheren
Wanneer meerdere regels voor binnenkomende synchronisatie zijn geconfigureerd om te worden bijgeschreven naar hetzelfde kenmerk voor de omgekeerde eigenschap, wordt de voor rang gebruikt om de winnaar te bepalen. De synchronisatie regel met de hoogste prioriteit (laagste numerieke waarde) gaat de waarde bijdragen. Hetzelfde gebeurt voor uitgaande regels. De synchronisatie regel met de hoogste prioriteit wint en draagt de waarde bij aan de verbonden Directory.

In sommige gevallen, in plaats van een waarde te bijdragen, moet de synchronisatie regel bepalen hoe andere regels zich gedraagt. Er worden enkele speciale letterlijke waarden gebruikt voor deze aanvraag.

Voor inkomende synchronisatie regels kan de letterlijke waarde **Null** worden gebruikt om aan te geven dat de stroom geen waarde voor bijdragen heeft. Een andere regel met een lagere prioriteit kan een waarde bijdragen. Als er geen regel is bijgedragen aan een waarde, wordt het kenmerk voor de eigenschap omgekeerd verwijderd. Als **Null** de laatste waarde is nadat alle synchronisatie regels zijn verwerkt, wordt de waarde in de verbonden Directory verwijderd.

De letterlijke **AuthoritativeNull** is vergelijkbaar met **Null** , maar met het verschil dat er geen lagere prioriteits regels kunnen bijdragen aan een waarde.

Een kenmerk stroom kan ook **IgnoreThisFlow** gebruiken. Het is vergelijkbaar met NULL in de zin dat het aangeeft dat er niets kan bijdragen. Het verschil is dat er geen bestaande waarde in het doel wordt verwijderd. Het lijkt erop dat de kenmerk stroom nog nooit is geweest.

Hier volgt een voorbeeld:

De volgende stroom kan worden gevonden in de *Exchange-hybride van AD-User* :  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Deze expressie moet worden gelezen als: als het postvak van de gebruiker zich in azure AD bevindt, stroomt u het kenmerk van Azure AD naar AD. Als dat niet het geval is, stroomt u niets terug naar Active Directory. In dit geval wordt de bestaande waarde in AD bewaard.

### <a name="importedvalue"></a>ImportedValue
De functie ImportedValue wijkt af van alle andere functies, omdat de naam van het kenmerk tussen aanhalings tekens in plaats van vier Kante haken moet worden geplaatst:  
`ImportedValue("proxyAddresses")`.

Normaal gesp roken gebruikt een kenmerk tijdens het synchroniseren de verwachte waarde, zelfs als deze nog niet is geëxporteerd of als er een fout is ontvangen tijdens het exporteren ("Top van de Tower"). Bij een inkomende synchronisatie wordt ervan uitgegaan dat een kenmerk dat nog niet is bereikt een verbonden Directory uiteindelijk bereikt. In sommige gevallen is het belang rijk dat u alleen een waarde synchroniseert die is bevestigd door de verbonden Directory (' hologram and Delta import Tower ').

Een voor beeld van deze functie vindt u in de out-of-Box-synchronisatie regel *in vanuit AD: gemeen schappelijk van Exchange*. In hybride uitwisseling moet de waarde die wordt toegevoegd door Exchange Online alleen worden gesynchroniseerd wanneer is bevestigd dat de waarde is geëxporteerd:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioriteit
Wanneer verschillende synchronisatie regels proberen dezelfde kenmerk waarde naar het doel te bijdragen, wordt de prioriteits waarde gebruikt om de winnaar te bepalen. De regel met de hoogste prioriteit, de laagste numerieke waarde, gaat het kenmerk in een conflict bijdragen.

![Typen samen voegen](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Deze volg orde kan worden gebruikt voor het definiëren van meer nauw keurige kenmerk stromen voor een kleine subset van objecten. De out-of-Box-Rules-regels zorgen er bijvoorbeeld voor dat kenmerken van een ingeschakeld account (**gebruiker AccountEnabled**) prioriteit hebben van andere accounts.

U kunt de prioriteit definiëren tussen connectors. Hiermee kunnen connectors met betere gegevens eerst bijdragen aan de waarden.

### <a name="multiple-objects-from-the-same-connector-space"></a>Meerdere objecten van dezelfde connector ruimte
Als er meerdere objecten in dezelfde connector ruimte zijn gekoppeld aan hetzelfde omgekeerde object, moet de prioriteit worden aangepast. Als verschillende objecten binnen het bereik van dezelfde synchronisatie regel vallen, kan de synchronisatie-engine de prioriteit niet bepalen. Het is ambigu welk bron object de waarde moet bijdragen aan de tekst. Deze configuratie wordt als dubbel zinnig gerapporteerd, zelfs als de kenmerken in de bron dezelfde waarde hebben.  
![Diagram waarin meerdere objecten worden weer gegeven die zijn gekoppeld aan hetzelfde MV-object met een transparante rode X-overlay. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Voor dit scenario moet u het bereik van de synchronisatie regels wijzigen zodat de bron objecten verschillende synchronisatie regels in het bereik hebben. Hiermee kunt u een andere prioriteit definiëren.  
![Meerdere objecten gekoppeld aan hetzelfde MV-object](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de expressie taal vindt u in [uitleg over declaratieve inrichtings expressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Zie hoe declaratieve inrichting out-of-box wordt gebruikt in [de standaard configuratie](concept-azure-ad-connect-sync-default-configuration.md).
* Zie een praktische wijziging aanbrengen met behulp van declaratieve inrichting in [hoe u een wijziging aanbrengt in de standaard configuratie](how-to-connect-sync-change-the-configuration.md).
* Blijf lezen hoe gebruikers en contact personen samen werken voor meer [informatie over gebruikers en contact personen](concept-azure-ad-connect-sync-user-and-contacts.md).

**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

**Onderwerpen met naslaginformatie**

* [Azure AD Connect Sync: Naslag informatie over functies](reference-connect-sync-functions-reference.md)
