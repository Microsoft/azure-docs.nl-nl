---
title: Overzicht van Azure Management - Azure Governance
description: Overzicht van de beheergebieden voor de Azure-toepassingen en -resources met koppelingen naar inhoud in Azure-beheerhulpprogramma's.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593922"
---
# <a name="what-are-the-azure-management-areas"></a>Wat zijn de Azure Management-gebieden?

Governance in Azure is slechts één aspect van Azure Management. In dit artikel worden de verschillende beheergebieden besproken voor het implementeren en onderhouden van uw resources in Azure.

Beheer refereert aan de taken en processen die nodig zijn om uw zakelijke toepassingen te onderhouden, evenals de resources die deze toepassingen ondersteunen. Azure heeft diverse services en hulpprogramma's die samenwerken om volledig beheer te bieden. Deze services zijn niet alleen voor resources in Azure, maar ook in andere clouds en on-premises. Inzicht in de verschillende hulpprogramma's en hoe ze samenwerken, is de eerste stap in het ontwerpen van een volledige beheeromgeving.

In het volgende diagram ziet u de verschillende beheergebieden die nodig zijn om een toepassing of resource te onderhouden. Deze verschillende gebieden kunnen worden beschouwd als een levenscyclus. Elk gebied moet tijdens de levensduur van de resource opeenvolgend worden doorlopen. De levenscyclus van de resource begint met de initiële implementatie, vervolgt met de actieve periode en eindigt wanneer de resource buiten gebruik wordt gesteld.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Diagram van de disciplines van Beheer in Azure." border="false":::
   Diagram van de elementen Migreren, Beveiligen, Beschermen, Bewaken, Configureren, en Governance op het servicewiel dat ondersteuning biedt voor Beheer en Governance in Azure. Beveiligen heeft Beveiligingsbeheer en Bedreigingsbeveiliging als subitems. Beschermen heeft Back-ups maken en Herstel na noodgeval als subitems. Bewaken heeft App-, infra- en netwerkbewaking, en Logboekanalyse en diagnostische gegevens als subitems. Configureren heeft Configuratie, Updatebeheer, Automation, en Scripting als subitems. En Governance heeft Beleidsbeheer en Kostenbeheer als subitems.
:::image-end:::

Er is geen enkele Azure-service die volledig aan de vereisten van een bepaald beheergebied voldoet. In plaats daarvan werken verschillende services samen om aan om de benodigde service te bieden. Sommige services, zoals Application Insights, leveren een gerichte bewakingsfunctionaliteit voor webtoepassingen. Andere, zoals Azure Monitor-logboeken, slaan beheergegevens op voor andere services. Met deze functie kunt u verschillende typen gegevens analyseren die door verschillende services zijn verzameld.

In de volgende gedeelten worden kort de diverse beheergebieden beschreven. Ook vindt u er koppelingen naar gedetailleerde inhoud over de belangrijkste Azure-services die voor deze gebieden bedoeld zijn.

## <a name="monitor"></a>Controleren

Controleren of monitoring is het verzamelen en analyseren van gegevens om de prestaties, status en beschikbaarheid van uw resources te controleren. Een efficiënte controlestrategie biedt inzicht in de werking van onderdelen en helpt u om door middel van meldingen uw uptime te vergroten. In [Bewaking van Azure-toepassingen en -resources](../azure-monitor/overview.md) vindt u een overzicht van bewaking in Azure en welke services hiervoor worden gebruikt.

## <a name="configure"></a>Configureren

Configureren heeft betrekking op de initiële implementatie en configuratie, en op voortdurend onderhoud van resources.
Automatisering van deze taken zorgt ervoor dat u redundantie wegneemt en zo de benodigde tijd en moeite beperkt, terwijl u de nauwkeurigheid en efficiëntie verbetert. [Azure Automation](../automation/automation-intro.md) biedt het merendeel van de services die bestemd zijn om configuratietaken te automatiseren. Tijdens de migratie van runbooks worden proces automatisering, configuratie en update beheer Help-configuratie beheerd.

## <a name="govern"></a>Governance

Governance biedt mechanismen en processen om de controle over uw toepassingen en resources in Azure te behouden. Dat omvat het plannen van initiatieven en het stellen van strategische prioriteiten.
Governance in Azure wordt hoofdzakelijk geïmplementeerd via twee services. Met [Azure Policy](./policy/overview.md) kunt u beleidsdefinities maken, toewijzen en beheren om regels voor uw resources af te dwingen.
Deze functie zorgt voor de naleving van de bedrijfsstandaarden door deze resources.
Met [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kunt u het gebruik van de cloud en de uitgaven voor uw Azure-resources en andere cloudproviders bijhouden.

## <a name="secure"></a>Beveiligen

De beveiliging van uw resources en gegevens beheren. Een beveiligingsprogramma omvat het beoordelen van bedreigingen, verzamelen en analyseren van gegevens en naleving van uw toepassingen en bronnen. Beveiligingsbewaking en bedreigingsanalyse worden door [Azure Security Center](../security-center/security-center-introduction.md) geboden, dat ook geïntegreerd beveiligingsbeheer en geavanceerde bedreigingsbescherming voor verschillende hybride cloudworkloads maakt. Zie [Inleiding tot Azure Security](../security/fundamentals/overview.md) voor uitgebreide informatie en richtlijnen over de beveiliging van Azure-resources.

## <a name="protect"></a>Beschermen

Bescherming is bedoeld om uw toepassingen en gegevens beschikbaar te houden, zelfs als er storingen optreden die buiten uw beheer vallen. Bescherming in Azure wordt door twee services geboden. [Azure Backup](../backup/backup-overview.md) biedt back-ups en herstel van uw gegevens in de cloud of on-premises. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) biedt bedrijfscontinuïteit en onmiddellijke herstel tijdens een noodgeval.

## <a name="migrate"></a>Migreren

Migratie heeft betrekking op het overhevelen naar de Azure-cloud van workloads die momenteel on-premises worden uitgevoerd.
[Azure Migrate](../migrate/migrate-services-overview.md) is een service waarmee u de geschiktheid voor migratie kunt beoordelen. Met Azure Site Recovery kunt u virtuele machines [van on-premises](../site-recovery/migrate-tutorial-on-premises-azure.md) of [vanuit Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) migreren. [Azure Database Migration](../dms/dms-overview.md) helpt u bij de migratie van meerdere databasebronnen naar Azure Data-platforms.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-governance:

- Zie de [Azure-governance-hub](./index.yml).
- Zie [Governance in het Cloud Adoption Framework voor Azure](/azure/cloud-adoption-framework/govern/)