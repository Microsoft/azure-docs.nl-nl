---
title: Toegangs beleid toewijzen aan service-eind punten
description: Meer informatie over het toewijzen van beveiligings beleid voor HTTP-of HTTPS-eind punten in uw Service Fabric-service.
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: c7d30e85848f045b5724bb8bdc6e5c810102c044
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75614652"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Een beleid voor beveiligings toegang toewijzen voor HTTP-en HTTPS-eind punten
Als u een uitvoeren als-beleid toepast en het service manifest HTTP-eindpunt bronnen declareert, moet u een **SecurityAccessPolicy** opgeven.  **SecurityAccessPolicy** zorgt ervoor dat de poorten die zijn toegewezen aan deze eind punten, correct worden beperkt tot het gebruikers account waarmee de service wordt uitgevoerd. Als dat niet het geval is, heeft **http.sys** geen toegang tot de service en worden er storingen met aanroepen van de client ontvangen. In het volgende voor beeld wordt het Customer1-account toegepast op een eind punt met de naam **endpointnaam**, waardoor het volledige toegangs rechten biedt.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Voor een HTTPS-eind punt duidt u ook de naam van het certificaat aan dat moet worden geretourneerd naar de client. U verwijst naar het certificaat met behulp van **EndpointBindingPolicy**.  Het certificaat wordt gedefinieerd in de sectie **certificaten** van het toepassings manifest.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Wanneer u HTTPS gebruikt, moet u niet dezelfde poort en hetzelfde certificaat gebruiken voor verschillende service-exemplaren (onafhankelijk van de toepassing) die op hetzelfde knoop punt zijn geïmplementeerd. Wanneer u twee verschillende services met dezelfde poort in verschillende toepassings exemplaren bijwerkt, resulteert dit in een upgrade fout. Zie [upgrades uitvoeren voor meerdere toepassingen met https-eind punten ](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints)voor meer informatie.
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Lees de volgende artikelen voor de volgende stappen:
* [Inzicht in het toepassings model](service-fabric-application-model.md)
* [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
