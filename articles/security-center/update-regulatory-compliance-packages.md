---
title: Het dashboard voor naleving van regelgeving gebruiken in Azure Security Center
description: Informatie over het toevoegen en verwijderen van regelgevingsstandaarden vanuit het dashboard voor naleving van regelgeving in Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 4ba65517b698896c57ca2254394efcadd6efbb1d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835034"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>De set standaarden aanpassen in uw dashboard voor naleving van regelgeving

Azure Security Center vergelijkt voortdurend de configuratie van uw resources met de vereisten in industriestandaarden, voorschriften en benchmarks. Het **dashboard voor naleving van regelgeving** biedt inzicht in uw nalevingsstatus op basis van hoe u voldoet aan specifieke nalevingsvereisten.

> [!TIP]
> Meer informatie over Security Center dashboard voor naleving van regelgeving in de [veelgestelde vragen](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hoe worden nalevingsstandaarden voor regelgeving weergegeven in Security Center?

Industriestandaarden, regelgevingsstandaarden en benchmarks worden weergegeven in Security Center dashboard voor naleving van regelgeving. Elke standaard is een initiatief dat is gedefinieerd in Azure Policy.

Als u in uw dashboard nalevingsgegevens wilt zien die als evaluaties zijn gebruikt, voegt u een nalevingsstandaard toe aan uw beheergroep of abonnement op de **pagina Beveiligingsbeleid.** Zie Werken met beveiligingsbeleid Azure Policy meer informatie over Azure Policy [en initiatieven.](tutorial-security-policy.md)

Wanneer u een standaard of benchmark hebt toegewezen aan uw geselecteerde bereik, wordt de standaard weergegeven in uw dashboard voor naleving van regelgeving met alle bijbehorende nalevingsgegevens die zijn toegewezen als evaluaties. U kunt ook samenvattingsrapporten downloaden voor alle toegewezen standaarden.

Microsoft houdt de regelgevingsstandaarden zelf bij en verbetert automatisch de dekking in sommige pakketten gedurende een bepaalde periode. Wanneer Microsoft nieuwe inhoud voor het initiatief publiceert, wordt deze automatisch in uw dashboard weergegeven als nieuw beleid dat is toe te passen aan besturingselementen in de standaard.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Welke nalevingsstandaarden voor regelgeving zijn beschikbaar in Security Center?

Standaard is aan elk abonnement de **Azure Security-benchmark** toegewezen. Dit zijn de door Microsoft opgestelde, azure-specifieke richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingskaders. [Meer informatie over Azure Security-benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

U kunt ook standaarden toevoegen, zoals:

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- UK Official en UK NHS
- Canada Federal PBMM
- Azure CIS 1.3.0
- CMMC-niveau 3
- Nieuw-Zeeland ISM beperkt

Standaarden worden toegevoegd aan het dashboard zodra ze beschikbaar komen.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Een regelgevingsstandaard toevoegen aan uw dashboard

In de volgende stappen wordt uitgelegd hoe u een pakket toevoegt om uw naleving te bewaken met een van de ondersteunde regelgevingsstandaarden.

> [!NOTE]
> Als u standaarden wilt toevoegen aan uw dashboard, moet het abonnement Azure Defender ingeschakeld. Bovendien hebben alleen gebruikers die eigenaar of beleidsbijdrager zijn de benodigde machtigingen om nalevingsstandaarden toe te voegen. 

1. Selecteer Security Center de zijbalk van de Security Center de optie **Naleving van regelgeving om** het dashboard voor naleving van regelgeving te openen. Hier ziet u de nalevingsstandaarden die momenteel zijn toegewezen aan de geselecteerde abonnementen.   

1. Selecteer bovenaan de pagina Nalevingsbeleid **beheren.** De pagina Beleidsbeheer wordt weergegeven.

1. Selecteer het abonnement of de beheergroep waarvoor u de nalevingsstatus van regelgeving wilt beheren. 

    > [!TIP]
    > We raden u aan het hoogste bereik te selecteren waarvoor de standaard van toepassing is, zodat nalevingsgegevens worden geaggregeerd en bij te houden voor alle geneste resources. 

1. Als u de standaarden wilt toevoegen die relevant zijn voor uw organisatie, klikt **u op Meer standaarden toevoegen.** 

1. Op de **pagina Nalevingsstandaarden voor regelgeving** toevoegen kunt u zoeken naar een van de beschikbare standaarden, waaronder:

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO en UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC-niveau 3**
    - **Nieuw-Zeelandse ISM beperkt**
    
    ![Regelgevingsstandaarden toevoegen aan Azure Security Center dashboard voor naleving van regelgeving](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selecteer **Toevoegen** en voer alle benodigde gegevens in voor het specifieke initiatief, zoals bereik, parameters en herstel.

1. Selecteer Security Center de zijbalk opnieuw  Naleving van regelgeving om terug te gaan naar het dashboard voor naleving van regelgeving.

    Uw nieuwe standaard wordt weergegeven in de lijst met industriestandaarden & regelgeving. 

    > [!NOTE]
    > Het kan enkele uren duren voordat een nieuwe standaard wordt weergegeven in het nalevingsdashboard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard voor naleving van regelgeving" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Een standaard verwijderen uit uw dashboard

Als een van de opgegeven regelgevingsstandaarden niet relevant is voor uw organisatie, is het een eenvoudig proces om ze uit de gebruikersinterface te verwijderen. Hiermee kunt u het dashboard voor naleving van regelgeving verder aanpassen en u richten op de standaarden die op u van toepassing zijn.

Een standaard verwijderen:

1. Selecteer Security Center beveiligingsbeleid in het **menu van de Security Center.**

1. Selecteer het relevante abonnement waarvan u een standaard wilt verwijderen.

    > [!NOTE]
    > U kunt een standaard verwijderen uit een abonnement, maar niet uit een beheergroep. 

    De pagina beveiligingsbeleid wordt geopend. Voor het geselecteerde abonnement worden het standaardbeleid, de industrie- en regelgevingsstandaarden en eventuele aangepaste initiatieven weergegeven die u hebt gemaakt.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Een regelgevingsstandaard verwijderen uit uw dashboard voor naleving van regelgeving in Azure Security Center":::

1. Selecteer Uitschakelen voor de standaard die u **wilt verwijderen.** Er wordt een bevestigingsvenster weergegeven.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Bevestig dat u de regelgevingsstandaard die u hebt geselecteerd, echt wilt verwijderen":::

1. Selecteer **Ja**. De standaard wordt verwijderd. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u **nalevingsstandaarden kunt toevoegen om** uw naleving met regelgevings- en branchestandaarden te bewaken.

Zie de volgende pagina's voor gerelateerd materiaal:

- [Azure Security-benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Dashboard voor naleving van regelgeving in Security Center:](security-center-compliance-dashboard.md) informatie over het bijhouden en exporteren van uw nalevingsgegevens voor regelgeving met Security Center en externe hulpprogramma's
- [Werken met beveiligingsbeleid](tutorial-security-policy.md)