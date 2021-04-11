---
title: Gebruikers toevoegen en beheren voor de commerciële Marketplace-Azure Marketplace
description: Meer informatie over het beheren van gebruikers in het commerciële Marketplace-programma voor een micro soft-account voor commerciële Marketplace in het partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108200"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Gebruikers toevoegen en beheren voor de commerciële Marketplace

**Juiste rollen**

- Eigenaar
- Manager

Met de sectie **gebruikers** van het partner centrum (onder **account instellingen**) kunt u Azure AD gebruiken voor het beheren van de gebruikers, groepen en Azure AD-toepassingen die toegang hebben tot uw partner centrum-account. Uw account moet machtigingen op beheerders niveau hebben voor het [werk account (Azure AD-Tenant)](company-work-accounts.md) waarin u gebruikers wilt toevoegen of bewerken. Als u gebruikers binnen een ander werk account/Tenant wilt beheren, moet u zich afmelden en vervolgens weer aanmelden als een gebruiker met **beheerders** machtigingen voor dat werk account/Tenant.

Nadat u bent aangemeld met uw werk account (Azure AD-Tenant), kunt u gebruikers toevoegen en beheren.

## <a name="add-existing-users"></a>Bestaande gebruikers toevoegen

Gebruikers toevoegen aan uw partner centrum-account dat al aanwezig is in het werk account van uw bedrijf [(Azure AD-Tenant)](company-work-accounts.md):

1. Ga naar **gebruikers** (onder **account instellingen**) en selecteer **gebruikers toevoegen**.
1. Selecteer een of meer gebruikers in de lijst die wordt weer gegeven. U kunt het zoekvak gebruiken om te zoeken naar specifieke gebruikers. * Als u meer dan één gebruiker selecteert om aan uw partner Center-account toe te voegen, moet u deze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere gebruikers met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
1. Wanneer u klaar bent met het kiezen van gebruikers, selecteert u **geselecteerde toevoegen**.
1. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de geselecteerde gebruiker (s) op.
1. Selecteer **Opslaan**.

## <a name="create-new-users"></a>Nieuwe gebruikers maken

Als u gloed nieuwe gebruikers accounts wilt maken, moet u een account hebben met [globale beheerders](/azure/active-directory/roles/permissions-reference) machtigingen.

1. Ga naar **gebruikers** (onder **account instellingen**), selecteer **gebruikers toevoegen** en kies vervolgens **nieuwe gebruikers maken**.
1. Voer voor elke nieuwe gebruiker een voor naam, achternaam en gebruikers naam in.
1. Als u wilt dat de nieuwe gebruiker een globaal beheerders account in de adres lijst van uw organisatie heeft, schakelt u het selectie vakje **deze gebruiker een globale beheerder in uw Azure AD maken in en volledig beheer over alle Directory bronnen**. Hiermee krijgt de gebruiker volledige toegang tot alle beheer functies in de Azure AD van uw bedrijf. Ze kunnen gebruikers toevoegen en beheren in het werk account van uw organisatie (Azure AD-Tenant), maar niet in partner centrum, tenzij u het account de juiste rol/machtigingen verleent.
1. Als u het selectie vakje inschakelt om **deze gebruiker een globale beheerder te maken**, moet u een *e-mail voor wachtwoord herstel* opgeven zodat de gebruiker het wacht woord zo nodig kan herstellen.
1. Selecteer in de sectie **groepslid maatschap** de groepen waartoe u de nieuwe gebruiker wilt maken.
1. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de gebruiker op.
1. Selecteer **Opslaan**.

Als u een nieuwe gebruiker in partner centrum maakt, wordt er ook een account voor die gebruiker gemaakt in het werk account (Azure AD-Tenant) waarbij u bent aangemeld. Als u wijzigingen aanbrengt aan de naam van een gebruiker in het partner centrum, worden dezelfde wijzigingen aangebracht in het werk account van uw organisatie (Azure AD-Tenant).

## <a name="invite-new-users-by-email"></a>Nieuwe gebruikers uitnodigen via e-mail

Als u gebruikers wilt uitnodigen die momenteel geen deel uitmaken van uw werk account (Azure AD-Tenant) via e-mail, moet u een account hebben met [globale beheerders](/azure/active-directory/roles/permissions-reference) machtigingen.

1. Ga naar **gebruikers** (onder **account instellingen**), selecteer **gebruikers toevoegen** en kies **gebruikers uitnodigen per e-mail**.
1. Voer een of meer e-mail adressen (Maxi maal 10) in, gescheiden door komma's of punt komma's.
1. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de gebruiker op.
1. Selecteer **Opslaan**.

De gebruikers die u hebt uitgenodigd, krijgen een e-mail uitnodiging om lid te worden van uw partner centrum-account. Er wordt een nieuw gast gebruikers account gemaakt in uw werk account (Azure AD-Tenant). Elke gebruiker moet de uitnodiging accepteren voordat ze toegang krijgen tot uw account.

Als u een uitnodiging opnieuw moet verzenden, gaat u naar de pagina *gebruikers* , zoekt u de uitnodiging in de lijst met gebruikers, selecteert u hun e-mail adres (of de tekst met de melding *uitnodiging in behandeling*). Selecteer vervolgens aan de onderkant van de pagina **uitnodiging opnieuw verzenden**.

Als uw organisatie gebruikmaakt van [adreslijst integratie](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) om de on-premises adreslijst service te synchroniseren met uw Azure AD, kunt u geen nieuwe gebruikers, groepen of Azure AD-toepassingen maken in het partner centrum. U (of een andere beheerder in uw on-premises Directory) moet u deze rechtstreeks in de on-premises map maken voordat u deze kunt bekijken en toevoegen in het partner centrum.

## <a name="remove-a-user"></a>Een gebruiker verwijderen

Als u een gebruiker uit uw werk account (Azure AD-Tenant) wilt verwijderen, gaat u naar **gebruikers** (onder **account instellingen**), selecteert u de gebruiker die u wilt verwijderen met het selectie vakje in de kolom uiterst rechts en kiest u vervolgens **verwijderen** in de beschik bare acties. Er wordt een pop-upvenster weer gegeven waarin u kunt bevestigen dat u de geselecteerde gebruiker (s) wilt verwijderen.

## <a name="change-a-user-password"></a>Een gebruikers wachtwoord wijzigen

Als een van uw gebruikers hun wacht woord moet wijzigen, kunnen ze dat zelf doen als u een *e-mail voor wachtwoord herstel* hebt opgegeven tijdens het maken van het gebruikers account. U kunt ook de volgende stappen uitvoeren om het wacht woord van een gebruiker bij te werken. Als u het wacht woord van een gebruiker in uw bedrijfs account (Azure AD-Tenant) wilt wijzigen, moet u zijn aangemeld bij een account met [globale beheerders](/azure/active-directory/roles/permissions-reference) machtigingen. Hiermee wordt het wacht woord van de gebruiker in uw Azure AD-Tenant gewijzigd, samen met het wacht woord dat ze gebruiken voor toegang tot het partner centrum.

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de naam van het gebruikers account dat u wilt bewerken.
1. Selecteer de knop **wacht woord opnieuw instellen** onder aan de pagina.
1. Er verschijnt een bevestigings pagina om de aanmeldings gegevens voor de gebruiker weer te geven, met inbegrip van een tijdelijk wacht woord. Zorg ervoor dat u deze gegevens afdrukt of kopieert en deze aan de gebruiker verstrekt, omdat u geen toegang meer hebt tot het tijdelijke wacht woord nadat u deze pagina verlaat.
