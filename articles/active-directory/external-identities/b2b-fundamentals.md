---
title: Aanbevolen procedures en aanbevelingen voor Azure Active Directory B2B
description: Lees de aanbevolen procedures en aanbevelingen voor toegang tot gast gebruikers van Business-to-Business (B2B) in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94fd488ceb7ddb3724dd576c97c9070481e95147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365630"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Best practices voor Azure Active Directory B2B
Dit artikel bevat aanbevelingen en aanbevolen procedures voor B2B-samen werking (Business-to-Business) in Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Vanaf 2021**: micro soft biedt geen ondersteuning meer voor de terugbetaling van uitnodigingen door het maken van niet-beheerde (' virale ' of ' just-in-time ') Azure AD-accounts en tenants voor B2B-samenwerkings scenario's. Op dat moment wordt de functie voor eenmalige e-mail wachtwoord code ingeschakeld voor alle bestaande tenants en standaard ingeschakeld voor nieuwe tenants. De functie voor eenmalige e-mail wachtwoord code wordt ingeschakeld, omdat deze een naadloze terugval verificatie methode biedt voor uw gast gebruikers. U hebt echter de mogelijkheid om deze functie uit te scha kelen als u deze niet wilt gebruiken. Zie voor meer informatie [verificatie via e-mail One-time wachtwoord code](one-time-passcode.md)


## <a name="b2b-recommendations"></a>B2B-aanbevelingen
| Aanbeveling | Opmerkingen |
| --- | --- |
| Voor een optimale aanmeldings ervaring met id-providers | Als dat mogelijk is, moet u rechtstreeks met id-providers communiceren zodat gebruikers die worden uitgenodigd om zich aan te melden bij uw gedeelde apps en bronnen zonder dat ze micro soft-accounts (Msa's) of Azure AD-accounts hoeven te maken. U kunt de [Google Federation-functie](google-federation.md) gebruiken om B2B-gast gebruikers toe te staan zich aan te melden met hun Google-account. U kunt ook de [functie direct Federation (preview)](direct-federation.md) gebruiken om directe Federatie in te stellen met een organisatie waarvan de ID-provider (IDP) het SAML 2,0-of WS-Fed-protocol ondersteunt. |
| De functie voor eenmalige e-mail wachtwoord code gebruiken voor B2B-gasten die niet op andere wijze kunnen worden geverifieerd | Met de functie voor [eenmalige e-mail code](one-time-passcode.md) worden B2B-gast gebruikers geverifieerd wanneer ze niet kunnen worden geverifieerd via andere manieren, zoals Azure AD, een Microsoft-account (MSA) of Google Federatie. Wanneer de gast gebruiker een uitnodiging heeft ingewisseld of een gedeelde resource opent, kunnen ze een tijdelijke code aanvragen, die wordt verzonden naar hun e-mail adres. Vervolgens voeren ze deze code in om door te gaan met aanmelden. |
| Huisstijl toevoegen aan uw aanmeldingspagina | U kunt uw aanmeldings pagina aanpassen zodat deze intuïtief is voor uw B2B-gast gebruikers. Zie [bedrijfs huisstijl toevoegen om u aan te melden en de pagina's van het toegangs venster](../fundamentals/customize-branding.md). |
| Uw privacyverklaring toevoegen aan de B2B-gebruiker voor het inwisselen van gast gebruikers | U kunt de URL van de privacyverklaring van uw organisatie toevoegen aan de eerste keer dat een uitgenodigde gebruiker toestemming moet geven om door te gaan met uw privacy-voor waarden. Zie [How to: de privacygegevens van uw organisatie toevoegen in azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| De functie voor bulk-uitnodiging (preview) gebruiken om meerdere B2B-gast gebruikers tegelijkertijd uit te nodigen | U kunt meerdere gast gebruikers tegelijk uitnodigen voor uw organisatie met behulp van de preview-functie voor bulksgewijs uitnodigen in de Azure Portal. Met deze functie kunt u een CSV-bestand uploaden om B2B-gast gebruikers te maken en uitnodigingen bulksgewijs te verzenden. Zie [zelf studie voor het bulksgewijs uitnodigen van B2B-gebruikers](tutorial-bulk-invite.md). |
| Beleid voor voorwaardelijke toegang afdwingen voor Multi-Factor Authentication (MFA) | We raden u aan MFA-beleid af te dwingen op de apps die u wilt delen met partner B2B-gebruikers. Op deze manier wordt MFA consistent afgedwongen op de apps in uw Tenant, ongeacht of de partner organisatie MFA gebruikt. Zie [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md). |
| Als u beleid voor voorwaardelijke toegang op basis van apparaten afdwingt, gebruikt u uitsluitings lijsten om toegang tot B2B-gebruikers toe te staan | Als op apparaten gebaseerd beleid voor voorwaardelijke toegang is ingeschakeld in uw organisatie, worden B2B-gast gebruikers apparaten geblokkeerd omdat ze niet worden beheerd door uw organisatie. U kunt uitsluitings lijsten met specifieke partner gebruikers maken om ze uit te sluiten van het beleid voor voorwaardelijke toegang op basis van apparaten. Zie [voorwaardelijke toegang voor B2B-samenwerkings gebruikers](conditional-access.md). |
| Een Tenant-specifieke URL gebruiken wanneer u directe koppelingen naar uw B2B-gast gebruikers levert | Als alternatief voor de uitnodigings-e-mail kunt u een gast een rechtstreekse koppeling geven naar uw app of portal. Deze directe koppeling moet een Tenant-specifiek zijn, wat inhoudt dat een Tenant-ID of geverifieerd domein moet worden opgenomen, zodat de gast kan worden geverifieerd in uw Tenant, waar de gedeelde app zich bevindt. Bekijk [de aflossings ervaring voor de gast gebruiker](redemption-experience.md). |
| Wanneer u een App ontwikkelt, gebruikt u User type om de gebruikers ervaring van gast te bepalen  | Als u een toepassing ontwikkelt en u verschillende ervaringen wilt bieden voor Tenant gebruikers en gast gebruikers, gebruikt u de eigenschap User type. De claim User type is momenteel niet opgenomen in het token. Toepassingen moeten de Microsoft Graph-API gebruiken om de map op te vragen voor de gebruiker om de User type op te halen. |
| De eigenschap User type *alleen* wijzigen als de relatie van de gebruiker met de organisatie is gewijzigd | Hoewel het mogelijk is om Power shell te gebruiken om de eigenschap User type voor een gebruiker van het ene naar het andere gast te converteren (en omgekeerd), moet u deze eigenschap alleen wijzigen als de relatie van de gebruiker met uw organisatie wordt gewijzigd. Zie [Eigenschappen van een B2B-gast gebruiker](user-properties.md).|

## <a name="next-steps"></a>Volgende stappen

[B2B delen beheren](delegate-invitations.md)