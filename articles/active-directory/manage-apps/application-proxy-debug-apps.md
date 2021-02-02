---
title: Toepassings proxy toepassingen debuggen-Azure Active Directory | Microsoft Docs
description: Problemen met de toepassings proxy toepassingen van Azure Active Directory (Azure AD) oplossen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2066030b04feae4e50fee3442a663209801d5875
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253388"
---
# <a name="debug-application-proxy-application-issues"></a>Toepassingsproblemen met toepassingsproxy oplossen 

Dit artikel helpt u bij het oplossen van problemen met de toepassingen van de toepassings proxy van Azure Active Directory (Azure AD). Als u de Application proxy-service gebruikt voor externe toegang tot een on-premises webtoepassing, maar u problemen hebt met het maken van verbinding met de toepassing, gebruikt u dit stroom diagram om problemen met de toepassing op te sporen. 

## <a name="before-you-begin"></a>Voordat u begint

Bij het oplossen van problemen met toepassings proxy raden we u aan om te beginnen met de connectors. Volg eerst de stroom voor probleem oplossing in problemen met de [Connector voor toepassings proxy](application-proxy-debug-connectors.md) om te controleren of de toepassings proxy-connectors juist zijn geconfigureerd. Als u nog steeds problemen ondervindt, keert u terug naar dit artikel voor het oplossen van problemen met de toepassing.  

Zie voor meer informatie over toepassings proxy:

- [Externe toegang tot on-premises toepassingen via toepassings proxy](application-proxy.md)
- [Application proxy-connectors](application-proxy-connectors.md)
- [Een connector installeren en registreren](application-proxy-add-on-premises-application.md)
- [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Stroom diagram voor toepassings problemen

Dit stroom diagram leidt u door de stappen voor het opsporen van fouten in een aantal van de meest voorkomende problemen met het maken van verbinding met de toepassing. Zie de tabel die volgt op het stroom diagram voor meer informatie over elke stap.

![Stroom diagram met stappen voor het opsporen van fouten in een toepassing](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| Stap | Bewerking | Beschrijving |
|---------|---------|---------|
|1 | Open een browser, ga naar de app en voer uw referenties in | Gebruik uw referenties om u aan te melden bij de app en controleer wat gebruikers fouten hebben, zoals [deze zakelijke app niet kan worden geopend](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | De gebruikers toewijzing voor de app controleren | Zorg ervoor dat uw gebruikers account gemachtigd is om toegang te krijgen tot de app vanuit het bedrijfs netwerk en test vervolgens het aanmelden bij de app door de stappen in de [toepassing testen](application-proxy-add-on-premises-application.md#test-the-application)te volgen. Zie [problemen met aanmelden oplossen](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)als de aanmeldings problemen zich blijven voordoen.  |
|3 | Open een browser en probeer toegang te krijgen tot de app | Als er onmiddellijk een fout wordt weer gegeven, controleert u of de toepassings proxy juist is geconfigureerd. Zie [problemen met toepassings proxy en fout berichten oplossen](application-proxy-troubleshoot.md)voor meer informatie over specifieke fout berichten.  |
|4 | Controleer de instellingen van uw aangepaste domein of los de fout op | Als de pagina helemaal niet wordt weer gegeven, controleert u of uw aangepaste domein juist is geconfigureerd door te controleren of u [aan de slag gaat met aangepaste domeinen](application-proxy-configure-custom-domain.md).<br></br>Als de pagina niet wordt geladen en er een fout bericht wordt weer gegeven, moet u de fout oplossen door te verwijzen naar problemen  [met toepassings proxy en fout berichten oplossen](application-proxy-troubleshoot.md). <br></br>Als het langer dan 20 seconden duurt voordat een fout bericht wordt weer gegeven, kan het zijn dat er een probleem is met de verbinding. Ga naar het artikel problemen met [Application proxy-connectors](application-proxy-debug-connectors.md) oplossen.  |
|5 | Als problemen zich blijven voordoen, gaat u naar connector fout opsporing | Er kan een connectiviteits probleem zijn tussen de proxy en de connector of tussen de connector en de back-end. Ga naar het artikel problemen met [Application proxy-connectors](application-proxy-debug-connectors.md) oplossen. |
|6 | Alle resources publiceren, browser ontwikkelaars hulpprogramma's controleren en koppelingen herstellen | Zorg ervoor dat het pad voor het publiceren alle benodigde afbeeldingen, scripts en opmaak modellen voor uw toepassing bevat. Zie [een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)voor meer informatie. <br></br>Gebruik de ontwikkel hulpprogramma's van de browser (F12-hulpprogram ma's in Internet Explorer of micro soft Edge) en controleer of er problemen zijn met de publicatie, zoals beschreven op de [toepassings pagina wordt niet correct weer gegeven](application-proxy-page-appearance-broken-problem.md). <br></br>Bekijk de opties voor het oplossen van verbroken koppelingen in [koppelingen op de pagina werken niet](application-proxy-page-links-broken-problem.md). |
|7 | Controleren op netwerk latentie | Als de pagina langzaam wordt geladen, kunt u meer te weten komen over manieren om de netwerk latentie te minimaliseren in overwegingen bij het [verminderen van de latentie](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zie aanvullende Help voor probleem oplossing | Als er problemen blijven bestaan, kunt u aanvullende artikelen over probleem oplossing vinden in de documentatie voor het oplossen van problemen met [toepassings proxy](application-proxy-troubleshoot.md) |

## <a name="next-steps"></a>Volgende stappen


* [Toepassingen publiceren op afzonderlijke netwerken en locaties met connector groepen](application-proxy-connector-groups.md)
* [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
* [Problemen met toepassings proxy en connector fouten oplossen](application-proxy-troubleshoot.md)
* [De Azure AD-toepassingsproxy-connector op de achtergrond installeren](application-proxy-register-connector-powershell.md)
