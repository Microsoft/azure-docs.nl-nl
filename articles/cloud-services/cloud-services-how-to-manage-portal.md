---
title: Algemene beheer taken voor Cloud Services | Microsoft Docs
description: Meer informatie over het beheren van Cloud Services in de Azure Portal. In deze voor beelden wordt gebruikgemaakt van de Azure Portal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 306da8dae31c2bbdb487e7128e3a2e24424c239d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743386"
---
# <a name="manage-cloud-services-classic-in-the-azure-portal"></a>Cloud Services (klassiek) beheren in de Azure Portal

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In het gedeelte **Cloud Services** van de Azure Portal kunt u het volgende doen:

* Een service functie of implementatie bijwerken.
* Bevorder een gefaseerde implementatie naar productie.
* Koppel resources aan uw Cloud service, zodat u de resource afhankelijkheden kunt zien en de resources in een geheel wilt schalen.
* Een Cloud service of een implementatie verwijderen.

Zie voor meer informatie over het schalen van uw Cloud service [automatisch schalen configureren voor een Cloud service in de portal](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Een Cloud service-rol of-implementatie bijwerken
Als u de toepassings code voor uw Cloud service moet bijwerken, gebruikt u **Update** op de Blade Cloud service. U kunt één rol of alle rollen bijwerken. Als u wilt bijwerken, kunt u een nieuw service pakket of service configuratie bestand uploaden.

1. Selecteer in de [Azure Portal][Azure portal]de Cloud service die u wilt bijwerken. Met deze stap wordt de Blade Cloud service-exemplaar geopend.

2. Selecteer **bijwerken** op de Blade.

    ![Knop bijwerken](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Werk de implementatie bij met een nieuw service pakket bestand (. cspkg) en een service configuratie bestand (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. U kunt desgewenst het opslag account en het implementatie label bijwerken.

5. Als rollen slechts één rolinstantie hebben, schakelt u het selectie vakje **implementeren zelfs als een of meer rollen één exemplaar bevatten** in om de upgrade te kunnen door gaan.

    Azure kan slechts 99,95 procent Beschik baarheid garanderen tijdens een update van de Cloud service als elke rol ten minste twee rolinstanties (virtuele machines) heeft. Met twee rolinstanties verwerkt één virtuele machine client aanvragen terwijl de andere wordt bijgewerkt.

6. Schakel het selectie vakje **implementatie starten** in om de update toe te passen nadat het uploaden van het pakket is voltooid.

7. Selecteer **OK** om te beginnen met het bijwerken van de service.

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Implementaties wisselen voor het promo veren van een gefaseerde implementatie naar productie
Wanneer u besluit een nieuwe release van een Cloud service te implementeren, faseren en test u uw nieuwe release in uw Cloud service-faserings omgeving. Gebruik **swap** om de url's te wijzigen waarmee de twee implementaties worden geadresseerd en promoot een nieuwe release naar productie.

U kunt implementaties van de **Cloud Services** pagina of het dash board wisselen.

1. Selecteer in de [Azure Portal][Azure portal]de Cloud service die u wilt bijwerken. Met deze stap wordt de Blade Cloud service-exemplaar geopend.

2. Selecteer **wisselen** op de Blade.

    ![Knop Cloud Services wisselen](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. De volgende bevestigings prompt wordt geopend:

    ![Cloud Services wisselen](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Nadat u de implementatie gegevens hebt gecontroleerd, selecteert u **OK** om de implementaties te wisselen.

    De implementatie swap vindt snel plaats omdat het enige wat het enige is dat wijzigingen zijn de virtuele IP-adressen (Vip's) voor de implementaties.

    Als u de reken kosten wilt besparen, kunt u de faserings implementatie verwijderen nadat u hebt gecontroleerd of uw productie-implementatie werkt zoals verwacht.

### <a name="common-questions-about-swapping-deployments"></a>Veelgestelde vragen over het wisselen van implementaties

**Wat zijn de vereisten voor het wisselen van implementaties?**

Er zijn twee belang rijke vereisten voor een geslaagde implementatie swap:

- Als u een statisch IP-adres voor uw productie sleuf wilt gebruiken, moet u deze ook reserveren voor uw staging-sleuf. Anders mislukt de wissel.

- Alle exemplaren van uw rollen moeten worden uitgevoerd voordat u de swap kunt uitvoeren. U kunt de status van uw instanties controleren op de Blade **overzicht** van de Azure Portal. U kunt ook de opdracht [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole?view=azuresmps-3.7.0&preserve-view=true) gebruiken in Windows Power shell.

Houd er rekening mee dat de bewerkingen voor het bijwerken van gast besturingssystemen en service retouchies ook kunnen leiden tot het mislukken van implementaties. Zie [problemen met Cloud service-implementatie oplossen](cloud-services-troubleshoot-deployment-problems.md)voor meer informatie.

**Maakt een wissel uitval tijd voor mijn toepassing plaats? Hoe kan ik dit afhandelen?**

Zoals beschreven in de vorige sectie, is een implementatie swap doorgaans snel omdat het een configuratie wijziging in de Azure-load balancer is. In sommige gevallen kan het 10 of meer seconden duren en leiden tot tijdelijke verbindings fouten. Als u de gevolgen voor uw klanten wilt beperken, kunt u overwegen de implementatie van de [client opnieuw](/azure/architecture/best-practices/transient-faults)te implementeren.

## <a name="delete-deployments-and-a-cloud-service"></a>Implementaties en een Cloud service verwijderen
Voordat u een Cloud service kunt verwijderen, moet u elke bestaande implementatie verwijderen.

Als u de reken kosten wilt besparen, kunt u de faserings implementatie verwijderen nadat u hebt gecontroleerd of uw productie-implementatie werkt zoals verwacht. U wordt gefactureerd voor de reken kosten voor geïmplementeerde rolinstanties die zijn gestopt.

Gebruik de volgende procedure om een implementatie of uw Cloud service te verwijderen.

1. Selecteer in de [Azure Portal][Azure portal]de Cloud service die u wilt verwijderen. Met deze stap wordt de Blade Cloud service-exemplaar geopend.

2. Selecteer **verwijderen** op de Blade.

    ![Knop Cloud Services verwijderen](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Als u de volledige Cloud service wilt verwijderen, schakelt u het selectie vakje **Cloud service en de implementaties** in. U kunt ook het selectie vakje **productie-implementatie** of **faserings implementatie** kiezen.

    ![Cloud Services verwijderen](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Selecteer onder **verwijderen** .

5. Als u de Cloud service wilt verwijderen, selecteert u **Cloud service verwijderen**. Selecteer vervolgens bij de bevestigings prompt **Ja**.

> [!NOTE]
> Wanneer een Cloud service wordt verwijderd en uitgebreide bewaking is geconfigureerd, moet u de gegevens hand matig verwijderen uit uw opslag account. Zie [Inleiding tot Cloud service monitoring](cloud-services-how-to-monitor.md)voor meer informatie over waar u de tabellen met metrische gegevens kunt vinden.


## <a name="find-more-information-about-failed-deployments"></a>Meer informatie over mislukte implementaties
De Blade **overzicht** bevat bovenaan een status balk. Wanneer u de balk selecteert, wordt er een nieuwe blade geopend en wordt er informatie over de fout weer gegeven. Als de implementatie geen fouten bevat, is de Blade informatie leeg.

![Overzicht van Cloud Services](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Volgende stappen
* [Algemene configuratie van uw Cloud service](cloud-services-how-to-configure-portal.md).
* Meer informatie over het [implementeren van een Cloud service](cloud-services-how-to-create-deploy-portal.md).
* Een [aangepaste domein naam](cloud-services-custom-domain-name-portal.md)configureren.
* [TLS/SSL-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren.