---
title: Kan geen toegang krijgen tot deze zakelijke toepassings fout met app proxy-app
description: Veelvoorkomende toegangs problemen oplossen met Azure AD-toepassingsproxy-toepassingen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b9fb68643c48c685194fa7ba1f1e5050d2d3cc7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254928"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>Fout bericht ' kan geen toegang krijgen tot deze bedrijfs toepassing ' wanneer u een toepassings proxy toepassing gebruikt

Dit artikel helpt u bij het oplossen van veelvoorkomende problemen met de fout ' deze zakelijke app kan niet worden geopend ' op een Azure AD-toepassingsproxy-toepassing.

## <a name="overview"></a>Overzicht

Wanneer u deze fout ziet, zoekt u de status code op de fout pagina. Deze code is waarschijnlijk een van de volgende status codes:

- **Time-out van Gateway**: de service Application proxy kan de connector niet bereiken. Deze fout duidt doorgaans op een probleem met de connector toewijzing, connector zelf of de netwerk regels rondom de connector.
- **Ongeldige gateway**: de connector kan de back-end-toepassing niet bereiken. Deze fout kan duiden op een onjuiste configuratie van de toepassing.
- **Verboden**: de gebruiker is niet gemachtigd om toegang te krijgen tot de toepassing. Deze fout kan optreden als de gebruiker niet is toegewezen aan de toepassing in Azure Active Directory, of als op de back-end de gebruiker geen machtiging heeft voor toegang tot de toepassing.

Als u de code wilt vinden, bekijkt u de tekst linksonder in het fout bericht voor het veld status code. Kijk ook naar extra tips onder aan de pagina.

![Voor beeld: time-out van Gateway](./media/application-proxy-sign-in-bad-gateway-timeout-error/connection-problem.png)

Zie de bijbehorende sectie hieronder voor meer informatie over het oplossen van de hoofd oorzaak van deze fouten en meer informatie over voorgestelde oplossingen.

## <a name="gateway-timeout-errors"></a>Time-outfouten voor gateway

Er treedt een time-out voor de gateway op wanneer de service de connector probeert te bereiken en niet binnen het time-outvenster kan worden gehaald. Deze fout wordt meestal veroorzaakt door een toepassing die is toegewezen aan een connector groep zonder werkende connectors, of sommige poorten die door de connector zijn vereist, zijn niet geopend.

## <a name="bad-gateway-errors"></a>Ongeldige gateway fouten

Een ongeldige gateway fout geeft aan dat de connector de back-end-toepassing niet kan bereiken. Zorg ervoor dat u de juiste toepassing hebt gepubliceerd. Veelvoorkomende fouten die deze fout veroorzaken, zijn:

- Een type of fout in de interne URL
- De hoofdmap van de toepassing niet publiceren. Bijvoorbeeld publiceren, `http://expenses/reimbursement` maar toegang proberen `http://expenses`
- Problemen met de configuratie van de Kerberos-beperkte overdracht (KCD)
- Problemen met de back-end-toepassing

## <a name="forbidden-errors"></a>Verboden fouten

Als er een verboden fout wordt weer geven, is de gebruiker niet toegewezen aan de toepassing. Deze fout kan zich voordoen in Azure Active Directory of op de back-end-toepassing.

Zie de [configuratie documentatie](application-proxy-add-on-premises-application.md#test-the-application)voor meer informatie over het toewijzen van gebruikers aan de toepassing in Azure.

Als u bevestigt dat de gebruiker is toegewezen aan de toepassing in azure, controleert u de gebruikers configuratie in de back-end-toepassing. Als u beperkte Kerberos-overdracht/geïntegreerde Windows-authenticatie gebruikt, raadpleegt u de pagina KCD Troubleshooting (Engelstalig) voor richt lijnen.

## <a name="check-the-applications-internal-url"></a>De interne URL van de toepassing controleren

Als eerste snelle stap controleert en corrigeert u de interne URL door de toepassing te openen via **zakelijke toepassingen** en vervolgens het menu **toepassings proxy** te selecteren. Controleer of de interne URL wordt gebruikt in uw on-premises netwerk om toegang te krijgen tot de toepassing.

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>Controleren of de toepassing is toegewezen aan een werkende connector groep

Controleren of de toepassing is toegewezen aan een werkende connector groep:

1. Open de toepassing in de portal door naar **Azure Active Directory** te gaan, op **bedrijfs toepassingen** en vervolgens op **alle toepassingen** te klikken. Open de toepassing en selecteer vervolgens **toepassings proxy** in het menu links.
1. Bekijk het veld connector groep. Als er geen actieve connectors in de groep aanwezig zijn, wordt er een waarschuwing weer gegeven. Als er geen waarschuwingen worden weer gegeven, gaat u naar om te controleren of alle [vereiste poorten](application-proxy-add-on-premises-application.md) zijn toegestaan.
1. Als de verkeerde connector groep wordt weer gegeven, gebruikt u de vervolg keuzelijst om de juiste groep te selecteren en controleert u of er geen waarschuwingen meer beschikbaar zijn. Als de bedoelde connector groep wordt weer gegeven, klikt u op het waarschuwings bericht om de pagina te openen met het beheer van connectors.
1. Hier volgen enkele manieren om verder in te zoomen:

   - Een actieve connector verplaatsen naar de groep: als u een actieve connector hebt die deel moet uitmaken van deze groep en een regel voor de detectie van de doel-backend-toepassing heeft, kunt u de connector verplaatsen naar de toegewezen groep. Als u dit wilt doen, klikt u op de connector. In het veld connector groep gebruikt u de vervolg keuzelijst om de juiste groep te selecteren en klikt u op opslaan.
   - Een nieuwe connector voor die groep downloaden: vanaf deze pagina kunt u de koppeling ophalen om [een nieuwe connector te downloaden](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download). Installeer de connector op een computer met een rechtstreekse gezichts lijn voor de back-end-toepassing. Normaal gesp roken wordt de connector geïnstalleerd op dezelfde server als de toepassing. Gebruik de koppeling connector downloaden om een connector te downloaden naar de doel computer. Klik vervolgens op de connector en gebruik de vervolg keuzelijst connector groep om te controleren of deze bij de juiste groep hoort.
   - Een inactieve connector onderzoeken: als een connector als inactief wordt weer gegeven, kan de service niet worden bereikt. Deze fout wordt meestal veroorzaakt doordat de vereiste poorten zijn geblokkeerd. Als u dit probleem wilt oplossen, gaat u naar om te controleren of alle vereiste poorten zijn toegestaan.

Nadat u deze stappen hebt uitgevoerd om ervoor te zorgen dat de toepassing wordt toegewezen aan een groep met werkende connectors, test u de toepassing opnieuw. Als het nog steeds niet werkt, gaat u door naar de volgende sectie.

## <a name="check-all-required-ports-are-open"></a>Controleer of alle vereiste poorten zijn geopend

Controleer of alle vereiste poorten zijn geopend. Zie voor de vereiste poorten het gedeelte poorten openen [in de zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in azure Active Directory](application-proxy-add-on-premises-application.md). Als alle vereiste poorten open zijn, gaat u naar de volgende sectie.

## <a name="check-for-other-connector-errors"></a>Controleren op andere connector fouten

Als geen van de bovenstaande stappen het probleem oplost, is de volgende stap het zoeken naar problemen of fouten met de connector zelf. In het [document problemen oplossen](./application-proxy-troubleshoot.md#connector-errors)vindt u enkele veelvoorkomende fouten.

U kunt ook direct zoeken in de connector Logboeken om eventuele fouten te identificeren. Veel van de fout berichten delen specifieke aanbevelingen voor oplossingen. Raadpleeg de [documentatie van connectors](application-proxy-connectors.md#under-the-hood)als u de logboeken wilt weer geven.

## <a name="additional-resolutions"></a>Aanvullende oplossingen

Als het probleem hiermee niet is opgelost, zijn er enkele mogelijke oorzaken. Het probleem identificeren:

Als uw toepassing is geconfigureerd voor het gebruik van geïntegreerde Windows-authenticatie (IWA), test u de toepassing zonder eenmalige aanmelding. Als dat niet het geval is, gaat u naar de volgende alinea. Als u de toepassing zonder eenmalige aanmelding wilt controleren, opent u uw toepassing via **bedrijfs toepassingen** en gaat u naar het menu voor **eenmalige aanmelding** . Wijzig de vervolg keuzelijst van ' geïntegreerde Windows-verificatie ' in ' voor eenmalige aanmelding bij Azure AD uitgeschakeld '.

Open nu een browser en probeer opnieuw toegang te krijgen tot de toepassing. U wordt gevraagd om verificatie en u krijgt toegang tot de toepassing. Als u zich kunt verifiëren, is het probleem de KCD-configuratie (Kerberos-beperkte overdracht) waarmee eenmalige aanmelding mogelijk is. Zie de pagina KCD Troubleshooting (Engelstalig) voor meer informatie.

Als u de fout blijft zien, gaat u naar de computer waarop de connector is geïnstalleerd, opent u een browser en probeert u de interne URL te bereiken die voor de toepassing wordt gebruikt. De connector fungeert als een andere client van dezelfde computer. Als u de toepassing niet kunt bereiken, onderzoekt u waarom die computer de toepassing niet kan bereiken, of gebruikt u een connector op een server die toegang heeft tot de toepassing.

Als u de toepassing vanaf die computer kunt bereiken, kunt u zoeken naar problemen of fouten met de connector zelf. In het [document problemen oplossen](application-proxy-troubleshoot.md#connector-errors)vindt u enkele veelvoorkomende fouten. U kunt ook direct zoeken in de connector Logboeken om eventuele fouten te identificeren. Veel van onze fout berichten kunnen specifieke aanbevelingen voor oplossingen delen. Zie [onze connectors-documentatie](application-proxy-connectors.md#under-the-hood)voor meer informatie over het weer geven van de logboeken.

## <a name="next-steps"></a>Volgende stappen

[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)