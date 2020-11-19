---
title: Uw Azure VMware-oplossings resource inschakelen
description: Meer informatie over het indienen van een ondersteunings aanvraag om uw Azure VMware-oplossings resource in te scha kelen. U kunt ook meer knoop punten aanvragen in uw bestaande privécloud van Azure VMware-oplossingen.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 8e1b891559cb2d01adc9fdf834ef3c9714fe1233
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888433"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Azure VMware Solution resource inschakelen
Meer informatie over het indienen van een ondersteunings aanvraag om uw [Azure VMware-oplossings](introduction.md) resource in te scha kelen. U kunt ook meer knoop punten aanvragen in uw bestaande privécloud van Azure VMware-oplossingen.

## <a name="eligibility-criteria"></a>Criteria om in aanmerking te komen

U hebt een Azure-account in een Azure-abonnement nodig. Het Azure-abonnement moet voldoen aan een van de volgende criteria:

* Een abonnement onder een [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) met micro soft.
* Een door de Cloud Solution Provider (CSP) beheerd abonnement onder een Azure-abonnement.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Azure VMware-oplossing voor EA-klanten inschakelen
Voordat u uw Azure VMware Solution-resource maakt, moet u een ondersteuningsticket indienen om uw knooppunten te laten toewijzen. Zodra het ondersteuningsteam uw aanvraag heeft ontvangen, duurt het maximaal vijf werkdagen om uw aanvraag te bevestigen en uw knooppunten toe te wijzen. Als u een bestaande privécloud van Azure VMware Solution hebt en u meer knooppunten wilt toewijzen, dan volgt u hetzelfde proces.


1. Maak in uw Azure Portal onder **Help en ondersteuning** een **[nieuwe ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support)** en geef de volgende informatie op voor het ticket:
   - **Type probleem:** Documentatie
   - **Abonnement:** Uw abonnement selecteren
   - **Service:** Alle services > Azure VMware-oplossing
   - **Resource:** Algemene vraag 
   - **Samen vatting:** Capaciteit nodig
   - **Probleem type:** Problemen met capaciteits beheer
   - **Subtype van probleem:** Klant aanvraag voor extra quotum/capaciteit van host

1. Geef in de **Beschrijving** van het ondersteunings ticket op het tabblad **Details** het volgende op:

   - HAALBAARHEIDs-of productie 
   - Naam regio
   - Aantal knooppunten
   - Eventuele andere gegevens

   >[!NOTE]
   >De Azure VMware-oplossing raadt ten minste drie knoop punten aan om uw privécloud te maken en voor redundantie-N + 1-knoop punten. 

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.

   Het duurt Maxi maal vijf werk dagen voor een ondersteunings medewerker om uw aanvraag te bevestigen.

   >[!IMPORTANT] 
   >Als u al een bestaande Azure VMware-oplossing hebt en u extra knoop punten aanvraagt, moeten we vijf werk dagen hebben om de knoop punten toe te wijzen. 

1. Voordat u uw knoop punten kunt inrichten, moet u ervoor zorgen dat u de resource provider **micro soft. AVS** registreert in de Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer manieren om de Azure Synapse-resourceprovider te registeren.

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Azure VMware-oplossing voor CSP-klanten inschakelen 

Csp's moeten [micro soft Partner Center](https://partner.microsoft.com) gebruiken om de Azure VMware-oplossing voor hun klanten in te scha kelen. 

   >[!IMPORTANT] 
   >Azure VMware Solution service biedt geen multi tenant-omgeving en daarom kunnen hosting partners nog niet worden ondersteund. 

1. Selecteer in **partner centrum** **CSP** om toegang te krijgen tot het gebied **klanten** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Het gebied klanten van micro soft-partner centrum" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selecteer uw klant en selecteer vervolgens **producten toevoegen**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partnercentrum" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selecteer **Azure-abonnement** en selecteer vervolgens **toevoegen aan winkel wagen**. 

1. Bekijk en voltooi de algemene instellingen van het abonnement voor Azure plan voor uw klant. Zie de [documentatie van micro soft Partner Center](https://docs.microsoft.com/partner-center/azure-plan-manage)voor meer informatie.

Na het configureren van het Azure-abonnement en de benodigde vSphere RBAC-machtigingen zijn aanwezig als CSP, stelt u micro soft in staat om het quotum voor een Azure-abonnement in te scha kelen. Toegang tot Azure Portal van het partner centrum door gebruik te maken van **beheerder namens** (administrate) procedure.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com).

1. Selecteer **CSP** om toegang te krijgen tot het gebied **klanten** .

1. Vouw klant gegevens uit en selecteer **Microsoft Azure Management Portal**.

1. Maak in Azure Portal onder **Help en ondersteuning** een **[nieuwe ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support)** en geef de volgende informatie op voor het ticket:
   - **Type probleem:** Documentatie
   - **Abonnement:** Uw abonnement selecteren
   - **Service:** Alle services > Azure VMware-oplossing
   - **Resource:** Algemene vraag 
   - **Samen vatting:** Capaciteit nodig
   - **Probleem type:** Problemen met capaciteits beheer
   - **Subtype van probleem:** Klant aanvraag voor extra quotum/capaciteit van host

1. Geef in de **Beschrijving** van het ondersteunings ticket op het tabblad **Details** het volgende op:

   - HAALBAARHEIDs-of productie 
   - Naam regio
   - Aantal knooppunten
   - Eventuele andere gegevens
   - Is bedoeld om meerdere klanten te hosten?

   >[!NOTE]
   >De Azure VMware-oplossing raadt ten minste drie knoop punten aan om uw privécloud te maken en voor redundantie-N + 1-knoop punten. 

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.

   Het duurt Maxi maal vijf werk dagen voor een ondersteunings medewerker om uw aanvraag te bevestigen.

   >[!IMPORTANT] 
   >Als u al een bestaande Azure VMware-oplossing hebt en u extra knoop punten aanvraagt, moeten we vijf werk dagen hebben om de knoop punten toe te wijzen. 

1. Zodra het Azure-abonnement is toegevoegd en u quota hebt ingeschakeld, kan de klant of de beheerder van de partner een Privécloud van Azure VMware-oplossing implementeren via de Azure Portal. Voordat u uw knoop punten kunt inrichten, moet u ervoor zorgen dat u de resource provider **micro soft. AVS** registreert in de Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) voor meer manieren om de Azure Synapse-resourceprovider te registeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw Azure VMware-oplossings resource hebt ingeschakeld en u de juiste netwerken hebt, kunt u [een privécloud maken](tutorial-create-private-cloud.md).