---
title: Automatisch schalen van Azure gebruiken met metrische gegevens van de gast in een sjabloon voor Linux-schaal sets
description: Meer informatie over het automatisch schalen met behulp van metrische gegevens van een virtuele machine in een Linux-Schaalset
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584997"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Automatisch schalen met metrische gegevens van de gast in een sjabloon voor een Linux-schaalset

Er zijn twee soorten metrische gegevens in azure die worden verzameld uit Vm's en schaal sets: metrische gegevens voor de host en metrische gegevens van de gast. Op hoog niveau, als u standaard CPU-, schijf-en netwerk gegevens wilt gebruiken, zijn de metrische gegevens van de host goed. Als u echter een grotere selectie metrische gegevens nodig hebt, moeten de metrische gegevens van de gast worden weer gegeven.

Voor metrische gegevens van de host is geen aanvullende installatie vereist omdat deze worden verzameld door de host-VM, terwijl voor metrische gegevens van de gast vereist is dat u de [Windows Azure Diagnostics-extensie](../virtual-machines/extensions/diagnostics-template.md) of de [extensie Linux Azure Diagnostics](../virtual-machines/extensions/diagnostics-linux.md) installeert in de gast-VM. Een veelvoorkomende reden voor het gebruik van metrische gegevens voor gasten in plaats van metrische gegevens over de host is dat de metrische gegevens van de gast een grotere selectie van metrische gegevens bieden dan metrische gegevens over de host. Een voor beeld hiervan zijn metrische gegevens over het geheugen verbruik, die alleen beschikbaar zijn via de metrische gegevens van de gast. De ondersteunde metrische gegevens van de host worden [hier](../azure-monitor/essentials/metrics-supported.md)vermeld, en veelgebruikte metrische gegevens voor de gast worden [hier](../azure-monitor/autoscale/autoscale-common-metrics.md)weer gegeven. In dit artikel wordt beschreven hoe u de [sjabloon basis levensvat bare schaal](virtual-machine-scale-sets-mvss-start.md) kunt aanpassen voor het gebruik van regels voor automatisch schalen op basis van metrische gegevens van de gast voor Linux-schaal sets.

## <a name="change-the-template-definition"></a>De sjabloon definitie wijzigen

In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) moesten we een basisschaalset-sjabloon maken. We gaan nu die eerdere sjabloon gebruiken en deze wijzigen om een sjabloon te maken waarmee een Linux-schaalset wordt geïmplementeerd met automatisch schalen op basis van de gast metriek.

Voeg eerst para meters voor `storageAccountName` en toe `storageAccountSasToken` . In de diagnostische agent worden metrische gegevens opgeslagen in een [tabel](../cosmos-db/tutorial-develop-table-dotnet.md) in dit opslag account. Vanaf de Linux Diagnostics agent versie 3,0, die een toegangs sleutel voor opslag gebruikt, wordt niet meer ondersteund. Gebruik in plaats daarvan een [SAS-token](../storage/common/storage-sas-overview.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Wijzig vervolgens de schaalset `extensionProfile` zodat deze de diagnostische extensie bevat. Geef in deze configuratie de resource-ID van de schaalset op waaruit u metrische gegevens wilt verzamelen, evenals het opslag account en het SAS-token dat moet worden gebruikt om de metrische gegevens op te slaan. Geef op hoe vaak de metrische gegevens worden geaggregeerd (in dit geval elke minuut) en welke metrische gegevens moeten worden gevolgd (in dit geval het percentage gebruikt geheugen). Raadpleeg [deze documentatie](../virtual-machines/extensions/diagnostics-linux.md)voor meer informatie over deze configuratie en andere metrische gegevens dan het percentage gebruikt geheugen.

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Voeg ten slotte een `autoscaleSettings` resource toe om automatisch schalen te configureren op basis van deze metrische gegevens. Deze resource bevat een- `dependsOn` component die verwijst naar de schaalset om ervoor te zorgen dat de schaalset bestaat voordat u deze automatisch kunt schalen. Als u een andere metriek kiest voor automatisch schalen, gebruikt u de `counterSpecifier` configuratie van de diagnostische gegevens extensie als de `metricName` in de configuratie voor automatisch schalen. Zie voor meer informatie over de configuratie van automatisch schalen de [Aanbevolen procedures voor automatisch schalen](../azure-monitor/autoscale/autoscale-best-practices.md) en de [referentie documentatie voor de Azure monitor rest API](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
