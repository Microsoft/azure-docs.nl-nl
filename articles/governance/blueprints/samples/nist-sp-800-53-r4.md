---
title: Overzicht van het blauwdrukvoorbeeld NIST SP 800-53 R4
description: Overzicht van het blauwdrukvoorbeeld NIST SP 800-53 R4. Met dit blauwdrukvoorbeeld kunnen klanten specifieke beheeropties van NIST SP 800-53 R4 bekijken.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: cff59a53642bcaf0828d9d6a99052bca4d651f31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577127"
---
# <a name="nist-sp-800-53-r4-blueprint-sample"></a>Blauwdrukvoorbeeld NIST SP 800-53 R4

Het blauwdrukvoorbeeld NIST SP 800-53 R4 biedt handvatten voor governance met [Azure Policy](../../policy/overview.md), waarmee u bepaalde NIST SP 800-53 R4-beheeropties kunt evalueren. Deze blauwdruk helpt klanten een kernset met beleid te implementeren voor architectuur die in Azure is geïmplementeerd en waarin NIST SP 800-53 R4-beheeropties moeten worden geïmplementeerd.

## <a name="control-mapping"></a>Toewijzing van beheeropties

De [toewijzing van het Azure Policy-besturingselement](../../policy/samples/nist-sp-800-53-r4.md) bevat details over beleidsdefinities die in deze blauwdruk zijn opgenomen en hoe deze beleidsdefinities worden toegewezen aan de **nalevingsdomeinen** en **besturingselementen** in NIST SP 800-53 R4. Wanneer resources worden toegewezen aan een architectuur, worden deze op niet-naleving van toegewezen beleidsregels geëvalueerd door Azure Policy. Zie [Azure Policy](../../policy/overview.md) voor meer informatie.

## <a name="deploy"></a>Implementeren

Als u het blauwdrukvoorbeeld NIST SP 800-53 R4 van Azure Blueprints wilt implementeren, moet u de volgende stappen uitvoeren:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

### <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het blauwdrukvoorbeeld **NIST SP 800-53 R4** onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het blauwdrukvoorbeeld NIST SP 800-53 R4.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

### <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar door die aanpassing is het mogelijk dat de kopie niet meer is afgestemd op de besturingselementen van NIST SP 800-53 R4.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals 'Eerste gepubliceerde versie op basis van het blauwdrukvoorbeeld NIST SP 800-53 R4'. Selecteer vervolgens **Publiceren** onderaan de pagina.

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
|\[Preview\]: NIST SP 800-53 R4-controles en implementatie van specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten controleren|Beleidstoewijzing|Id van de Log Analytics-werkruimte waarvoor VM's moeten worden geconfigureerd|Dit is de id (GUID) van de Log Analytics-werkruimte waarvoor de VM's moeten worden geconfigureerd.|
|\[Preview\]: NIST SP 800-53 R4-controles en implementatie van specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten controleren|Beleidstoewijzing|Lijst met resourcetypen waarvoor diagnostische logboeken moeten zijn ingeschakeld|Lijst met resourcetypen die moeten worden gecontroleerd als de instelling voor diagnostische logboeken niet is ingeschakeld. Acceptabele waarden vindt u in de [schema's van diagnostische logboeken van Azure Monitor](../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|\[Preview\]: NIST SP 800-53 R4-controles en implementatie van specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten controleren|Beleidstoewijzing|Lijst met gebruikers die moeten worden uitgesloten van de groep Beheerders voor Windows-VM's|Een lijst met leden die niet moeten worden opgenomen in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|\[Preview\]: NIST SP 800-53 R4-controles en implementatie van specifieke VM-extensies implementeren ter ondersteuning van de controlevereisten controleren|Beleidstoewijzing|Lijst met gebruikers die moeten worden opgenomen in de groep Beheerders voor Windows-VM's|Een lijst met leden die moeten worden opgenomen in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|\[Preview\]: Log Analytics-agent voor Linux VM Scale Sets (VMSS) implementeren|Beleidstoewijzing|Log Analytics-werkruimte voor Linux-VM-schaalsets (VMSS)|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u aan de principal-id van de beleidstoewijzing handmatig de rechten voor Inzender voor Log Analytics (of vergelijkbaar) toekennen.|
|\[Preview\]: Log Analytics-agent voor Linux VM Scale Sets (VMSS) implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Linux-besturingssysteem die aan het bereik kunnen worden toegevoegd|Een lege matrix kan worden gebruikt om aan te geven dat er geen optionele parameters zijn: \[\]|
|\[Preview\]: Log Analytics-agent voor virtuele Linux-machines implementeren|Beleidstoewijzing|Log Analytics-werkruimte voor virtuele Linux-machines|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u aan de principal-id van de beleidstoewijzing handmatig de rechten voor Inzender voor Log Analytics (of vergelijkbaar) toekennen.|
|\[Preview\]: Log Analytics-agent voor virtuele Linux-machines implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Linux-besturingssysteem die aan het bereik kunnen worden toegevoegd|Een lege matrix kan worden gebruikt om aan te geven dat er geen optionele parameters zijn: \[\]|
|\[Preview\]: Log Analytics-agent voor Windows-VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Log Analytics-werkruimte voor Windows-VM-schaalsets (VMSS)|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u aan de principal-id van de beleidstoewijzing handmatig de rechten voor Inzender voor Log Analytics (of vergelijkbaar) toekennen.|
|\[Preview\]: Log Analytics-agent voor Windows-VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Windows-besturingssysteem die aan het bereik kunnen worden toegevoegd|Een lege matrix kan worden gebruikt om aan te geven dat er geen optionele parameters zijn: \[\]|
|\[Preview\]: Log Analytics-agent voor Windows-VM's implementeren|Beleidstoewijzing|Log Analytics-werkruimte voor Windows-VM's|Als deze werkruimte buiten het bereik van de toewijzing valt, moet u aan de principal-id van de beleidstoewijzing handmatig de rechten voor Inzender voor Log Analytics (of vergelijkbaar) toekennen.|
|\[Preview\]: Log Analytics-agent voor Windows-VM's implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Windows-besturingssysteem die aan het bereik kunnen worden toegevoegd|Een lege matrix kan worden gebruikt om aan te geven dat er geen optionele parameters zijn: \[\]|
|Advanced Threat Protection implementeren voor opslagaccounts|Beleidstoewijzing|Effect|Informatie over de effecten van het beleid vindt u bij [Inzicht in de effecten van Azure Policy](../../policy/concepts/effects.md)|
|Controle op SQL-servers implementeren|Beleidstoewijzing|De waarde in dagen van de bewaarperiode (0 betekent een onbeperkte bewaarperiode)|Bewaarperiode in dagen (optioneel, 180 dagen als u niets opgeeft)|
|Controle op SQL-servers implementeren|Beleidstoewijzing|De resourcegroepnaam voor het opslagaccount voor SQL Server-controles|De controle schrijft databasegebeurtenissen naar een auditlogboek in uw Azure Storage-account (er wordt een opslagaccount aangemaakt in elke regio waarin een SQL-server wordt gemaakt die wordt gedeeld door alle servers in die regio). Belangrijk: voor de juiste werking van de controle mag u geen resourcegroepen of opslagaccounts verwijderen of hernoemen.|
|Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren|Beleidstoewijzing|Het voorvoegsel van het opslagaccount voor netwerkbeveiligingsgroepdiagnoses|Dit voorvoegsel vormt in combinatie met de locatie van de netwerkbeveiligingsgroep de naam van het gemaakte opslagaccount.|
|Diagnostische instellingen voor netwerkbeveiligingsgroepen implementeren|Beleidstoewijzing|De resourcegroepnaam voor het opslagaccount voor netwerkbeveiligingsgroepdiagnoses (moet bestaan)|De resourcegroep waarin het opslagaccount wordt gemaakt. Deze resourcegroep moet al bestaan.|

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
