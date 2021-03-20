---
title: Beleids instellingen voor Web Application Firewall met Azure front-deur
description: Meer informatie over Web Application firewall (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75932615"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Beleids instellingen voor Web Application firewall op de voor deur van Azure

Met een WAF-beleid (Web Application firewall) kunt u de toegang tot uw webtoepassingen beheren met een set aangepaste en beheerde regels. De naam van het WAF-beleid moet uniek zijn. U krijgt een validatie fout als u een bestaande naam probeert te gebruiken. Er zijn meerdere beleids instellingen die van toepassing zijn op alle regels die voor dat beleid zijn opgegeven, zoals beschreven in dit artikel.

## <a name="waf-state"></a>WAF-status

Een WAF-beleid voor de voor deur kan een van de volgende twee statussen hebben:
- **Ingeschakeld:** Wanneer een beleid is ingeschakeld, inspecteert WAF actief inkomende aanvragen en worden de bijbehorende acties uitgevoerd volgens regel definities
- **Uitgeschakeld:** -wanneer een beleid is uitgeschakeld, wordt de WAF-inspectie onderbroken. Binnenkomende aanvragen passeren WAF en worden naar back-ends verzonden op basis van front-deur routering.

## <a name="waf-mode"></a>WAF-modus

Een WAF-beleid kan in de volgende twee modi worden uitgevoerd:

- **Detectie modus** Wanneer u de detectie modus uitvoert, neemt WAF geen andere acties dan de controle en registreert de aanvraag en de overeenkomende WAF-regel naar WAF-Logboeken. Diagnostische gegevens over logboek registratie inschakelen voor de voor deur (wanneer u Portal gebruikt, kunt u dit bereiken door naar de sectie **Diagnostische gegevens** in de Azure portal te gaan).

- **Preventie modus** Wanneer de WAF is geconfigureerd om te worden uitgevoerd in de preventie modus, neemt de opgegeven actie af als een aanvraag overeenkomt met een regel. Alle overeenkomende aanvragen worden ook in de WAF-logboeken vastgelegd.

## <a name="waf-response-for-blocked-requests"></a>WAF-antwoord voor geblokkeerde aanvragen

Wanneer WAF een aanvraag blokkeert vanwege een overeenkomende regel, retourneert deze standaard de status code 403 met- **de aanvraag is een geblokkeerd** bericht. Er wordt ook een verwijzings reeks geretourneerd voor logboek registratie.

U kunt een aangepaste antwoord status code en een antwoord bericht definiëren wanneer een aanvraag wordt geblokkeerd door WAF. De volgende aangepaste status codes worden ondersteund:

- 200 OK
- 403 verboden
- methode 405 niet toegestaan
- 406 niet toegestaan
- 429 te veel aanvragen

De status code en het antwoord bericht van de aangepaste reactie is een instelling voor het beleids niveau. Zodra deze is geconfigureerd, krijgen alle geblokkeerde aanvragen dezelfde aangepaste reactie status en een antwoord bericht.

## <a name="uri-for-redirect-action"></a>URI voor de omleidings actie

U moet een URI definiëren om aanvragen om te leiden als de **omleidings** actie is geselecteerd voor een van de regels die in een WAF-beleid zijn opgenomen. Deze omleidings-URI moet een geldige HTTP (S)-site zijn en eenmaal zijn geconfigureerd, worden alle aanvragen die overeenkomen met een actie ' omleiden ' omgeleid naar de opgegeven site.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het definiëren van [aangepaste antwoorden](waf-front-door-configure-custom-response-code.md) op WAF
