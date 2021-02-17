---
title: DNS-instellingen van Azure Firewall beleid
description: U kunt Azure Firewall-beleid configureren met DNS-server-en DNS-proxy-instellingen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633645"
---
# <a name="azure-firewall-policy-dns-settings"></a>DNS-instellingen van Azure Firewall beleid

U kunt een aangepaste DNS-server configureren en DNS-proxy inschakelen voor Azure Firewall-beleid. U kunt deze instellingen configureren wanneer u de firewall of hoger op de pagina **DNS-instellingen** implementeert.

## <a name="dns-servers"></a>DNS-servers

Een DNS-server onderhoudt en verhelpt domein namen naar IP-adressen. Azure Firewall maakt standaard gebruik van Azure DNS voor naam omzetting. Met de **DNS-server** instelling kunt u uw eigen DNS-servers configureren voor Azure firewall naam omzetting. U kunt één of meerdere servers configureren.

### <a name="configure-custom-dns-servers"></a>Aangepaste DNS-servers configureren

1. Selecteer uw firewall-beleid.
2. Selecteer **DNS-instellingen** onder **instellingen**.
3. Onder **DNS-servers** kunt u bestaande DNS-servers die eerder zijn opgegeven in uw Virtual Network typen of toevoegen.
4. Selecteer **Opslaan**.
5. De firewall stuurt nu DNS-verkeer naar de opgegeven DNS-server (s) voor naam omzetting.

## <a name="dns-proxy"></a>DNS-proxy

U kunt Azure Firewall configureren om te fungeren als een DNS-proxy. Een DNS-proxy fungeert als een intermediair voor DNS-aanvragen van virtuele client machines naar een DNS-server. Als u een aangepaste DNS-server configureert, moet u DNS-proxy inschakelen om te voor komen dat DNS-omzetting niet overeenkomen en FQDN-filtering inschakelen in netwerk regels.

Als u DNS-proxy niet inschakelt, kunnen DNS-aanvragen van de client op een ander tijdstip naar een DNS-server worden getransporteerd of een andere reactie retour neren vergeleken met de firewall. De DNS-proxy plaatst Azure Firewall in het pad van de client aanvragen om inconsistentie te voor komen.

De DNS-proxy configuratie vereist drie stappen:

1. Schakel DNS-proxy in Azure Firewall DNS-instellingen in.
2. Configureer eventueel uw aangepaste DNS-server of gebruik de standaard waarde.
3. Ten slotte moet u het privé-IP-adres van de Azure Firewall configureren als een aangepast DNS-adres in de instellingen van de DNS-server van uw virtuele netwerk. Dit zorgt ervoor dat DNS-verkeer wordt omgeleid naar Azure Firewall.

### <a name="configure-dns-proxy"></a>DNS-proxy configureren

Als u een DNS-proxy wilt configureren, moet u de instelling van de DNS-servers voor het virtuele netwerk configureren voor gebruik van het privé IP-adres van de firewall Schakel vervolgens DNS-proxy in Azure Firewall **DNS-instellingen** voor beleid.

#### <a name="configure-virtual-network-dns-servers"></a>DNS-servers voor het virtuele netwerk configureren

1. Selecteer het virtuele netwerk waarin het DNS-verkeer wordt doorgestuurd via de Azure Firewall.
2. Selecteer onder **Instellingen** de optie **DNS-servers**.
3. Selecteer **aangepast** onder **DNS-servers**.
4. Voer het privé IP-adres van de firewall in.
5. Selecteer **Opslaan**.

#### <a name="enable-dns-proxy"></a>DNS-proxy inschakelen

1. Selecteer uw Azure Firewall-beleid.
2. Selecteer **DNS-instellingen** onder **instellingen**.
3. **DNS-proxy** is standaard uitgeschakeld. Wanneer deze functie is ingeschakeld, luistert de firewall op poort 53 en stuurt DNS-aanvragen door naar de geconfigureerde DNS-servers.
4. Controleer de configuratie van de **DNS-servers** om ervoor te zorgen dat de instellingen geschikt zijn voor uw omgeving.
5. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[FQDN-filtering in netwerk regels](fqdn-filtering-network-rules.md)