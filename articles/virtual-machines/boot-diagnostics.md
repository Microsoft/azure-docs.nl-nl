---
title: Diagnostische gegevens over Azure-opstarten
description: Overzicht van diagnostische gegevens over het opstarten van Azure en beheerde diagnostische gegevens over opstarten
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 030f19d61e19349de7e2d3416a3aee1385fd03c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607519"
---
# <a name="azure-boot-diagnostics"></a>Diagnostische gegevens over Azure-opstarten

Diagnostische gegevens over opstarten is een functie voor het opsporen van fouten in azure virtual machines (VM) die de diagnose van opstart fouten van VM'S mogelijk maakt. Met diagnostische gegevens over opstarten kan een gebruiker de status van de virtuele machine observeren terwijl deze opstart door seriële logboek gegevens en scherm afbeeldingen te verzamelen.

## <a name="boot-diagnostics-storage-account"></a>Opslag account voor diagnostische gegevens over opstarten
Bij het maken van een virtuele machine in Azure Portal is diagnostische gegevens over opstarten standaard ingeschakeld. De aanbevolen diagnostische gegevens over het opstarten zijn het gebruik van een beheerd opslag account, omdat het de prestaties van een Azure-VM aanzienlijk verduidelijkt. Dit komt doordat er een Azure Managed Storage-account wordt gebruikt, waarbij de tijd die nodig is om een nieuw gebruikers opslag account te maken voor het opslaan van de diagnostische gegevens over opstarten wordt verwijderd.

> [!IMPORTANT]
> De gegevens-blobs van de opstart diagnose (met inbegrip van Logboeken en moment opnamen) worden opgeslagen in een beheerd opslag account. Klanten worden alleen in rekening gebracht op gebruikte GiBs door de blobs, niet op de ingerichte grootte van de schijf. De momentopname meters worden gebruikt voor de facturering van het beheerde opslag account. Omdat de beheerde accounts worden gemaakt op standaard-LRS of standaard-ZRS, worden klanten op $0,05/GB per maand gefactureerd voor de grootte van de blobs van de diagnostische gegevens. Zie [prijzen van Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over deze prijzen. Klanten krijgen deze kosten te zien die zijn gekoppeld aan hun VM-resource-URI. 

Een alternatieve ervaring voor het opstarten van de diagnose is het gebruik van een door de gebruiker beheerd opslag account. Een gebruiker kan een nieuw opslag account maken of een bestaande gebruiken.
> [!NOTE]
> Voor door de gebruiker beheerde opslag accounts die zijn gekoppeld aan de diagnostische gegevens over opstarten, moeten het opslag account en de bijbehorende virtuele machines zich in hetzelfde abonnement bevinden. 



## <a name="boot-diagnostics-view"></a>Weer gave diagnostische gegevens over opstarten
De optie diagnostische gegevens over opstarten bevindt zich op de Blade van de virtuele machine onder het gedeelte *ondersteuning en probleem oplossing* in de Azure Portal. Als u Diagnostische gegevens over opstarten selecteert, wordt een scherm opname en seriële logboek informatie weer gegeven. Het seriële logboek bevat kernel-berichten en de scherm opname is een moment opname van de huidige status van uw virtuele machines. Op basis van als op de virtuele machine Windows of Linux wordt uitgevoerd, wordt bepaald hoe de verwachte scherm afbeelding eruitziet. Voor Windows krijgen gebruikers een bureaublad achtergrond en voor Linux te zien, zien gebruikers een aanmeldings prompt.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Scherm afbeelding van diagnostische gegevens over Linux-opstarten":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Scherm opname van Windows diagnostische gegevens over opstarten":::

## <a name="enable-managed-boot-diagnostics"></a>Diagnostische gegevens over beheerde opstarten inschakelen 
Diagnostische gegevens over beheerde opstarten kunnen worden ingeschakeld via de sjablonen Azure Portal, CLI en ARM. Inschakelen via Power shell wordt nog niet ondersteund. 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Diagnostische gegevens over beheerde opstarten inschakelen met behulp van de Azure Portal
Bij het maken van een virtuele machine in de Azure Portal is de standaard instelling dat diagnostische gegevens over opstarten zijn ingeschakeld met een beheerd opslag account. Als u dit wilt weer geven, gaat u naar het tabblad *beheer* tijdens het maken van de virtuele machine. 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="Scherm opname voor het inschakelen van beheerde diagnostische gegevens over opstarten tijdens het maken van VM'S.":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>Diagnostische gegevens over beheerde opstarten inschakelen met CLI
Diagnostische gegevens over opstarten met een beheerd opslag account worden ondersteund in azure CLI 2.12.0 en hoger. Als u geen naam of URI voor een opslag account invoert, wordt een beheerd account gebruikt. Zie de [cli-documentatie voor diagnostische](/cli/azure/vm/boot-diagnostics)gegevens over opstarten voor meer informatie en code voorbeelden.

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Diagnostische sjablonen voor beheerde opstarten met Azure Resource Manager (ARM) inschakelen
Alle na de API-versie 2020-06-01 ondersteunen diagnostische gegevens over het opstarten van de beheerder. Zie voor meer informatie de [instantie weergave diagnostische gegevens over opstarten](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics).

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>Beperkingen
- Diagnostische gegevens over opstarten zijn alleen beschikbaar voor Azure Resource Manager-Vm's.
- Diagnostische gegevens over beheerde opstarten ondersteunen geen Vm's met niet-beheerde besturingssysteem schijven.
- Diagnostische gegevens over het opstarten bieden geen ondersteuning voor Premium Storage-accounts. als een Premium Storage-account wordt gebruikt voor diagnostische gegevens over opstarten, treedt er een `StorageAccountTypeNotSupported` fout op bij het starten van de virtuele machine. 
- Beheerde opslag accounts worden ondersteund in de Resource Manager API-versie 2020-06-01 en hoger.
- De seriële console van Azure is momenteel niet compatibel met een beheerd opslag account voor diagnostische gegevens over opstarten. Meer informatie over [Azure Serial console](/troubleshoot/azure/virtual-machines/serial-console-overview).
- De portal biedt alleen ondersteuning voor het gebruik van diagnostische gegevens over opstarten met een beheerd opslag account voor Vm's met één exemplaar.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure Serial console](/troubleshoot/azure/virtual-machines/serial-console-overview) en over het gebruik van diagnostische gegevens over opstarten voor het [oplossen van problemen met virtuele machines in azure](/troubleshoot/azure/virtual-machines/boot-diagnostics).