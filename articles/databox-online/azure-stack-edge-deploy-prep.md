---
title: Zelfstudie voor het voorbereiden van de Azure-portal, datacenteromgeving om Azure Stack Edge Pro te implementeren | Microsoft Docs
description: De eerste zelfstudie over het implementeren van Azure Stack Edge Pro omvat het voorbereiden van de Azure-portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 07b526d443b5f1b41bc6f811b7cccc0fbc6165ee
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761712"
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

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Zorg dat een ondersteund abonnement gebruikt zoals [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) of [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Abonnementen waar u betaalt per gebruik worden niet ondersteund.

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

  * Minimaal 10 Mbps downloadbandbreedte om ervoor te zorgen dat het apparaat bijgewerkt blijft.
  * Minimaal 20 Mbps toegewezen upload- en downloadbandbreedte voor het overdragen van bestanden.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Azure Stack Edge-resource hebt voor het beheer van uw fysieke apparaat, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

Voer de volgende stappen uit in de Azure-portal om een Azure Stack Edge-resource te maken.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op 

    - Ga naar de Azure-portal op deze URL: [https://portal.azure.com](https://portal.azure.com).
    - Of de Azure Government Portal op deze URL: [https://portal.azure.us](https://portal.azure.us). Ga naar [Verbinding maken met Azure Government met behulp van de portal](../azure-government/documentation-government-get-started-connect-with-portal.md) voor meer informatie.

2. Selecteer **En een resource maken** in het linkerdeelvenster. Zoek en selecteer **Azure Stack Edge / Data Box Gateway**. Selecteer **Maken**.
3. Kies het abonnement dat u wilt gebruiken voor het Azure Stack Edge Pro-apparaat. Selecteer de regio waar u de Azure Stack Edge-resource wilt implementeren. Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Azure Stack Edge-resource beschikbaar is.

    Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. In de regio worden alleen de metagegevens voor apparaatbeheer opgeslagen. De werkelijke gegevens kunnen worden opgeslagen in elk opslagaccount.
    
    Selecteer bij **Maken** bij de optie **Azure Stack Edge Pro**.

    ![Azure Stack Edge-service zoeken](media/azure-stack-edge-deploy-prep/data-box-edge-sku.png)

3. Voer op het tabblad **Basisinstellingen** de volgende **Projectdetails** in of selecteer deze.
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Dit wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure-resourcegroepen](../azure-resource-manager/management/overview.md).     |

4. Voer de volgende **exemplaardetails** in of selecteer deze.

    |Instelling  |Waarde  |
    |---------|---------|
    |Naam   | Een beschrijvende naam om de resource aan te duiden.<br>De naam heeft een waarde van 2 tot 50 tekens, inclusief letters, cijfers en afbreek streepjes.<br> De naam begint en eindigt met een letter of cijfer.        |
    |Regio     |Zie [Azure-producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) voor een lijst met alle regio's waar de Azure Stack Edge-resource beschikbaar is. Als Azure Government wordt gebruikt, zijn alle overheidsregio's beschikbaar, zoals wordt weergegeven in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/).<br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|

    ![Details van het project en exemplaar](media/azure-stack-edge-deploy-prep/data-box-edge-resource.png)

5. Selecteer **Volgende: Verzendadres**.

    - Als u al een apparaat hebt, selecteert u de keuze lijst met invoervak voor **Ik heb een Azure stack edge-apparaat**.
    - Als dit het nieuwe apparaat is dat u bestelt, voert u de naam van de contactpersoon, het bedrijf, het adres voor het verzenden van het apparaat en contactgegevens in.

    ![Verzendadres voor nieuw apparaat](media/azure-stack-edge-deploy-prep/data-box-edge-resource1.png)

6. Selecteer **Volgende: Beoordelen en maken**.

7. Bekijk op het tabblad **Controleren en maken** de **Prijsdetails**, **Gebruiksvoorwaarden** en de details van uw resource. Selecteer de keuzelijst met invoervak voor **Ik heb de privacyvoorwaarden gecontroleerd**.

    ![Details van de resource en de privacyverklaring van Azure Stack Edge controleren](media/azure-stack-edge-deploy-prep/data-box-edge-resource2.png)

8. Selecteer **Maken**.

   Het maken van de resource duurt enkele minuten. Nadat de resource succesvol is gemaakt en geïmplementeerd, wordt u daarvan op de hoogte gebracht. Selecteer **Ga naar resource**.

   ![Ga naar de Azure Stack Edge-resource](media/azure-stack-edge-deploy-prep/data-box-edge-resource3.png)

Nadat de bestelling is geplaatst, controleert Microsoft de bestelling en neemt contact met u op (via e-mail) met verzendgegevens.

![Melding voor beoordeling van de volgorde van de Azure Stack Edge Pro](media/azure-stack-edge-deploy-prep/data-box-edge-resource4.png)


> [!NOTE]
> Als u meerdere bestellingen tegelijk wilt doen of een bestaande bestelling wilt klonen, kunt u de [scripts in Azure Samples](https://github.com/Azure-Samples/azure-stack-edge-order) gebruiken. Zie het Leesmij-bestand voor meer informatie.

## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Azure Stack Edge-resource is geactiveerd, hebt u de activeringssleutel nodig. Deze sleutel wordt gebruikt om uw Azure Stack Edge Pro-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Ga naar de resource die u hebt gemaakt en selecteer **overzicht**. U ziet een melding over dat uw bestelling wordt verwerkt.

    ![Selecteer Overzicht](media/azure-stack-edge-deploy-prep/data-box-edge-select-devicesetup.png)

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