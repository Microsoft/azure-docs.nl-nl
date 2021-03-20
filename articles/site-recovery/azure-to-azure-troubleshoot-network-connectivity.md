---
title: Problemen met de verbinding met Azure voor herstel na nood gevallen met Azure Site Recovery oplossen
description: Verbindings problemen met nood herstel voor Azure VM oplossen
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: 24ffce1528aa5c82fec9666fa0cb7b8717107f54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652259"
---
# <a name="troubleshoot-azure-to-azure-vm-network-connectivity-issues"></a>Problemen met Azure-naar-Azure-VM-netwerk connectiviteit oplossen

In dit artikel worden veelvoorkomende problemen met de netwerk verbinding beschreven wanneer u Azure virtual machines (VM) van de ene regio naar een andere regio repliceert en herstelt. Zie de [connectiviteits vereisten voor het repliceren van virtuele Azure-machines](azure-to-azure-about-networking.md)voor meer informatie over netwerk vereisten.

Voor een goede werking van Site Recovery replicatie is uitgaande verbinding met specifieke Url's of IP-adresbereiken vereist van de virtuele machine. Als uw virtuele machine zich achter een firewall bevindt of gebruikmaakt van regels voor netwerk beveiligings groepen (NSG) om de uitgaande connectiviteit te beheren, kunt u een van deze problemen tegen komen.

| **Naam**                  | **Commercieel**                               | **Overheid**                                 | **Beschrijving** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Vereist zodat gegevens kunnen worden geschreven naar het cache-opslag account in de bron regio van de virtuele machine. Als u alle cache opslag accounts voor uw virtuele machines weet, kunt u een lijst met toegestane Url's gebruiken voor de specifieke URL van het opslag account. Bijvoorbeeld, `cache1.blob.core.windows.net` `cache2.blob.core.windows.net` in plaats van `*.blob.core.windows.net` . |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Vereist voor autorisatie en verificatie voor de Url's van de Site Recovery-service. |
| Replicatie               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Vereist zodat de Site Recovery service communicatie kan worden uitgevoerd vanaf de virtuele machine. U kunt het bijbehorende _site Recovery IP-adres_ gebruiken als uw firewall proxy ip's ondersteunt. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Vereist zodat de Site Recovery bewakings-en diagnostische gegevens van de virtuele machine kunnen worden geschreven. U kunt de bijbehorende _site Recovery bewakings-IP_ gebruiken als uw firewall proxy ip's ondersteunt. |

## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>Uitgaande connectiviteit voor Site Recovery Url's of IP-adresbereiken (fout code 151037 of 151072)

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195"></a>Probleem 1: de virtuele machine van Azure kan niet worden geregistreerd met Site Recovery (151195)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Site Recovery-eind punten vanwege een probleem met het omzetten van een Domain Name System (DNS). Dit probleem komt vaker voor tijdens het opnieuw beveiligen van de virtuele machine, maar de DNS-server is niet bereikbaar vanuit de regio voor nood herstel (DR).

#### <a name="resolution"></a>Oplossing

Als u aangepaste DNS gebruikt, moet u ervoor zorgen dat de DNS-server toegankelijk is vanuit het gebied voor nood herstel.

Controleren of de virtuele machine gebruikmaakt van een aangepaste DNS-instelling:

1. Open **virtuele machines** en selecteer de VM.
1. Navigeer naar de **instellingen** van de virtuele machines en selecteer **netwerken**.
1. Selecteer in **virtueel netwerk/subnet** de koppeling om de resource pagina van het virtuele netwerk te openen.
1. Ga naar **instellingen** en selecteer **DNS-servers**.

Probeer toegang te krijgen tot de DNS-server vanaf de virtuele machine. Als de DNS-server niet toegankelijk is, maakt u deze toegankelijk door een failover uit te voeren voor de DNS-server of door de regel van de site te maken tussen DR-netwerk en DNS.

  :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-fout":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>Probleem 2: configuratie van Site Recovery is mislukt (151196)

> [!NOTE]
> Als de virtuele machines zich achter een **standaard** interne Load Balancer, heeft deze standaard geen toegang tot de Microsoft 365 IP-adressen zoals `login.microsoftonline.com` . Wijzig deze in een **Basic** interne Load Balancer type of maak uitgaande toegang zoals vermeld in het artikel [Configureer taak verdeling en uitgaande regels in Standard Load Balancer met behulp van Azure cli](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard#create-outbound-rule-configuration).

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Microsoft 365 authenticatie-en identiteits IP4-eind punten.

#### <a name="resolution"></a>Oplossing

- Azure Site Recovery moet toegang hebben tot de Microsoft 365 IP-adresbereiken voor authenticatie.
- Als u Azure Network Security Group (NSG) regels/firewall proxy gebruikt voor het beheren van uitgaande netwerk connectiviteit op de virtuele machine, moet u ervoor zorgen dat u communicatie met de Microsoft 365 IP-bereiken toestaat. Maak een NSG-regel op basis van een [Azure Active Directory-service (Azure AD)](../virtual-network/network-security-groups-overview.md#service-tags) die toegang biedt tot alle IP-adressen die overeenkomen met Azure AD.
- Als nieuwe adressen in de toekomst worden toegevoegd aan Azure AD, moet u nieuwe NSG-regels maken.

### <a name="example-nsg-configuration"></a>Voor beeld van NSG-configuratie

In dit voor beeld ziet u hoe u NSG-regels configureert voor replicatie van een virtuele machine.

- Als u NSG-regels gebruikt om de uitgaande connectiviteit te beheren, gebruikt u HTTPS-regels voor **uitgaande verbindingen toestaan** voor poort 443 voor alle vereiste IP-adresbereiken.
- In het voor beeld wordt ervan uitgegaan dat de bron locatie van de virtuele machine **VS-Oost** is en dat de doel locatie **VS centraal** is.

#### <a name="nsg-rules---east-us"></a>NSG-regels-VS-Oost

1. Maak een uitgaande HTTPS-beveiligings regel voor de NSG, zoals weer gegeven in de volgende scherm afbeelding. In dit voor beeld wordt de servicetag van de **doel service** gebruikt: _opslag. oostelijk_ en **doel poortbereiken**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/storage-tag.png" alt-text="Scherm afbeelding toont een deel venster uitgaande beveiligings regel toevoegen voor een beveiligings regel voor opslag dot Oost U S.":::

1. Maak een uitgaande HTTPS-beveiligings regel voor de NSG, zoals weer gegeven in de volgende scherm afbeelding. In dit voor beeld wordt de servicetag van de **doel service** gebruikt: _AzureActiveDirectory_ en **doel poortbereiken**: _443_.

     :::image type="content" source="./media/azure-to-azure-about-networking/aad-tag.png" alt-text="Scherm afbeelding toont een deel venster uitgaande beveiligings regel toevoegen voor een beveiligings regel voor Azure Active Directory.":::

1. Net als hierboven, maakt u een uitgaande HTTPS (443) beveiligings regel voor ' EventHub. Centralus ' op de NSG die overeenkomt met de doel locatie. Hiermee krijgt u toegang tot Site Recovery bewaking.
1. Maak een uitgaande HTTPS (443) beveiligings regel voor ' AzureSiteRecovery ' op de NSG. Hiermee krijgt u toegang tot Site Recovery-service in elke regio.

#### <a name="nsg-rules---central-us"></a>NSG-regels-VS-Centraal

Voor dit voor beeld zijn deze NSG-regels vereist zodat replicatie kan worden ingeschakeld vanuit de doel regio naar de bron regio na failover:

1. Een uitgaande HTTPS-beveiligings regel maken voor _opslag. centraalus_:

   - **Doel** servicetag: _opslag. centraalus_
   - **Poort bereik van doel**: _443_

1. Maak een HTTPS-regel voor uitgaande beveiliging voor _AzureActiveDirectory_.

   - **Doel** servicetag: _AzureActiveDirectory_
   - **Poort bereik van doel**: _443_

1. Net als hierboven, maakt u een uitgaande HTTPS (443) beveiligings regel voor ' EventHub. Eastus ' op de NSG die overeenkomt met de bron locatie. Hiermee krijgt u toegang tot Site Recovery bewaking.
1. Maak een uitgaande HTTPS (443) beveiligings regel voor ' AzureSiteRecovery ' op de NSG. Hiermee krijgt u toegang tot Site Recovery-service in elke regio.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>Probleem 3: configuratie van Site Recovery is mislukt (151197)

#### <a name="possible-cause"></a>Mogelijke oorzaak

Er kan geen verbinding tot stand worden gebracht met Azure Site Recovery service-eind punten.

#### <a name="resolution"></a>Oplossing

Als u een Azure Network Security Group (NSG) regel/firewall proxy gebruikt om uitgaande netwerk connectiviteit op de computer te beheren, zijn er verschillende service tags die moeten worden toegestaan. [Meer informatie](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

### <a name="issue-4-azure-to-azure-replication-failed-when-the-network-traffic-goes-through-on-premises-proxy-server-151072"></a>Probleem 4: Azure-naar-Azure-replicatie is mislukt wanneer het netwerk verkeer via on-premises proxy server loopt (151072)

#### <a name="possible-cause"></a>Mogelijke oorzaak

De aangepaste proxy instellingen zijn ongeldig en de Azure Site Recovery Mobility Service-agent heeft de proxy-instellingen niet automatisch gedetecteerd vanuit Internet Explorer (IE).

#### <a name="resolution"></a>Oplossing

1. De Mobility Service-agent detecteert de proxy-instellingen van Internet Explorer in Windows en `/etc/environment` op Linux.
1. Als u liever proxy alleen instelt voor Azure Site Recovery Mobility-service, kunt u de proxy gegevens opgeven in _ProxyInfo. conf_ op:

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. De _ProxyInfo. conf_ moet de proxy-instellingen in de volgende _ini_ -indeling hebben:

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Azure Site Recovery Mobility Service-agent ondersteunt alleen **niet-geverifieerde proxy's**.

### <a name="fix-the-problem"></a>Het probleem oplossen

Volg de stappen in het [document voor netwerk](./azure-to-azure-about-networking.md)ondersteuning om [de vereiste url's](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) of de [vereiste IP-bereiken](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)toe te staan.

## <a name="next-steps"></a>Volgende stappen

[Virtuele Azure-machines repliceren naar een andere Azure-regio](azure-to-azure-how-to-enable-replication.md)