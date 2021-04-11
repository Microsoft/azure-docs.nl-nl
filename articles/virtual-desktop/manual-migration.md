---
title: Hand matig migreren vanuit virtueel bureau blad van Windows (klassiek)-Azure
description: Hand matig migreren van Windows virtueel bureau blad (klassiek) naar virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 350b7306b6d39b42fbfebcc6566762df305b11b4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445972"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Hand matig migreren van virtueel bureau blad van Windows (klassiek)

Met het virtuele bureau blad van Windows (klassiek) wordt de service omgeving gemaakt met Power shell-cmdlets, REST Api's en service-objecten. Een ' object ' in een Windows Virtual Desktop-service omgeving is een ding van het maken van een virtueel bureau blad van Windows. Service objecten omvatten tenants, hostgroepen, toepassings groepen en sessie-hosts.

Windows virtueel bureau blad (klassiek) is echter niet geïntegreerd met Azure. Zonder Azure-integratie worden objecten die u maakt, niet automatisch beheerd door de Azure Portal omdat ze niet zijn verbonden met uw Azure-abonnement.

De recente grote update van Windows virtueel bureau blad markeert een verschuiving in de service naar volledige integratie van Azure. Objecten die u in Windows virtueel bureau blad maakt, worden automatisch beheerd door de Azure Portal.

In dit artikel wordt uitgelegd waarom u zou kunnen overwegen om te migreren naar de nieuwste versie van virtueel bureau blad van Windows. Daarna laten we u zien hoe u hand matig migreert van Windows virtueel bureau blad (klassiek) naar de nieuwste update van Windows virtueel bureau blad.

## <a name="why-migrate"></a>Voordelen van migratie

Grote updates kunnen onhandig zijn, met name wat u hand matig moet doen. Er zijn echter enkele redenen waarom u niet automatisch kunt migreren:

- Bestaande service objecten die zijn gemaakt met de klassieke release, hebben geen representatie in Azure. Hun bereik is niet langer dan de virtueel-bureaublad service van Windows.
- Met de meest recente update is de toepassings-ID van de service gewijzigd om toestemming voor apps te verwijderen zoals voor het virtuele Windows-bureau blad (klassiek). U kunt geen nieuwe Azure-objecten met het virtuele Windows-bureau blad maken, tenzij ze zijn geverifieerd met de nieuwe toepassings-ID.

Ondanks de gedoe is het migreren van de klassieke versie nog steeds belang rijk. U kunt na het migreren het volgende doen:

- Virtueel bureau blad van Windows beheren via de Azure Portal.
- Wijs Azure Active Directory (AD)-gebruikers groepen toe aan toepassings groepen.
- Gebruik de verbeterde functie Log Analytics om problemen met uw implementatie op te lossen.
- Gebruik Azure-systeem eigen op rollen gebaseerd toegangs beheer (Azure RBAC) voor het beheren van beheerders toegang.

## <a name="when-should-i-migrate"></a>Wanneer moet ik migreren?

Wanneer u wordt gevraagd de migratie uit te voeren, moet u ook rekening houden met de huidige en toekomstige situatie van uw implementatie.

Er zijn enkele scenario's waarin u het beste hand matig kunt migreren:

- U hebt een test voor een hostgroep met een klein aantal gebruikers.
- U hebt een configuratie voor een pool van een productie-host met een klein aantal gebruikers, maar u wilt uiteindelijk Maxi maal honderden gebruikers plannen.
- U hebt een eenvoudige installatie die eenvoudig kan worden gerepliceerd. Als uw Vm's bijvoorbeeld een galerie afbeelding gebruiken.

> [!IMPORTANT]
> Als u een geavanceerde configuratie gebruikt die lange tijd duurde om te stabiliseren of veel gebruikers heeft, wordt het niet aanbevolen om hand matig te migreren.

## <a name="prepare-for-migration"></a>Voorbereiden op migratie

Voordat u aan de slag gaat, moet u controleren of uw omgeving klaar is voor migratie.

Dit is wat u nodig hebt om het migratie proces te starten:

- Een Azure-abonnement waar u nieuwe Azure-service objecten gaat maken.
- Zorg ervoor dat u bent toegewezen aan de volgende rollen:
    
    - Inzender
    - Beheerder van gebruikerstoegang
    
    Met de rol Inzender kunt u Azure-objecten in uw abonnement maken en met de rol beheerder van gebruikers toegang kunt u gebruikers toewijzen aan toepassings groepen.

## <a name="how-to-migrate-manually"></a>Hand matig migreren

Nu u voor het migratie proces hebt voor bereid, is het tijd om daad werkelijk te migreren.

Hand matig migreren van virtueel bureau blad van Windows (klassiek) naar virtueel bureau blad van Windows:

1. Volg de instructies in [een hostgroep maken met de Azure Portal](create-host-pools-azure-marketplace.md) om alle objecten op hoog niveau te maken met de Azure Portal.
2. Als u de virtuele machines die u al gebruikt, wilt overzetten, volgt u de instructies in de [virtuele machines registreren bij de hostgroep voor virtuele Windows-bureau blad](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) om ze hand matig te registreren bij de nieuwe hostgroep die u in stap 1 hebt gemaakt.
3. Nieuwe RemoteApp-app-groepen maken.
4. Publiceer gebruikers of gebruikers groepen naar de nieuwe bureau blad-en RemoteApp-app-groepen.
5. Werk het beleid voor voorwaardelijke toegang bij zodat de nieuwe objecten worden toegestaan door de instructies te volgen in [multi-factor Authentication instellen](set-up-mfa.md).

Om uitval tijd te voor komen, moet u eerst uw bestaande sessie-hosts registreren bij de Azure Resource Manager-geïntegreerde hostgroepen in kleine groepen tegelijk. Daarna brengen uw gebruikers langzaam over naar de nieuwe, door Azure Resource Manager geïntegreerde app-groepen.

## <a name="next-steps"></a>Volgende stappen

Zodra u hebt gemigreerd, weet u hoe Windows virtueel bureau blad werkt door [onze zelf studies](create-host-pools-azure-marketplace.md)te controleren. Meer informatie over geavanceerde beheer mogelijkheden bij [het uitbreiden van een bestaande hostgroep en het](expand-existing-host-pool.md) [aanpassen van RDP-eigenschappen](customize-rdp-properties.md).

Zie [Windows Virtual Desktop Environment](environment-setup.md)voor meer informatie over service objecten.
