---
title: Zelfstudie voor het voorbereiden van de Azure-portal, datacenteromgeving om Azure Stack Edge Pro te implementeren | Microsoft Docs
description: De eerste zelfstudie over het implementeren van Azure Stack Edge Pro omvat het voorbereiden van de Azure-portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 9ceba84cb3bbe52dc5ba51d0f4945f5bad0a5034
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573939"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro"></a>Zelfstudie: Voorbereidingen voor de implementatie van Azure Stack Edge Pro  

Dit is de eerste zelfstudie in de reeks zelfstudies voor implementatie die noodzakelijk zijn voor het voltooien van de implementatie van Azure Stack Edge Pro. In deze zelfstudie wordt beschreven hoe u de Azure-portal voorbereidt voor de implementatie van een Azure Stack Edge-resource. 

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="get-started"></a>Aan de slag

Raadpleeg de volgende zelfstudies in de voorgeschreven volgorde voor het implementeren van Azure Stack Edge Pro.

| **#** | **In deze stap** | **Gebruikt u deze documenten** |
| --- | --- | --- | 
| 1. |**[De Azure-portal voorbereiden voor Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)** |Maak en configureer uw Azure Stack Edge-resource voordat u een fysiek Azure Stack Edge-apparaat installeert. |
| 2. |**[Azure Stack Edge Pro installeren](azure-stack-edge-deploy-install.md)**|Pak het fysieke Azure Stack Edge Pro-apparaat uit, plaats het en sluit het aan.  |
| 3. |**[Azure Stack Edge Pro verbinden, instellen en activeren](azure-stack-edge-deploy-connect-setup-activate.md)** |Maak verbinding met de lokale webinterface, voltooi het instellen van het apparaat en activeer het. Het apparaat is klaar om er SMB- of NFS-shares op in te stellen.  |
| 4. |**[Gegevens overdragen met Azure Stack Edge Pro](azure-stack-edge-deploy-add-shares.md)** |Voeg shares toe en maak verbinding met shares via SMB of NFS. |
| 5. |**[Gegevens transformeren met Azure Stack Edge Pro](azure-stack-edge-deploy-configure-compute.md)** |Configureer de rekenmodules op het apparaat om gegevens te transformeren wanneer ze naar Azure worden overgezet. |

U kunt nu Azure Portal gaan instellen.

## <a name="prerequisites"></a>Vereisten

Hier volgen de configuratievereisten voor uw Azure Stack Edge-resource, uw Azure Stack Edge Pro-apparaat en het datacenternetwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Voor de Azure Stack Edge-resource

Zorg voordat u begint voor het volgende:

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Zorg dat een ondersteund abonnement gebruikt zoals [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) of [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Betalen per gebruik-abonnementen worden niet ondersteund.

* U hebt toegang als eigenaar of inzender op het niveau van de resourcegroep voor de Azure Stack Edge/Data Box Gateway, IoT Hub en Azure Storage-resources.

  * U moet een **Eigenaar** op abonnementsniveau zijn om Inzender-toegang te verlenen. Als u Inzender-toegang wilt verlenen aan iemand anders, gaat u in de Azure-portal naar **Alle services** > **Abonnementen** > **Access Control (IAM)**  >  **+Toevoegen** > **Roltoewijzing toevoegen**. Zie [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van de Azure-portal](../role-based-access-control/quickstart-assign-role-user-portal.md).

  * Als u een Azure Stack Eedge / Data Box Gateway resource wilt maken, moet u machtigingen hebben als inzender (of hoger) op het niveau van de resourcegroep. U moet er ook voor zorgen dat de `Microsoft.DataBoxEdge`-resourceprovider is geregistreerd. Ga voor meer informatie over het registreren van een resourceprovider naar [Resourceprovider registreren](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Als u een IoT Hub resource wilt maken, moet u ervoor zorgen dat de provider micro soft. devices is geregistreerd. Ga voor meer informatie over het registreren naar [Resourceprovider registreren](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Als u een resource voor een Storage-account wilt maken, moet u een toegangsbereik van een inzender of hoger hebben op het niveau van de resourcegroep. Azure Storage is standaard een geregistreerde resourceprovider.
* U hebt beheerder-of gebruikerstoegang tot Azure Active Directory Graph API. Zie [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) voor meer informatie.
* U hebt een Microsoft Azure Storage-account met toegangsreferenties.
* U bent niet geblokkeerd door een Azure-beleid dat door uw systeembeheerder is ingesteld. Zie voor meer informatie over beleid [Quickstart: Een beleidstoewijzing maken om niet-conforme resources te identificeren](../governance/policy/assign-policy-portal.md).

### <a name="for-the-azure-stack-edge-pro-device"></a>Voor het Azure Stack Edge Pro-apparaat

Voordat u een fysiek apparaat implementeert, controleert u of:

* U hebt de beveiligingsgegevens die zijn opgenomen in het verzendpakket gecontroleerd.
* Er een 1U-sleuf beschikbaar is in een standaard 19-inch rek in uw datacenter om het apparaat te plaatsen.
* U beschikt over een vlak, stabiel en horizontaal werkoppervlak waar u het apparaat veilig kunt neerleggen.
* De locatie waar u het apparaat wilt neerzetten, beschikt over reguliere wisselstroom van een onafhankelijke bron of over een vermogenseenheid (PDU) met noodvoeding (UPS).
* U hebt toegang tot een fysiek apparaat.

### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

* Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Azure Stack Edge Pro-apparaat. Zie [Systeemvereisten voor Azure Stack Edge Pro](azure-stack-edge-system-requirements.md) voor meer informatie.

* Voor normale bedrijfsomstandigheden van uw Azure Stack Edge Pro hebt u het volgende nodig:

  * Mini maal 10 Mbps down load band breedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
  * Mini maal 20 Mbps toegewezen upload-en download bandbreedte voor het overdragen van bestanden.

## <a name="create-new-resource-for-existing-device"></a>Nieuwe resource maken voor bestaand apparaat

Als u een bestaande Azure Stack Edge Pro-klant bent, gebruikt u de volgende procedure om een nieuwe resource te maken als u uw bestaande apparaat moet vervangen of opnieuw wilt instellen.

Als u een nieuwe klant bent, raden we u aan om te verkennen met behulp van Azure Stack Edge Pro GPU-apparaten voor uw workloads. Ga voor meer informatie naar [Wat is Azure stack Edge Pro met GPU](azure-stack-edge-gpu-overview.md). Voor informatie over het best Ellen van een Azure Stack Edge Pro met GPU-apparaat, gaat u naar [een nieuwe resource maken voor Azure stack Edge Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Als u een nieuwe Azure Stack Edge Pro-resource wilt maken voor een bestaand apparaat, voert u de volgende stappen uit in de Azure Portal.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op:

    - Ga naar de Azure-portal op deze URL: [https://portal.azure.com](https://portal.azure.com).
    - Of de Azure Government Portal op deze URL: [https://portal.azure.us](https://portal.azure.us). Ga naar [Verbinding maken met Azure Government met behulp van de portal](../azure-government/documentation-government-get-started-connect-with-portal.md) voor meer informatie.

1. Selecteer **+ Een resource maken**. Zoek en selecteer **Azure stack rand**. Selecteer vervolgens **Maken**.

1. Selecteer het abonnement voor de Azure Stack Edge Pro-apparaat en het land waarnaar het apparaat moet worden **verzonden.**

   ![Het abonnement en het verzend land voor uw apparaat selecteren](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. Selecteer **Azure stack Edge Pro-FPGA** in de lijst met apparaattypen die worden weer gegeven. Kies dan de optie **Selecteren**. 

   Het apparaat type **Azure stack Edge Pro-FPGA** wordt alleen weer gegeven als u een bestaand apparaat hebt. Als u een nieuw apparaat wilt best Ellen, gaat u naar [een nieuwe resource maken voor Azure stack Edge Pro-GPU](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Azure Stack Edge-service zoeken](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. Op het tabblad **Basis**:

   1. Voer de volgende **Projectdetails** in of selecteer deze.
    
       |Instelling  |Waarde  |
       |---------|---------|
       |Abonnement    |Deze waarde wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
       |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).     |

   1. Voer de volgende **exemplaardetails** in of selecteer deze.

       |Instelling  |Waarde  |
       |---------|---------|
       |Naam   | Een beschrijvende naam om de resource aan te duiden.<br>De naam heeft een waarde van 2 tot 50 tekens, inclusief letters, cijfers en afbreek streepjes.<br> De naam begint en eindigt met een letter of cijfer.        |
       |Regio     |Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Azure Stack Edge-resource beschikbaar is. Als Azure Government wordt gebruikt, zijn alle overheidsregio's beschikbaar, zoals wordt weergegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

   1. Selecteer **Controleren + maken**.

    ![Details van het project en exemplaar](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. Bekijk op het tabblad **controleren en maken** de **Gebruiksvoorwaarden**, **prijs informatie** en de details van uw resource. Selecteer vervolgens **Maken**.

    ![Details van de resource en de privacyverklaring van Azure Stack Edge controleren](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. Het maken van de resource duurt enkele minuten. Nadat de resource succesvol is gemaakt en geïmplementeerd, wordt u daarvan op de hoogte gebracht. Selecteer **Ga naar resource**.

   ![Ga naar de Azure Stack Edge-resource](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Nadat de bestelling is geplaatst, controleert micro soft de bestelling en maakt u via e-mail contact met de verzend gegevens.

![Melding voor beoordeling van de volgorde van de Azure Stack Edge Pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Stack Edge-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Azure Stack Edge Pro-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Ga naar de resource die u hebt gemaakt en selecteer **overzicht**. U ziet een melding over dat uw bestelling wordt verwerkt.

    ![Selecteer Overzicht](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. Nadat de bestelling is verwerkt en het apparaat onderweg is, wordt het **Overzicht** bijgewerkt. Accepteer de standaardwaarde voor **Azure Key Vault-naam** of voer een nieuwe naam in. Selecteer **Activeringssleutel genereren**. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.

    ![Activeringssleutel ophalen](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> * Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over onderwerpen met betrekking tot Azure Stack Edge Pro, zoals:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De activeringssleutel ophalen

Ga naar de volgende zelfstudie om te lezen hoe u Azure Stack Edge Pro installeert.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro installeren](./azure-stack-edge-deploy-install.md)