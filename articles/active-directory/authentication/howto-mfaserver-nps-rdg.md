---
title: RDG en Azure MFA-server met RADIUS-Azure Active Directory
description: Dit is de pagina Azure Multi-Factor Authentication die u helpt bij het implementeren van RD-gateway (Extern bureaublad) en Azure Multi-Factor Authentication-server met RADIUS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89e8c803e0edf5245a62d625a6367d68de96ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742065"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS

Extern bureaublad-gateway (RD) gebruikt de lokale [Network Policy Services (NPS)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) vaak om gebruikers te verifiëren. In dit artikel wordt beschreven hoe u RADIUS-aanvragen van de Extern bureaublad-gateway (via de lokale NPS) omleidt naar de Multi-Factor Authentication-server. De combinatie van Azure MFA en RD-gateway zorgt ervoor dat uw gebruikers overal toegang hebben tot hun werkomgevingen terwijl sterke verificatie wordt uitgevoerd.

Omdat Windows-verificatie voor Terminal Services niet wordt ondersteund voor Server 2012 R2, dient u RD-gateway en RADIUS te gebruiken voor integratie met de MFA-server.

Installeer de Azure Multi-Factor Authentication-server op een afzonderlijke server. Deze stuurt de RADIUS-aanvraag vervolgens terug naar de NPS op de Extern bureaublad-gatewayserver. Nadat NPS de gebruikersnaam en het wachtwoord heeft gevalideerd, wordt een antwoord naar de Multi-Factor Authentication-server gestuurd. Vervolgens voert de MFA-server de tweede factor van de verificatie uit en retourneert een resultaat naar de gateway.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Zie [integratie met RADIUS-verificatie voor Azure multi-factor Authentication](howto-mfa-nps-extension.md)als u gebruikmaakt van op de cloud gebaseerde MFA.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="prerequisites"></a>Vereisten

- Een in een domein opgenomen Azure MFA-server. Als u er nog niet een hebt geïnstalleerd, volgt u de stappen in [Aan de slag met de Azure Multi-Factor Authentication-server](howto-mfaserver-deploy.md).
- Een bestaande NPS-server die is geconfigureerd.
- Een Extern bureaublad-gateway die verifieert met behulp van Network Policy Services.

> [!NOTE]
> Dit artikel moet alleen worden gebruikt met MFA-server implementaties, niet voor Azure MFA (op basis van de Cloud).

## <a name="configure-the-remote-desktop-gateway"></a>De Extern bureaublad-gateway configureren

Configureer de RD-gateway om RADIUS-verificatie naar een Azure Multi-Factor Authentication-server te verzenden.

1. Klik in RD-gatewaybeheer met de rechtermuisknop op de servernaam en selecteer **Eigenschappen**.
2. Ga naar het tabblad **RD CAP-archief** en selecteer **Centrale NPS-server**.
3. Voeg een of meer Azure Multi-Factor Authentication-servers toe als RADIUS-servers door de naam of het IP-adres van elke server in te voeren.
4. Maak een gedeeld geheim voor elke server.

## <a name="configure-nps"></a>NPS configureren

De RD-gateway gebruikt NPS om de RADIUS-aanvraag te verzenden naar Azure Multi-Factor Authentication. Als u NPS wilt configureren, wijzigt u eerst de time-outinstellingen om te voorkomen dat er een time-out optreedt op de RD-gateway voordat de verificatie in twee stappen is voltooid. Daarna kunt u NPS bijwerken zodat deze RADIUS-verificaties van uw MFA-server kan ontvangen. Gebruik de volgende procedure om NPS te configureren:

### <a name="modify-the-timeout-policy"></a>Het time-outbeleid wijzigen

1. Open in NPS het menu **RADIUS-clients en -server** in de linkerkolom en selecteer **Externe RADIUS-servergroepen**.
2. Selecteer de **TS-GATEWAYSERVERGROEP**.
3. Ga naar het tabblad **Taakverdeling**.
4. Wijzig het **Aantal seconden zonder reactie voordat een aanvraag als een genegeerde aanvraag wordt beschouwd** en het **Aantal seconden tussen aanvragen wanneer de server is geïdentificeerd als Niet beschikbaar** in 30 tot 60 seconden. (Als op de server nog steeds een time-out optreedt tijdens de verificatie, kunt u teruggaan en het aantal seconden verhogen.)
5. Ga naar het tabblad **Verificatie/Account** en controleer of de opgegeven RADIUS-poorten overeenkomen met de poorten waarnaar de Multi-Factor Authentication-server luistert.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>NPS voorbereiden om verificaties te ontvangen van de MFA-server

1. Klik in de linkerkolom onder RADIUS-clients en -servers met de rechtermuisknop op **RADIUS-clients** en selecteer **Nieuw**.
2. Voeg de Azure Multi-Factor Authentication-server toe als een RADIUS-client. Kies een beschrijvende naam en geef een gedeeld geheim op.
3. Open het menu **Beleid** in de linkerkolom en selecteer **Beleid voor verbindingsaanvragen**. Hier moet een beleid met de naam TS-GATEWAY-AUTORISATIEBELEID worden vermeld dat tijdens de configuratie van RD-gateway is gemaakt. Dit beleid stuurt RADIUS-verzoeken door naar de Multi-Factor Authentication-server.
4. Klik met de rechtermuisknop op **TS-GATEWAY-AUTORISATIEBELEID** en selecteer **Beleid dupliceren**.
5. Open het nieuwe beleid en ga naar het tabblad **Voorwaarden**.
6. Voeg een voorwaarde toe die de Beschrijvende naam van client koppelt aan de Beschrijvende naam die in stap 2 is ingesteld voor de RADIUS-client voor de Azure Multi-Factor Authentication-server.
7. Ga naar het tabblad **Instellingen** en selecteer **Verificatie**.
8. De verificatieprovider wijzigen voor het **verifiëren van aanvragen op deze server**. Wanneer NPS een RADIUS-aanvraag ontvangt van de Azure MFA-server, zorgt dit beleid ervoor dat de verificatie lokaal wordt uitgevoerd in plaats van dat er een RADIUS-aanvraag wordt teruggestuurd naar de Azure Multi-Factor Authentication-server, waardoor een oneindige lus zou ontstaan.
9. Om een lus te voorkomen, zorgt u ervoor dat het nieuwe beleid BOVEN het oorspronkelijke beleid wordt gerangschikt in het deelvenster **Beleid voor verbindingsaanvragen**.

## <a name="configure-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication configureren

De Azure Multi-Factor Authentication-server is geconfigureerd als een RADIUS-proxy tussen RD-gateway en NPS.  Het moet worden geïnstalleerd op een in het domein opgenomen server die is gescheiden van de RD-gatewayserver. Gebruik de volgende procedure om de Azure Multi-Factor Authentication-server te configureren.

1. Open de Azure Multi-Factor Authentication-server en selecteer het pictogram RADIUS-verificatie.
2. Schakel het selectievakje **RADIUS-verificatie inschakelen** in.
3. Controleer op het tabblad Clients of de poorten overeenkomen met wat in NPS is geconfigureerd en selecteer **Toevoegen**.
4. Voeg het IP-adres van de RD-gatewayserver, de naam van de toepassing (optioneel) en een gedeeld geheim toe. Het gedeelde geheim moet hetzelfde zijn op de Azure Multi-Factor Authentication-server en de RD-gateway.
3. Ga naar het tabblad **Doel** en selecteer het keuzerondje **RADIUS-server(s)**.
4. Selecteer **Toevoegen** en voer het IP-adres, het gedeeld geheim en de poorten van de NPS-server in. De RADIUS-client en het RADIUS-doel zijn hetzelfde, tenzij u een centrale NPS gebruikt. Het gedeeld geheim moet overeenkomen met het gedeeld geheim dat is ingesteld in de sectie RADIUS-client van de NPS-server.

![RADIUS-verificatie in MFA-server](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Volgende stappen

- Azure MFA en [IIS Web Apps](howto-mfaserver-iis.md) integreren

- Antwoorden krijgen in de [Veelgestelde vragen Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)