---
title: Problemen oplossen met een Application Gateway in azure – ILB ASE | Microsoft Docs
description: Meer informatie over het oplossen van problemen met een toepassings gateway met behulp van een interne Load Balancer met een App Service Environment in azure
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84628536"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Het back-endserver certificaat is niet toegestaan voor een toepassings gateway die gebruikmaakt van een interne Load Balancer met een App Service Environment

In dit artikel wordt het volgende probleem opgelost: een certificaat is niet toegestaan in de lijst wanneer u een toepassings gateway maakt met behulp van een interne Load Balancer (ILB) in combi natie met een App Service Environment (ASE) bij de back-end wanneer u end-to-end TLS gebruikt in Azure.

## <a name="symptoms"></a>Symptomen

Wanneer u een toepassings gateway maakt met behulp van een ILB met een ASE aan de back-end, wordt de back-endserver mogelijk beschadigd. Dit probleem treedt op als het verificatie certificaat van de toepassings gateway niet overeenkomt met het geconfigureerde certificaat op de back-endserver. Zie het volgende scenario als voor beeld:

**Application Gateway configuratie:**

- **Listener:** Meerdere locaties
- **Poort:** 443
- **Hostnaam:** test.appgwtestase.com
- **SSL-certificaat:** CN = test. appgwtestase. com
- **Back-end-groep:** IP-adres of FQDN
- **IP-adres:**: 10.1.5.11
- **Http-instellingen:** HTTPS
- **Poort:**: 443
- **Aangepaste test:** Hostnaam – test.appgwtestase.com
- **Verificatie certificaat:** . cer van test.appgwtestase.com
- **Back-end status:** Onjuist: het back-endserver certificaat is niet toegestaan met Application Gateway.

**ASE-configuratie:**

- **ILB IP:** 10.1.5.11
- **Domein naam:** appgwtestase.com
- **App service:** test.appgwtestase.com
- **SSL-binding:** SNI SSL – CN = test. appgwtestase. com

Wanneer u de toepassings gateway opent, wordt het volgende fout bericht weer gegeven omdat de back-endserver niet in orde is:

**502 – webserver heeft een ongeldig antwoord ontvangen terwijl deze fungeert als gateway of proxy server.**

## <a name="solution"></a>Oplossing

Wanneer u geen hostnaam gebruikt voor toegang tot een HTTPS-website, retourneert de back-endserver het geconfigureerde certificaat op de standaard website, in het geval SNI is uitgeschakeld. Voor een ILB-ASE is het standaard certificaat afkomstig van het ILB-certificaat. Als er geen certificaten zijn geconfigureerd voor de ILB, is het certificaat afkomstig uit het ASE-app-certificaat.

Wanneer u een Fully Qualified Domain Name (FQDN) gebruikt om toegang te krijgen tot de ILB, retourneert de back-endserver het juiste certificaat dat is geüpload in de HTTP-instellingen. Als dat niet het geval is, moet u rekening houden met de volgende opties:

- Gebruik FQDN in de back-end-pool van de toepassings gateway om te verwijzen naar het IP-adres van de ILB. Deze optie werkt alleen als u een privé-DNS-zone of een aangepaste DNS-configuratie hebt geconfigureerd. Anders moet u een A-record maken voor een open bare DNS-server.

- Gebruik het geüploade certificaat op de ILB of het standaard certificaat (ILB-certificaat) in de HTTP-instellingen. De Application Gateway haalt het certificaat op wanneer het het IP-adres van de ILB voor de test opent.

- Gebruik een certificaat met Joker tekens op de ILB en de back-endserver, zodat voor alle websites het certificaat gebruikelijk is. Deze oplossing is echter alleen mogelijk in het geval van subdomeinen en niet als voor elk van de websites verschillende hostnamen zijn vereist.

- Schakel de optie **voor app service gebruiken** voor de toepassings gateway uit als u het IP-adres van de ILB gebruikt.

Als u de overhead wilt beperken, kunt u het ILB-certificaat uploaden in de HTTP-instellingen om het probe-pad te laten werken. (Deze stap is alleen bedoeld voor vermelding toestaan. Het wordt niet gebruikt voor TLS-communicatie.) U kunt het ILB-certificaat ophalen door de ILB te openen met het IP-adres van uw browser op HTTPS en vervolgens het TLS/SSL-certificaat te exporteren in een met base64 64 gecodeerde CER-indeling en het certificaat te uploaden naar de respectievelijke HTTP-instellingen.

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
