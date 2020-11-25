---
title: 'Quickstart: Azure Bastion configureren en verbinding maken met een VM via een privé IP-adres en een browser'
titleSuffix: Azure Bastion
description: In deze quickstart leert u hoe u een Azure Bastion-host maakt op basis van een virtuele machine en in de browser veilig verbinding maakt met de VM door middel van een privé IP-adres.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021483"
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
| Resourcegroep | TestRG |
| Regio | VS - oost |
| Virtueel netwerk | TestVNet1 |
| Adresruimte | 10.0.0.0/16 |
| Subnetten | FrontEnd: 10.0.0.0/24 |

**Azure Bastion-waarden:**

|**Naam** | **Waarde** |
| --- | --- |
| Naam | TestVNet1-bastion |
| + Subnet-naam | AzureBastionSubnet |
| AzureBastionSubnet-adressen | Een subnet binnen uw VNet-adresruimte met een /27-subnetmasker. Bijvoorbeeld 10.0.1.0/27.  |
| Openbaar IP-adres |  Nieuwe maken |
| Naam openbaar IP-adres | VNet1BastionPIP  |
| Openbaar IP-adres SKU |  Standard  |
| Toewijzing  | Statisch |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Een Bastion-host maken

U kunt een bastion-host op verschillende manieren configureren. In de volgende stappen maakt u rechtstreeks vanuit uw VM een bastion-host in Azure Portal. Wanneer u een host vanuit een VM maakt, worden er automatisch verschillende instellingen ingevuld die overeenkomen met de virtuele machine en/of het virtuele netwerk.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar de VM waarmee u verbinding wilt maken en selecteer **Verbinding maken**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="Instellingen van virtuele machines" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. In de vervolgkeuzelijst selecteert u **Bastion**.
1. Op de **pagina TestVM | Verbinding maken** selecteer **Bastion gebruiken**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Bastion selecteren" border="false":::

1. Vul op de pagina **Bastion** de volgende instellingsvelden in:

   * **Naam**: Geef de Bastion-host een naam.
   * **Subnet**: Dit is de adresruimte van het virtuele netwerk waarop de Bastion-resource wordt geïmplementeerd. Het subnet moet worden gemaakt met de naam **AzureBastionSubnet**. U moet een subnet van minimaal /27 of groter (/27, /26, /25, enzovoort) gebruiken.
   * Selecteer **Subnetconfiguratie beheren**.
1. Op de pagina **Subnetten** selecteert u **+Subnet**.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="+ Subnet":::
    
1. Op de pagina **Subnet toevoegen** typt u voor **Naam** **AzureBastionSubnet**.
   * Kies voor het subnetadresbereik een subnetadres dat zich binnen de adresruimte van uw virtuele netwerk bevindt.
   * Pas geen andere instellingen aan. Selecteer **OK** om de wijzigingen aan het subnet te accepteren en op te slaan.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="Subnet toevoegen":::
1. Klik op de knop Terug in de browser om terug te gaan naar de pagina **Bastion** en door te gaan met het opgeven van waarden.
   * **Openbaar IP-adres**: laten staan als **Nieuwe maken**.
   * **Openbare IP-adresnaam**: De naam van de resource voor het openbare IP-adres.
   * **Toewijzing**: wordt standaard ingesteld op Statisch. U kunt geen dynamische toewijzing voor Azure Bastion gebruiken.
   * **Resourcegroep**: Dezelfde resourcegroep als de VM.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="Maak de bastion-host":::
1. Selecteer **Maken** om de bastion-host te maken. Azure valideert uw instellingen en maakt vervolgens de host. Het duurt ongeveer vijf minuten om de host en de resources te maken en te implementeren.

## <a name="connect"></a><a name="connect"></a>Verbinding maken

Nadat Bastion in het virtuele netwerk is geïmplementeerd, verschijnt de pagina Verbinding maken.

1. Voer de gebruikersnaam en het wachtwoord voor uw virtuele machine in. Selecteer vervolgens **Verbinding maken**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="Schermopname van het dialoogvenster Verbinding maken met behulp van Azure Bastion.":::
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
