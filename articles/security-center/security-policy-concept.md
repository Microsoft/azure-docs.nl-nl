---
title: Informatie over beveiligingsbeleid, initiatieven en aanbevelingen in Azure Security Center
description: Meer informatie over beveiligingsbeleid, initiatieven en aanbevelingen in Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4eea2611997732a263e9e824bc150b45ed145ecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738968"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Wat zijn beveiligingsbeleid, initiatieven en aanbevelingen?

Security Center beveiligingsinitiatieven worden toegepast op uw abonnementen. Deze initiatieven bevatten een of meer beveiligingsbeleidsregels. Elk van deze beleidsregels resulteert in een beveiligingsaanbeveling voor het verbeteren van uw beveiligingsstatus. Op deze pagina worden al deze ideeën in detail uitgelegd.


## <a name="what-is-a-security-policy"></a>Wat is een beveiligingsbeleid?

Een Azure-beleidsdefinitie, gemaakt in Azure Policy, is een regel over specifieke beveiligingsvoorwaarden die u wilt controleren. Ingebouwde definities omvatten zaken zoals het beheren van welk type resources kan worden geïmplementeerd of het afdwingen van het gebruik van tags voor alle resources. U kunt ook uw eigen aangepaste beleidsdefinities maken.

Als u deze beleidsdefinities (ingebouwde of aangepaste) wilt implementeren, moet u deze toewijzen. U kunt elk van deze typen beleid toewijzen via Azure Portal, PowerShell of Azure CLI.

Er zijn verschillende soorten beleidsregels in Azure Policy. Security Center gebruikt voornamelijk 'Audit'-beleidsregels die specifieke voorwaarden en configuraties controleren en vervolgens rapporteren over naleving. Er zijn ook beleidsregels 'Afdwingen' die kunnen worden gebruikt om beveiligde instellingen toe te passen.

## <a name="what-is-a-security-initiative"></a>Wat is een beveiligingsinitiatief?

Een Azure-initiatief is een verzameling Azure-beleidsdefinities of -regels die zijn gegroepeerd op een specifiek doel of doel. Azure-initiatieven vereenvoudigen het beheer van uw beleid door een reeks beleidsregels logisch te groeperen als één item.

Een beveiligingsinitiatief definieert de gewenste configuratie van uw workloads en helpt ervoor te zorgen dat u voldoet aan de beveiligingsvereisten van uw bedrijf of regelgeving.

Net als beveiligingsbeleid worden Security Center ook in de Azure Policy. U kunt [Azure Policy](../governance/policy/overview.md) om uw beleid te beheren, initiatieven te bouwen en initiatieven toe te wijzen aan meerdere abonnementen of voor hele beheergroepen.

Het standaardinitiatief dat automatisch wordt toegewezen aan elk abonnement in Azure Security Center is Azure Security Benchmark. Deze benchmark is de door Microsoft opgestelde, voor Azure specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingskaders. Deze breed gerespecteerde benchmark bouwt voort op de controles van het [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) met de focus op cloudgerichte beveiliging. Meer informatie over [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

Security Center biedt de volgende opties voor het werken met beveiligingsinitiatieven en -beleid:

- **Het ingebouwde** standaardinitiatief weergeven en bewerken: wanneer u Security Center inschakelen, wordt het initiatief met de naam 'Azure Security Benchmark' automatisch toegewezen aan alle Security Center geregistreerde abonnementen. Als u dit initiatief wilt aanpassen, kunt u afzonderlijke beleidsregels in het initiatief in- of uitschakelen door de parameters van een beleid te bewerken. Bekijk de lijst [met ingebouwde beveiligingsbeleidsregels](./policy-reference.md) voor meer informatie over de beschikbare opties.

- **Uw eigen aangepaste initiatieven toevoegen:** als u de beveiligingsinitiatieven wilt aanpassen die zijn toegepast op uw abonnement, kunt u dit binnen een Security Center. Vervolgens ontvangt u aanbevelingen als uw machines niet het beleid volgen dat u maakt. Zie Aangepaste beveiligingsinitiatieven en -beleid gebruiken voor instructies over het bouwen en toewijzen van [aangepaste beleidsregels.](custom-security-policies.md)

- **Nalevingsstandaarden** voor regelgeving toevoegen als initiatieven: het dashboard voor naleving van regelgeving van Security Center toont de status van alle evaluaties binnen uw omgeving in de context van een bepaalde standaard of regelgeving (zoals Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Zie Uw naleving van regelgeving [verbeteren voor meer informatie.](security-center-compliance-dashboard.md)

## <a name="what-is-a-security-recommendation"></a>Wat is een beveiligingsaanbeveling?

Met behulp van het beleid Security Center periodiek de nalevingsstatus van uw resources analyseren om mogelijke onjuiste beveiligingsconfiguraties en zwakke plekken te identificeren. Vervolgens krijgt u aanbevelingen voor het oplossen van deze problemen. Aanbevelingen zijn het resultaat van het beoordelen van uw resources op basis van het relevante beleid en het identificeren van resources die niet voldoen aan uw gedefinieerde vereisten.

Security Center doet beveiligingsaanbevelingen op basis van uw gekozen initiatieven. Wanneer een beleid van uw initiatief wordt vergeleken met uw resources en een of meer vindt die niet compatibel zijn, wordt het als een aanbeveling weergegeven in Security Center.

Aanbevelingen zijn acties die u moet ondernemen om uw resources te beveiligen en te beveiligen. Elke aanbeveling biedt u de volgende informatie:

- Een korte beschrijving van het probleem
- De herstelstappen die moeten worden uitgevoerd om de aanbeveling te implementeren
- De betrokken resources

In de praktijk werkt het als het volgende:

1. Azure Security Benchmark is een ***initiatief***
1. Het bevat een beleid ***om*** te vereisen dat alle Azure Storage-accounts de netwerktoegang beperken, zodat hun aanvalsoppervlak wordt verkleind. Dit beleid heet 'Opslagaccounts moeten netwerktoegang beperken met behulp van regels voor virtuele netwerken' en kan worden uitgeschakeld of ingeschakeld vanuit Azure Policy
1. Als Azure Security Center een Azure Storage-account vindt voor een van uw beveiligde abonnementen, worden deze accounts beoordeeld om te zien of ze zijn beveiligd met regels voor virtuele netwerken. Als dat niet het geval is, wordt een aanbeveling ***weergegeven om*** die situatie op te lossen en de beveiliging van deze resources te verbeteren. 

Een initiatief (1) bevat dus beleidsregels (2) die waar nodig aanbevelingen genereren (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>De relatie tussen een aanbeveling en een beleid weergeven

Zoals eerder vermeld, Security Center de ingebouwde aanbevelingen van de klant gebaseerd op de Azure Security-benchmark. Bijna elke aanbeveling heeft een onderliggend beleid dat is afgeleid van een vereiste in de benchmark.

Wanneer u de details van een aanbeveling bekijkt, is het vaak handig om het onderliggende beleid te kunnen zien. Voor elke aanbeveling die door  een beleid wordt ondersteund, gebruikt u de koppeling Beleidsdefinitie weergeven op de pagina met details van de aanbeveling om rechtstreeks naar de Azure Policy voor het relevante beleid te gaan:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Koppeling naar Azure Policy pagina voor het specifieke beleid dat een aanbeveling ondersteunt":::

Gebruik deze koppeling om de beleidsdefinitie weer te geven en de evaluatielogica te controleren. 

Als u de lijst met aanbevelingen in onze referentiehandleiding voor beveiligingsaanbevelingen [bekijkt,](recommendations-reference.md)ziet u ook koppelingen naar de pagina's met beleidsdefinitie:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Toegang tot de Azure Policy voor een specifiek beleid rechtstreeks vanaf de Azure Security Center aanbevelingen":::


## <a name="next-steps"></a>Volgende stappen

Op deze pagina worden op hoog niveau de basisconcepten en relaties tussen beleid, initiatieven en aanbevelingen uitgelegd. Zie voor verwante informatie:

- [Aangepaste initiatieven maken](custom-security-policies.md)
- [Beveiligingsbeleid uitschakelen om aanbevelingen uit te schakelen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Meer informatie over het bewerken van een beveiligingsbeleid in Azure Policy](../governance/policy/tutorials/create-and-manage.md)