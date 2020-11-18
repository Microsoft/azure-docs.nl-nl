---
title: 'Azure VPN Gateway: pakket opname configureren'
description: Meer informatie over de functionaliteit voor het vastleggen van pakketten die u kunt gebruiken op VPN-gateways om de oorzaak van een probleem te beperken.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: c6aa2b63344ef629d55f5a4efb66e4ee0e55975e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94661032"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>Pakket opname voor VPN-gateways configureren

Problemen met de connectiviteit en prestaties zijn vaak complex. Het kan aanzienlijk tijd en moeite duren om de oorzaak van het probleem te beperken. Pakket opname kan u helpen het bereik van een probleem te beperken tot bepaalde delen van het netwerk. Hiermee kunt u bepalen of het probleem zich aan de kant van het netwerk, de Azure-zijde van het netwerk of ergens anders bevindt. Nadat u het probleem hebt verkort, is het efficiënter om fouten op te sporen en de herstel actie te ondernemen.

Er zijn een aantal algemeen beschik bare hulpprogram ma's voor pakket opname. Het verkrijgen van relevante pakket opnames met deze hulpprogram ma's kan lastig zijn, met name bij scenario's met een groot aantal verkeer. De filter functies die worden geboden door Azure VPN Gateway pakket vastleggen, zijn een belang rijke onderscheid. U kunt VPN Gateway pakket opname samen met algemeen beschik bare hulpprogram ma's voor pakket opname gebruiken.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Filter mogelijkheden voor het vastleggen van pakket VPN Gateway

U kunt VPN Gateway pakket opname uitvoeren op de gateway of op een specifieke verbinding, afhankelijk van uw behoeften. U kunt ook pakket opname op meerdere tunnels tegelijk uitvoeren. U kunt in één richting of bidirectionele verkeer, IKE-en ESP-verkeer, en binnenste pakketten samen met filteren op een VPN-gateway vastleggen.

Het is handig om een vijf-tuple filter (bron subnet, doel-subnet, bron poort, doel poort, Protocol) en TCP-vlaggen (SYN, ACK, FIN, URG, PSH, RST) te gebruiken wanneer u problemen isoleert met veel volume verkeer.

De volgende voor beelden van JSON en een JSON-schema geven uitleg over elke eigenschap. Hier volgen enkele beperkingen die u moet onthouden wanneer u pakket opnames uitvoert:
- In het schema dat hier wordt weer gegeven, is het filter een matrix, maar op dit moment kan slechts één filter tegelijkertijd worden gebruikt.
- Het is niet mogelijk om meerdere pakket opnames voor de gateway tegelijk uit te voeren.
- Het is niet mogelijk om meerdere pakket opnames op één verbinding tegelijk uit te voeren. U kunt meerdere pakket opnames op verschillende verbindingen tegelijk uitvoeren.
- U kunt Maxi maal vijf pakket opnames per gateway tegelijk uitvoeren. Deze pakket opnames kunnen bestaan uit een combi natie van pakket opnames voor de hele gateway en pakket opnames per verbinding.

### <a name="example-json"></a>Voor beeld JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON-schema
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="set-up-packet-capture-by-using-powershell"></a>Pakket opname instellen met behulp van Power shell

In de volgende voor beelden ziet u Power shell-opdrachten die pakket opnames starten en stoppen. Zie [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)voor meer informatie over de parameter opties.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Pakket opname starten voor een VPN-gateway

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

U kunt de optionele para meter gebruiken `-FilterData` om een filter toe te passen.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Pakket opname stoppen voor een VPN-gateway

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Pakket opname starten voor een VPN-gateway verbinding

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

U kunt de optionele para meter gebruiken `-FilterData` om een filter toe te passen.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Pakket opname stoppen op een VPN-gateway verbinding

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Belangrijkste overwegingen

- Het uitvoeren van pakket opname kan invloed hebben op de prestaties. Vergeet niet om de pakket opname te stoppen wanneer u deze niet nodig hebt.
- De voorgestelde minimale pakket opname duur is 600 seconden. Vanwege synchronisatie problemen tussen meerdere onderdelen op het pad, bieden kortere pakket opnames mogelijk geen volledige gegevens.
- Gegevens bestanden voor pakket opname worden gegenereerd in de PCAP-indeling. Gebruik wireshark of andere algemeen beschik bare toepassingen om PCAP-bestanden te openen.
- Pakket opnames worden niet ondersteund op op beleid gebaseerde gateways.
- Als de `SASurl` para meter niet juist is geconfigureerd, kan de tracering mislukken met opslag fouten. `SASurl`Zie [Stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture)voor voor beelden van het correct genereren van een para meter.



## <a name="next-steps"></a>Volgende stappen

Zie [Wat is VPN gateway?](vpn-gateway-about-vpngateways.md)voor meer informatie over VPN gateway.