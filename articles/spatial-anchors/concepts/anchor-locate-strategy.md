---
title: Strategie voor anker zoeken
description: Meer informatie over de verschillende strategieën bij het aanroepen van de zoek-API
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048473"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Informatie over de klasse AnchorLocateCriteria
In dit artikel vindt u informatie over de verschillende opties die u kunt gebruiken bij het uitvoeren van een query op een anker. We gaan over de AnchorLocateCriteria-klasse, de opties en geldige combi Naties van oplossingen.

## <a name="anchor-locate-criteria"></a>Criteria voor anker zoeken
De [AnchorLocateCriteria-klasse](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) helpt u bij het opvragen van de service voor eerder gemaakte ankers. Eén AnchorLocateCriteria-object kan op elk gewenst moment per Watcher worden gebruikt. Elk AnchorLocateCriteria-object moet **exact een** van de volgende eigenschappen bevatten: [identifiers](#identifiers), [NearAnchor](#nearanchor)of [NearDevice](#neardevice). Aanvullende eigenschappen, zoals [strategie](#strategy), [BypassCache](#bypasscache)en [RequestedCategories](#requestedcategories) , kunnen worden ingesteld indien gewenst. 

### <a name="properties"></a>Eigenschappen
Definieer **exact een** van de volgende eigenschappen in de Watcher:
#### <a name="identifiers"></a>Id's
*Standaard waarde: lege teken reeks matrix*

Met behulp van Id's kunt u een lijst met anker-Id's definiëren voor ankers die u wilt zoeken. Anker-Id's worden in eerste instantie naar u geretourneerd nadat het maken van een anker is geslaagd. Met de opgegeven Id's wordt met AnchorLocateCriteria de set aangevraagde ankers beperkt tot ankers met overeenkomende anker-Id's. Deze eigenschap wordt opgegeven met behulp van een teken reeks matrix. 

#### <a name="nearanchor"></a>NearAnchor
*Standaard waarde: niet ingesteld*

Met NearAnchor kunt u opgeven dat AnchorLocateCriteria de set aangevraagde ankers beperkt tot ankers binnen een gewenste afstand van een gekozen anker. U moet dit gekozen anker opgeven als bron anker. U kunt ook de gewenste afstand van het bron anker instellen, en het maximum aantal geretourneerde ankers om de zoek opdracht verder te beperken.
Deze eigenschap wordt opgegeven met behulp van een NearAnchorCriteria-object.

#### <a name="neardevice"></a>NearDevice
*Standaard waarde: niet ingesteld*

Met NearDevice kunt u opgeven dat de set aangevraagde ankers door AnchorLocateCriteria wordt beperkt tot de fysieke locatie van het apparaat. Alle ingeschakelde Sens oren worden gebruikt om ankers rond uw apparaat te ontdekken. Als u de beste kans hebt om ankers te vinden, moet u SensorCapabilities configureren om de sessie toegang te geven tot alle geschikte Sens oren. Voor meer informatie over het instellen en gebruiken van deze eigenschap raadpleegt u [Grove relokalisatie-Azure spatiale ankers | Microsoft docs](./coarse-reloc.md) en *het maken en vinden van ankers met ruwe Herlokalisatie* in [C#](../how-tos/set-up-coarse-reloc-unity.md), [objectief-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [c++/ndk](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [c++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Deze eigenschap wordt opgegeven met behulp van een NearDeviceCriteria-object.

### <a name="additional-properties"></a>Aanvullende eigenschappen
#### <a name="bypasscache"></a>BypassCache
*Standaard waarde: False*

Wanneer een anker is gemaakt of in een sessie is gevonden, wordt deze ook opgeslagen in de cache.  Als deze eigenschap is ingesteld op False, retourneert elke volgende query in dezelfde sessie de waarde in de cache. Er is geen aanvraag naar de ASA-service gedaan.

#### <a name="requestedcategories"></a>RequestedCategories
*Standaard waarde: eigenschappen | Ruimtelijke*

Deze eigenschap wordt gebruikt om te bepalen welke gegevens worden geretourneerd door een query met behulp van AnchorLocateCriteria. De standaard waarde retourneert zowel eigenschappen als ruimtelijke gegevens. Dit mag niet worden gewijzigd als de eigenschappen en ruimtelijke gegevens beide gewenst zijn. Deze eigenschap kan worden opgegeven met de opsomming AnchorDataCategory.

AnchorDataCategory Enum-waarde | Geretourneerde gegevens
-----|------------
Geen | Er zijn geen gegevens geretourneerd
Eigenschappen| Anker eigenschappen, waaronder AppProperties, worden geretourneerd.
Ruimtelijk| Ruimtelijke informatie over een anker wordt geretourneerd.

#### <a name="strategy"></a>Strategie
*Standaard waarde: AnyStrategy*

Strategie bepaalt hoe ankers moeten worden gevonden. De strategie-eigenschap kan worden opgegeven met behulp van een LocateStrategy Enum.

LocateStrategy Enum-waarde | Beschrijving
---------------|------------
AnyStrategy | Met deze strategie kan het systeem combi Naties van VisualInformation-en relatie strategieën gebruiken om ankers te vinden. 
VisualInformation|Deze strategie probeert ankers te vinden door de visuele informatie van de huidige omgeving te vergelijken met die van de visuele footprint van het anker. De visuele footprint van een anker verwijst naar de visuele informatie die momenteel aan het anker is gekoppeld. Deze visuele informatie wordt doorgaans niet alleen verzameld tijdens het maken van het anker. Deze strategie is op dit moment alleen toegestaan in combi natie met de eigenschappen NearDevice of identifiers.
Relatie|Deze strategie probeert ankers te vinden door gebruik te maken van bestaande [verbonden ankers](./anchor-relationships-way-finding.md#connect-anchors). Deze strategie is op dit moment alleen toegestaan in combi natie met de eigenschappen NearAnchor of identifiers. In combi natie met de eigenschap identifiers is het vereist dat de gebruiker in dezelfde sessie eerder een anker (s) heeft gevonden met reeds gemaakte verbindings relaties met de ankers waarvan de Id's zijn opgegeven in de ID-matrix. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Geldige combi Naties van eigenschappen LocateStrategy en AnchorLocateCriteria 

Niet alle combi Naties van strategie-en AnchorLocateCriteria-eigenschappen zijn momenteel toegestaan door het systeem. In de volgende tabel ziet u de toegestane combi Naties:



Eigenschap | AnyStrategy | Relatie | VisualInformation
-------- | ------------|--------------|-------------------
Id's | &check;    | &check;     | &check;
NearAnchor  | &check;   (wordt standaard ingesteld op relatie) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Volgende stappen

Zie [ankers maken en vinden met behulp van ruimtelijke ankers](../create-locate-anchors-overview.md) voor meer voor beelden met behulp van de AnchorLocateCriteria-klasse.