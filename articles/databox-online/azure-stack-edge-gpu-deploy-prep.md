---
title: Zelfstudie voor het voorbereiden van het Azure-portal, datacenteromgeving om Azure Stack Edge Pro GPU te implementeren | Microsoft Docs
description: De eerste zelfstudie over het implementeren van Azure Stack Edge Pro GPU omvat het voorbereiden van de Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: a58146c2c8121f3f0e3b564caafbb09396d39672
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568530"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-with-gpu"></a>Zelfstudie: Voorbereidingen voor de implementatie van Azure Stack Edge Pro met GPU 

Dit is de eerste zelfstudie in de reeks zelfstudies voor implementatie. Deze zelfstudies zijn noodzakelijk voor de volledige implementatie van Azure Stack Edge Pro met GPU. In deze zelfstudie wordt beschreven hoe u de Azure-portal voorbereidt voor de implementatie van een Azure Stack Edge-resource.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

### <a name="get-started"></a>Aan de slag

Voor de implementatie van Azure Stack Edge Pro moet u eerst uw omgeving voorbereiden. Zodra de omgeving gereed is, voert u de vereiste stappen en, indien nodig, optionele stappen en procedures uit om het apparaat volledig te implementeren. In de stapsgewijze implementatie-instructies wordt aangeven wanneer u deze optionele stappen moet uitvoeren.

| Stap | Beschrijving |
| --- | --- |
| **Voorbereiding** |Deze moeten worden voltooid ter voorbereiding van de implementatie. |
| **[Configuratiecontrolelijst voor implementatie](#deployment-configuration-checklist)** |Gebruik deze controlelijst om informatie te verzamelen en te registreren voorafgaand aan en tijdens de implementatie. |
| **[Vereisten voor implementatie](#prerequisites)** |Aan de hand van deze vereisten wordt gecontroleerd of de omgeving gereed is voor implementatie. |
|  | |
|**Zelfstudies voor implementatie** |Deze zelfstudies zijn vereist als u uw Azure Stack Edge Pro-apparaat in productie wilt implementeren. |
|**[1. De Azure Portal voorbereiden voor Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)** |Maak en configureer uw Azure Stack Edge-resource voordat u een fysiek Azure Stack Edge-apparaat installeert. |
|**[2. Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md)**|Het fysieke Azure Stack Edge Pro-apparaat uitpakken, plaatsen en aansluiten.  |
|**[3. Verbinding maken met Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-connect.md)** |Zodra het apparaat is geïnstalleerd, maakt u verbinding met de lokale webinterface van het apparaat.  |
|**[4. Netwerkinstellingen configureren voor Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)** |Configureer een netwerk, inclusief het rekennetwerk en de webproxy-instellingen voor uw apparaat.   |
|**[5. Apparaatinstellingen configureren voor Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)** |Wijs een apparaatnaam en een DNS-domein toe, configureer de updateserver en de apparaattijd. |
|**[6. Beveiligingsinstellingen configureren voor Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-certificates.md)** |Configureer certificaten voor het apparaat. Gebruik certificaten die door het apparaat zijn gegenereerd of gebruik uw eigen certificaten.   |
|**[7. Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md)** |Gebruik de activeringssleutel van de service om het apparaat te activeren. Het apparaat is klaar om er SMB- of NFS-shares op in te stellen of om via REST verbinding te maken. |
|**[8. Rekenproces configureren](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configureer de rekenprocesrol op het apparaat. Er wordt ook een Kubernetes-cluster gemaakt. |
|**[9A. Gegevens overdragen met behulp van Edge-shares](./azure-stack-edge-gpu-deploy-add-shares.md)** |Voeg shares toe en maak verbinding met shares via SMB of NFS. |
|**[9B. Gegevens overdragen met Edge-opslagaccounts](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)** |Voeg opslagaccounts toe en maak verbinding met de blobopslag via REST API's. |


U kunt nu beginnen met het verzamelen van informatie over de softwareconfiguratie voor uw Azure Stack Edge Pro-apparaat.

## <a name="deployment-configuration-checklist"></a>Configuratiecontrolelijst voor implementatie

Voordat u uw apparaat implementeert, moet u informatie verzamelen om de software op uw Azure Stack Edge Pro-apparaat te configureren. De implementatie van het Azure Stack Edge-apparaat in uw omgeving verloopt gestroomlijnder wanneer u deze gegevens zoveel mogelijk op voorhand voorbereidt. Gebruik de [Configuratiecontrolelijst voor de implementatie van Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-checklist.md) om de configuratiegegevens te noteren terwijl u het apparaat implementeert.


## <a name="prerequisites"></a>Vereisten

Hier volgen de configuratievereisten voor uw Azure Stack Edge-resource, uw Azure Stack Edge Pro-apparaat en het datacenternetwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

Zorg voordat u begint voor het volgende:

- Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Zorg dat een ondersteund abonnement gebruikt zoals [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) of [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Betalen per gebruik-abonnementen worden niet ondersteund. Zie [Wat is een Azure-aanbieding?](../cost-management-billing/manage/switch-azure-offer.md#what-is-an-azure-offer) om te achterhalen welk type Azure-abonnement u hebt.
- U hebt toegang als eigenaar of inzender op het niveau van de resourcegroep voor de Azure Stack Edge Pro-/Data Box Gateway-, IoT Hub- en Azure Storage-resources.

    - Als u een Azure Stack Eedge / Data Box Gateway resource wilt maken, moet u machtigingen hebben als inzender (of hoger) op het niveau van de resourcegroep. 
    - U moet er ook voor zorgen dat de `Microsoft.DataBoxEdge` en `MicrosoftKeyVault`-resourceproviders zijn geregistreerd. Als u een IoT Hub-resource wilt maken, moet u de provider `Microsoft.Devices` registreren. 
        - Als u een resourceprovider wilt registreren, gaat u in de Azure Portal naar **Home > Abonnementen > Uw abonnement > Resourceproviders**. 
        - Zoek naar de specifieke resourceprovider, bijvoorbeeld `Microsoft.DataBoxEdge`, en registreer de resourceprovider. 
    - Als u een resource voor een Storage-account wilt maken, moet u een toegangsbereik van een inzender of hoger hebben op het niveau van de resourcegroep. Azure Storage is standaard een geregistreerde resourceprovider.
- U hebt beheerders- of gebruikerstoegang tot Azure Active Directory Graph API voor het genereren van bewerkingen voor een activeringssleutel of een referentie, zoals het maken van een share die gebruikmaakt van een opslagaccount. Zie [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) voor meer informatie.


### <a name="for-the-azure-stack-edge-pro-device"></a>Voor het Azure Stack Edge Pro-apparaat

Voordat u een fysiek apparaat implementeert, controleert u of:

- U hebt de beveiligingsgegevens die zijn opgenomen in het verzendpakket gecontroleerd.
- Er een 1U-sleuf beschikbaar is in een standaard 19-inch rek in uw datacenter om het apparaat te plaatsen.
- U beschikt over een vlak, stabiel en horizontaal werkoppervlak waar u het apparaat veilig kunt neerleggen.
- De locatie waar u het apparaat wilt neerzetten, beschikt over reguliere wisselstroom van een onafhankelijke bron of over een vermogenseenheid (PDU) met noodvoeding (UPS).
- U hebt toegang tot een fysiek apparaat.


### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Azure Stack Edge Pro-apparaat. Zie [Systeemvereisten voor Azure Stack Edge Pro](azure-stack-edge-system-requirements.md) voor meer informatie.

- Voor normale bedrijfsomstandigheden van uw Azure Stack Edge Pro hebt u het volgende nodig:

    - Minimaal 10 Mbps downloadbandbreedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
    - Minimaal 20 Mbps toegewezen upload- en downloadbandbreedte voor het overdragen van bestanden.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Azure Stack Edge-resource hebt voor het beheer van uw fysieke apparaat, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit in de Azure-portal om een Azure Stack Edge-resource te maken.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden bij de Azure-portal op de volgende URL: [https://portal.azure.com](https://portal.azure.com).

2. Selecteer **En een resource maken** in het linkerdeelvenster. Zoek en selecteer **Azure Stack Edge / Data Box Gateway**. Selecteer **Maken**. 

3. Kies het abonnement dat u wilt gebruiken voor het Azure Stack Edge Pro-apparaat. Selecteer het land waar u dit fysieke apparaat naar wilt verzenden. Selecteer **Apparaten weergeven**.

    ![Een resource maken 1](media/azure-stack-edge-gpu-deploy-prep/create-resource-1.png)

4. Selecteer het apparaattype. Kies onder **Azure Stack Edge Commercial** de optie **Azure Stack Edge Pro met GPU** en kies **Selecteren**. Als u problemen ziet of als u het apparaattype niet kunt selecteren, gaat u naar [Problemen met de volgorde oplossen](azure-stack-edge-troubleshoot-ordering.md).

    ![Een resource maken 3](media/azure-stack-edge-gpu-deploy-prep/create-resource-3.png)

5. Op basis van uw bedrijfsbehoeften kunt u Azure Stack Edge Pro selecteren met 1 of 2 grafische processoreenheden (Gpu's) van NVIDIA selecteren. 

    ![Een resource maken 4](media/azure-stack-edge-gpu-deploy-prep/create-resource-4.png)

6. Voer op het tabblad **Basisinstellingen** de volgende **Projectdetails** in of selecteer deze.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Het abonnement wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).     |

7. Voer de volgende **exemplaardetails** in of selecteer deze.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam   | Een beschrijvende naam om de resource aan te duiden.<br>De naam heeft tussen de 2 en 50 tekens, inclusief letters, cijfers en afbreekstreepjes.<br> De naam begint en eindigt met een letter of cijfer.        |
    |Regio     |Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Azure Stack Edge-resource beschikbaar is. Als Azure Government wordt gebruikt, zijn alle overheidsregio's beschikbaar, zoals wordt weergegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

    ![Een resource maken 5](media/azure-stack-edge-gpu-deploy-prep/create-resource-5.png)

8. Selecteer **Volgende: Verzendadres**.

    - Als u al een apparaat hebt, selecteert u de keuzelijst met invoervak **Ik heb al een apparaat**.

        ![Een resource maken 6](media/azure-stack-edge-gpu-deploy-prep/create-resource-6.png)

    - Als dit het nieuwe apparaat is dat u bestelt, voert u de naam van de contact persoon, het bedrijf, het adres voor het verzenden van het apparaat en contact gegevens in.

        ![Een resource maken 7](media/azure-stack-edge-gpu-deploy-prep/create-resource-7.png)

9. Selecteer **Volgende: Tags**. Geef eventueel tags op voor het categoriseren van resources en het samenvoegen van facturering. Selecteer **Volgende: Beoordelen en maken**.

10. Bekijk op het tabblad **Controleren en maken** de **Prijsdetails**, **Gebruiksvoorwaarden** en de details van uw resource. Selecteer de keuzelijst met invoervak voor **Ik heb de privacyvoorwaarden gecontroleerd**.

    ![Een resource maken 8](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png) 

    U wordt ook gewaarschuwd wanneer er een Managed Service Identity (MSI) wordt gemaakt, waarmee u zich kunt verifiëren bij cloudservices. Deze identiteit bestaat zolang de resource bestaat.

11. Selecteer **Maken**.

    Het maken van de resource duurt enkele minuten. Er wordt ook een MSI gemaakt waarmee het Azure Stack Edge-apparaat kan communiceren met de resourceprovider in Azure.

    Nadat de resource succesvol is gemaakt en geïmplementeerd, wordt u daarvan op de hoogte gebracht. Selecteer **Ga naar resource**.

    ![Ga naar de Azure Stack Edge Pro-resource](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-1.png)

Nadat de bestelling is geplaatst, controleert micro soft de bestelling en maakt u via e-mail contact met de verzend gegevens.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png)-->

> [!NOTE]
> Als u meerdere bestellingen tegelijk wilt doen of een bestaande bestelling wilt klonen, kunt u de [scripts in Azure Samples](https://github.com/Azure-Samples/azure-stack-edge-order) gebruiken. Zie het Leesmij-bestand voor meer informatie.

Als u problemen ondervindt tijdens het bestelproces, raadpleegt u [Problemen met de bestelling oplossen](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als dat nodig is, kunt u uw omgeving voorbereiden voor Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Als u een Azure Stack Edge-resource wilt maken, voert u de volgende opdrachten uit in azure CLI.

1. Maak een resource groep met de opdracht [AZ Group Create](/cli/azure/group#az_group_create) of gebruik een bestaande resource groep:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Als u een apparaat wilt maken, gebruikt u de opdracht [AZ databoxedge Device Create](/cli/azure/databoxedge/device#az_databoxedge_device_create) :

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgeP_Base
   ```

   Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. In de regio worden alleen de metagegevens voor apparaatbeheer opgeslagen. De werkelijke gegevens kunnen worden opgeslagen in elk opslagaccount.

   Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Azure Stack Edge-resource beschikbaar is. Als Azure Government wordt gebruikt, zijn alle overheidsregio's beschikbaar, zoals wordt weergegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).

1. Voer de opdracht [AZ databoxedge order Create](/cli/azure/databoxedge/order#az_databoxedge_order_create) uit om een order te maken:

   ```azurecli 
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Het maken van de resource duurt enkele minuten. Voer de opdracht [AZ databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) uit om de volg orde te bekijken:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Nadat u een bestelling hebt geplaatst, controleert micro soft de bestelling en maakt u via e-mail contact met de verzend gegevens.

---

## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Stack Edge-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Azure Stack Edge Pro-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt en selecteer **Overzicht**.

2. Geef in het rechterdeelvenster een naam op voor de Azure Key Vault of accepteer de standaardnaam. De naam van de sleutelkluis mag tussen de 3 en 24 tekens lang zijn.

   Er wordt een sleutelkluis gemaakt voor elke Azure Stack Edge-resource die met uw apparaat wordt geactiveerd. Met de sleutelkluis kunt u geheimen opslaan en openen, de CIK (Channel Integrity Key) voor de service wordt bijvoorbeeld opgeslagen in de sleutelkluis. 

   Wanneer u een naam voor de sleutelkluis hebt opgegeven, selecteert u **Sleutel genereren** om een activeringssleutel te maken. 

   ![Activeringssleutel ophalen](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

   Wacht enkele minuten terwijl de sleutelkluis en de activeringssleutel worden gemaakt. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.<!--Verify that the new screen has a copy icon.-->


> [!IMPORTANT]
> - De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> - Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over onderwerpen met betrekking tot Azure Stack Edge Pro, zoals:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Ga naar de volgende zelfstudie om te lezen hoe u Azure Stack Edge Pro installeert.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro installeren](./azure-stack-edge-gpu-deploy-install.md)