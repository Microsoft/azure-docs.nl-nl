---
title: Problemen met uitgaande SMTP-verbindingen in azure oplossen | Microsoft Docs
description: Meer informatie over de aanbevolen methode voor het verzenden van e-mail en het oplossen van problemen met uitgaande SMTP-connectiviteit in Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 063ebc40fd845fe6300b008e7ca048357a2fce49
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95806670"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Problemen met uitgaande SMTP-connectiviteit in azure oplossen

Vanaf 15 november 2017 worden uitgaande e-mail berichten die rechtstreeks naar externe domeinen (zoals outlook.com en gmail.com) worden verzonden vanaf een virtuele machine (VM), alleen beschikbaar gesteld voor bepaalde abonnements typen in Microsoft Azure. Uitgaande SMTP-verbindingen die gebruikmaken van TCP-poort 25, zijn geblokkeerd. (Poort 25 wordt voornamelijk gebruikt voor niet-geverifieerde e-mail bezorging.)

Deze wijziging in gedrag geldt alleen voor nieuwe abonnementen en nieuwe implementaties sinds 15 november 2017.

## <a name="recommended-method-of-sending-email"></a>Aanbevolen methode voor het verzenden van e-mail

We raden u aan geverifieerde SMTP-relay-services te gebruiken (die meestal verbinding maken via TCP-poort 587 of 443, maar andere poorten ondersteunen) om e-mail te verzenden van Azure-Vm's of van Azure-app Services. Deze services worden gebruikt om de IP-of domein reputatie bij te houden om de kans te verkleinen dat e-mail providers van derden het bericht kunnen afwijzen. Dergelijke SMTP-relay-services bevatten maar niet beperkt tot [SendGrid](https://sendgrid.com/partners/azure/). Het is ook mogelijk dat u een beveiligde SMTP-relay-service hebt die on-premises wordt uitgevoerd en die u kunt gebruiken.

Het gebruik van deze services voor het leveren van e-mail is niet beperkt in azure, ongeacht het type abonnement.

## <a name="enterprise-agreement"></a>Enterprise Agreement

Voor Enterprise Agreement Azure-gebruikers is er geen wijziging in de technische mogelijkheid om e-mail te verzenden zonder gebruik te maken van een geverifieerde relay. Zowel nieuwe als bestaande Enterprise Agreement gebruikers kunnen uitgaande e-mail van Azure-Vm's rechtstreeks naar externe e-mail providers verzenden zonder enige beperkingen van het Azure-platform. Hoewel het niet gegarandeerd is dat e-mail providers inkomende e-mail van een bepaalde gebruiker accepteren, worden de bezorgings pogingen niet geblokkeerd door het Azure-platform voor Vm's binnen Enterprise Agreement abonnementen. U moet rechtstreeks met e-mail providers werken om berichten over het afleveren of oplossen van problemen met specifieke providers te verhelpen.

## <a name="pay-as-you-go"></a>Betalen per gebruik

Als u vóór 15 november 2017 bent aangemeld voor het betalen per gebruik-abonnement, is er geen wijziging in de technische mogelijkheid om uitgaande e-mail bezorging uit te proberen. U kunt uitgaande e-mail bezorging van Azure-Vm's binnen deze abonnementen rechtstreeks naar externe e-mail providers zonder enige beperkingen van het Azure-platform. Het is niet gegarandeerd dat e-mail providers inkomende e-mail van een wille keurige gebruiker accepteren en dat gebruikers rechtstreeks met e-mail providers aan de slag kunnen gaan met het oplossen van problemen met de aflevering van berichten of ongewenste e-mail met betrekking tot specifieke providers.

Voor betalen naar gebruik-abonnementen die zijn gemaakt na 15 november 2017, zijn er technische beperkingen die de e-mail blok keren die rechtstreeks vanuit de virtuele machines in deze abonnementen wordt verzonden. Als u de mogelijkheid wilt bieden om e-mail van Azure-Vm's rechtstreeks naar externe e-mail providers te verzenden (niet via een geverifieerde SMTP-relay), kunt u een aanvraag indienen om de beperking te verwijderen in het gedeelte **connectiviteit** van de Blade **problemen vaststellen en oplossen** voor een Azure Virtual Network-resource in de Azure Portal. Als u dit hebt gekwalificeerd, wordt uw abonnement ingeschakeld of ontvangt u instructies voor de volgende stappen.

Nadat een abonnement voor betalen per gebruik is uitgesloten en de Vm's zijn gestopt en gestart in de Azure Portal, worden alle Vm's in dat abonnement doorgestuurd. De uitzonde ring geldt alleen voor het aangevraagde abonnement en alleen voor VM-verkeer dat rechtstreeks naar het Internet wordt gerouteerd.

> [!NOTE]
> Micro soft behoudt zich het recht voor deze uitzonde ring in te trekken als wordt vastgesteld dat er een schending van de service voorwaarden is opgetreden.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, onderwijs, Azure for students, Visual Studio en gratis proef versie

Als u een MSDN-, Azure Pass-, Azure in Open-, onderwijs-, Azure student, gratis proef versie of een Visual Studio-abonnement hebt gemaakt na 15 november 2017, hebt u technische beperkingen die een e-mail blok keren dat vanaf Vm's in deze abonnementen rechtstreeks naar e-mail providers wordt verzonden. De beperkingen worden uitgevoerd om misbruik te voor komen. Er worden geen aanvragen voor het verwijderen van deze beperking verleend.

Als u deze typen abonnementen gebruikt, raden we u aan om SMTP-relay-services te gebruiken, zoals eerder in dit artikel is beschreven of het type abonnement wijzigen.

## <a name="cloud-service-provider-csp"></a>Cloud serviceprovider (CSP)

Als u Azure-resources gebruikt via CSP, kunt u een aanvraag indienen om de beperking te verwijderen in het gedeelte ' connectiviteit ' van de Blade ' Diagnostic and solve ' voor een Virtual Network-resource in azure Portal. Als u dit hebt gekwalificeerd, wordt uw abonnement ingeschakeld of ontvangt u instructies voor de volgende stappen.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark plus of Azure Sponsorship

Voor Microsoft Partner Network (MPN), BizSpark plus-of Azure Sponsorship-abonnementen die zijn gemaakt na 15 november 2017, worden er technische beperkingen gesteld voor het blok keren van e-mail die rechtstreeks vanuit de virtuele machines in deze abonnementen wordt verzonden. Als u de mogelijkheid wilt om e-mail van Azure-vm's rechtstreeks naar externe e-mail providers te verzenden (niet via een geverifieerde SMTP-relay), kunt u een aanvraag indienen door een ondersteunings aanvraag te openen met behulp van het volgende probleem type: **technische**  >  **Virtual Network**  >  **connectiviteit**  >  **kan geen e-mail verzenden (SMTP/poort 25)**. Zorg ervoor dat u gegevens toevoegt over waarom uw implementatie e-mail rechtstreeks naar e-mail providers moet verzenden in plaats van een geverifieerde relay te gebruiken. Aanvragen worden beoordeeld en goedgekeurd door micro soft. Aanvragen kunnen pas worden verleend nadat aanvullende fraude controles zijn voltooid. 

Nadat een abonnement voor betalen per gebruik is uitgesloten en de Vm's zijn gestopt en gestart in de Azure Portal, worden alle Vm's in dat abonnement doorgestuurd. De uitzonde ring geldt alleen voor het aangevraagde abonnement en alleen voor VM-verkeer dat rechtstreeks naar het Internet wordt gerouteerd.

## <a name="restrictions-and-limitations"></a>Beperkingen en beperkingen

- Routerings poort 25 verkeer via Azure PaaS services zoals [Azure firewall](https://azure.microsoft.com/services/azure-firewall/) wordt niet ondersteund.

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met de ondersteuning om uw probleem snel op te lossen met behulp van het volgende probleem type: aanvraag type voor het beheer van het **abonnement** : **aanvragen om een e-mail stroom voor poort 25**
