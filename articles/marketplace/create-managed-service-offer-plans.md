---
title: Plannen maken voor uw beheerde service aanbod op Azure Marketplace
description: Meer informatie over het maken van plannen voor uw beheerde service aanbod op Azure Marketplace met behulp van micro soft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 624a3a194b451dc1f498a0ded9d68f641b304eab
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918285"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Plannen maken voor uw beheerde service-aanbieding

Aanbiedingen voor beheerde services die via micro soft Commercial Marketplace worden verkocht, moeten ten minste één abonnement hebben. U kunt diverse plannen maken met verschillende opties binnen dezelfde aanbieding. Deze plannen (ook wel Sku's genoemd) kunnen verschillen qua versie, verdiensten maximaliseren of service lagen. Zie [Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen](./plans-pricing.md)voor gedetailleerde richt lijnen voor abonnementen.

## <a name="create-a-plan"></a>Een plan maken

1. Selecteer **+ nieuw plan maken** op het tabblad **overzicht van plannen** van uw aanbieding in partner centrum.
2. Voer in het dialoog venster dat wordt weer gegeven onder **plan-id** een unieke plan-id in. Gebruik Maxi maal 50 kleine letters, streepjes of onderstrepings tekens. U kunt de plan-ID niet wijzigen nadat u **maken** hebt geselecteerd. Deze ID is zichtbaar voor uw klanten.
3. Voer in het vak **naam van abonnement** een unieke naam in voor dit abonnement. Gebruik Maxi maal 50 tekens. Deze naam is zichtbaar voor uw klanten.
4. Selecteer **Maken**.

## <a name="define-the-plan-listing"></a>De plan vermelding definiëren

Op het tabblad **plan vermelding** definieert u de naam en beschrijving van het abonnement zoals u wilt dat deze in de commerciële Marketplace worden weer gegeven.

1. In het vak **plan naam** wordt de naam weer gegeven die u eerder hebt ingesteld voor dit plan. U kunt deze op elk gewenst moment wijzigen. Deze naam wordt weer gegeven in de commerciële Marketplace als de titel van het abonnement van uw aanbieding.
2. Geef in het vak **samen vatting van abonnement** een korte beschrijving van uw plan op, dat kan worden gebruikt in Zoek resultaten voor Marketplace.
3. Geef in het vak **plan beschrijving** op hoe dit plan uniek is en verschilt van andere abonnementen binnen uw aanbieding.
4. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad.

## <a name="define-pricing-and-availability"></a>Prijzen en beschik baarheid definiëren

Het enige prijs model dat beschikbaar is voor aanbiedingen voor beheerde services is **Bring your own License (BYOL)**. Dit betekent dat u uw klanten rechtstreeks factureert voor kosten met betrekking tot deze aanbieding, en micro soft brengt u geen kosten in rekening.

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen (openbaar) of alleen voor een specifieke doel groep (privé).

> [!NOTE]
> Privé plannen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het programma van de Cloud Solution Provider (CSP).

> [!IMPORTANT]
> Zodra een plan als openbaar is gepubliceerd, kunt u het niet wijzigen in persoonlijk. Gebruik een privé abonnement om te bepalen welke klanten uw aanbieding mogen accepteren en resources kunnen delegeren. Met een openbaar abonnement kunt u de beschik baarheid van bepaalde klanten of zelfs voor een bepaald aantal klanten niet beperken (hoewel u ervoor kiest om het abonnement niet volledig te verkopen). U kunt de toegang tot een overdracht verwijderen nadat een klant alleen een aanbieding heeft geaccepteerd als u een autorisatie hebt opgenomen waarbij de roldefinitie is ingesteld op de functie voor het verwijderen van de registratie toewijzing van beheerde services tijdens het publiceren van de aanbieding. U kunt ook contact opnemen met de klant en vragen om uw toegang te verwijderen.

## <a name="make-your-plan-public"></a>Uw plan openbaar maken

1. Selecteer **openbaar** onder **plan zicht baarheid**.
2. Selecteer **Concept opslaan**. Als u wilt terugkeren naar het tabblad Overzicht van het plan, selecteert u **overzicht van plan** in de linkerbovenhoek.
3. Als u een ander abonnement voor deze aanbieding wilt maken, selecteert u **+ nieuw plan maken** op het tabblad Overzicht van het **plan** .

## <a name="make-your-plan-private"></a>Uw abonnement privé maken

U verleent toegang tot een persoonlijk abonnement met behulp van Azure-abonnement-Id's. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 10.000 abonnement-Id's toevoegen met behulp van een. CSV-bestand.

Om Maxi maal 10 abonnements-Id's hand matig toe te voegen:

1. Selecteer **privé** onder **plan zicht baarheid**.
2. Voer de ID van het Azure-abonnement in van de doel groep waaraan u toegang wilt verlenen.
3. Voer desgewenst een beschrijving van deze doel groep in het vak **Beschrijving** in.
4. Als u nog een ID wilt toevoegen, selecteert u **toevoegen id (Maxi maal 10)**.
5. Wanneer u klaar bent met het toevoegen van Id's, selecteert u **concept opslaan**.

Om Maxi maal 10.000 abonnement-Id's toe te voegen aan een. CSV-bestand:

1. Selecteer **privé** onder **plan zicht baarheid**.
2. Selecteer de koppeling **doel groep exporteren (CSV)** . Hiermee wordt een gedownload. CSV-bestand.
3. Open de. CSV-bestand. Voer in de kolom id de Azure **-abonnements-** id's in waaraan u toegang wilt verlenen.
4. In de kolom **Beschrijving**   hebt u de optie om een beschrijving voor elk item toe te voegen.
5. In de ****   kolom Type voegt u **SubscriptionId** toe   aan elke rij met een id.
6. Sla het bestand op als een. CSV-bestand.
7. Selecteer in partner centrum de koppeling **doel groep importeren (CSV)** .
8.  ****   Selecteer **Ja** in het dialoog venster bevestigen en upload het. CSV-bestand.
9. Selecteer **concept opslaan**.

## <a name="technical-configuration"></a>Technische configuratie

In deze sectie wordt een manifest gemaakt met autorisatie-informatie voor het beheren van klant resources. Deze informatie is vereist om [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md)in te scha kelen.

Bekijk [tenants, rollen en gebruikers in azure Lighthouse-scenario's](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) om te begrijpen welke rollen worden ondersteund en wat de aanbevolen procedures zijn voor het definiëren van uw autorisaties.

> [!NOTE]
> De gebruikers en rollen in uw autorisatie vermeldingen worden toegepast op elke klant die het plan activeert. Als u de toegang tot een specifieke klant wilt beperken, moet u een privé-abonnement publiceren voor hun exclusieve gebruik.

### <a name="manifest"></a>Manifest

1. Geef onder **manifest** een **versie** op voor het manifest. Gebruik de indeling n. n. n (bijvoorbeeld 1.2.5).
2. Voer uw **Tenant-id** in. Dit is een GUID die is gekoppeld aan de Tenant-ID van de Azure Active Directory (Azure AD) van uw organisatie. dat wil zeggen, de Tenant beheren van waaruit u de resources van uw klanten wilt openen. Als u dit niet hebt, kunt u het vinden door over te grenzen van de account naam in de rechter bovenhoek van de Azure Portal of door te klikken op **overschakelen naar een andere map**.

Als u een nieuwe versie van uw aanbieding publiceert en een bijgewerkt manifest moet maken, selecteert u **+ Nieuw manifest**. Zorg ervoor dat u het versie nummer van de vorige manifest versie verhoogt.

### <a name="authorizations"></a>Autorisaties

Autorisaties definiëren de entiteiten in uw Tenant beheren die toegang hebben tot resources en abonnementen voor klanten die het abonnement hebben gekocht. Aan elk van deze entiteiten is een ingebouwde rol toegewezen waarmee specifieke toegangs niveaus worden verleend.

U kunt Maxi maal 20 autorisaties voor elk abonnement maken.

> [!TIP]
> In de meeste gevallen moet u rollen toewijzen aan een Azure AD-gebruikers groep of Service-Principal, in plaats van aan een reeks afzonderlijke gebruikers accounts. Hiermee kunt u toegang voor afzonderlijke gebruikers toevoegen of verwijderen zonder dat u het plan hoeft bij te werken en opnieuw te publiceren wanneer uw toegangs vereisten veranderen. Bij het toewijzen van rollen aan Azure AD-groepen [moet het groeps type Security en niet Office 365 zijn](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Zie voor aanvullende aanbevelingen [tenants, rollen en gebruikers in azure Lighthouse-scenario's](../lighthouse/concepts/tenants-users-roles.md).

Voor elke autorisatie moet u de volgende informatie opgeven. U kunt vervolgens en zo vaak als nodig **autorisaties toevoegen** om meer gebruikers en roldefinities toe te voegen.

* **Aad-object-id**: de Azure ad-id van een gebruiker, gebruikers groep of toepassing waaraan bepaalde machtigingen worden toegekend (zoals gedefinieerd door de roldefinitie) aan de resources van uw klanten.
* **Weergave naam van Aad-object**: een beschrijvende naam om de klant te helpen het doel van deze autorisatie te begrijpen. De klant krijgt deze naam te zien bij het delegeren van resources.
* **Roldefinitie**: Selecteer een van de beschik bare ingebouwde Azure AD-rollen in de lijst. Met deze rol bepaalt u de machtigingen die de gebruiker in het veld **Principal-id** heeft op de resources van uw klanten. Zie voor beschrijvingen van deze rollen [ingebouwde rollen](../role-based-access-control/built-in-roles.md) en [functie ondersteuning voor Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> Als toepasselijke nieuwe ingebouwde rollen worden toegevoegd aan Azure, worden ze hier beschikbaar. Er kan enige vertraging optreden voordat ze worden weer gegeven.

* **Toewijs bare rollen**: deze optie wordt alleen weer gegeven als u gebruikers toegangs beheerder hebt geselecteerd in de **roldefinitie** voor deze autorisatie. Als dat het geval is, moet u hier een of meer toewijs bare rollen toevoegen. De gebruiker in het **object-ID-veld van Azure AD** kan deze rollen toewijzen aan beheerde identiteiten, wat vereist is om [beleid te implementeren dat kan worden hersteld](../lighthouse/how-to/deploy-policy-remediation.md). Geen enkele andere machtigingen die normaal gesp roken zijn gekoppeld aan de rol beheerder van gebruikers toegang, zijn van toepassing op deze gebruiker.

> [!TIP]
> Om ervoor te zorgen dat u zo nodig de [toegang tot een overdracht kunt verwijderen](../lighthouse/how-to/remove-delegation.md) , moet u een **autorisatie** toevoegen waarbij de **roldefinitie** is ingesteld op de rol van de [registratie toewijzing voor beheerde services verwijderen](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de Tenant van de klant.

Zodra u alle secties voor uw abonnement hebt voltooid, kunt u **+ nieuw plan maken** selecteren om extra plannen te maken. Wanneer u klaar bent, selecteert u **concept opslaan** voordat u doorgaat.

## <a name="next-steps"></a>Volgende stappen

* [Beoordelen en publiceren](review-publish-offer.md)
