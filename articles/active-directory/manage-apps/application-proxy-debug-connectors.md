---
title: Fout opsporing voor Application proxy-connectors-Azure Active Directory | Microsoft Docs
description: Problemen met de connector voor toepassings proxy van Azure Active Directory (Azure AD) oplossen.
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
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256963"
---
# <a name="debug-application-proxy-connector-issues"></a>Fouten opsporen in connector van toepassingsproxy 

Dit artikel helpt u bij het oplossen van problemen met de connectors voor toepassings proxy van Azure Active Directory (Azure AD). Als u de Application proxy-service gebruikt voor externe toegang tot een on-premises webtoepassing, maar u problemen hebt met het maken van verbinding met de toepassing, gebruikt u dit stroom diagram om problemen met de connector op te lossen. 

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de connector voor de toepassings proxy hebt geïnstalleerd en een probleem ondervindt. Bij het oplossen van problemen met toepassings proxy raden we u aan om te beginnen met deze probleemoplossings stroom om te bepalen of toepassings proxy-connectors correct zijn geconfigureerd. Als u nog steeds problemen hebt met het maken van verbinding met de toepassing, volgt u de stroom problemen oplossen in toepassings [problemen met toepassings proxy](application-proxy-debug-apps.md).  


Voor meer informatie over toepassings proxy en het gebruik van de connectors raadpleegt u:

- [Externe toegang tot on-premises toepassingen via toepassings proxy](application-proxy.md)
- [Application proxy-connectors](application-proxy-connectors.md)
- [Een connector installeren en registreren](application-proxy-add-on-premises-application.md)
- [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Stroom diagram voor connector problemen

Dit stroom diagram leidt u door de stappen voor het opsporen van fouten in een aantal van de meest voorkomende problemen met de connector. Zie de tabel die volgt op het stroom diagram voor meer informatie over elke stap.

![Stroom diagram met stappen voor het opsporen van fouten in een connector](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Stap | Bewerking | Beschrijving |
|---------|---------|---------|
|1 | De connector groep zoeken die is toegewezen aan de app | U hebt waarschijnlijk een connector op meerdere servers geïnstalleerd. in dat geval moeten de connectors worden [toegewezen aan connector groepen](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Zie [toepassingen op verschillende netwerken en locaties publiceren met connector groepen](application-proxy-connector-groups.md)voor meer informatie over connector groepen. |
|2 | De connector installeren en een groep toewijzen | Als u geen connector hebt geïnstalleerd, raadpleegt u [een connector installeren en registreren](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Zie [probleem met het](application-proxy-connector-installation-problem.md)installeren van de connector als u problemen ondervindt bij het installeren van de connector.<br></br> Als de connector niet is toegewezen aan een groep, raadpleegt [u de connector toewijzen aan een groep](application-proxy-connector-groups.md#create-connector-groups).<br></br>Als de toepassing niet is toegewezen aan een connector groep, raadpleegt [u de toepassing toewijzen aan een connector groep](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Een poort test uitvoeren op de connector server | Voer op de connector server een poort test uit met behulp van [Telnet](/windows-server/administration/windows-commands/telnet) of een ander hulp programma voor poort testen om te controleren of de poorten [443 en 80 zijn geopend](application-proxy-add-on-premises-application.md#open-ports).|
|4 | De domeinen en poorten configureren | [Zorg ervoor dat uw domeinen en poorten correct zijn geconfigureerd](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) De connector werkt alleen goed als er bepaalde poorten zijn geopend en Url's waartoe de server toegang moet hebben. |
|5 | Controleren of een back-end-proxy wordt gebruikt | Controleer of de connectors gebruikmaken van back-endservers van proxy servers of overs Laan. Zie problemen [met Connector proxy en problemen met de service connectiviteit oplossen](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)voor meer informatie. |
|6 | De connector en de updater bijwerken om de back-end-proxy te gebruiken | Als een back-end-proxy wordt gebruikt, moet u controleren of de connector dezelfde proxy gebruikt. Zie [werken met bestaande on-premises proxy servers](application-proxy-configure-connectors-with-proxy-servers.md)voor meer informatie over het oplossen van problemen en het configureren van connectors voor gebruik met proxy servers. |
|7 | De interne URL van de app op de connector server laden | Laad de interne URL van de app op de connector server. |
|8 | Interne netwerk verbinding controleren | Er is een probleem met de connectiviteit in uw interne netwerk die niet kan worden vastgesteld door deze fout opsporen. De toepassing moet intern toegankelijk zijn voor de werking van de connectors. U kunt connector gebeurtenis logboeken inschakelen en weer geven zoals beschreven in [Application proxy-connectors](application-proxy-connectors.md#under-the-hood). |
|9 | De time-outwaarde voor de back-end verlengen | Wijzig in de **aanvullende instellingen** voor uw toepassing de time-outinstelling van de **back-end-toepassing** in **lang**. Zie [een on-premises app toevoegen aan Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Als problemen zich blijven voordoen, kunt u specifieke stroom problemen verhelpen, de app-en SSO-fout opsporing controleren | Gebruik de stroom probleem oplossing toepassings problemen oplossen van de toepassings [proxy](application-proxy-debug-apps.md) . |

## <a name="next-steps"></a>Volgende stappen


* [Toepassingen publiceren op afzonderlijke netwerken en locaties met connector groepen](application-proxy-connector-groups.md)
* [Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
* [Problemen met toepassings proxy en connector fouten oplossen](application-proxy-troubleshoot.md)
* [De Azure AD-toepassingsproxy-connector op de achtergrond installeren](application-proxy-register-connector-powershell.md)