---
title: Lockbox voor Azure Data Box configureren
description: Meer informatie over het gebruik van Klanten-lockbox met Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92124908"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Klanten-lockbox gebruiken voor Azure Data Box (preview-versie)

Azure Data Box wordt gebruikt om klant gegevens over te dragen van en naar Azure. Er zijn exemplaren waarvan Microsoft Ondersteuning mogelijk toegang moet hebben tot klant gegevens tijdens een Ondersteuningsaanvraag. U kunt Klanten-lockbox als een interface gebruiken om deze aanvragen voor gegevens toegang te controleren en goed te keuren of af te wijzen. 

In dit artikel wordt beschreven hoe Klanten-lockbox aanvragen worden gestart en bijgehouden voor Data Box importeren, evenals export orders. Het artikel is van toepassing op zowel Azure Data Box apparaten als Azure Data Box Heavy apparaten. 

## <a name="devops-workflow-for-data-access"></a>DevOps-werk stroom voor gegevens toegang

Het IT-team van ondersteuning en Data Box van micro soft heeft doorgaans geen toegang tot klant gegevens. Ze proberen problemen op te lossen met behulp van standaard Programma's en telemetrie. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Als de problemen niet kunnen worden opgelost en vereisen dat Microsoft Ondersteuning om gegevens te onderzoeken of te herstellen, vraagt hij verhoogde toegang via JIT-Portal (just-in-time). De JIP-Portal valideert het machtigings niveau, biedt multi-factor Authentication en omvat ook een goed keuring van de interne micro soft-goed keurders. De goed keurder kan bijvoorbeeld DevOps Manager zijn. 

Nadat uw aanvraag voor verhoogde toegang is goedgekeurd via de JIT-Portal, moet micro soft ook uw expliciete toestemming voor toegang tot de gegevens hebben ingeschakeld. De toegang wordt aangevraagd en getraceerd via de Klanten-lockbox-service in de portal. 

Als u lockbox niet hebt ingeschakeld, is uw toestemming niet nodig om toegang te krijgen tot de gegevens.


## <a name="prerequisites-for-access-request"></a>Vereisten voor de toegangs aanvraag

Zorg voordat u begint voor het volgende:

1. U hebt een Azure Data Box volgorde gemaakt volgens de instructies in:
    1. [Zelf studie: bestel Azure data Box](data-box-deploy-ordered.md) voor import orders.
    1. [Zelf studie: bestel Azure data Box](data-box-deploy-export-ordered.md) voor export orders.

2. U hebt Klanten-lockbox voor Data Box geconfigureerd. Dit is een opt-in-service. 

    1. Klanten-lockbox is momenteel beschikbaar als preview-versie voor Data Box-Service. Als u Klanten-lockbox voor Data Box voor uw organisatie wilt inschakelen, meldt u zich aan voor [klanten-lockbox voor de open bare preview van Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. Klanten-lockbox is automatisch beschikbaar voor alle klanten die een ondersteunings abonnement voor Azure hebben met een mini maal ontwikkel niveau. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Er is al een service aanvraag of ondersteunings ticket geopend voor dit probleem. Zie voor meer informatie over de ondersteunings ticket [een service aanvraag indienen voor data Box](data-box-disk-contact-microsoft-support.md).


## <a name="track-approve-request-via-lockbox"></a>Bijhouden, aanvraag goed keuren via lockbox

Voer de volgende stappen uit om een aanvraag voor toegang tot klant gegevens bij te houden en goed te keuren:

1. Micro soft detecteert dat er een probleem is tijdens het uploaden of downloaden van de gegevens in azure Data Center. De Data Box order wordt bijvoorbeeld gestopt tijdens de fase voor het **kopiëren van gegevens** . 

    De ondersteunings technicus maakt verbinding met Data Box via een ondersteunings sessie en probeert het probleem op te lossen met behulp van standaard hulpprogramma's en telemetrie. Als de Data Box schijven zijn vergrendeld en shares niet toegankelijk zijn, maakt de ondersteunings technicus een lockbox-aanvraag. 
 
2. Wanneer de aanvraag wordt gemaakt, wordt de melding doorgaans verzonden naar de abonnements beheerder, maar u kunt ook een groep voor meldingen configureren. 

3. U kunt de lockbox-aanvraag bekijken in de Azure Portal voor uw goed keuring. 

    ![Aanvraag in Azure Portal](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Als u de lockbox-aanvraag wilt goed keuren vanuit de portal, kunt u **goed keuren** selecteren.

    ![Aanvraag goed keuren](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    Zodra de aanvraag is goedgekeurd, zijn de schijven ontgrendeld en zijn shares toegankelijk in de ondersteunings sessie.

4. De ondersteunings technicus lost het upload probleem op en schakelt vervolgens de ondersteunings sessie uit.

Nadat het probleem is opgelost, wordt de voortgang van de gegevens kopie taak voltooid.


## <a name="next-steps"></a>Volgende stappen

- [Klanten-lockbox voor Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->