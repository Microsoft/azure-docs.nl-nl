---
title: Meer informatie over beveiligings beleid, initiatieven en aanbevelingen in Azure Security Center
description: Meer informatie over beveiligings beleid, initiatieven en aanbevelingen in Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176430"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Wat zijn beveiligings beleid, initiatieven en aanbevelingen?

Security Center past beveiligings initiatieven toe op uw abonnementen. Deze initiatieven bevatten een of meer beveiligings beleidsregels. Elk van deze beleids regels resulteert in een beveiligings aanbeveling voor het verbeteren van uw beveiligings postuur. Op deze pagina vindt u meer informatie over deze ideeën.


## <a name="what-is-a-security-policy"></a>Wat is een beveiligings beleid?

Een Azure-beleids definitie, gemaakt in Azure Policy, is een regel voor specifieke beveiligings voorwaarden die u wilt beheren. Ingebouwde definities bevatten dingen zoals het beheren van het type resources dat kan worden geïmplementeerd of het gebruik van labels voor alle resources. U kunt ook uw eigen aangepaste beleids definities maken.

Als u deze beleids definities wilt implementeren (ongeacht of deze zijn ingebouwd of aangepast), moet u deze toewijzen. U kunt elk van deze typen beleid toewijzen via Azure Portal, PowerShell of Azure CLI.

Er zijn verschillende typen beleids regels in Azure Policy. Security Center hoofd zakelijk een controle beleid gebruikt dat specifieke voor waarden en configuraties controleert en vervolgens rapporteert op naleving. Er zijn ook beleid afdwingen dat kan worden gebruikt om beveiligde instellingen toe te passen.

## <a name="what-is-a-security-initiative"></a>Wat is een beveiligings initiatief?

Een Azure-initiatief is een verzameling van Azure-beleids definities of-regels die samen worden gegroepeerd op een specifiek doel of doel. Met Azure-initiatieven wordt het beheer van uw beleid vereenvoudigd door een set beleids regels samen, logisch, als één item te groeperen.

Een Security Initiative definieert de gewenste configuratie van uw workloads en zorgt ervoor dat u voldoet aan de beveiligings vereisten van uw bedrijf of regel.

Net als bij beveiligings beleid worden Security Center-initiatieven ook gemaakt in Azure Policy. U kunt [Azure Policy](../governance/policy/overview.md) gebruiken om uw beleid te beheren, initiatieven te bouwen en initiatieven toe te wijzen aan meerdere abonnementen of voor hele beheer groepen.

Het standaard initiatief dat automatisch wordt toegewezen aan elk abonnement in Azure Security Center is Azure Security Bench Mark. Deze bench Mark is de door micro soft ontworpen, specifieke set richt lijnen voor beveiliging en naleving aanbevolen procedures op basis van algemene nalevings kaders. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging. Meer informatie over [Azure Security Benchmark](../security/benchmarks/introduction.md).

Security Center biedt de volgende opties voor het werken met beveiligings initiatieven en-beleid:

- **Het ingebouwde standaard initiatief weer geven en bewerken** : wanneer u Security Center inschakelt, wordt de initiatief naam ' Azure Security Bench Mark ' automatisch toegewezen aan alle Security Center geregistreerde abonnementen. Als u dit initiatief wilt aanpassen, kunt u afzonderlijke beleids regels in-of uitschakelen door de para meters van een beleid te bewerken. Bekijk de lijst met [ingebouwde beveiligings beleidsregels](./policy-reference.md) om inzicht te krijgen in de beschik bare opties.

- **Uw eigen aangepaste initiatieven toevoegen** : als u de beveiligings initiatieven wilt aanpassen die op uw abonnement zijn toegepast, kunt u dit doen in Security Center. U ontvangt dan aanbevelingen als uw computers niet voldoen aan het beleid dat u maakt. Zie [aangepaste beveiligings initiatieven en-beleid gebruiken](custom-security-policies.md)voor instructies voor het maken en toewijzen van aangepaste beleids regels.

- **Normen voor naleving van regelgeving toevoegen als initiatieven** -Security Center het nalevings Dashboard van de regelgeving toont de status van alle evaluaties in uw omgeving in de context van een bepaalde standaard of verordening (zoals Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Zie voor meer informatie [uw reglementaire naleving verbeteren](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Wat is een beveiligings aanbeveling?

Met behulp van de beleids regels Security Center regel matig de nalevings status van uw resources analyseren om mogelijke beveiligings problemen en zwakke plekken te identificeren. Vervolgens krijgt u aanbevelingen voor het oplossen van deze problemen. Aanbevelingen zijn het resultaat van het evalueren van uw resources ten opzichte van het relevante beleid en het identificeren van resources die niet voldoen aan uw gedefinieerde vereisten.

Security Center zijn aanbevelingen voor beveiliging op basis van uw gekozen initiatieven. Wanneer een beleid van uw initiatief wordt vergeleken met uw resources en er een of meer wordt gevonden dat niet compatibel is, wordt dit weer gegeven als een aanbeveling in Security Center.

Aanbevelingen zijn acties die u kunt uitvoeren om uw resources te beveiligen en te beschermen. Elke aanbeveling biedt u de volgende informatie:

- Een korte beschrijving van het probleem
- De herstel stappen die moeten worden uitgevoerd om de aanbeveling te implementeren
- De betrokken resources

In de praktijk werkt dit als volgt:

1. Azure Security Bench Mark is een ***initiatief***
1. Het bevat een ***beleid*** dat alle Azure Storage accounts nodig heeft om netwerk toegang te beperken, zodat de kwets baarheid van het beveiligingslek wordt verminderd. Dit beleid heet ' opslag accounts moeten netwerk toegang beperken met regels voor het virtuele netwerk ' en kunnen worden in-Azure Policy uitgeschakeld of ingeschakeld.
1. Als Azure Security Center een Azure Storage-account vindt op een van uw beveiligde abonnementen, worden deze accounts beoordeeld om te zien of ze zijn beveiligd met regels voor het virtuele netwerk. Als dat niet het geval is, wordt er een ***aanbeveling*** weer gegeven om deze situatie op te lossen en de beveiliging van deze bronnen te beveiligen. 

Een initiatief (1) bevat daarom beleid (2) waarmee aanbevelingen worden gegenereerd, indien van toepassing (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>De relatie tussen een aanbeveling en een beleid weer geven

Zoals hierboven vermeld, zijn de ingebouwde aanbevelingen van Security Center gebaseerd op de Security Bench Mark van Azure. Bijna elke aanbeveling heeft een onderliggend beleid dat is afgeleid van een vereiste in de Bench Mark.

Wanneer u de details van een aanbeveling bekijkt, is het vaak handig om het onderliggende beleid te kunnen zien. Voor elke aanbeveling die wordt ondersteund door een beleid, gebruikt u de koppeling **beleids definitie weer geven** op de pagina aanbevelings Details om rechtstreeks naar het Azure Policy item voor het relevante beleid te gaan:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Koppeling naar Azure Policy pagina voor het specifieke beleid dat een aanbeveling ondersteunt":::

Gebruik deze koppeling om de beleids definitie weer te geven en de evaluatie logica te controleren. 

Als u de lijst met aanbevelingen in onze [Naslag Gids voor beveiligings aanbevelingen](recommendations-reference.md)bekijkt, ziet u ook koppelingen naar de pagina met beleids definities:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Toegang tot de Azure Policy-pagina voor een specifiek beleid rechtstreeks op de pagina met Azure Security Center aanbevelingen":::


## <a name="next-steps"></a>Volgende stappen

Op deze pagina worden op hoog niveau de basis concepten en relaties tussen beleid, initiatieven en aanbevelingen uitgelegd. Zie voor verwante informatie:

- [Aangepaste initiatieven maken](custom-security-policies.md)
- [Beveiligings beleid uitschakelen om aanbevelingen uit te scha kelen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Meer informatie over het bewerken van een beveiligings beleid in Azure Policy](../governance/policy/tutorials/create-and-manage.md)