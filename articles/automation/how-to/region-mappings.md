---
title: Ondersteunde regio's voor gekoppelde Log Analytics-werkruimte
description: In dit artikel worden de ondersteunde regio toewijzingen beschreven tussen een Automation-account en een Log Analytics-werk ruimte, aangezien deze betrekking hebben op bepaalde functies van Azure Automation.
ms.date: 04/01/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 4ac2a575c9fe551d5b1b396ab06b2735a749f9da
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221871"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Ondersteunde regio's voor gekoppelde Log Analytics-werkruimte

In Azure Automation kunt u de functies Updatebeheer, Wijzigingen bijhouden en inventarisatie en VM's buiten bedrijfsuren starten/stoppen in te scha kelen voor uw servers en virtuele machines. Deze functies hebben een afhankelijkheid van een Log Analytics-werk ruimte en daarom moet u de werk ruimte koppelen aan een Automation-account. Er worden echter alleen bepaalde regio's ondersteund om ze samen te koppelen. In het algemeen is de toewijzing *niet* van toepassing als u van plan bent om een Automation-account te koppelen aan een werk ruimte waarvoor deze functies niet zijn ingeschakeld.

De toewijzingen die hier worden beschreven, zijn alleen van toepassing op het koppelen van de Log Analytics-werk ruimte aan een Automation-account. Ze zijn niet van toepassing op de virtuele machines (Vm's) die zijn verbonden met de werk ruimte die is gekoppeld aan het Automation-account. Vm's zijn niet beperkt tot de regio's die worden ondersteund door een bepaalde Log Analytics-werk ruimte. Ze kunnen zich in elke regio bevinden. Houd er rekening mee dat de virtuele machines in een andere regio de status, lokale en landspecifieke vereisten of de nalevings vereisten van uw bedrijf kunnen beïnvloeden. Het hebben van Vm's in een andere regio kan ook kosten voor de gegevens bandbreedte veroorzaken.

Voordat u Vm's verbindt met een werk ruimte in een andere regio, moet u de vereisten en mogelijke kosten controleren om te bevestigen en inzicht te krijgen in de juridische en kosten.

In dit artikel worden de ondersteunde toewijzingen beschreven om deze functies in uw Automation-account in te scha kelen en te gebruiken.

Zie [log Analytics-werk ruimte en het Automation-account](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)voor meer informatie.

## <a name="supported-mappings"></a>Ondersteunde toewijzingen

> [!NOTE]
> Zoals in de volgende tabel wordt weer gegeven, kan er slechts één toewijzing bestaan tussen Log Analytics en Azure Automation.

De volgende tabel bevat de ondersteunde toewijzingen:

|**Log Analytics werkruimte regio**|**Azure Automation regio**|
|---|---|
|**VS**||
|Oost,<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|NorthCentralUS|NorthCentralUS|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**Brazilië**||
|BrazilSouth|BrazilSouth|
|**Canada**||
|CanadaCentral|CanadaCentral|
|**China**||
|ChinaEast2<sup>3</sup>|ChinaEast2|
|**Azië en Stille Oceaan**||
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|**India**||
|CentralIndia|CentralIndia|
|**Japan**||
|JapanEast|JapanEast|
|**Australië**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|**Korea**||
|KoreaCentral|KoreaCentral|
|**Noorwegen**||
|NorwayEast|NorwayEast|
|**Europa**||
|NorthEurope|NorthEurope|
|West-Europa|West-Europa|
|**Frankrijk**||
|FranceCentral|FranceCentral|
|**Verenigd Koninkrijk**
|UKSouth|UKSouth|
|**Zwitserland**||
|SwitzerlandNorth|SwitzerlandNorth|
|**Verenigde Arabische Emiraten**||
|UAENorth|UAENorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|

<sup>1</sup> -oostus toewijzing voor log Analytics-werk ruimten aan Automation-accounts is niet exact een regionale toewijzing, maar is de juiste toewijzing.

<sup>2</sup> EastUS2 toewijzing voor log Analytics-werk ruimten aan Automation-accounts is niet exact een regionale toewijzing, maar is de juiste toewijzing.

<sup>3</sup> in deze regio wordt alleen updatebeheer ondersteund en andere functies, zoals wijzigingen bijhouden en inventaris, zijn op dit moment niet beschikbaar.

## <a name="unlink-a-workspace"></a>Een werk ruimte ontkoppelen

Als u besluit dat u uw Automation-account niet meer wilt integreren met een Log Analytics-werk ruimte, kunt u uw account rechtstreeks van de Azure Portal ontkoppelen. Voordat u doorgaat, moet u eerst Updatebeheer, Wijzigingen bijhouden en inventaris, en VM's buiten bedrijfsuren starten/stoppen [verwijderen](move-account.md#remove-features) als u deze gebruikt. Als u deze niet verwijdert, kunt u de ontkoppelings bewerking niet volt ooien.

Als de functies zijn verwijderd, kunt u de onderstaande stappen volgen om het Automation-account te ontkoppelen.

> [!NOTE]
> Sommige functies, waaronder eerdere versies van de Azure SQL-bewakings oplossing, hebben mogelijk Automation-assets gemaakt die moeten worden verwijderd voordat de koppeling van de werk ruimte wordt opgeheven.

1. Open uw Automation-account vanuit het Azure Portal. Selecteer op de pagina Automation-account de optie **gekoppelde werk ruimte** onder **gerelateerde resources**.

2. Op de pagina werk ruimte ontkoppelen selecteert u **werk ruimte ontkoppelen**. U ontvangt een prompt waarin u wordt gevraagd of u wilt door gaan.

3. Terwijl Azure Automation het account ontkoppelt van uw Log Analytics-werk ruimte, kunt u de voortgang bijhouden onder **meldingen** in het menu.

4. Als u Updatebeheer hebt gebruikt, kunt u eventueel de volgende items verwijderen die niet meer nodig zijn:

    * Update schema's: elk heeft een naam die overeenkomt met een update-implementatie die u hebt gemaakt.
    * Hybrid worker-groepen die zijn gemaakt voor de functie: elk heeft een naam die vergelijkbaar is met  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Als u VM's buiten bedrijfsuren starten/stoppen hebt gebruikt, kunt u desgewenst de volgende items verwijderen die niet meer nodig zijn:

    * VM-runbook-schema's starten en stoppen
    * VM-runbooks starten en stoppen
    * Variabelen

U kunt de werk ruimte ook ontkoppelen van uw Automation-account in de werk ruimte.

1. Selecteer in de werk ruimte **Automation-account** onder **gerelateerde resources**.
2. Selecteer op de pagina Automation-account de optie **account loskoppelen**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Updatebeheer in [updatebeheer Overview](../update-management/overview.md).
* Meer informatie over Wijzigingen bijhouden en inventaris in [Wijzigingen bijhouden en inventaris overzicht](../change-tracking/overview.md).
* Meer informatie over VM's buiten bedrijfsuren starten/stoppen in [VM's buiten bedrijfsuren starten/stoppen Overview](../automation-solution-vm-management.md).