---
title: Dubbele versleuteling in Microsoft Azure
description: In dit artikel wordt beschreven hoe Microsoft Azure dubbele versleuteling biedt voor gegevens in rust en gegevens die onderweg zijn.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88227153"
---
# <a name="double-encryption"></a>Dubbele versleuteling
Dubbele versleuteling is waar twee of meer onafhankelijke coderings lagen zijn ingeschakeld om te beschermen tegen inbreuken op een wille keurige laag van versleuteling. Het gebruik van twee versleutelings lagen vermindert bedreigingen die bij het versleutelen van gegevens komen. Bijvoorbeeld:

- Configuratie fouten in de gegevens versleuteling
- Implementatie fouten in het versleutelings algoritme
- Inbreuk op één versleutelings sleutel

Azure biedt dubbele versleuteling voor gegevens in rust en gegevens die onderweg zijn.

## <a name="data-at-rest"></a>Inactieve gegevens
De benadering van micro soft om twee versleutelings lagen in te scha kelen voor gegevens in rust is:

- **Schijf versleuteling met door de klant beheerde sleutels**. U geeft uw eigen sleutel op voor schijf versleuteling. U kunt uw eigen sleutels naar uw Key Vault (BYOK – Bring Your Own Key) brengen of nieuwe sleutels in Azure Key Vault genereren om de gewenste resources te versleutelen.
- **Infrastructuur versleuteling met door het platform beheerde sleutels**.  Standaard worden schijven automatisch versleuteld met behulp van door het platform beheerde versleutelings sleutels.

## <a name="data-in-transit"></a>Actieve gegevens
De benadering van micro soft om twee versleutelings lagen in te scha kelen voor gegevens die onderweg zijn, is:

- **Transit versleuteling met behulp van Transport Layer Security (TLS) 1,2 voor het beveiligen van gegevens wanneer er wordt gereisd tussen de Cloud Services en u**. Al het verkeer dat een Data Center verlaat, wordt tijdens de overdracht versleuteld, zelfs als de bestemming van het verkeer een andere domein controller in dezelfde regio is. TLS 1,2 is het standaard beveiligings protocol dat wordt gebruikt. TLS biedt sterke verificatie, privacy van berichten en integriteit (inschakelen van detectie van bericht manipulatie, onderscheping en vervalsing), interoperabiliteit, algoritme flexibiliteit en gemakkelijke implementatie en gebruik.
- **Extra versleutelings code die wordt meegeleverd bij de laag van de infra structuur**. Een gegevenskoppeling slaag-versleutelings methode met behulp van de IEEE 802.1 AE MAC-beveiligings normen (ook wel bekend als MACsec) wordt toegepast vanaf Point-to-Point over de onderliggende netwerkhardware. Wanneer het verkeer van Azure-klanten tussen data centers wordt verplaatst: buiten de fysieke grenzen die niet worden beheerd door micro soft (of namens micro soft), worden de pakketten versleuteld en ontsleuteld op de apparaten voordat ze worden verzonden, waardoor fysieke ' man-in-the-middle ' of ' wiretapping-aanvallen worden voor komen. Omdat deze technologie is geïntegreerd op de netwerkhardware zelf, biedt deze een regel frequentie versleuteling op de netwerkhardware zonder meet bare koppelings latentie verhoging. Deze MACsec-versleuteling is standaard ingeschakeld voor alle Azure-verkeer dat binnen een regio of tussen regio's reist, en er is geen actie vereist voor het onderdeel van de klanten om in te scha kelen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over hoe [versleuteling wordt gebruikt in azure](encryption-overview.md).
