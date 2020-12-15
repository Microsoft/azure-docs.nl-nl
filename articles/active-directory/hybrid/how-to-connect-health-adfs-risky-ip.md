---
title: Azure AD Connect Health met AD FS riskive IP-rapport | Microsoft Docs
description: Hierin wordt het Azure AD Connect Health AD FS riskive IP-rapport beschreven.
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57d74272d77183baa2284265aee298967f641250
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504879"
---
# <a name="risky-ip-report-public-preview"></a>Risk ante IP-rapport (open bare preview)
AD FS klanten kunnen wacht woord verificatie-eind punten beschikbaar stellen aan Internet om verificatie services te bieden aan eind gebruikers die toegang hebben tot SaaS-toepassingen, zoals Microsoft 365. In dat geval kunnen criminelen aanmeldpogingen uitvoeren op uw AD FS-systeem om het wachtwoord van een eindgebruiker te achterhalen en toegang te krijgen tot toepassingsresources. AD FS biedt de extranetfunctionaliteit voor accountvergrendeling om dergelijke aanvallen te voorkomen vanaf AD FS in Windows Server 2012 R2. Als u een lagere versie gebruikt, raden we u ten zeerste aan uw AD FS-systeem naar Windows Server 2016 te upgraden. <br />

Daarnaast is het mogelijk dat vanaf één IP-adres meerdere aanmeldpogingen voor meerdere gebruikers worden uitgevoerd. In dit soort gevallen is het aantal pogingen per gebruiker mogelijk lager dan de drempel voor beveiliging met accountvergrendeling in AD FS. Azure AD Connect Health biedt nu het rapport Riskant IP-adres dat deze gebeurtenis detecteert en beheerders hierover waarschuwt. De belangrijkste voordelen van dit rapport zijn: 
- Detectie van IP-adressen die een drempel voor mislukte aanmeldingen op basis van wachtwoord overschrijden
- Ondersteuning voor mislukte aanmeldingen vanwege een onjuist wachtwoord of een vergrendelde status in extranet
- E-mailmeldingen voor beheerders zodra dit gebeurt, met aanpasbare e-mailinstellingen
- Aanpasbare drempelwaarde voor afstemming op het beveiligingsbeleid van een organisatie
- Downloadbare rapporten voor offline analyse en integratie met andere systemen via automatisering

> [!NOTE]
> Om dit rapport te gebruiken, moet u controles voor AD FS hebben ingeschakeld. Ga voor meer informatie naar [Controles voor AD FS inschakelen](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs). <br />
> Voor toegang tot de preview-versie is een machtiging als Globale beheerder of [Beveiligingslezer](../../role-based-access-control/built-in-roles.md#security-reader) vereist.  
>

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *white list*, een term die micro soft niet meer gebruikt. Wanneer de periode van de software wordt verwijderd, worden deze uit dit artikel verwijderd.

## <a name="what-is-in-the-report"></a>Wat is er in het rapport?
De mislukte aanmeldings activiteit client-IP-adressen worden geaggregeerd via Web Application proxy-servers. Elk item in het rapport Riskant IP-adres toont verzamelde informatie over mislukte AD FS-aanmeldactiviteiten die de aangewezen drempelwaarde overschrijden. Het bevat de volgende informatie: ![ scherm afbeelding met een Risk ante IP-rapport met kolom koppen gemarkeerd.](./media/how-to-connect-health-adfs/report4a.png)

| Rapportitem | Beschrijving |
| ------- | ----------- |
| Tijdstempel | Geeft het tijdstempel weer op basis van de lokale tijd in Azure Portal wanneer de detectieperiode start.<br /> Alle gebeurtenissen per dag worden gegenereerd om 24:00 uur UTC-tijd. <br />Bij gebeurtenissen per uur is het tijdstempel afgerond naar het begin van het uur. U vindt de begintijd van de eerste activiteit vanaf firstAuditTimestamp in het geëxporteerde bestand. |
| Triggertype | Geeft het type tijdvenster voor detectie weer. De triggertypen voor aggregatie zijn per uur of per dag. Dit is handig voor het detecteren van een beveiligingsaanval met hoge frequentie en een trage beveiligingsaanval waarbij het aantal pogingen over de dag is verdeeld. |
| IP-adres | Het specifieke riskante IP-adres voor activiteiten met betrekking tot onjuiste wachtwoorden of extranetvergrendeling. Dit kan een IPv4-of een IPv6-adres zijn. |
| Aantal mislukte wachtwoordpogingen | Het aantal mislukte wachtwoordpogingen vanaf het IP-adres tijdens de detectieperiode. Mislukte wachtwoordpogingen kunnen meerdere keren plaatsvinden bij bepaalde gebruikers. Dit omvat geen mislukte pogingen vanwege verlopen wachtwoorden. |
| Aantal mislukte extranetvergrendelingen | Het aantal mislukte extranetvergrendelingen vanaf het IP-adres tijdens de detectieperiode. Mislukte extranetvergrendelingen kunnen meerdere keren plaatsvinden bij bepaalde gebruikers. Dit is alleen zichtbaar als Vergrendeling van het extranet is geconfigureerd in AD FS (versies 2012R2 of hoger). <b>Opmerking</b>: we raden u aan deze functie in te schakelen als u extranetaanmeldingen met wachtwoorden toestaat. |
| Pogingen unieke gebruikersnaam | Het aantal mislukte pogingen met unieke gebruikersnaam vanaf het IP-adres tijdens de detectieperiode. Dit biedt een mechanisme om een aanvalspatroon met een enkele gebruiker van een aanvalspatroon met meerdere gebruikers te onderscheiden.  |

Het onderstaande rapportitem geeft bijvoorbeeld aan dat in het tijdvenster van 18:00 tot 19:00 uur op 28-02-2018 het IP-adres <i>104.2XX.2XX.9</i> geen mislukte wachtwoordpogingen en 284 mislukte extranetvergrendelingen had. 14 unieke gebruikers zijn volgens de criteria beïnvloed. De activiteitsgebeurtenis overschrijdt de aangewezen drempelwaarde per uur van het rapport. 

![Scherm afbeelding met een voor beeld van een Risk ante IP-rapport vermelding.](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - Alleen activiteiten die de aangewezen drempelwaarde overschrijden, worden in de rapportlijst weergegeven. 
> - Dit rapport kan tot 30 dagen terug worden bekeken.
> - Dit waarschuwingsrapport geeft geen Exchange-IP-adressen of privé-IP-adressen weer. Ze worden wel opgenomen in de exportlijst. 
>

![Scherm opname van het rapport Risk ante IP met de opties ' downloaden ', ' meldings instellingen ' en ' drempel instellingen ' gemarkeerd.](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>IP-adressen van Load Balancer in de lijst
Load balancer verzamelt mislukte aanmeldactiviteiten en bereikt de drempelwaarde voor waarschuwingen. Als u load balancer IP-adressen ziet, is het zeer waarschijnlijk dat uw externe load balancer het client-IP-adres niet verzendt wanneer deze de aanvraag naar de proxyserver van de webtoepassing doorstuurt. Configureer de load balancer op een juiste manier om het client-IP-adres door te schakelen. 

## <a name="download-risky-ip-report"></a>Risk ante IP-rapport downloaden 
Met behulp van de functie **Downloaden** kan de volledige lijst met riskante IP-adressen van de afgelopen 30 dagen worden geëxporteerd vanuit de Connect Health-portal. De export bevat alle mislukte AD FS-aanmeldactiviteiten in elke detectieperiode, zodat u de filters na het exporteren kunt aanpassen. De export geeft naast de gemarkeerde aggregaties in de portal ook meer informatie over mislukte aanmeldactiviteiten per IP-adres weer:

|  Rapportitem  |  Beschrijving  | 
| ------- | ----------- | 
| firstAuditTimestamp | Geeft de eerste tijdstempel weer voor de start van de mislukte activiteiten tijdens de detectieperiode.  | 
| lastAuditTimestamp | Geeft de laatste tijdstempel weer voor het einde van de mislukte activiteiten tijdens de detectieperiode.  | 
| attemptCountThresholdIsExceeded | De markering voor wanneer de huidige activiteiten de drempelwaarde voor waarschuwingen overschrijden.  | 
| isWhitelistedIpAddress | De markering voor wanneer het IP-adres is uitgesloten voor waarschuwingen en rapporten. Privé-IP-adressen (<i>10.x.x.x, 172.x.x.x en 192.168.x.x</i>) en Exchange-IP-adressen worden eruit gefilterd en als waar gemarkeerd. Als u privé-IP-adresbereiken ziet, is het zeer waarschijnlijk dat de externe load balancer het client-IP-adres niet verzendt wanneer deze de aanvraag naar de proxyserver van de webtoepassing doorstuurt.  | 

## <a name="configure-notification-settings"></a>Instellingen voor meldingen configureren
Beheerders van het rapport kunnen met de **Meldingsinstellingen** op de hoogte worden gehouden. De e-mailmelding voor riskante IP-adressen is standaard uitgeschakeld. U kunt de melding inschakelen door de knop onder 'E-mailmeldingen ontvangen voor een rapport van IP-adressen waarvoor de drempelwaarden voor mislukte aanmeldingen zijn overschreden' in te schakelen. Net als in de algemene meldingsinstellingen in Connect Health kunt u hier de ontvangerslijst voor Riskant IP-adres-rapporten aanpassen. U kunt ook alle globale beheerders informeren bij het maken van de wijziging. 

## <a name="configure-threshold-settings"></a>Drempel instellingen configureren
De drempelwaarde voor waarschuwingen kan via de drempelwaarde-instellingen worden bijgewerkt. De drempelwaarde is in het begin standaard ingesteld in het systeem. De standaard waarden worden hieronder gegeven. Er zijn vier categorieën in de drempelwaarde-instellingen voor Riskant IP-adres-rapporten:

![Portal voor Azure AD Connect Health](./media/how-to-connect-health-adfs/report4d.png)

| Drempelwaarde-item | Beschrijving |
| --- | --- |
| (Slechte U/P + vergrendeling van het extranet) / dag  | Ingestelde drempelwaarde per **dag** voor het rapporteren van de activiteit en het activeren van een melding wanneer het aantal onjuiste wachtwoordpogingen plus het aantal extranetvergrendelingen is overschreden. De standaard waarde is 100.|
| (Slechte U/P + vergrendeling van het extranet) / uur | Ingestelde drempelwaarde per **uur** voor het rapporteren van de activiteit en het activeren van een melding wanneer het aantal onjuiste wachtwoordpogingen plus het aantal extranetvergrendelingen is overschreden. De standaard waarde is 50.|
| Vergrendeling van het extranet / dag | Ingestelde drempelwaarde per **dag** voor het rapporteren van de activiteit en het activeren van een melding wanneer het aantal extranetvergrendelingen is overschreden. De standaardwaarde is 50.|
| Vergrendeling van het extranet / uur| Ingestelde drempelwaarde per **uur** voor het rapporteren van de activiteit en het activeren van een melding wanneer het aantal extranetvergrendelingen is overschreden. De standaard waarde is 25|

> [!NOTE]
> - Een wijziging van de drempelwaarde van het rapport wordt na een uur toegepast. 
> - Bestaande gemelde items worden niet beïnvloed door het wijzigen van de drempelwaarde. 
> - We raden u aan om het aantal gebeurtenissen binnen uw omgeving te analyseren en de drempelwaarde aan de hand hiervan bij te stellen. 
>
>

## <a name="faq"></a>Veelgestelde vragen
**Waarom zie ik privé-IP-adresbereiken in het rapport?**  <br />
Privé-IP-adressen (<i>10. x. x. x, 172. x. x. x & 192.168. x. x</i>) en Exchange-IP-adressen worden gefilterd en als waar gemarkeerd in de lijst met goedgekeurde IP. Als u privé-IP-adresbereiken ziet, is het zeer waarschijnlijk dat de externe load balancer het client-IP-adres niet verzendt wanneer deze de aanvraag naar de proxyserver van de webtoepassing doorstuurt.

**Waarom krijg ik load balancer IP-adressen in het rapport te zien?**  <br />
Als u load balancer IP-adressen ziet, is het zeer waarschijnlijk dat uw externe load balancer het client-IP-adres niet verzendt wanneer deze de aanvraag naar de proxyserver van de webtoepassing doorstuurt. Configureer de load balancer op een juiste manier om het client-IP-adres door te schakelen. 

**Hoe kan ik een IP-adres blokkeren?**  <br />
Voeg geïdentificeerde schadelijke IP-adressen toe aan de firewall of blokkeer deze in Exchange.   <br />

**Waarom zie ik geen items in dit rapport?** <br />
- Het aantal mislukte aanmeldactiviteiten is lager dan de ingestelde drempelwaarde.
- Zorg ervoor dat er geen melding als 'Health-service is niet up-to-date' actief is in de AD FS-serverlijst.  Lees meer informatie over [het oplossen van deze waarschuwing](how-to-connect-health-data-freshness.md).
- Controles zijn niet ingeschakeld in AD FS-farms.

**Waarom krijg ik geen toegang tot het rapport?**  <br />
Voor toegang is een machtiging als Globale beheerder of [Beveiligingslezer](../../role-based-access-control/built-in-roles.md#security-reader) vereist. Neem contact op met uw globale beheerder om toegang te krijgen.


## <a name="next-steps"></a>Volgende stappen
* [Azure AD Connect Health (Engelstalig)](./whatis-azure-ad-connect.md)
* [De Azure AD Connect Health-agent installeren](how-to-connect-health-agent-install.md)
