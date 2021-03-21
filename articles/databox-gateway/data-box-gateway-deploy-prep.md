---
title: Zelfstudie over het voorbereiden van Azure Portal voor de implementatie van Data Box Gateway | Microsoft Docs
description: De eerste zelfstudie voor het implementeren van Azure Data Box Gateway omvat het voorbereiden van Azure Portal.
services: databox-edge-gateway
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: ceda5015770ad0b9898def181fa7199f119920db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706092"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Zelfstudie: Voorbereidingen voor de implementatie van Azure Data Box Gateway

Dit is de eerste zelfstudie in de reeks zelfstudies voor implementatie, die noodzakelijk zijn voor het voltooien van de implementatie van uw Azure Data Box Gateway. In deze zelfstudie wordt beschreven hoe u Azure Portal voorbereidt voor de implementatie van de Data Box Gateway-resource.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De installatiekopie voor het virtuele apparaat downloaden
> * De activeringssleutel ophalen

## <a name="get-started"></a>Aan de slag

Raadpleeg de volgende zelfstudies in de voorgeschreven volgorde voor het implementeren van uw Data Box Gateway.

| **#** | **In deze stap** | **Gebruikt u deze documenten** |
| --- | --- | --- | 
| 1. |**[Azure Portal voorbereiden voor Data Box Gateway](data-box-gateway-deploy-prep.md)** |Maak en configureer uw Data Box Gateway-resource voordat u een virtueel Data Box Gateway-apparaat inricht. |
| 2. |**[Data Box Gateway inrichten in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Data Box Gateway inrichten in VMware](data-box-gateway-deploy-provision-vmware.md)**|Voor Hyper-V moet u een virtueel Data Box Gateway-apparaat inrichten en verbinden op een hostsysteem waarop Hyper-V wordt uitgevoerd in Windows Server 2016 of Windows Server 2012 R2. <br><br><br> Voor VMware moet u een virtueel Data Box Gateway-apparaat inrichten en verbinden op een hostsysteem waarop VMware ESXi 6.0, 6.5 of 6.7 wordt uitgevoerd.<br></br> |
| 3. |**[Azure Data Box Gateway verbinden, instellen en activeren](data-box-gateway-deploy-connect-setup-activate.md)** |Maak verbinding met de lokale webinterface, voltooi het instellen van het apparaat en activeer het. Vervolgens kunt u SMB-shares inrichten.  |
| 4. |**[Gegevens overdragen met Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Shares toevoegen en verbinding maken met shares via SMB of NFS. |

U kunt nu Azure Portal gaan instellen.

## <a name="prerequisites"></a>Vereisten

Hier vindt u de configuratievereisten voor uw Data Box Gateway-resource, uw Data Box Gateway-apparaat en het datacenternetwerk.

### <a name="for-the-data-box-gateway-resource"></a>Voor de Data Box Gateway-resource

Zorg voordat u begint voor het volgende:

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Zorg dat een ondersteund abonnement gebruikt zoals [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) of [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* U hebt toegang als eigenaar of inzender op het niveau van de resourcegroep voor de Azure Stack Edge/Data Box Gateway, IoT Hub en Azure Storage-resources.
    - Als u een Azure Stack Eedge / Data Box Gateway resource wilt maken, moet u machtigingen hebben als inzender (of hoger) op het niveau van de resourcegroep. U moet er ook voor zorgen dat de `Microsoft.DataBoxEdge` provider is geregistreerd. Ga voor meer informatie over het registreren naar [Resourceprovider registreren](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers).
    - Als u een resource voor een Storage-account wilt maken, moet u een toegangsbereik van een inzender of hoger hebben op het niveau van de resourcegroep. Azure Storage is standaard een geregistreerde resourceprovider.
- U hebt beheerder- of gebruikerstoegang nodig voor Microsoft Graph-API. Raadpleeg [Microsoft Graph-machtigingen](/graph/permissions-reference) voor meer informatie.
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

### <a name="for-the-data-box-gateway-device"></a>Voor het Data Box Gateway-apparaat

Voordat u een virtueel apparaat implementeert, controleert u of:

- U toegang hebt tot een hostsysteem met Hyper-V in Windows Server 2012 R2 of hoger, of VMware (ESXi 6.0, 6.5 of 6.7) dat kan worden gebruikt voor het inrichten van een apparaat.
- Het hostsysteem kan de volgende resources volledig toewijzen aan het inrichten van uw virtuele Data Box-apparaat:
  
  - Minimaal 4 virtuele processors.
  - Ten minste 8 GB RAM-geheugen. We raden u ten zeerste aan om ten minste 16 GB RAM-geheugen.
  - Eén netwerkinterface.
  - Een besturingssysteemschijf van 250 GB.
  - Een virtuele schijf van 2 TB voor systeemgegevens

### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Data Box Gateway-apparaat. Raadpleeg [Systeemvereisten voor Data Box Gateway](data-box-gateway-system-requirements.md) voor meer informatie.

- Voor normale bedrijfsomstandigheden van uw Data Box Gateway moet u het volgende hebben:

    - Minimaal 10 Mbps downloadbandbreedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
    - Minimaal 20 Mbps toegewezen upload- en downloadbandbreedte voor het overdragen van bestanden.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Data Box Gateway-resource hebt voor het beheer van uw virtuele apparaten, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

Voer de volgende stappen uit in Azure Portal om een Data Box Gateway-resource te maken.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden bij een van deze portals:

    - Ga naar de Azure-portal op deze URL: [https://portal.azure.com](https://portal.azure.com).
    - De Azure Government Portal op deze URL: [https://portal.azure.us](https://portal.azure.us). Ga naar [Verbinding maken met Azure Government met behulp van de portal](../azure-government/documentation-government-get-started-connect-with-portal.md) voor meer informatie.
    
2. Selecteer **+ Een resource maken**.

    ![De knop Een resource maken in Azure Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-create-a-resource.png)

3. Typ **Data Box Gateway** in het zoekvak en druk op Enter.

    ![Zoeken naar de Data Box Gateway-service](media/data-box-gateway-deploy-prep/data-box-gateway-search-box.png)

    Selecteer vervolgens **Data Box Gateway**.

    ![Selecteer de Data Box Gateway-SKU](media/data-box-gateway-deploy-prep/data-box-gateway-sku.png)

4. Selecteer **Maken**.

    ![Klik op Maken om de Data Box Gateway-resource te maken](media/data-box-gateway-deploy-prep/data-box-gateway-create.png)

5. Op het tabblad **Basis**:

    Voer de volgende **Projectdetails** in of selecteer deze.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Kies het abonnement dat u wilt gebruiken voor uw Data Box Gateway-apparaat. Het abonnement is gekoppeld aan uw factureringsrekening.|
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).|

   Voer de volgende **exemplaardetails** in of selecteer deze.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam   |Een beschrijvende naam om de resource aan te duiden.<br>De naam is tussen 2 en 50 tekens lang en kan letters, cijfers en afbreekstreepjes bevatten. <br> De naam moet beginnen en eindigen met een letter of cijfer. |
    |Regio  |Selecteer de regio waar u uw resource wilt implementeren. Kies een locatie die het dicht bij de geografische regio ligt waar u uw apparaat wilt implementeren. <br> Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Data Base Gateway/Azure Stack Edge-resources beschikbaar zijn. <br> Voor Azure Government zijn alle regio's die worden genoemd in [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/) beschikbaar.|

   Selecteer vervolgens **Beoordelen + maken** om uw bestelling te controleren.

   ![Details de vermelding van het project en de instantie voor een Data Box Gateway-bestelling](media/data-box-gateway-deploy-prep/data-box-gateway-basics.png)

7. Bekijk op het tabblad **Controleren en maken** de **Prijsdetails**, **Gebruiksvoorwaarden** en de details van uw resource. Selecteer **Maken**.

    ![Data Box Gateway-resourcegegevens weergegeven voor beoordeling](media/data-box-gateway-deploy-prep/data-box-gateway-resource-review-create.png)

Het maken van de resource duurt enkele minuten. Nadat de resource succesvol is gemaakt en geïmplementeerd, wordt u daarvan op de hoogte gebracht. Selecteer **Ga naar resource**.

![Een voltooide Data Box Gateway-bestelling](media/data-box-gateway-deploy-prep/data-box-gateway-completed-order.png)

## <a name="download-the-virtual-device-image"></a>De installatiekopie voor het virtuele apparaat downloaden

Nadat u de Data Box Gateway-resource hebt gemaakt, downloadt u de juiste installatiekopie voor het virtuele apparaat om een virtueel apparaat in te richten op uw hostsysteem. De installatiekopieën van virtuele apparaten zijn specifiek voor een besturingssysteem.

> [!IMPORTANT]
> De software die wordt uitgevoerd op de Data Box Gateway kan alleen worden gebruikt met de Data Box Gateway-resource.

Volg deze stappen in de [Azure Portal](https://portal.azure.com/) om een installatiekopie van een virtueel apparaat te downloaden.

1. Selecteer de resource die u hebt gemaakt en selecteer vervolgens **Overzicht**. Als u een bestaande Azure Data Box Gateway-resource hebt, selecteert u de resource en gaat u naar **Overzicht**. Selecteer **Apparaatinstallatie**.

    ![Nieuwe Data Box Gateway-resource](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Selecteer op de tegel **Installatiekopie downloaden** de installatiekopie van het virtuele apparaat dat overeenkomt met het besturingssysteem in de hostserver die wordt gebruikt om de VM in te richten. De installatiekopieën zijn ongeveer 5,6 GB.
   
   * [VHDX voor Hyper-V in Windows Server 2012 R2 en hoger](https://aka.ms/dbe-vhdx-2012).
   * [VMDK voor VMware ESXi 6.0, 6.5 of 6.7](https://aka.ms/dbe-vmdk).

    ![Installatiekopie van virtueel Data Box Gateway-apparaat downloaden](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Download het bestand en pak het uit op een lokale schijf, en onthoud waar het zipbestand is uitgepakt.


## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Data Box Gateway-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Data Box Gateway-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt en selecteer vervolgens **Overzicht**. Ga in **Apparaat instellen** naar de tegel **Configureren en activeren**.

    ![Tegel configureren en activeren](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selecteer **Sleutel genereren** om een activeringssleutel te maken. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.

    ![Activeringssleutel ophalen](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> - Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Gateway, zoals:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De installatiekopie voor het virtuele apparaat downloaden
> * De activeringssleutel ophalen

Ga naar de volgende zelfstudie om te lezen hoe u een virtuele machine kunt inrichten voor uw Data Box Gateway. Raadpleeg hier de specifieke instructies voor het besturingssysteem van uw host:

> [!div class="nextstepaction"]
> [Een Azure Data Box Gateway inrichten in Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OF

> [!div class="nextstepaction"]
> [Een Azure Data Box Gateway inrichten in VMware](./data-box-gateway-deploy-provision-vmware.md)