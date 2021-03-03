---
title: 'Quickstart: Azure Bastion configureren en verbinding maken met een VM via een privé IP-adres en een browser'
titleSuffix: Azure Bastion
description: In deze quickstart leert u hoe u een Azure Bastion-host maakt op basis van een virtuele machine en in de browser veilig verbinding maakt met de VM door middel van een privé IP-adres.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 8aeba13954283ca35c3eb0060a0e588ba6a7adbe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707137"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Quickstart: veilig verbinding maken met een VM via een browser door middel van een privé IP-adres

U kunt in een browser verbinding maken met een virtuele machine (VM) met behulp van Azure Portal en Azure Bastion. In deze quickstart leest u hoe u Azure Bastion configureert op basis van uw VM-instellingen en vervolgens via de portal verbinding maakt met uw VM. Voor de VM zijn geen openbaar IP-adres, clientsoftware, agent of speciale configuratie nodig. Zodra de service is ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. Zie [Wat is Azure Bastion?](bastion-overview.md) voor meer informatie over Azure Bastion.

## <a name="prerequisites"></a><a name="prereq"></a>Vereisten

* Een Azure-account met een actief abonnement. Als u dit niet hebt, [kunt u er gratis een maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Als u via uw browser verbinding wilt maken met een VM met behulp van Bastion, moet u zich kunnen aanmelden bij Azure Portal.

* Een virtuele Windows-machine in een virtueel netwerk. Als u nog geen VM hebt, maakt u er een met behulp van [Quickstart: een VM maken](../virtual-machines/windows/quick-create-portal.md).

  * Als u voorbeeldwaarden nodig hebt, raadpleegt u de opgegeven [voorbeeldwaarden](#values).
  * Als u al een virtueel netwerk hebt, selecteert u dit op het tabblad Netwerken tijdens het maken van de VM.
  * Als u nog geen virtueel netwerk hebt, kunt u er een maken op het moment dat u uw VM maakt.
  * U hoeft geen openbaar IP-adres voor deze VM te hebben om verbinding te kunnen maken via Azure Bastion.

* Vereiste VM-rollen:
  * De lezerrol op de virtuele machine.
  * De lezerrol op de NIC met het privé-IP-adres van de virtuele machine.
  
* Vereiste VM-poorten:
  * Poorten voor inkomend verkeer: RDP (3389)

### <a name="example-values"></a><a name="values"></a>Voorbeeldwaarden

U kunt de volgende voorbeeldwaarden gebruiken bij het maken van deze configuratie, maar u kunt ze ook vervangen door uw eigen waarden.

**VNet- en VM-basiswaarden:**

|**Naam** | **Waarde** |
| --- | --- |
| Virtuele machine| TestVM |
| Resourcegroep | TestRG1 |
| Regio | VS - oost |
| Virtueel netwerk | VNet1 |
| Adresruimte | 10.1.0.0/16 |
| Subnetten | Front-end: 10.1.0.0/24 |

**Azure Bastion-waarden:**

|**Naam** | **Waarde** |
| --- | --- |
| Naam | VNet1-Bastion |
| + Subnet-naam | AzureBastionSubnet |
| AzureBastionSubnet-adressen | Een subnet binnen uw VNet-adresruimte met een /27-subnetmasker. Bijvoorbeeld 10.1.1.0/27.  |
| Openbaar IP-adres |  Nieuwe maken |
| Naam openbaar IP-adres | VNet1-IP  |
| Openbaar IP-adres SKU |  Standard  |
| Toewijzing  | Statisch |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Een Bastion-host maken

U kunt een bastion-host op verschillende manieren configureren. In de volgende stappen maakt u rechtstreeks vanuit uw VM een bastion-host in Azure Portal. Wanneer u een host vanuit een VM maakt, worden er automatisch verschillende instellingen ingevuld die overeenkomen met de virtuele machine en/of het virtuele netwerk.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar de VM waarmee u verbinding wilt maken en selecteer **Verbinding maken**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Scherm opname van de instellingen van de virtuele machine." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. In de vervolgkeuzelijst selecteert u **Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Scherm opname van Bastion vervolg keuzelijst." lightbox="./media/quickstart-host-portal/bastion.png":::
1. Op de **pagina TestVM | Verbinding maken** selecteer **Bastion gebruiken**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Scherm opname van use Bastion.":::

1. Configureer de waarden op de pagina **verbinding maken met Azure Bastion** .

   * **Stap 1:** De waarden zijn vooraf ingevuld omdat u de bastion-host rechtstreeks vanuit uw VM maakt.

   * **Stap 2:** De adres ruimte is vooraf ingevuld met een aanbevolen adres ruimte. De AzureBastionSubnet moet een adres ruimte hebben van/27 of groter (/26,/25, enzovoort)...

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Scherm opname van het Bastion-subnet maken.":::

1. Klik op **subnet maken** om de AzureBastionSubnet te maken.
1. Nadat het subnet is gemaakt, wordt de pagina automatisch door **lopen naar stap 3**. Gebruik voor stap 3 de volgende waarden:

   * **Naam:** Noem de bastion-host.
   * **Openbaar IP-adres**: selecteer **Nieuwe maken**.
   * **Naam van openbaar IP-adres:** De naam van de resource voor het open bare IP-adres.
   * **SKU openbaar IP-adres:** Vooraf geconfigureerd als **standaard**
   * **Toewijzing:** Vooraf geconfigureerd naar **statisch**. U kunt geen dynamische toewijzing voor Azure Bastion gebruiken.
   * **Resourcegroep**: Dezelfde resourcegroep als de VM.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="Scherm afbeelding van stap 3.":::
1. Nadat u de waarden hebt voltooid, selecteert **u Azure Bastion maken met de standaard instellingen**. Azure valideert uw instellingen en maakt vervolgens de host. Het duurt ongeveer vijf minuten om de host en de resources te maken en te implementeren.

## <a name="connect"></a><a name="connect"></a>Verbinding maken

Nadat Bastion in het virtuele netwerk is geïmplementeerd, verschijnt de pagina Verbinding maken.

1. Voer de gebruikersnaam en het wachtwoord voor uw virtuele machine in. Selecteer vervolgens **Verbinding maken**.

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Schermopname van het dialoogvenster Verbinding maken met behulp van Azure Bastion.":::
1. De RDP-verbinding met deze virtuele machine wordt rechtstreeks geopend in Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Verbinding maken met RDP":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruiken van het virtuele netwerk en de virtuele machines, verwijdert u de resourcegroep en alle resources die deze groep bevat:

1. Voer de naam van uw resourcegroep in het vak **Zoeken** bovenaan de portal in en selecteer het in de zoekresultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer uw resourcegroep in voor **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een bastion-host voor uw virtuele netwerk gemaakt en vervolgens via de Bastion-host veilig verbonden met een virtuele machine. U kunt nu doorgaan met de volgende stap als u verbinding wilt maken met een virtuele-machineschaalset.

> [!div class="nextstepaction"]
> [Verbinding maken met een virtuele-machineschaalset met behulp van Azure Bastion](bastion-connect-vm-scale-set.md)
