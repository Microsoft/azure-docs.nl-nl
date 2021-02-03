---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491005"
---
#### <a name="requirements-for-key-vault-firewall"></a>Vereisten voor Key Vault firewall

Als [Key Vault firewall](../articles/key-vault/general/network-security.md) is ingeschakeld in uw sleutel kluis, zijn de volgende aanvullende vereisten van belang:

* U moet de door het **systeem toegewezen** beheerde identiteit van het API Management exemplaar gebruiken voor toegang tot de sleutel kluis.
* Schakel in Key Vault firewall de optie **vertrouwde micro soft-services mogen deze firewall overs Laan** in.

#### <a name="virtual-network-requirements"></a>Vereisten voor het virtuele netwerk

Als het API Management-exemplaar in een virtueel netwerk is geïmplementeerd, moet u ook de volgende netwerk instellingen configureren:

* Schakel een [service-eind punt](../articles/key-vault/general/overview-vnet-service-endpoints.md) in om te Azure Key Vault op het API Management subnet.
* Configureer een regel voor een netwerk beveiligings groep (NSG) om uitgaand verkeer naar de AzureKeyVault-en AzureActiveDirectory- [service Tags](../articles/virtual-network/service-tags-overview.md)toe te staan. 

Zie netwerk configuratie details in [verbinding maken met een virtueel netwerk](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)voor meer informatie.