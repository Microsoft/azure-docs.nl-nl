---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ba0e7173c41e26a698596fa18bf1fc1453f3fb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99628905"
---
### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Heb ik een openbare IP nodig in mijn virtuele machine om verbinding te maken met Azure Bastion?

Nee. Wanneer u verbinding maakt met een VM met behulp van Azure Bastion, hebt u geen openbaar IP-adres nodig op de virtuele Azure-machine waarmee u verbinding maakt. De Bastion-service opent de sessie/verbinding via RDP/SSH met uw virtuele machine via het privé-IP-adres van uw virtuele machine, binnen uw virtuele netwerk.

### <a name="is-ipv6-supported"></a>Wordt IPv6 ondersteund?

Op dit moment wordt IPv6 niet ondersteund. Azure Bastion ondersteunt alleen IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Heb ik een RDP- of SSH-client nodig?

Nee. U hebt geen RDP- of SSH-client nodig om toegang te krijgen tot uw virtuele Azure-machine in de Azure-portal. Gebruik de [Azure-portal](https://portal.azure.com) om via RDP/SSH rechtstreeks toegang te krijgen tot uw virtuele machine.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Heb ik een agent nodig die wordt uitgevoerd op de virtuele Azure-machine?

Nee. U hoeft geen agent of software te installeren in uw browser of op uw virtual Azure-machine. De Bastion-service werkt zonder agenten en er is geen aanvullende software vereist voor RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Hoeveel gelijktijdige RDP- en SSH-sessies worden ondersteund door Azure Bastion?

Zowel RDP als SSH is een op gebruik gebaseerde protocol. Intensief gebruik van sessies heeft tot gevolg dat de Bastion-host een lager totaal aantal sessies ondersteunt. De onderstaande cijfers gaan uit van normale dagelijkse workflows.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Welke functies worden ondersteund in een RDP-sessie?

Momenteel wordt alleen het kopiëren en plakken van tekst ondersteund. Functies, zoals het kopiëren van bestanden, worden niet ondersteund. U kunt uw feedback over nieuwe functies delen op de [feedbackpagina van Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Werkt Bastion-versterking samen met AADJ-extensies die aan de virtuele machine zijn gekoppeld?

Deze functie werkt niet met AADJ machines die aan de virtuele machine zijn gekoppeld met behulp van Azure AD-gebruikers. Zie [Virtuele Windows Azure-machines en Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements) voor meer informatie.

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Welke browsers worden ondersteund?

Gebruik Microsoft Edge of Google Chrome in Windows. Voor Apple Mac gebruikt u Google Chrome. Microsoft Edge Chromium wordt ook ondersteund voor zowel Windows als Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Waar worden klantgegevens opgeslagen door Azure Bastion?

Er worden door Azure Bastion geen klantgegevens verplaatst of opgeslagen buiten de regio waarin de service is geïmplementeerd.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Zijn er rollen vereist voor toegang tot een virtuele machine?

De volgende rollen zijn vereist om verbinding te maken:

* De rol van Lezer op de virtuele machine
* De rol van Lezer op de NIC met het privé-IP-adres van de virtuele machine
* De rol van Lezer in de Azure Bastion-resource

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Wat zijn de prijzen?

Zie de pagina [prijzen](https://aka.ms/BastionHostPricing) voor meer informatie.

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Is voor Azure Bastion een RDS CAL vereist voor administratieve doeleinden op VM's die door Azure worden gehost?

Nee, voor toegang tot VM's met Windows Server door Azure Bastion is geen [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) vereist wanneer dit uitsluitend voor administratieve doeleinden is.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Welke toetsenbordindelingen worden ondersteund tijdens de externe sessie met Bastion?

Azure Bastion ondersteunt momenteel de toetsenbordindeling en-us-qwerty op de VM.  Er wordt gewerkt aan ondersteuning voor andere landinstellingen voor toetsenbordindelingen.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Wordt door de gebruiker gedefinieerde routering (UDR) ondersteund in een subnet van Azure Bastion?

Nee. UDR wordt niet ondersteund in een subnet van Azure Bastion.

Voor scenario's met zowel Azure Bastion als Azure Firewall/Network Virtual Appliance (NVA) in hetzelfde virtuele netwerk, hoeft u verkeer van een Azure Bastion-subnet niet om te leiden naar Azure Firewall omdat de communicatie tussen Azure Bastion en uw VM's privé is. Zie voor meer informatie het artikel over [toegang tot VM's achter Azure Firewall met Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Waarom krijg ik een foutbericht dat mijn sessie is verlopen voordat de Bastion-sessie is gestart?

Een sessie mag alleen worden gestart vanuit de Azure-portal. Meld u aan bij de Azure-portal en start de sessie opnieuw. Als u rechtstreeks vanuit een andere sessie of ander tabblad in de browser naar de URL gaat, is deze fout te verwachten. De fout helpt ervoor te zorgen dat uw sessie veiliger is en dat de sessie alleen via de Azure-portal kan worden geopend.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr">Wat doe ik met implementatiefouten?</a>

Bekijk de foutberichten goed en [dien een ondersteuningsaanvraag in de Azure-portal in](../articles/azure-portal/supportability/how-to-create-azure-support-request.md), als dat nog nodig is. Implementatiefouten kunnen het gevolg zijn van [limieten voor Azure-abonnementen, quota en beperkingen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). Klanten kunnen met name te maken krijgen met de limiet voor het aantal openbare IP-adressen dat is toegestaan per abonnement. Hierdoor kan de implementatie van Azure Bastion mislukken.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Hoe neem ik Azure Bastion op in mijn plan voor herstel na noodgevallen?

Azure Bastion wordt geïmplementeerd binnen VNets of gepeerde VNets en wordt gekoppeld aan een Azure-regio. U bent er verantwoordelijk voor om Azure Bastion te implementeren in een VNet voor herstel na noodgevallen. In het geval van een Azure-regiofout, voert u een failoverbewerking uit voor uw VM's naar de regio voor herstel na noodgevallen. Vervolgens gebruikt u de Azure Bastion-host die is geïmplementeerd in de regio voor herstel na noodgevallen om verbinding te maken met de VM's die daar nu zijn geïmplementeerd.
