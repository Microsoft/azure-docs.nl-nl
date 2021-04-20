---
title: Problemen met toegang tot Azure EA Portal oplossen
description: In dit artikel worden enkele veelvoorkomende problemen beschreven die zich kunnen voordoen met een Azure Enterprise Agreement (EA) in Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726462"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Problemen met toegang tot Azure EA Portal oplossen

In dit artikel worden enkele veelvoorkomende problemen beschreven die zich kunnen voordoen met een Azure Enterprise Agreement (EA). Azure EA Portal wordt gebruikt om gebruikers en kosten voor een Enterprise Agreement te beheren. U kunt deze problemen tegenkomen wanneer u de toegang tot Azure EA Portal configureert of bijwerkt.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemen bij het toevoegen van een beheerder aan een inschrijving

Er zijn verschillende typen verificatieniveaus voor ondernemingsinschrijvingen. Als de verificatieniveaus onjuist worden toegepast, ervaart u mogelijk problemen als u zich probeert aan te melden bij Azure EA Portal.

U kunt Azure EA Portal gebruiken om toegang te verlenen aan gebruikers met verschillende verificatieniveaus. Ondernemingsbeheerders kunnen het verificatieniveau bijwerken om te voldoen aan de beveiligingsvereisten van de organisatie.

### <a name="authentication-level-types"></a>Typen verificatieniveaus

- Alleen Microsoft-accounts: voor organisaties die gebruikers willen gebruiken, maken en beheren via Microsoft-accounts.
- Werk- of schoolaccount: voor organisaties die Active Directory met federatie hebben ingesteld voor de cloud en waarbij alle accounts zich in één tenant bevinden.
- Werk- of schoolaccount (meerdere tenants): voor organisaties die Active Directory met federatie hebben ingesteld voor de cloud en waarbij accounts verdeeld zijn over meerdere tenants.
- Gemengd account: hiermee kunt u gebruikers met een Microsoft-account en/of met een werk- of schoolaccount toevoegen.

Het eerste werk- of schoolaccount dat aan de inschrijving wordt toegevoegd, bepaalt het _standaard_ domein. Als u een werk- of schoolaccount aan een andere tenant wilt toevoegen, moet u het verificatieniveau van de inschrijving veranderen in verificatie voor meerdere tenants.

Het verificatieniveau bijwerken:

1. Meld u als ondernemingsbeheerder bij Azure EA Portal.
2. Klik op **Beheren** in het linkernavigatievenster.
3. Klik op het tabblad **Inschrijving**.
4. Selecteer **Verificatieniveau** onder **Details van de inschrijving**.
5. Klik op het potloodsymbool.
6. Klik op **Opslaan**.

![Voorbeeld met verificatieniveaus ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Voor Microsoft-accounts moet er een bijbehorende id worden gemaakt via [https://signup.live.com](https://signup.live.com/).

Werk- en schoolaccounts zijn beschikbaar voor organisaties die Active Directory met federatie hebben ingesteld en waarbij alle accounts zich in één tenant bevinden. Gebruikers kunnen worden toegevoegd met federatieve gebruikersverificatie voor op het werk of op school, als de interne Active Directory van het bedrijf federatief is.

Als uw organisatie niet gebruikmaakt van Active Directory-federatie kunt u het e-mailadres voor werk of school niet gebruiken. U kunt in plaats daarvan een nieuw e-mailadres registreren of maken en dit registreren als Microsoft-account.

## <a name="unable-to-access-the-azure-ea-portal"></a>Er kan geen toegang worden verkregen tot Azure EA Portal

Als er een foutmelding wordt weergegeven wanneer u zich probeert aan te melden bij Azure EA Portal, gebruikt u de volgende stappen voor het oplossen van problemen:

- Zorg ervoor dat u de juiste Azure EA Portal-URL gebruikt: https://ea.azure.com.
- Bepaal of met een werk- of schoolaccount of met een Microsoft-account toegang hebt verkregen tot Azure EA Portal.
  - Als u uw werkaccount gebruikt, voert u uw zakelijke e-mailadres en het bijbehorende wachtwoord in. Het wachtwoord dat u nodig hebt, wordt door uw organisatie aangeleverd. Neem contact op met de IT-afdeling voor informatie over het opnieuw instellen van het wachtwoord als u problemen ervaart.
  - Als u een Microsoft-account gebruikt, voert u het e-mailadres en wachtwoord van uw Microsoft-account in. Als u het wachtwoord van uw Microsoft-account bent vergeten, kunt u het opnieuw instellen via [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Open de browser privé of incognito om u aan te melden. Er worden dan geen cookies of informatie uit de cache bewaard van eerdere of huidige sessies. Wis de cache van uw browser en gebruik een privé- of incognito venster om https://ea.azure.com te openen.
- Als de foutmelding _Ongeldige gebruiker_ wordt weergegeven bij het gebruik van een Microsoft-account, kan dit zijn omdat u meerdere Microsoft-accounts hebt. Het account waarmee u zich probeert aan te melden, is niet die met het primaire e-mailadres.
Als u een foutmelding ziet over een _ongeldige gebruiker_, kan dat zijn omdat het onjuiste accounttype is gebruikt bij het toevoegen van de gebruiker aan de inschrijving. Het kan bijvoorbeeld zijn dat er een werk- of schoolaccount is gebruikt in plaats van een Microsoft-account. In dit voorbeeld laat u een andere EA-beheerder het juiste account toevoegen, of neemt u contact op met de [ondersteuning](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Als u de primaire alias wilt controleren, gaat u naar [https://account.live.com](https://account.live.com). Klik vervolgens op **Uw gegevens** en klik vervolgens op **Beheren hoe u zich aanmeldt bij Microsoft**. Volg de aanwijzingen voor het verifiëren van een alternatief e-mailadres en het verkrijgen van een code voor toegang tot gevoelige informatie. Voer de beveiligingscode in. Selecteer **Later instellen** als u de tweeledige verificatiemethode niet wilt instellen.
  - U ziet de pagina **Aanmelden bij Microsoft beheren** waar u uw accountaliassen kunt bekijken. Controleer of u de primaire alias gebruikt voor het aanmelden bij Azure EA Portal. Als dat niet het geval is, kunt u van uw huidige alias de primaire alias maken. U kunt in plaats daarvan ook de primaire alias voor Azure EA Portal gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Azure EA Portal-beheerders moeten [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
- Lees de [Cost Management + Billing veelgestelde vragen](../cost-management-billing-faq.yml) en antwoorden over veelvoorkomende problemen met Azure EA-activering.
