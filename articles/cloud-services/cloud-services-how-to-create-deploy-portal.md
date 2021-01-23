---
title: Een Cloud service maken en implementeren (klassiek) | Microsoft Docs
description: Meer informatie over hoe u de methode voor snel maken gebruikt om een Cloud service te maken en uploads te gebruiken om een Cloud service-pakket te uploaden en te implementeren in Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 879b86714adf50b5a4da4398389405063ac046dc
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743403"
---
# <a name="how-to-create-and-deploy-an-azure-cloud-service-classic"></a>Een Azure-Cloud service maken en implementeren (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

De Azure Portal biedt twee manieren om een Cloud service te maken en te implementeren: *snel maken* en *aangepast maken*.

In dit artikel wordt uitgelegd hoe u de methode voor snel maken gebruikt om een nieuwe Cloud service te maken en vervolgens **uploaden** kunt gebruiken om een Cloud service-pakket te uploaden en te implementeren in Azure. Wanneer u deze methode gebruikt, maakt de Azure Portal handige koppelingen beschikbaar voor het volt ooien van alle vereisten. Als u klaar bent voor het implementeren van uw Cloud service wanneer u deze maakt, kunt u beide op hetzelfde moment doen met behulp van aangepaste maken.

> [!NOTE]
> Als u van plan bent om uw Cloud service vanuit Azure DevOps te publiceren, gebruikt u snel maken en stelt u vervolgens Azure DevOps Publishing in vanuit de Snelstartgids of het dash board van Azure. Zie [continue levering naar Azure met behulp van Azure DevOps][TFSTutorialForCloudService]of Raadpleeg de Help voor de pagina **snel starten** voor meer informatie.
>
>

## <a name="concepts"></a>Concepten
Er zijn drie onderdelen vereist voor het implementeren van een toepassing als een Cloud service in Azure:

* **Service definitie**  
  Het definitie bestand van de Cloud service (. csdef) definieert het service model, inclusief het aantal rollen.
* **Service configuratie**  
  Het configuratie bestand van de Cloud service (. cscfg) bevat configuratie-instellingen voor de Cloud service en afzonderlijke rollen, met inbegrip van het aantal rolinstanties.
* **Service pakket**  
  Het service pakket (. cspkg) bevat de toepassings code en configuraties en het service definitie bestand.

U vindt hier meer informatie over hoe u [hier](cloud-services-model-and-package.md)een pakket maakt.

## <a name="prepare-your-app"></a>Uw app voorbereiden
Voordat u een Cloud service kunt implementeren, moet u het Cloud service-pakket (. cspkg) maken op basis van uw toepassings code en een configuratie bestand (. cscfg) voor de Cloud service. De Azure SDK bevat hulpprogram ma's voor het voorbereiden van deze vereiste implementatie bestanden. U kunt de SDK installeren vanaf de pagina [Azure-down loads](https://azure.microsoft.com/downloads/) , in de taal waarin u de code van uw toepassing wilt ontwikkelen.

Voor drie Cloud service functies zijn speciale configuraties vereist voordat u een service pakket exporteert:

* Als u een Cloud service wilt implementeren die gebruikmaakt van Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), voor gegevens versleuteling, moet u [uw toepassing configureren](cloud-services-configure-ssl-certificate-portal.md#modify) voor TLS.
* Als u Extern bureaublad verbindingen met rolinstanties wilt configureren, configureert u [de functies](cloud-services-role-enable-remote-desktop-new-portal.md) voor extern bureaublad.
* Als u uitgebreide bewaking voor uw Cloud service wilt configureren, schakelt u Azure Diagnostics in voor de Cloud service. *Minimale bewaking* (het standaard bewakings niveau) gebruikt prestatie meter items die worden verzameld van de besturings systemen van de host voor rolinstanties (virtuele machines). Met *uitgebreide bewaking* worden extra metrische gegevens verzameld op basis van de prestaties in de rolinstanties om een nauwere analyse van problemen mogelijk te maken die zich voordoen tijdens de verwerking van toepassingen. Zie voor meer informatie over het inschakelen van Azure Diagnostics [Diagnostische gegevens inschakelen in azure](cloud-services-dotnet-diagnostics.md).

Als u een Cloud service wilt maken met implementaties van webrollen of werk rollen, moet u [het service pakket maken](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Voordat u begint
* Als u de Azure SDK nog niet hebt geïnstalleerd, klikt u op **Azure SDK installeren** om de [pagina Azure-down loads](https://azure.microsoft.com/downloads/)te openen. down load vervolgens de SDK voor de taal waarin u uw code wilt ontwikkelen. (U kunt dit later doen.)
* Als een van de rolinstanties een certificaat vereist, maakt u de certificaten. Cloud Services vereisen een. pfx-bestand met een persoonlijke sleutel. U kunt de certificaten uploaden naar Azure wanneer u de Cloud service maakt en implementeert.

## <a name="create-and-deploy"></a>Maken en implementeren
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Klik op **een resource maken > Compute** en schuif omlaag naar en klik vervolgens op **Cloud service**.

    ![Uw Cloud Service1 publiceren](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Voer in het deel venster nieuwe **Cloud service** een waarde in voor de **DNS-naam**.
4. Maak een nieuwe **resource groep** of selecteer een bestaande.
5. Selecteer een **locatie**.
6. Klik op **pakket**. Hiermee opent u het deel venster **een pakket uploaden** . Vul de vereiste velden in. Als een van uw rollen één exemplaar bevat, moet u ervoor zorgen dat **ook wordt geïmplementeerd als een of meer rollen een enkel exemplaar bevatten** .
7. Zorg ervoor dat **implementatie starten** is geselecteerd.
8. Klik op **OK** om het deel venster **een pakket uploaden** te sluiten.
9. Als er geen certificaten zijn om toe te voegen, klikt u op **maken**.

    ![Uw Cloud Service2 publiceren](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Een certificaat uploaden
Als uw implementatie pakket is [geconfigureerd voor het gebruik van certificaten](cloud-services-configure-ssl-certificate-portal.md#modify), kunt u het certificaat nu uploaden.

1. Selecteer **certificaten** en selecteer in het deel venster **certificaten toevoegen** het bestand TLS/SSL Certificate. pfx en geef het **wacht woord** voor het certificaat op.
2. Klik op **certificaat koppelen** en klik vervolgens op **OK** in het deel venster **certificaten toevoegen** .
3. Klik op **maken** in het deel venster **Cloud service** . Wanneer de implementatie de status **gereed** heeft bereikt, kunt u door gaan met de volgende stappen.

    ![Uw Cloud Service3 publiceren](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Controleren of uw implementatie is voltooid
1. Klik op het Cloud service-exemplaar.

    De status geeft aan dat de service wordt **uitgevoerd**.
2. Onder **Essentials** klikt u op de **site-URL** om uw Cloud service in een webbrowser te openen.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: ./cloud-services-choose-me.md

## <a name="next-steps"></a>Volgende stappen
* [Algemene configuratie van uw Cloud service](cloud-services-how-to-configure-portal.md).
* Een [aangepaste domein naam](cloud-services-custom-domain-name-portal.md)configureren.
* [Uw Cloud service beheren](cloud-services-how-to-manage-portal.md).
* [TLS/SSL-certificaten](cloud-services-configure-ssl-certificate-portal.md)configureren.