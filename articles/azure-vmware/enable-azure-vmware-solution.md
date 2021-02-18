---
title: Host-quota aanvragen en Azure VMware-oplossing inschakelen
description: Meer informatie over het aanvragen van quotum/capaciteit van de host en het inschakelen van de Azure VMware Solution resource provider. U kunt ook meer hosts aanvragen in een bestaande privécloud van Azure VMware-oplossingen.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653160"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Host-quota aanvragen en Azure VMware-oplossing inschakelen

In deze procedure leert u hoe u het quotum/de capaciteit van de host kunt aanvragen en de [Azure VMware Solution](introduction.md) resource provider kunt inschakelen, waardoor de service wordt ingeschakeld. Voordat u de Azure VMware-oplossing kunt inschakelen, moet u een ondersteunings ticket indienen waaraan uw hosts zijn toegewezen. Als u een bestaande privécloud van Azure VMware-oplossing hebt en meer hosts wilt toewijzen, volgt u hetzelfde proces.

>[!IMPORTANT]
>Het kan enkele dagen duren voordat de hosts zijn toegewezen, afhankelijk van het aangevraagde aantal.  Vraag wat er nodig is voor het inrichten, zodat u niet zo vaak een quotum verhoging hoeft aan te vragen.


Het algehele proces is eenvoudig en bevat twee stappen:
- Vraag extra quotum/capaciteit van de host aan voor [EA-klanten](#request-host-quota-for-ea-customers) of [CSP-klanten](#request-host-quota-for-csp-customers) 
- De resource provider micro soft. AVS inschakelen

## <a name="eligibility-criteria"></a>Criteria om in aanmerking te komen

U hebt een Azure-account in een Azure-abonnement nodig. Het Azure-abonnement moet volgen op een van de volgende criteria:

- Een abonnement onder een [Azure Enterprise overeenkomst (EA)](../cost-management-billing/manage/ea-portal-agreements.md) met micro soft.
- Een door de Cloud Solution Provider (CSP) beheerd abonnement onder een bestaande CSP Azure biedt een contract of een Azure-abonnement.

## <a name="request-host-quota-for-ea-customers"></a>Host-quota aanvragen voor EA-klanten

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
   - Aantal hosts
   - Eventuele andere gegevens

   >[!NOTE]
   >De Azure VMware-oplossing raadt ten minste drie hosts aan om uw privécloud uit te breiden en voor redundantie-N + 1-hosts. 

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.


## <a name="request-host-quota-for-csp-customers"></a>Host-quota aanvragen voor CSP-klanten 

Csp's moeten [micro soft Partner Center](https://partner.microsoft.com) gebruiken om de Azure VMware-oplossing voor hun klanten in te scha kelen. In dit artikel wordt [CSP Azure-abonnement](/partner-center/azure-plan-lp) gebruikt als voor beeld om de inkoop procedure voor partners te illustreren.

Open de Azure Portal met behulp van de administrate-procedure ( **beheerder namens** ) van het partner centrum.

>[!IMPORTANT] 
>Azure VMware Solution service biedt geen multitenancy vereist. Hosting partners die dit vereisen, worden niet ondersteund. 

1. Configureer het CSP Azure-abonnement:

   1. Selecteer in **partner centrum** **CSP** om toegang te krijgen tot het gebied **klanten** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Het gebied klanten van micro soft-partner centrum" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Selecteer uw klant en selecteer vervolgens **producten toevoegen**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partnercentrum" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Selecteer **Azure-abonnement** en selecteer vervolgens **toevoegen aan winkel wagen**. 
   
   1. Bekijk en voltooi de algemene instellingen van het abonnement voor Azure plan voor uw klant. Zie de [documentatie van micro soft Partner Center](/partner-center/azure-plan-manage)voor meer informatie.

1. Nadat u het Azure-abonnement hebt geconfigureerd en u beschikt over de benodigde [Azure RBAC-machtigingen](/partner-center/azure-plan-manage) voor het abonnement, vraagt u het quotum voor uw abonnement op Azure abonnement. 

   1. Toegang tot Azure Portal van [micro soft-partner centrum](https://partner.microsoft.com) met de **beheerder namens** (administrate).
   
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
      - Aantal hosts
      - Eventuele andere gegevens
      - Is bedoeld om meerdere klanten te hosten?
   
      >[!NOTE]
      >De Azure VMware-oplossing raadt ten minste drie hosts aan om uw privécloud uit te breiden en voor redundantie-N + 1-hosts. 
   
   1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.

## <a name="register-the-microsoftavs-resource-provider"></a>De resource provider van **micro soft. AVS** registreren

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Volgende stappen

Na het inschakelen van de resource en de juiste netwerken op locatie, kunt u [een privécloud maken](tutorial-create-private-cloud.md).
