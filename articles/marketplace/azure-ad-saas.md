---
title: Azure Active Directory en transactable SaaS-aanbiedingen in de commerciële Marketplace
description: Meer informatie over de werking van Azure Active Directory met transactable SaaS-aanbiedingen in micro soft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93124914"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Azure AD en transactable SaaS-aanbiedingen in de commerciële Marketplace

Met de micro soft cloud-gebaseerde service voor identiteits-en toegangs beheer, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) kunnen gebruikers zich aanmelden en kunnen ze toegang krijgen tot interne en externe resources. In de micro soft Commercial Marketplace biedt Azure AD transactable SaaS eenvoudiger en veiliger voor iedereen, inclusief uitgevers, kopers en gebruikers. Met Azure AD kunnen uitgevers het inrichten van gebruikers automatiseren met hun SaaS-apps (Software as a Service) en kunnen kopers zelf deze ingerichte gebruikers beheren. 

Verder biedt [Azure AD eenmalige aanmelding](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) beveiliging en gebruiks gemak wanneer gebruikers zich aanmelden bij apps in azure AD. Snellere betrokkenheid en geoptimaliseerde ervaringen betrouwt ook het vertrouwen van kopers en gebruikers van de eerste interactie met de SaaS-app van een uitgever. Dit geeft een positieve indruk bij het opbouwen van zicht baarheid en herhalingen.

Door de instructies in dit artikel te volgen, kunt u uw SaaS-aanbieding in de commerciële Marketplace certificeren. Lees voor meer informatie over certificering het gedetailleerde [certificaat beleid voor commerciële Marketplace](/legal/marketplace/certification-policies#100-general), met inbegrip van degenen die [specifiek zijn voor SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Voordat u begint

Wanneer u [uw SaaS-aanbieding](./create-new-saas-offer.md) in het partner centrum maakt, kiest u uit een set specifieke aanbiedings opties die worden weer gegeven in de aanbieding. Uw keuze bepaalt hoe uw aanbieding wordt verwerkt in de commerciële Marketplace. Aanbiedingen die via micro soft worden verkocht, worden aanbiedingen genoemd. De klant wordt namens u in rekening gebracht voor alle transactable-aanbiedingen. Als u ervoor kiest om via micro soft te verkopen en u trans acties voor de Amerikaanse host hebt (de optie **Ja** ), hebt u gekozen voor het maken van een transactable-aanbieding en dit artikel is voor u bedoeld. We raden u aan deze volledig te lezen.

Als u ervoor kiest om uw aanbieding alleen via de commerciële Marketplace en trans acties afzonderlijk te verwerken (de optie **Nee** ), hebt u drie opties voor de manier waarop potentiële klanten toegang krijgen tot uw aanbieding: nu downloaden (gratis), gratis proef versie en contact met mij opnemen. Als u **nu downloaden (gratis)** of **gratis proef versie** selecteert, is dit artikel niet voor u. In plaats daarvan raadpleegt [u de landings pagina voor uw gratis of proef SaaS-aanbieding bouwen in de commerciële Marketplace](./azure-ad-free-or-trial-landing-page.md) voor meer informatie. Als u **contact opnemen** selecteert, zijn er geen directe Publisher-verantwoordelijkheden. Ga door met het maken van uw aanbieding in het partner centrum.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Hoe Azure AD werkt met de commerciële Marketplace voor SaaS-aanbiedingen

Azure AD maakt naadloze aankoop, uitvoering en beheer van oplossingen voor commerciële Marketplace mogelijk. In afbeelding 1 ziet u hoe de uitgever, de koper en de gebruiker interactie hebben met het kopen en activeren van een abonnement. Ook wordt uitgelegd hoe klanten SaaS-toepassingen gebruiken en beheren die ze van de commerciële Marketplace ophalen. Voor de doel einden van deze illustratie is de gebruiker de SaaS-toepassing die een aankoop van de commerciële Marketplace initieert.

Zoals wordt weer gegeven in afbeelding 1 en een koper uw aanbieding selecteert, gaan ze een keten van werk stromen met aankoop, abonnementen en gebruikers beheer. Binnen deze keten is de uitgever verantwoordelijk voor bepaalde vereisten, waarbij micro soft ondersteuning biedt op basis van belang rijke punten.

***Afbeelding 1: Azure AD gebruiken voor SaaS-aanbiedingen in de commerciële Marketplace***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Illustreert het aankoop beheer, abonnements beheer en de stappen voor het uitvoeren van een optioneel gebruikers beheer.":::

De volgende secties bevatten informatie over de vereisten voor elke proces stap.

## <a name="process-steps-for-purchase-management"></a>Proces stappen voor aankoop beheer

In deze afbeelding ziet u de vier proces stappen voor het beheer van aankopen.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Illustreert de vier proces stappen voor aankoop beheer.":::

Deze tabel bevat details over de stappen voor het aankoop beheer proces.

| Proces stap | Publisher-actie | Aanbevolen of vereist voor uitgevers |
| ------------ | ------------- | ------------- |
| 1. de koper meldt zich aan bij de commerciële Marketplace met hun Azure ID-identiteit en selecteert een SaaS-aanbieding. | Er is geen uitgever actie vereist. | Niet van toepassing |
| 2. na de aankoop selecteert de koper **account configureren** in azure Marketplace of **nu configureren** in AppSource, waarmee de koper naar de landings pagina van de uitgever voor deze aanbieding wordt geleid. De koper moet zich kunnen aanmelden bij de SaaS-toepassing van de uitgever met Azure AD SSO en mag alleen worden gevraagd om minimale toestemming waarvoor Azure AD-beheerder geen goed keuring vereist. | Ontwerp een [landings pagina](azure-ad-transactable-saas-landing-page.md) voor de aanbieding zodat deze een gebruiker ontvangt met hun Azure AD-of Microsoft-account (MSA)-identiteit en de aanvullende inrichting of installatie die vereist is, vergemakkelijkt. | Vereist |
| 3. de uitgever vraagt om details van de SaaS fulfillment API. | Met behulp van een [toegangs token](./partner-center-portal/pc-saas-registration.md) dat is gegenereerd op basis van de toepassings-id van de landings pagina [roept u het eind punt voor omzetten](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) op om details over de aankoop op te halen. | Vereist |
| 4. via Azure AD en de Microsoft Graph-API verzamelt de uitgever de bedrijfs-en gebruikers gegevens die nodig zijn om de koper in te richten in de SaaS-toepassing van de uitgever.  | Het Azure AD-gebruikers token afbreken om naam en e-mail adres te vinden, of [de Microsoft Graph-API aanroepen](/graph/use-the-api) en gedelegeerde machtigingen gebruiken om informatie op te [halen](/graph/api/user-get) over de gebruiker die is aangemeld. | Vereist |
||||

## <a name="process-steps-for-subscription-management"></a>Proces stappen voor het beheer van abonnementen

In deze afbeelding ziet u de twee proces stappen voor het beheer van abonnementen.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Illustreert de twee proces stappen voor het beheer van abonnementen.":::

In deze tabel worden de details van de proces stappen voor het abonnements beheer beschreven.

| Proces stap | Publisher-actie | Aanbevolen of vereist voor uitgevers |
| ------------ | ------------- | ------------- |
| 5. de uitgever beheert het abonnement op de SaaS-toepassing via de API voor SaaS-uitvoering. | Wijzigingen in abonnementen en andere beheer taken verwerken via de [SaaS-fulfillment-api's](./partner-center-portal/pc-saas-fulfillment-api-v2.md).<br><br>Voor deze stap is een toegangs token vereist zoals beschreven in proces stap 3. | Vereist |
| 6. Wanneer u prijzen met een licentie verbruikt, worden er gebruiks gebeurtenissen in de API voor de meter service verzonden. | Als uw SaaS-app gebruikmaakt van facturering op basis van gebruik, maakt u gebruiks meldingen via de Api's van de [Marketplace-meter service](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Voor deze stap is een toegangs token vereist, zoals beschreven in stap 3. | Vereist voor meting |
||||

## <a name="process-steps-for-user-management"></a>Proces stappen voor gebruikers beheer

In deze afbeelding ziet u de drie proces stappen voor gebruikers beheer.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Illustreert de drie optionele proces stappen voor gebruikers beheer.":::

Proces stappen 7 t/m 9 zijn optionele proces stappen voor gebruikers beheer. Ze bieden extra voor delen voor uitgevers die ondersteuning bieden voor eenmalige aanmelding (SSO) van Azure AD. In deze tabel worden de details van de proces stappen voor gebruikers beheer beschreven.

| Proces stap | Publisher-actie | Aanbevolen of vereist voor uitgevers |
| ------------ | ------------- | ------------- |
| 7. Azure AD-beheerders bij het bedrijf van de koper kunnen eventueel de toegang voor gebruikers en groepen beheren via Azure AD. | Er is geen actie van de uitgever vereist om deze in te scha kelen als Azure AD SSO is ingesteld voor gebruikers (stap 9). | Niet van toepassing |
| 8. de Azure AD-inrichtings service communiceert wijzigingen tussen Azure AD en de SaaS-toepassing van de uitgever. | [Implementeer een scim-eind punt](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) voor het ontvangen van updates van Azure AD wanneer gebruikers worden toegevoegd en verwijderd. | Aanbevolen |
| 9. nadat de app is gemachtigd en ingericht, kunnen gebruikers van het bedrijf van de koper Azure AD SSO gebruiken om zich aan te melden bij de SaaS-toepassing van de uitgever. | [Gebruik Azure AD SSO](../active-directory/manage-apps/what-is-single-sign-on.md) zodat gebruikers zich één keer kunnen aanmelden met een account voor de SaaS-toepassing van de uitgever. | Aanbevolen |
||||

## <a name="next-steps"></a>Volgende stappen

- [Bouw de landings pagina voor uw voor transactable SaaS-aanbieding in de commerciële Marketplace](azure-ad-transactable-saas-landing-page.md)
- [Bouw de landings pagina voor uw gratis of proef SaaS-aanbieding in de commerciële Marketplace](azure-ad-free-or-trial-landing-page.md)
- [Een SaaS-aanbieding maken in de commerciële Marketplace](create-new-saas-offer.md)