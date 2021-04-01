---
title: bestand opnemen
description: bestand opnemen
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "99808530"
---
## <a name="create-the-virtual-network-and-subnet"></a>Het virtuele netwerk en subnet maken

In deze sectie gaat u een virtueel netwerk en een subnet maken.

1. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **Nieuwe maken**, voer **\<resource-group-name>** in en selecteer vervolgens OK, of selecteer een bestaande **\<resource-group-name>** op basis van parameters. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | **\<virtual-network-name>** invoeren                                    |
    | Region           | **\<region-name>** selecteren |

3. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

4. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | **\<IPv4-address-space>** invoeren |

5. Onder **Subnetnaam** selecteert u het woord **standaard**.

6. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | **\<subnet-name>** invoeren |
    | Subnetadresbereik | **\<subnet-address-range>** invoeren

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

9. Selecteer **Maken**.