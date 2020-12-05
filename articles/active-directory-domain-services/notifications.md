---
title: E-mail meldingen voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het configureren van e-mail meldingen om u te waarschuwen over problemen in een Azure Active Directory Domain Services beheerd domein
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619161"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>E-mail meldingen configureren voor problemen in Azure Active Directory Domain Services

De status van een beheerd domein van Azure Active Directory Domain Services (Azure AD DS) wordt bewaakt door het Azure-platform. Op de status pagina van de Azure Portal worden waarschuwingen voor het beheerde domein weer gegeven. Om ervoor te zorgen dat problemen tijdig worden beantwoord, kunnen e-mail meldingen worden geconfigureerd om te rapporteren over status waarschuwingen zodra ze worden gedetecteerd in het beheerde domein van Azure AD DS.

Dit artikel laat u zien hoe u e-mail meldingen kunt configureren voor een beheerd domein.

## <a name="email-notification-overview"></a>Overzicht van e-mail meldingen

U kunt e-mail meldingen configureren om u te waarschuwen over problemen met een beheerd domein. Deze e-mail meldingen geven het beheerde domein op waarop de waarschuwing aanwezig is, evenals het tijdstip van de detectie en een koppeling naar de status pagina in het Azure Portal. U kunt de problemen vervolgens oplossen door het probleem op te lossen.

In het volgende voor beeld van een e-mail melding wordt aangegeven dat er een kritieke waarschuwing of waarschuwing is gegenereerd op het beheerde domein:

![Voor beeld van e-mail melding](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Zorg er altijd voor dat het e-mail bericht afkomstig is van een geverifieerde afzender van micro soft voordat u op de koppelingen in het bericht klikt. De e-mail meldingen zijn altijd afkomstig van het `azure-noreply@microsoft.com` adres.

### <a name="why-would-i-receive-email-notifications"></a>Waarom ontvang ik e-mail meldingen?

Azure AD DS verzendt e-mail meldingen voor belang rijke updates over het beheerde domein. Deze meldingen zijn alleen bedoeld voor dringende problemen die van invloed zijn op de service en direct moeten worden opgelost. Elke e-mail melding wordt geactiveerd door een waarschuwing op het beheerde domein. De waarschuwingen worden ook weer gegeven in de Azure Portal en kunnen worden weer gegeven op de [status pagina van Azure AD DS][check-health].

Azure AD DS stuurt geen e-mail berichten voor advertenties, updates of verkoop doeleinden.

### <a name="when-will-i-receive-email-notifications"></a>Wanneer ontvang ik e-mail meldingen?

Er wordt direct een melding verzonden wanneer een [nieuwe waarschuwing][troubleshoot-alerts] wordt gevonden in een beheerd domein. Als de waarschuwing niet wordt opgelost, worden er om de vier dagen extra e-mail meldingen verzonden als een herinnering.

### <a name="who-should-receive-the-email-notifications"></a>Wie moet de e-mail meldingen ontvangen?

De lijst met e-mail ontvangers voor Azure AD DS moet bestaan uit personen die het beheerde domein kunnen beheren en wijzigen. Deze e-mail lijst moet worden beschouwd als uw ' eerste responders ' op waarschuwingen en problemen.

U kunt Maxi maal vijf geadresseerden voor e-mail meldingen toevoegen. Als u meer dan vijf ontvangers voor e-mail meldingen wilt, maakt u een distributie lijst en voegt u deze toe aan de lijst met meldingen.

U kunt er ook voor kiezen alle *globale beheerders* van de Azure AD-Directory en elk lid van de groep *Aad DC-Administrators* e-mail meldingen te ontvangen. Azure AD DS verzendt alleen een melding naar Maxi maal 100 e-mail adressen, met inbegrip van de lijst met globale beheerders en AAD DC-beheerders.

## <a name="configure-email-notifications"></a>E-mailmeldingen configureren

Voer de volgende stappen uit om de bestaande ontvangers van e-mail meldingen te controleren of extra ontvangers toe te voegen:

1. Zoek en selecteer **Azure AD Domain Services** in de Azure-portal.
1. Selecteer uw beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer aan de linkerkant van het Azure AD DS-resource venster **instellingen voor meldingen**. De bestaande ontvangers voor e-mail meldingen worden weer gegeven.
1. Als u een e-mail ontvanger wilt toevoegen, voert u het e-mail adres in in de tabel extra geadresseerden.
1. Wanneer u klaar bent, selecteert u **Opslaan** in de bovenste navigatie balk.

> [!WARNING]
> Wanneer u de instellingen voor meldingen wijzigt, worden de instellingen voor meldingen voor het hele beheerde domein bijgewerkt, niet alleen uzelf.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Ik heb een e-mail melding ontvangen voor een waarschuwing, maar wanneer ik ben aangemeld bij de Azure Portal is er geen waarschuwing. Wat is er gebeurd?

Als een waarschuwing is opgelost, wordt de waarschuwing uit het Azure Portal verwijderd. De meest waarschijnlijke reden is dat iemand die e-mail meldingen ontvangt, de waarschuwing voor het beheerde domein heeft opgelost of dat deze automatisch wordt omgezet door het Azure-platform.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Waarom kan ik de instellingen voor meldingen niet bewerken?

Als u geen toegang hebt tot de pagina met instellingen voor meldingen in de Azure Portal, hebt u geen machtigingen om het beheerde domein te bewerken. Neem contact op met een globale beheerder om machtigingen te krijgen voor het bewerken van Azure AD DS resource of uit de lijst met ontvangers verwijderd.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Ik wil geen e-mail meldingen ontvangen, maar ik heb mijn e-mail adres ingevoerd. Waarom?

Controleer uw e-mail adres of map in uw e-mail op de melding en zorg ervoor dat de afzender van wordt toegestaan `azure-noreply@microsoft.com` .

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen op een beheerd domein oplossen][troubleshoot-alerts]voor meer informatie over het oplossen van enkele van de problemen die kunnen worden gerapporteerd.

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
