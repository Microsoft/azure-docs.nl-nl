---
title: On-premises Azure AD-wachtwoord beveiliging inschakelen
description: Meer informatie over het inschakelen van Azure AD-wachtwoord beveiliging voor een on-premises Active Directory Domain Services omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741776"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>On-premises Azure Active Directory wachtwoord beveiliging inschakelen

Gebruikers maken vaak wachtwoorden waarin woorden voorkomen die lokaal veel worden gebruikt, zoals de naam van een school, een sportvereniging of een beroemde persoon. Deze wachtwoorden zijn gemakkelijk te raden en zijn slecht bestand tegen zogenaamde woordenboekaanvallen. Voor het afdwingen van sterke wacht woorden in uw organisatie, biedt Azure Active Directory-wachtwoord beveiliging van Azure Active Directory een globale en aangepaste lijst met verboden wacht woorden. Een aanvraag voor het wijzigen van het wacht woord is mislukt als er een overeenkomst is gevonden in de lijst met verboden wacht woorden.

Om uw on-premises Active Directory Domain Services-omgeving (AD DS) te beveiligen, kunt u Azure AD-wachtwoord beveiliging installeren en configureren om met uw on-premises DC te werken. In dit artikel wordt beschreven hoe u Azure AD-wachtwoord beveiliging inschakelt voor uw on-premises omgeving.

Zie [Azure AD-wachtwoord beveiliging afdwingen voor Windows Server Active Directory](concept-password-ban-bad-on-premises.md)voor meer informatie over de werking van Azure AD-wachtwoord beveiliging in een on-premises omgeving.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt beschreven hoe u Azure AD-wachtwoord beveiliging inschakelt voor uw on-premises omgeving. Voordat u dit artikel voltooit, [installeert en registreert u de Azure AD-service voor wachtwoord beveiliging proxy en DC-agents](howto-password-ban-bad-on-premises-deploy.md) in uw on-premises AD DS omgeving.

## <a name="enable-on-premises-password-protection"></a>On-premises wachtwoord beveiliging inschakelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en blader naar **Azure Active Directory**  >  **beveiligings**  >  **verificatie methoden**  >  **wachtwoord beveiliging**.
1. Stel de optie voor het **inschakelen van wachtwoord beveiliging op Windows Server Active Directory** in op *Ja*.

    Als deze instelling is ingesteld op *Nee*, gaan alle geïmplementeerde Azure AD-agenten voor wachtwoord beveiliging domein controller in een quiescent-modus waarbij alle wacht woorden worden geaccepteerd als-is. Er worden geen validatie activiteiten uitgevoerd en er worden geen controle gebeurtenissen gegenereerd.

1. Het is raadzaam om de **modus** in te stellen op *controleren*. Nadat u vertrouwd bent met de functie en de gevolgen voor gebruikers in uw organisatie, kunt u de **modus** instellen op *afgedwongen*. Zie de volgende sectie in de [bewerkings modus](#modes-of-operation)voor meer informatie.
1. Selecteer **Opslaan** wanneer u klaar bent.

    [![On-premises wachtwoord beveiliging inschakelen onder verificatie methoden in de Azure Portal](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Bedrijfsmodi

Wanneer u on-premises Azure AD-wachtwoord beveiliging inschakelt, kunt u de *controle* modus of de *afdwingings* modus gebruiken. We raden u aan de eerste implementatie en het testen altijd uit te voeren in de controle modus. Vermeldingen in het gebeurtenis logboek moeten vervolgens worden bewaakt om te anticiperen of bestaande operationele processen worden verstoord zodra de modus *afdwingen* is ingeschakeld.

### <a name="audit-mode"></a>Controle modus

De *controle* modus is bedoeld als een manier om de software uit te voeren in een ' What '-modus. Elke Azure AD-Agent service voor wachtwoord beveiliging evalueert een inkomend wacht woord volgens het huidige actieve beleid.

Als het huidige beleid zodanig is geconfigureerd dat het zich in de controle modus bevindt, worden de wacht woorden ' onjuist ' geretourneerd in gebeurtenis logboek berichten, maar worden ze verwerkt en bijgewerkt. Dit gedrag is het enige verschil tussen de controle-en afdwingings modus. Alle andere bewerkingen worden hetzelfde uitgevoerd.

### <a name="enforced-mode"></a>Afgedwongen modus

De *afgedwongen* modus is bedoeld als de definitieve configuratie. Net als in de controle modus evalueert elke Azure AD-Agent service voor wachtwoord beveiliging binnenkomende wacht woorden volgens het huidige actieve beleid. Als afgedwongen modus is ingeschakeld, wordt een wacht woord dat als onveilig wordt beschouwd volgens het beleid, geweigerd.

Wanneer een wacht woord in afgedwongen modus wordt afgewezen door de Azure AD-agent voor wachtwoord beveiliging, ziet een eind gebruiker een vergelijk bare fout zoals ze zouden zien als hun wacht woord is geweigerd door traditionele on-premises wachtwoord complexiteit afdwingen. Een gebruiker kan bijvoorbeeld het volgende traditionele fout bericht zien op het Windows-aanmeld scherm of het wacht woord wijzigen:

*Het wacht woord kan niet worden bijgewerkt. De waarde die is opgegeven voor het nieuwe wacht woord voldoet niet aan de vereisten voor de lengte, complexiteit of geschiedenis van het domein.*

Dit bericht bevat slechts één voor beeld van een aantal mogelijke resultaten. Het specifieke fout bericht kan variëren, afhankelijk van de werkelijke software of het scenario dat probeert een onveilig wacht woord in te stellen.

Betrokken eind gebruikers moeten mogelijk samen werken met hun IT-mede werkers om inzicht te krijgen in de nieuwe vereisten en om veilige wacht woorden te kiezen.

> [!NOTE]
> Azure AD-wachtwoord beveiliging heeft geen controle over het specifieke fout bericht dat wordt weer gegeven door de client computer wanneer een zwak wacht woord wordt afgewezen.

## <a name="next-steps"></a>Volgende stappen

Als u de lijst met geblokkeerde wacht woorden voor uw organisatie wilt aanpassen, raadpleegt u [de lijst met aangepaste verboden wacht woorden voor Azure AD-wachtwoord beveiliging configureren](tutorial-configure-custom-password-protection.md).

Zie [on-premises Azure AD-wachtwoord beveiliging controleren](howto-password-ban-bad-on-premises-monitor.md)voor meer informatie over het controleren van on-premises gebeurtenissen.
