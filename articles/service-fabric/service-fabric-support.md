---
title: Meer informatie over de ondersteunings opties voor Azure Service Fabric
description: Ondersteunde cluster versies van Azure Service Fabric en koppelingen naar bestands ondersteunings tickets
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92316502"
---
# <a name="azure-service-fabric-support-options"></a>Ondersteunings opties voor Azure Service Fabric

Er zijn een aantal opties voor ondersteunings aanvragen voor het beheren van uw Service Fabric clusters en toepassings werkbelastingen. Afhankelijk van de urgentie van de vereiste ondersteuning en de ernst van het probleem, kunt u de optie kiezen die geschikt is voor u.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Productie problemen melden of betaalde ondersteuning aanvragen voor Azure

Open een ondersteunings ticket [in de Azure portal of de](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) [micro soft-ondersteunings portal](https://support.microsoft.com/oas/default.aspx?prid=16146)om problemen te rapporteren die betrekking hebben op uw service Fabric cluster dat wordt uitgevoerd op Azure.

Meer informatie over:
 
- [Ondersteuning van micro soft voor Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Micro soft Premier Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Met clusters die worden uitgevoerd op een Bronze-betrouwbaarheids-of een cluster met één knoop punt, kunt u alleen test werkbelastingen uitvoeren. Als u problemen ondervindt met een cluster dat wordt uitgevoerd op Bronze betrouw baarheid of cluster met één knoop punt, helpt het micro soft-ondersteunings team u bij het beperken van het probleem, maar wordt er geen analyse van de hoofd oorzaak uitgevoerd. Raadpleeg [de betrouw bare kenmerken van het cluster](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)voor meer informatie.
>
> Raadpleeg de [controle lijst voor productie voorbereiding](./service-fabric-production-readiness-checklist.md)voor meer informatie over wat er is vereist voor een cluster dat gereed is voor productie.

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Productie problemen melden of betaalde ondersteuning aanvragen voor zelfstandige Service Fabric clusters

Voor het rapporteren van problemen met betrekking tot Service Fabric clusters die on-premises of in andere Clouds worden uitgevoerd, kunt u een ticket voor professionele ondersteuning openen op de [micro soft-ondersteunings portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Meer informatie over:

- [Professionele ondersteuning van micro soft voor on-premises](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Micro soft Premier Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Problemen met Azure-Service Fabric melden

We hebben een GitHub-opslag plaats ingesteld voor het melden van Service Fabric problemen.  De volgende forums worden ook actief bewaakt.

### <a name="github-repo"></a>GitHub opslag plaats 

Meld problemen met Azure Service Fabric op de [service Fabric github](https://github.com/microsoft/service-fabric/issues). Deze opslag plaats is bedoeld voor rapportage-en tracerings problemen, en het maken van kleine functie aanvragen met betrekking tot Azure Service Fabric. **Gebruik dit medium niet voor het melden van problemen met Live-sites**.

### <a name="stackoverflow-and-msdn-forums"></a>Stack overflow en MSDN-Forums

De [service Fabric tag op stack overflow][stackoverflow] en het [service Fabric forum op MSDN][msdn-forum] worden het beste gebruikt voor het stellen van algemene vragen over hoe het platform werkt en hoe u dit kunt gebruiken om bepaalde taken uit te voeren.

### <a name="azure-feedback-forum"></a>Azure-feedback forum

Het [Azure-feedback forum voor service Fabric][uservoice-forum] is de beste plaats voor het indienen van belang rijke ideeën voor product functies. We bekijken de meest populaire aanvragen en beoordelen deze voor onze middel grote tot lange termijn planning. We raden u aan rally ondersteuning te bieden voor uw suggesties in de community.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Preview-versies van Service Fabric-niet ondersteund voor productie gebruik

U maakt af en toe speciale Preview-releases met belang rijke wijzigingen in de onderdelen waarvoor we vroegtijdige feedback willen bekijken. Gebruik alleen Preview-versies in geïsoleerde test omgevingen die geen productie werkbelastingen leveren. In uw productie cluster moet altijd een ondersteunde, stabiele, Service Fabric versie worden uitgevoerd. We bieden geen betaalde ondersteunings optie voor deze Preview-versies.

Een preview-versie begint altijd met een primair en secundair versie nummer van 255. Als u bijvoorbeeld een Service Fabric versie 255.255.5703.949 ziet, is deze versie in Preview en is alleen bedoeld voor gebruik in test clusters. Deze Preview-versies worden ook aangekondigd op het [service Fabric team blog](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) en bevatten details over de functies die zijn opgenomen. Gebruik een van de opties die worden vermeld onder [rapport over Azure-service Fabric-problemen](#report-azure-service-fabric-issues) om vragen te stellen of feedback te geven.

## <a name="next-steps"></a>Volgende stappen

[Ondersteunde Service Fabric versies](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform