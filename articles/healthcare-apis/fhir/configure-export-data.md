---
title: Instellingen voor exporteren in azure API configureren voor FHIR
description: In dit artikel wordt beschreven hoe u instellingen voor exporteren configureert in azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046976"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>De instelling exporteren configureren en het opslag account instellen

De Azure-API voor FHIR ondersteunt $export opdracht waarmee u de gegevens uit de Azure API voor het FHIR-account kunt exporteren naar een opslag account.

Er zijn drie stappen betrokken bij het configureren van exporteren in azure API voor FHIR:

1. Schakel beheerde identiteit in op de Azure-API voor de FHIR-service.
2. Het maken van een Azure Storage-account (als dit nog niet is gedaan) en het toewijzen van machtigingen aan de Azure API voor FHIR aan het opslag account.
3. Het opslag account in de Azure-API voor FHIR als export-opslag account te selecteren.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Beheerde identiteit inschakelen op de Azure-API voor FHIR

De eerste stap bij het configureren van de Azure-API voor FHIR voor export is het inschakelen van System Wide Managed Identity voor de service. Zie [informatie over beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in Azure.

Als u dit wilt doen, gaat u naar de Azure API for FHIR-service en selecteert u **identiteit**. Als de status wordt gewijzigd in **aan** , wordt beheerde identiteit ingeschakeld in azure API voor de FHIR-service.

![Beheerde identiteit inschakelen](media/export-data/fhir-mi-enabled.png)

U kunt nu door gaan met de volgende stap door een opslag account te maken en toestemming te geven voor onze service.

## <a name="adding-permission-to-storage-account"></a>Machtiging voor het opslag account toevoegen

De volgende stap bij het exporteren is het toewijzen van machtigingen voor de Azure API voor de FHIR-service om naar het opslag account te schrijven.

Nadat u een opslag account hebt gemaakt, gaat u naar **Access Control (IAM)** in het opslag account en selecteert u **roltoewijzing toevoegen**.

![Roltoewijzing exporteren](media/export-data/fhir-export-role-assignment.png)

Hier gaat u de rol **opslag BLOB-gegevens bijdrager** toevoegen aan de naam van de service en selecteert u vervolgens **Opslaan**.

![Rol toevoegen](media/export-data/fhir-export-role-add.png)

Nu bent u klaar om het opslag account te selecteren in de Azure-API voor FHIR als een standaard opslag account voor $export.

## <a name="selecting-the-storage-account-for-export"></a>Het opslag account voor $export selecteren

De laatste stap is het toewijzen van het Azure-opslag account dat door Azure API voor FHIR wordt gebruikt om de gegevens te exporteren naar. Als u dit wilt doen, gaat u naar **integratie** in azure API for FHIR-service en selecteert u het opslag account.

![FHIR-export opslag](media/export-data/fhir-export-storage.png)

Nadat u deze laatste stap hebt voltooid, kunt u de gegevens nu exporteren met behulp van $export opdracht.

> [!Note]
> Alleen opslag accounts in hetzelfde abonnement als die voor Azure API voor FHIR mogen worden geregistreerd als bestemming voor $export bewerkingen.

Zie voor meer informatie over het configureren van database instellingen, Toegangs beheer, het inschakelen van diagnostische logboek registratie en het gebruik van aangepaste headers om gegevens toe te voegen aan audit logboeken:

>[!div class="nextstepaction"]
>[Aanvullende instellingen](azure-api-for-fhir-additional-settings.md)
