---
title: Problemen met uitgaande SMTP-verbindingen in azure oplossen | Microsoft Docs
description: Meer informatie over de aanbevolen methode voor het verzenden van e-mail en het oplossen van problemen met uitgaande SMTP-verbindingen in Azure.
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
ms.openlocfilehash: 0a69df8a20c4e1502de151c38c60b54667c2d4dc
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814479"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Problemen met uitgaande SMTP-connectiviteit in azure oplossen

Vanaf 15 november 2017 worden uitgaande e-mail berichten die rechtstreeks naar externe domeinen (zoals outlook.com en gmail.com) worden verzonden vanaf een virtuele machine (VM) alleen beschikbaar gesteld voor bepaalde typen abonnementen in Azure. Uitgaande SMTP-verbindingen die gebruikmaken van TCP-poort 25, zijn geblokkeerd. (Poort 25 wordt hoofd zakelijk gebruikt voor niet-geverifieerde e-mail bezorging.)

Deze wijziging in gedrag geldt alleen voor abonnementen en implementaties die zijn gemaakt na 15 november 2017.

## <a name="recommended-method-of-sending-email"></a>Aanbevolen methode voor het verzenden van e-mail

U wordt aangeraden geverifieerde SMTP-relay-services te gebruiken voor het verzenden van e-mail van virtuele Azure-machines of van Azure App Service. (Deze relay-Services maken meestal verbinding via TCP-poort 587 of 443, maar ondersteunen andere poorten.) Deze services worden gebruikt om de IP-of domein reputatie bij te houden om de kans te verkleinen dat e-mail providers van derden berichten kunnen afwijzen. [SendGrid](https://sendgrid.com/partners/azure/) is een dergelijke SMTP-relay-service, maar er zijn er andere. Mogelijk hebt u ook een beveiligde SMTP-relay-service die on-premises wordt uitgevoerd en die u kunt gebruiken.

Het gebruik van deze services voor het leveren van e-mail is niet beperkt in azure, ongeacht het type abonnement.

## <a name="enterprise-agreement"></a>Enterprise Agreement

Voor Enterprise Agreement Azure-gebruikers is er geen wijziging in de technische mogelijkheid om e-mail te verzenden zonder gebruik te maken van een geverifieerde relay. Zowel nieuwe als bestaande Enterprise Agreement gebruikers kunnen uitgaande e-mail van Azure-Vm's rechtstreeks naar externe e-mail providers verzenden zonder enige beperkingen van het Azure-platform. Er is geen garantie dat e-mail providers inkomende e-mail ontvangen van een bepaalde gebruiker. Het Azure-platform blokkeert echter geen bezorgings pogingen voor Vm's binnen Enterprise Agreement-abonnementen. U moet rechtstreeks met e-mail providers werken om berichten over het afleveren of oplossen van problemen met specifieke providers te verhelpen.

## <a name="pay-as-you-go"></a>Betalen naar gebruik

Als u vóór 15 november 2017 bent aangemeld voor een abonnement met betalen per gebruik, is er geen wijziging in uw technische mogelijkheid om uitgaande e-mail bezorging uit te proberen. U kunt echter nog steeds uitgaande e-mail van Azure-Vm's binnen deze abonnementen rechtstreeks naar externe e-mail providers verzenden zonder enige beperkingen van het Azure-platform. Er is nog geen garantie dat e-mail providers inkomende e-mail van een bepaalde gebruiker accepteren. Gebruikers moeten rechtstreeks met e-mail providers werken om bericht verzendings-of SPAM filterings problemen met betrekking tot specifieke providers te verhelpen.

Voor betalen naar gebruik-abonnementen die zijn gemaakt na 15 november 2017, zijn er technische beperkingen die een e-mail blok keren dat rechtstreeks vanuit de virtuele machines in de abonnementen wordt verzonden. Als u rechtstreeks e-mail berichten van Azure-Vm's naar externe e-mail providers wilt verzenden (zonder een geverifieerde SMTP-relay) en u hebt een account in goede staat met een betaalrun, kunt u een aanvraag indienen om de beperking te verwijderen. U kunt dit doen in het gedeelte **connectiviteit** van de Blade **problemen vaststellen en oplossen** voor een Azure Virtual Network-resource in de Azure Portal. Als uw aanvraag wordt geaccepteerd, wordt uw abonnement ingeschakeld of ontvangt u instructies voor de volgende stappen. 

Wanneer een abonnement op basis van betalen per gebruik wordt uitgesloten en de Vm's worden gestopt en opnieuw gestart in de Azure Portal, worden alle Vm's in dat abonnement doorgestuurd. De uitzonde ring geldt alleen voor het aangevraagde abonnement en alleen voor VM-verkeer dat rechtstreeks naar het Internet wordt gerouteerd.

> [!NOTE]
> Micro soft behoudt zich het recht voor deze uitzonde ringen in te trekken als wordt vastgesteld dat er een schending van de service voorwaarden is opgetreden.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, onderwijs, Azure for students, Visual Studio en gratis proef versie

Als u na 15 november 2017 een van de volgende typen abonnementen hebt gemaakt, hebt u technische beperkingen waarbij e-mail die wordt verzonden van Vm's binnen het abonnement, rechtstreeks naar e-mail providers wordt geblokkeerd:
- MSDN
- Azure Pass
- Azure in Open
- Onderwijs
- Azure for Students
- Gratis proefversie
- Een Visual Studio-abonnement  

De beperkingen zijn van kracht om misbruik te voor komen. Aanvragen om deze beperkingen te verwijderen, worden niet verleend.

Als u deze typen abonnementen gebruikt, raden we u aan SMTP relay-services te gebruiken, zoals eerder in dit artikel is beschreven, of om het type abonnement te wijzigen.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider

Als u Azure-resources gebruikt via een provider van cloud oplossingen, kunt u een aanvraag indienen om de beperking te verwijderen in het gedeelte **connectiviteit** van het deel venster **diagnose en oplossen** van een virtuele netwerk bron in de Azure Portal. Als uw aanvraag wordt geaccepteerd, wordt uw abonnement ingeschakeld of ontvangt u instructies voor de volgende stappen.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark plus of Azure Sponsorship

Voor abonnementen van de volgende typen die zijn gemaakt na 15 november 2017, zijn er technische beperkingen die het blok keren van e-mail die rechtstreeks vanuit Vm's binnen de abonnementen wordt verzonden:

- Microsoft Partner Network (MPN)
- BizSpark plus
- Azure Sponsorship

Als u rechtstreeks e-mail berichten van Azure-vm's naar externe e-mail providers wilt verzenden (zonder een geverifieerde SMTP-relay), kunt u een aanvraag indienen door een ondersteunings aanvraag te openen met behulp van het volgende probleem type: **technische**  >  **Virtual Network**  >  **connectiviteit**  >  **kan geen e-mail verzenden (SMTP/poort 25)**. Zorg ervoor dat u gegevens toevoegt over waarom uw implementatie e-mail rechtstreeks naar e-mail providers moet verzenden in plaats van een geverifieerde relay te gebruiken. Aanvragen worden beoordeeld en goedgekeurd door micro soft. Aanvragen worden pas verleend nadat aanvullende AntiFraud-controles zijn voltooid. 

Nadat een abonnement is uitgesloten en de Vm's zijn gestopt en opnieuw zijn opgestart in de Azure Portal, worden alle Vm's in dat abonnement doorgestuurd. De uitzonde ring geldt alleen voor het aangevraagde abonnement en alleen voor VM-verkeer dat rechtstreeks naar het Internet wordt gerouteerd.

## <a name="restrictions-and-limitations"></a>Beperkingen en beperkingen

Routerings poort 25 verkeer via Azure PaaS services zoals [Azure firewall](https://azure.microsoft.com/services/azure-firewall/) wordt niet ondersteund.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen. Gebruik dit probleem type: met **technische**  >  **Virtual Network**  >  **connectiviteit**  >  **kan geen e-mail worden verzonden (SMTP/poort 25)**.
