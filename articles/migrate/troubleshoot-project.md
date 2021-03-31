---
title: Problemen met Azure Migrate-projecten oplossen
description: Helpt u bij het oplossen van problemen met het maken en beheren van Azure Migrate projecten.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753379"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Problemen met Azure Migrate-projecten oplossen

Dit artikel helpt u bij het oplossen van problemen bij het maken en beheren van [Azure migrate](migrate-services-overview.md) projecten.

## <a name="how-to-add-new-project"></a>Hoe kunt u een nieuw project toevoegen?

U kunt meerdere Azure Migrate-projecten in een abonnement hebben. [Meer informatie over](./create-manage-projects.md) het maken van een project voor de eerste keer of het [toevoegen van extra](create-manage-projects.md#create-additional-projects) projecten.

## <a name="what-azure-permissions-are-needed"></a>Welke Azure-machtigingen zijn er nodig?

U hebt machtigingen voor Inzender of eigenaar nodig in het abonnement om een Azure Migrate project te maken.

## <a name="cant-find-a-project"></a>Kan geen project vinden

Als u een bestaand Azure Migrate project zoekt, is afhankelijk van of u de huidige of oude versie van Azure Migrate gebruikt. [Volgen](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Kan geen geografie vinden

U kunt een Azure Migrate project maken in ondersteunde geografies voor [open bare](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheids Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="what-are-vm-limits"></a>Wat zijn VM-limieten?

U kunt Maxi maal 35.000 VMware-Vm's of Maxi maal 35.000 virtuele Hyper-V-machines in één project evalueren. Een project kan zowel virtuele VMware-machines als virtuele Hyper-V-machines bevatten, tot aan de evaluatie limieten.

## <a name="can-i-upgrade-old-project"></a>Kan ik een upgrade uitvoeren voor een oud project?

Projecten van de vorige versie van Azure Migrate kunnen niet worden bijgewerkt. U moet [een nieuw project maken](./create-manage-projects.md)en er hulp middelen aan toevoegen.

## <a name="cant-create-a-project"></a>Kan geen project maken

Als u een project probeert te maken en er een implementatie fout optreedt:

- Probeer het project opnieuw te maken voor het geval er een tijdelijke fout is opgetreden. Klik in **implementaties** op **opnieuw implementeren** om het opnieuw te proberen.
- Controleer of u machtigingen voor Inzender of eigenaar hebt in het abonnement.
- Als u in een nieuwe geografische regio implementeert, moet u een korte tijd wachten en het opnieuw proberen.
- Als u de fout melding ' aanvragen moeten gebruikers identiteits headers bevatten ' ontvangt, kan dit erop wijzen dat u geen toegang hebt tot de Azure Active Directory (Azure AD)-Tenant van de organisatie. In dat geval:
    - Wanneer u voor de eerste keer aan een Azure AD-Tenant toevoegt, ontvangt u een e-mail uitnodiging om lid te worden van de Tenant.
    - Accepteer de uitnodiging om toe te voegen aan de Tenant.
    - Als u het e-mail bericht niet kunt zien, neemt u contact op met een gebruiker die toegang heeft tot de Tenant en vraagt u [de uitnodiging opnieuw](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users) naar u te verzenden.
    - Open de e-mail uitnodiging en selecteer de koppeling om de uitnodiging te accepteren. Meld u vervolgens af bij de Azure Portal en meld u opnieuw aan. (het vernieuwen van de browser werkt niet.) Vervolgens kunt u beginnen met het maken van het migratie project.

## <a name="how-do-i-delete-a-project"></a>Een project Hoe kan ik verwijderen

[Volg deze instructies](create-manage-projects.md#delete-a-project) voor het verwijderen van een project. Houd er rekening mee dat wanneer u een project verwijdert, het project en de meta gegevens over gedetecteerde computers in het project worden verwijderd.

## <a name="added-tools-dont-show"></a>Toegevoegde hulpprogram ma's worden niet weer gegeven

Controleer of u het juiste project hebt geselecteerd. Klik in de Azure Migrate hub > **servers** of in **data bases** op **wijzigen** naast **project migreren (wijzigen)** in de rechter bovenhoek van het scherm. Kies de juiste abonnement-en project naam > **OK**. De pagina moet worden vernieuwd met de toegevoegde hulp middelen van het geselecteerde project.

## <a name="next-steps"></a>Volgende stappen

Voeg hulpprogram ma's voor [evaluatie](how-to-assess.md) of [migratie](how-to-migrate.md) toe om Azure migrate projecten.