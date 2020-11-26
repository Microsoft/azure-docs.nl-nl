---
title: Identiteit en toegang bewaken in Azure Security Center | Microsoft Docs
description: Lees hoe u de functies voor identiteit en toegang in Azure Security Center gebruikt om problemen met de toegang en identiteit van uw gebruikers te bewaken.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: b36f52fd97a9f7ff294f14ec394ff9a9f607b5b0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187150"
---
# <a name="monitor-identity-and-access"></a>Identiteit en toegang bewaken

De beveiligingsperimeter is ontwikkeld van een netwerkperimeter naar een identiteitsperimeter. Met deze ontwikkeling is de beveiliging minder dan het beschermen van uw netwerk en meer informatie over het beheren van de beveiliging van uw apps, gegevens en gebruikers.

Door de activiteiten en configuratie-instellingen die betrekking hebben op identiteit te bewaken, kunt u proactieve acties uitvoeren voordat een incident plaatsvindt of reactieve acties om pogingen tot aanvallen te stoppen.

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>Welke identiteits-en toegangs beveiliging bieden Security Center? 

Azure Security Center heeft twee specifieke beveiligings controles om ervoor te zorgen dat u voldoet aan de identiteits-en beveiligings vereisten van uw organisatie: 

 - **Toegang en machtigingen beheren** : we raden u aan het [toegangs model voor de minimale bevoegdheid te gebruiken](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) en ervoor te zorgen dat u uw gebruikers alleen de toegang verleent die nodig zijn om hun taken uit te voeren. Dit besturings element bevat ook aanbevelingen voor het implementeren van [op rollen gebaseerd toegangs beheer van Azure (Azure RBAC)](../role-based-access-control/overview.md) voor het beheren van de toegang tot uw resources.
 
 - **Schakel MFA** in als u [MFA](https://www.microsoft.com/security/business/identity/mfa) hebt ingeschakeld, uw accounts veilig zijn en gebruikers nog steeds kunnen worden geverifieerd bij vrijwel elke toepassing met eenmalige aanmelding.

### <a name="example-recommendations-for-identity-and-access"></a>Voor beelden van aanbevelingen voor identiteit en toegang

Voor beelden van aanbevelingen die u kunt zien in deze twee besturings elementen op de pagina met **aanbevelingen** van Security Center:

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement
- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement
- Afgeschafte accounts moeten worden verwijderd uit uw abonnement (afgeschafte accounts zijn accounts die niet meer nodig zijn, en zijn geblokkeerd voor het aanmelden door Azure Active Directory)

> [!TIP]
> Zie [aanbevelingen voor identiteits-en toegangs rechten](recommendations-reference.md#recs-identity)voor meer informatie over deze aanbevelingen en andere in deze besturings elementen.

### <a name="limitations"></a>Beperkingen

Er zijn enkele beperkingen ten aanzien van de identiteits-en toegangs beveiliging van Security Center:

- Er zijn geen aanbevelingen voor identiteiten beschikbaar voor abonnementen met meer dan 600 accounts. In dergelijke gevallen worden deze aanbevelingen vermeld onder "niet-beschik bare evaluaties".
- Er zijn geen aanbevelingen voor identiteiten beschikbaar voor de beheer agenten van de Cloud Solution Provider (CSP)-partner.
- Met identiteits aanbevelingen worden geen accounts geïdentificeerd die worden beheerd met een systeem voor privileged Identity Management (PIM). Als u een PIM-hulp programma gebruikt, ziet u mogelijk onnauwkeurige resultaten in het beheer van **toegang en machtigingen beheren** .

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>Multi-factor Authentication (MFA) en Azure Active Directory 

Voor het inschakelen van MFA zijn [Tenant machtigingen voor Azure Active Directory (AD)](../active-directory/roles/permissions-reference.md)vereist.

- Als u een Premium-editie van AD hebt, schakelt u MFA in met behulp van [voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md).
- Als u de gratis versie van AD gebruikt, schakelt u de **standaard instellingen voor beveiliging** in, zoals beschreven in [Azure Active Directory documentatie](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>Accounts identificeren waarvoor MFA (multi-factor Authentication) is ingeschakeld

Als u wilt zien voor welke accounts geen MFA is ingeschakeld, gebruikt u de volgende Azure resource Graph-query. De query retourneert alle beschadigde resources-accounts-van de aanbeveling ' MFA moet worden ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement '. 

1. Open **Azure resource Graph Explorer**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="De pagina aanbeveling van Azure resource Graph Explorer * * wordt gestart" :::

1. Voer de volgende query in en selecteer **query uitvoeren**.

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. De `additionalData` eigenschap toont de lijst met account-object-id's voor accounts waarvoor MFA niet is afgedwongen. 

    > [!NOTE]
    > De accounts worden weer gegeven als object-Id's in plaats van account namen om de privacy van de account houders te beveiligen.

> [!TIP]
> U kunt ook de evaluaties van de methode REST API van Security Center gebruiken [-ophalen](/rest/api/securitycenter/assessments/get).


## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:

- [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)