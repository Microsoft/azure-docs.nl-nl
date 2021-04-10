---
title: Het nalevings dashboard voor regelgeving gebruiken in Azure Security Center
description: Meer informatie over het toevoegen en verwijderen van reglementaire normen van het regelgevings dashboard voor naleving in Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 768f686889663d9b1af4b88d84b361ac9460a5a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381729"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>De set van standaarden aanpassen in het dash board nalevings regelgeving

Azure Security Center vergelijkt voortdurend de configuratie van uw resources met vereisten in de industrie normen,-voor schriften en-benchmarks. Het **nalevings dashboard** van de regelgeving biedt inzicht in uw nalevings postuur op basis van de manier waarop u aan specifieke nalevings vereisten voldoet.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Hoe worden nalevings normen voor regelgeving weer gegeven in Security Center?

Industrie normen, reglementaire normen en benchmarks worden weer gegeven in het dash board voor naleving van de regelgeving van de Security Center. Elke standaard is een initiatief dat is gedefinieerd in Azure Policy.

Als u de nalevings gegevens wilt zien die zijn toegewezen als beoordelingen in uw dash board, voegt u een nalevings standaard toe aan uw beheer groep of abonnement vanuit de pagina **beveiligings beleid** . Zie [werken met beveiligings beleid](tutorial-security-policy.md)voor meer informatie over Azure Policy en initiatieven.

Wanneer u een Standard-of Bench Mark aan het geselecteerde bereik hebt toegewezen, wordt de standaard weer gegeven in het dash board nalevings beleid met alle gekoppelde nalevings gegevens die zijn toegewezen als evaluaties. U kunt ook samenvattings rapporten downloaden voor de standaarden die zijn toegewezen.

Micro soft houdt zelf de regelgevings normen bij en verbetert de dekking van een aantal pakketten in de loop van de tijd. Wanneer micro soft nieuwe inhoud voor het initiatief uitgeeft, wordt deze automatisch in het dash board weer gegeven als nieuw beleid dat is toegewezen aan besturings elementen in de standaard.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Welke normen voor naleving van regelgeving zijn beschikbaar in Security Center?

Aan elk abonnement is standaard de **Azure Security-benchmark** waarde toegewezen. Dit zijn de door micro soft ontworpen, specifieke Azure-richt lijnen voor de aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevings kaders. [Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

U kunt ook standaarden toevoegen zoals:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- UK-officiële en UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Standaarden worden toegevoegd aan het dash board zodra deze beschikbaar komen.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Een regelgevings norm toevoegen aan uw dash board

In de volgende stappen wordt uitgelegd hoe u een pakket kunt toevoegen om te controleren of u voldoet aan een van de ondersteunde regelgevings standaarden.

> [!NOTE]
> Als u standaarden wilt toevoegen aan uw dash board, moet Azure Defender zijn ingeschakeld voor het abonnement. Daarnaast hebben alleen gebruikers die eigenaar of Inzender zijn de benodigde machtigingen om nalevings standaarden toe te voegen. 

1. Selecteer op de zijbalk van Security Center **reglementaire naleving** om het nalevings dashboard voor regelgeving te openen. Hier kunt u de nalevings standaarden zien die momenteel zijn toegewezen aan de momenteel geselecteerde abonnementen.   

1. Selecteer aan de bovenkant van de pagina **nalevings beleid beheren**. De pagina beleids beheer wordt weer gegeven.

1. Selecteer het abonnement of de beheer groep waarvoor u de nalevings postuur wilt beheren. 

    > [!TIP]
    > U kunt het beste het hoogste bereik selecteren waarvoor de standaard geldt, zodat de nalevings gegevens worden geaggregeerd en bijgehouden voor alle geneste resources. 

1. Als u de standaarden wilt toevoegen die relevant zijn voor uw organisatie, klikt u op **meer standaarden toevoegen**. 

1. Op de pagina **nalevings normen voor regelgeving toevoegen** kunt u zoeken naar een van de beschik bare standaarden, waaronder:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO en UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Reglementaire normen toevoegen aan het nalevings Dashboard van het regelgevend Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Selecteer **toevoegen** en voer alle benodigde gegevens in voor het specifieke initiatief, zoals Scope, para meters en herstel.

1. Selecteer op de zijbalk van Security Center opnieuw **regelgevende naleving** om terug te gaan naar het dash board voor nalevings vereisten.

    Uw nieuwe standaard wordt weer gegeven in uw lijst met industrie & reglementaire normen. 

    > [!NOTE]
    > Het kan enkele uren duren voordat een nieuw toegevoegde standaard wordt weer gegeven in het dash board voor compatibiliteit.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard voor naleving van regelgeving" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Een standaard uit uw dash board verwijderen

Als een van de opgegeven regelgevings normen niet relevant is voor uw organisatie, is het een eenvoudig proces om ze te verwijderen uit de gebruikers interface. Zo kunt u het nalevings Dashboard van de regelgeving verder aanpassen en zich richten op de standaarden die van toepassing zijn op u.

Een standaard verwijderen:

1. Selecteer in het menu van Security Center het **beveiligings beleid**.

1. Selecteer het relevante abonnement waarvan u een standaard wilt verwijderen.

    > [!NOTE]
    > U kunt een standaard van een abonnement verwijderen, maar niet uit een beheer groep. 

    De pagina beveiligings beleid wordt geopend. Voor het geselecteerde abonnement worden het standaard beleid, de industrie-en regelgevings normen en eventuele aangepaste initiatieven weer gegeven die u hebt gemaakt.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Het verwijderen van een regelgevings standaard van uw regelgevings dashboard voor naleving in Azure Security Center":::

1. Selecteer **uitschakelen** voor de standaard die u wilt verwijderen. Er wordt een bevestigings venster weer gegeven.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Bevestig dat u de geselecteerde regelgevings standaard wilt verwijderen":::

1. Selecteer **Ja**. De standaard wordt verwijderd. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u **nalevings normen kunt toevoegen** om uw naleving met reglementaire en industrie normen te bewaken.

Zie de volgende pagina's voor gerelateerde materialen:

- [Azure Security-benchmark](../security/benchmarks/introduction.md)
- [Security Center compliance-nalevings dashboard](security-center-compliance-dashboard.md) : informatie over het volgen en exporteren van uw nalevings gegevens met Security Center en externe hulpprogram ma's
- [Werken met beveiligingsbeleid](tutorial-security-policy.md)