---
title: Werken met beveiligings beleid | Microsoft Docs
description: In dit artikel wordt beschreven hoe u kunt werken met beveiligings beleid in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 19128f0372f9a5bda0d16155167a507eccaf436a
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986587"
---
# <a name="manage-security-policies"></a>Beveiligingsbeleid beheren

In dit artikel wordt uitgelegd hoe beveiligings beleid wordt geconfigureerd en hoe u deze in Security Center kunt weer geven. 

## <a name="introduction-to-security-policies"></a>Inleiding tot beveiligings beleid

Een beveiligings beleid definieert de gewenste configuratie van uw workloads en zorgt ervoor dat u voldoet aan de beveiligings vereisten van uw bedrijf of regel.

Azure Security Center worden de beveiligings aanbevelingen gebaseerd op uw gekozen beleid. Security Center beleid is gebaseerd op beleids initiatieven die zijn gemaakt in Azure Policy. U kunt [Azure Policy](../governance/policy/overview.md) gebruiken om uw beleid te beheren en beleid in te stellen tussen beheer groepen en meerdere abonnementen.

Security Center biedt de volgende opties voor het werken met beveiligings beleid:

* **Het ingebouwde standaard beleid weer geven en bewerken** : wanneer u Security Center inschakelt, wordt de initiatief naam ' Azure Security Bench Mark ' automatisch toegewezen aan alle Security Center geregistreerde abonnementen. Als u dit initiatief wilt aanpassen, kunt u afzonderlijk beleids regels in-of uitschakelen. Bekijk de lijst met [ingebouwde beveiligings beleidsregels](./policy-reference.md) om inzicht te krijgen in de beschik bare opties.

* **Uw eigen aangepaste beleids regels toevoegen** : als u de beveiligings initiatieven wilt aanpassen die op uw abonnement zijn toegepast, kunt u dit doen in Security Center. U ontvangt dan aanbevelingen als uw computers niet voldoen aan het beleid dat u maakt. Zie [aangepaste beveiligings beleidsregels gebruiken](custom-security-policies.md)voor instructies voor het maken en toewijzen van aangepaste beleids regels.

* **Nalevings beleid voor regelgeving toevoegen** : het nalevings Dashboard van de Security Center is de status van alle evaluaties in uw omgeving in de context van een bepaalde standaard of regelgeving (zoals Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Zie voor meer informatie [uw reglementaire naleving verbeteren](security-center-compliance-dashboard.md).


## <a name="manage-your-security-policies"></a>Uw beveiligings beleid beheren

Ga als volgt te werk als u uw beveiligingsbeleidsregels wilt weergeven in Security Center:

1. Selecteer in het **Security Center** -dash board **beveiligings beleid**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="De pagina beleids beheer":::

   In het scherm **beleids beheer** ziet u het aantal beheer groepen, abonnementen en werk ruimten, evenals de structuur van uw beheer groep.

1. Selecteer het abonnement of de beheer groep waarvan u het beleid wilt weer geven.

1. De pagina beveiligings beleid voor het abonnement of de beheer groep wordt weer gegeven. Het beschik bare en toegewezen beleid wordt weer gegeven.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Pagina beveiligings beleid van Security Center" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Als er naast het standaard beleid een label "MG inherited" is, betekent dit dat het beleid is toegewezen aan een beheer groep en dat is overgenomen door het abonnement dat u bekijkt.


1. Kies uit de beschik bare opties op deze pagina:

    1. Als u wilt werken met beleids regels voor de branche, selecteert u **meer standaarden toevoegen**. Zie [bijwerken naar dynamische nalevings pakketten](update-regulatory-compliance-packages.md)voor meer informatie.

    1. Als u aangepaste initiatieven wilt toewijzen en beheren, selecteert u **aangepaste initiatieven toevoegen**. Zie [aangepaste beveiligings beleidsregels gebruiken](custom-security-policies.md)voor meer informatie.

    1. Als u het standaard beleid wilt weer geven en bewerken, selecteert u **effectief beleid weer geven** en gaat u verder, zoals hieronder wordt beschreven. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Scherm effectief beleid":::

       Dit scherm van het **beveiligings beleid** toont de actie die wordt uitgevoerd door het beleid dat is toegewezen aan het abonnement of de beheer groep die u hebt geselecteerd.
       
       * Gebruik de koppelingen aan de bovenkant om een beleids **toewijzing** te openen die van toepassing is op het abonnement of de beheer groep. Met deze koppelingen krijgt u toegang tot de toewijzing en kunt u het beleid bewerken of uitschakelen. Als u bijvoorbeeld ziet dat een bepaalde beleids toewijzing de eindpunt beveiliging effectief weigert, gebruikt u de koppeling om het beleid te bewerken of uit te scha kelen.
       
       * In de lijst met beleids regels kunt u de juiste toepassing van het beleid voor uw abonnement of beheer groep bekijken. De instellingen van elk beleid dat van toepassing is op het bereik, worden in aanmerking genomen en het cumulatieve resultaat van de acties die door het beleid worden uitgevoerd, wordt weer gegeven. Als bijvoorbeeld in één toewijzing van het beleid is uitgeschakeld, maar in een andere methode is ingesteld op AuditIfNotExist, wordt met het cumulatieve effect AuditIfNotExist toegepast. Het meer actieve effect heeft altijd voor rang.
       
       * Het effect van het beleid kan zijn: Append, audit, AuditIfNotExists, deny, DeployIfNotExists, disabled. Zie [beleids effecten begrijpen](../governance/policy/concepts/effects.md)voor meer informatie over hoe effecten worden toegepast.

       > [!NOTE]
       > Wanneer u toegewezen beleids regels bekijkt, kunt u meerdere toewijzingen zien en kunt u zien hoe elke toewijzing zelf is geconfigureerd.


## <a name="who-can-edit-security-policies"></a>Wie kan beveiligings beleid bewerken?

U kunt beveiligings beleid via de Azure Policy Portal bewerken via REST API of met behulp van Windows Power shell.

Security Center maakt gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer), dat ingebouwde rollen biedt die u kunt toewijzen aan Azure-gebruikers,-groepen en-services. Wanneer gebruikers Security Center openen, zien ze alleen informatie met betrekking tot de resources waartoe ze toegang hebben. Dit betekent dat gebruikers de rol van *eigenaar*, *bijdrager* of *lezer* aan het abonnement van de resource krijgen toegewezen. Er zijn ook twee specifieke Security Center rollen:

- **Beveiligings lezer**: heeft rechten voor het weer geven van Security Center items, zoals aanbevelingen, waarschuwingen, beleid en status. Kan geen wijzigingen aanbrengen.
- **Beveiligings beheerder**: heeft dezelfde weergave rechten als de *beveiligings lezer*. Kan het beveiligings beleid ook bijwerken en waarschuwingen negeren.


## <a name="disable-security-policies-and-disable-recommendations"></a>Beveiligings beleid uitschakelen en aanbevelingen uitschakelen

Wanneer uw Security Initiative een aanbeveling activeert die irrelevant is voor uw omgeving, kunt u voor komen dat de aanbeveling weer wordt weer gegeven. Als u een aanbeveling wilt uitschakelen, schakelt u het specifieke beleid uit waarmee de aanbeveling wordt gegenereerd.

De aanbeveling die u wilt uitschakelen, wordt nog steeds weer gegeven als deze is vereist voor een regelgevings norm die u hebt toegepast met de nalevings tools van de Security Center. Zelfs als u een beleid hebt uitgeschakeld in het ingebouwde initiatief, wordt de aanbeveling door een beleid in het regelgevings standaard initiatief nog steeds geactiveerd als dit nodig is voor naleving. U kunt beleids regels niet uitschakelen op basis van regelgevende standaard initiatieven.

Zie [beveiligings aanbevelingen beheren](security-center-recommendations.md)voor meer informatie over aanbevelingen.

1. Selecteer in Security Center in het gedeelte **beleids & naleving** de optie **beveiligings beleid**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Het proces voor beleids beheer in Azure Security Center starten":::

2. Selecteer het abonnement of de beheer groep waarvoor u de aanbeveling wilt uitschakelen.

   > [!NOTE]
   > Houd er rekening mee dat in een beheergroep het beleid wordt toepast op alle abonnementen. Als u dus het beleid van een abonnement uitschakelt en het abonnement behoort tot een beheergroep waarin hetzelfde beleid wordt gebruikt, blijft u de beleidsaanbevelingen ontvangen. Het beleid wordt nog steeds toegepast op beheerniveau en de aanbevelingen worden nog steeds gegenereerd.

1. Selecteer **effectief beleid weer geven**.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="Het effectief beleid openen dat is toegewezen aan uw abonnement":::

1. Selecteer het toegewezen beleid.

   ![beleid selecteren](./media/tutorial-security-policy/security-policy.png)

1. Zoek in de sectie **para meters** naar het beleid dat de aanbeveling aanroept die u wilt uitschakelen, en selecteer in de vervolg keuzelijst de optie **uitgeschakeld**

   ![beleid uitschakelen](./media/tutorial-security-policy/disable-policy.png)

1. Selecteer **Opslaan**.

   > [!NOTE]
   > Het kan tot 12 uur duren voordat de wijzigingen van het beleid uitschakelen zijn doorgevoerd.

## <a name="next-steps"></a>Volgende stappen
Op deze pagina wordt het beveiligings beleid uitgelegd. Zie de volgende pagina's voor verwante informatie:

- [Meer informatie over het instellen van beleid met behulp van Power shell](../governance/policy/assign-policy-powershell.md) - 
- [Meer informatie over het bewerken van een beveiligings beleid in Azure Policy](../governance/policy/tutorials/create-and-manage.md) - 
- [Meer informatie over het instellen van een beleid in abonnementen of op beheer groepen met behulp van Azure Policy](../governance/policy/overview.md).
- [Meer informatie over het inschakelen van Security Center voor alle abonnementen in een beheer groep](onboard-management-group.md)