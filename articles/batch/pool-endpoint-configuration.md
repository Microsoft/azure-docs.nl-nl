---
title: Knoop punten in Azure Batch groep configureren
description: Toegang tot SSH-of RDP-poorten op reken knooppunten in een Azure Batch pool configureren of uitschakelen.
ms.topic: how-to
ms.date: 02/13/2018
ms.openlocfilehash: 4e7df7da539be75ef1befdff4b4e1fe5244c1702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109303"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Externe toegang tot reken knooppunten in een Azure Batch groep configureren of uitschakelen

Met batch kan een [knooppunt gebruiker](/rest/api/batchservice/computenode/adduser) met netwerk verbinding standaard verbinding maken met een reken knooppunt in een batch-pool. Een gebruiker kan bijvoorbeeld verbinding maken met Extern bureaublad (RDP) op poort 3389 met een reken knooppunt in een Windows-groep. Op dezelfde manier kan een gebruiker standaard verbinding maken via SSH (Secure Shell) op poort 22 tot een reken knooppunt in een Linux-groep. 

In uw omgeving moet u deze standaard instellingen voor externe toegang mogelijk beperken of uitschakelen. U kunt deze instellingen wijzigen met behulp van de batch-Api's om de eigenschap [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) in te stellen. 

## <a name="about-the-pool-endpoint-configuration"></a>Over de eindpunt configuratie van de pool
De eindpunt configuratie bestaat uit een of meer [Network Address Translation (NAT) groepen](/rest/api/batchservice/pool/add#inboundnatpool) van frontend-poorten. (Verwar een NAT-groep niet met de batch-pool van reken knooppunten.) U stelt elke NAT-pool zo in dat de standaard verbindings instellingen voor de reken knooppunten van de pool worden overschreven. 

Elke configuratie van een NAT-groep bevat een of meer [regels voor netwerk beveiligings groepen (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Met elke NSG regel kan bepaald netwerk verkeer naar het eind punt worden toegestaan of geweigerd. U kunt ervoor kiezen om al het verkeer toe te staan of te weigeren, verkeer dat wordt geïdentificeerd [door een servicetag](../virtual-network/network-security-groups-overview.md#service-tags) (zoals ' Internet ') of verkeer van specifieke IP-adressen of subnetten.

### <a name="considerations"></a>Overwegingen
* De configuratie van de pool-eind punt maakt deel uit van de [netwerk configuratie](/rest/api/batchservice/pool/add#networkconfiguration)van de pool. De netwerk configuratie kan eventueel instellingen bevatten voor het toevoegen van de groep aan een [virtueel Azure-netwerk](batch-virtual-network.md). Als u de pool in een virtueel netwerk hebt ingesteld, kunt u NSG-regels maken die gebruikmaken van adres instellingen in het virtuele netwerk.
* U kunt meerdere NSG-regels configureren wanneer u een NAT-groep configureert. De regels worden in volg orde van prioriteit gecontroleerd. Zodra een regel van toepassing is, worden geen andere regels meer getest.


## <a name="example-deny-all-rdp-traffic"></a>Voor beeld: alle RDP-verkeer weigeren

Het volgende C#-code fragment laat zien hoe u het RDP-eind punt configureert op reken knooppunten in een Windows-groep om al het netwerk verkeer te weigeren. Het eind punt gebruikt een front-end-groep poorten in het bereik *60000-60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Voor beeld: alle SSH-verkeer van Internet weigeren

Het volgende python-code fragment laat zien hoe u het SSH-eind punt configureert op reken knooppunten in een Linux-groep om al het Internet verkeer te weigeren. Het eind punt gebruikt een front-end-groep poorten in het bereik *4000-4100*. 

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                    source_address_prefix='Internet'
                )
            ]
        )
        ]
    )
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Voor beeld: RDP-verkeer van een specifiek IP-adres toestaan

In het volgende C#-code fragment ziet u hoe u het RDP-eind punt op reken knooppunten in een Windows-groep configureert om alleen RDP-toegang vanaf het IP-adres *198.51.100.7* toe te staan. De tweede NSG-regel weigert verkeer dat niet overeenkomt met het IP-adres.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Voor beeld: SSH-verkeer van een specifiek subnet toestaan

Het volgende python-code fragment laat zien hoe u het SSH-eind punt op reken knooppunten in een Linux-groep configureert om alleen toegang toe te staan vanaf het subnet *192.168.1.0/24*. De tweede NSG-regel weigert verkeer dat niet overeenkomt met het subnet.

```python
pool.network_configuration = batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                    priority=170,
                    access='allow',
                    source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                    priority=175,
                    access='deny',
                    source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Zie [netwerk verkeer filteren met netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie over NSG-regels in Azure.