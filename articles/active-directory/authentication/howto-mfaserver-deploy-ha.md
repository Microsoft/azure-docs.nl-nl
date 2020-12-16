---
title: Hoge Beschik baarheid voor Azure MFA-server-Azure Active Directory
description: Implementeer meerdere exemplaren van Azure Multi-Factor Authentication-server in configuraties die hoge Beschik baarheid bieden.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584473"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Azure Multi-Factor Authentication-server configureren voor maximale Beschik baarheid

Voor een hoge Beschik baarheid van uw Azure server MFA-implementatie moet u meerdere MFA-servers implementeren. Deze sectie bevat informatie over een ontwerp met gelijke taak verdeling om uw doelen voor hoge Beschik baarheid te behalen in azure MFS Server-implementatie.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="mfa-server-overview"></a>Overzicht van MFA-server

De service architectuur van de Azure MFA-server omvat verschillende onderdelen, zoals wordt weer gegeven in het volgende diagram:

 ![Onderdelen van de MFA-server architectuur](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Een MFA-server is een Windows-Server waarop de Azure Multi-Factor Authentication-software is geïnstalleerd. De MFA-Server instantie moet worden geactiveerd door de MFA-service in azure om te kunnen functioneren. Meer dan één MFA-server kan on-premises worden geïnstalleerd.

De eerste MFA-server die is geïnstalleerd, is de primaire MFA-server bij activering door de Azure MFA-service. De primaire MFA-server heeft een schrijf bare kopie van de Phone factor. pfdata-data base. Volgende installaties van exemplaren van MFA-server worden ook wel onderliggende niveaus genoemd. De MFA-onderliggende niveaus hebben een gerepliceerde alleen-lezen kopie van de Phone factor. pfdata-data base. MFA-servers repliceren gegevens met behulp van Remote Procedure Call (RPC). Alle MFA-servers moeten gezamenlijk lid zijn van een domein of zelfstandig zijn om gegevens te repliceren.

Zowel de primaire als de onderliggende MFA-server van MFA communiceren met de MFA-service wanneer twee ledige verificatie is vereist. Wanneer een gebruiker bijvoorbeeld probeert toegang te krijgen tot een toepassing waarvoor twee ledige verificatie is vereist, wordt de gebruiker eerst geverifieerd door een id-provider, zoals Active Directory (AD).

Nadat de verificatie met AD is geslaagd, communiceert de MFA-server met de MFA-service. De MFA-server wacht op meldingen van de MFA-service om de gebruiker toegang tot de toepassing toe te staan of te weigeren.

Als de primaire MFA-server offline gaat, kunnen verificaties nog steeds worden verwerkt, maar bewerkingen waarvoor wijzigingen in de MFA-data base nodig zijn, kunnen niet worden verwerkt. (Voor beelden hiervan zijn: het toevoegen van gebruikers, pincode wijzigingen van de selfservice, het wijzigen van gebruikers gegevens of toegang tot de gebruikers Portal)

## <a name="deployment"></a>Implementatie

Houd rekening met de volgende belang rijke punten voor de taak verdeling van de Azure MFA-server en de bijbehorende onderdelen.

* **RADIUS-standaard gebruiken om hoge Beschik baarheid te garanderen**. Als u Azure MFA-servers als RADIUS-servers gebruikt, kunt u één MFA-server als primaire RADIUS-authenticatie doel en andere Azure MFA-servers als secundaire verificatie doelen configureren. Deze methode om maximale Beschik baarheid te bereiken, is echter mogelijk niet praktisch omdat u moet wachten totdat er een time-outperiode optreedt wanneer authenticatie mislukt voor het primaire authenticatie doel voordat u kunt worden geverifieerd op basis van het secundaire authenticatie doel. Het is efficiënter om het RADIUS-verkeer tussen de RADIUS-client en de RADIUS-servers te verdelen (in dit geval de Azure MFA-servers die fungeren als RADIUS-servers), zodat u de RADIUS-clients kunt configureren met één URL waarnaar ze kunnen verwijzen.
* **U moet de MFA-ondergeschikten hand matig promo veren**. Als de primaire Azure MFA-server offline gaat, blijven de secundaire Azure MFA-servers de MFA-aanvragen verwerken. Voordat een primaire MFA-server beschikbaar is, kunnen beheerders echter geen gebruikers toevoegen of MFA-instellingen wijzigen, en kunnen gebruikers geen wijzigingen aanbrengen via de gebruikers Portal. Het promo veren van een MFA-ondergeschikte voor de primaire rol is altijd een hand matig proces.
* **Separability van onderdelen**. De Azure MFA-server omvat verschillende onderdelen die kunnen worden geïnstalleerd op hetzelfde exemplaar van Windows Server of op verschillende exemplaren. Deze onderdelen zijn onder andere de gebruikers Portal, de webservice voor de mobiele app en de ADFS-adapter (agent). Met deze separability kunt u de Web Application proxy gebruiken om de webserver van de gebruikers Portal en de mobiele app te publiceren vanuit het perimeter netwerk. Een dergelijke configuratie wordt toegevoegd aan de algehele beveiliging van uw ontwerp, zoals wordt weer gegeven in het volgende diagram. De gebruikers portal van MFA en de webserver voor mobiele apps kunnen ook worden geïmplementeerd in HA-configuraties met gelijke taak verdeling.

   ![MFA-server met een perimeter netwerk](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Eenmalig wacht woord (OTP) via SMS (ook wel bekend als SMS) vereist het gebruik van plak sessies als het verkeer gelijkmatig wordt verdeeld**. Eenrichtings-SMS is een verificatie optie die ervoor zorgt dat de MFA-server de gebruikers een SMS-bericht met een OTP verzendt. De gebruiker voert de OTP in een prompt venster in om de MFA-Challenge te volt ooien. Als u een taak verdeling voor Azure MFA-servers hebt, moet dezelfde server die de eerste verificatie aanvraag heeft bediend de server zijn die het OTP-bericht van de gebruiker ontvangt. Als een andere MFA-server het OTP-antwoord ontvangt, mislukt de verificatie vraag. Zie [eenmalig wacht woord via SMS dat is toegevoegd aan de Azure MFA-server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server)voor meer informatie.
* **Voor implementaties met een gelijke taak verdeling van de gebruikers Portal en de webservice voor mobiele apps zijn plak sessies vereist**. Als u taak verdeling de MFA-gebruikers Portal en de webservice voor mobiele apps hebt, moet elke sessie op dezelfde server blijven.

## <a name="high-availability-deployment"></a>Implementatie met hoge Beschik baarheid

Het volgende diagram toont de volledige HA-implementatie van Azure MFA en de bijbehorende onderdelen, samen met ADFS voor referentie.

 ![Implementatie van Azure MFA server HA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Houd rekening met de volgende items voor het bijbehorende gedeelte van het voor gaande diagram.

1. De twee Azure MFA-servers (MFA1 en MFA2) zijn taak verdeling (mfaapp.contoso.com) en zijn geconfigureerd voor het gebruik van een statische poort (4443) voor het repliceren van de Phone factor. pfdata-data base. De Web Service SDK is geïnstalleerd op elke MFA-server om communicatie via TCP-poort 443 met de ADFS-servers in te scha kelen. De MFA-servers worden geïmplementeerd in een stateless configuratie met gelijke taak verdeling. Als u echter OTP wilt gebruiken via SMS, moet u de statusloze taak verdeling gebruiken.
   ![Azure MFA-server-app server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Omdat RPC dynamische poorten gebruikt, is het niet raadzaam om firewalls te openen tot het bereik van dynamische poorten die door RPC mogelijk kunnen worden gebruikt. Als u een firewall **tussen** uw MFA-toepassings servers hebt, moet u de MFA-server zo configureren dat deze communiceert op een statische poort voor het replicatie verkeer tussen de onderliggende en primaire servers en de poort op de firewall opent. U kunt de statische poort afdwingen door een DWORD-register waarde ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` te maken bij ```Pfsvc_ncan_ip_tcp_port``` de naam en de waarde in te stellen op een beschik bare statische poort. Verbindingen worden altijd geïnitieerd door de onderliggende MFA-servers naar de primaire server, de statische poort is alleen vereist voor de primaire, maar omdat u een onderliggend niveau op elk gewenst moment kunt verhogen, moet u de statische poort instellen op alle MFA-servers.

2. De twee gebruikers Portal/MFA Mobile App servers (MFA-UP-MAS1 en MFA-UP-MAS2) worden gelijkmatig verdeeld in een **stateful** configuratie (MFA.contoso.com). U herinnert dat Sticky Sessions een vereiste zijn voor de taak verdeling van de MFA-gebruikers Portal en de mobiele App Service.
   ![Azure MFA-Server-gebruikers Portal en mobiele App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. De AD FS-server farm wordt gelijkmatig verdeeld en gepubliceerd op Internet via ADFS-proxy's met taak verdeling in het perimeter netwerk. Elke ADFS-server gebruikt de ADFS-agent om te communiceren met de Azure MFA-servers met een single load-balanced URL (mfaapp.contoso.com) via TCP-poort 443.

## <a name="next-steps"></a>Volgende stappen

* [Azure MFA-server installeren en configureren](howto-mfaserver-deploy.md)
