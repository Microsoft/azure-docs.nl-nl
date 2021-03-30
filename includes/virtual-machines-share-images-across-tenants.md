---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "73903710"
---
Maar als u installatie kopieën wilt delen buiten uw Azure-Tenant, moet u op schaal een app-registratie maken om het delen te vergemakkelijken.  Het gebruik van een app-registratie kan meer complexe scenario's voor delen mogelijk maken, zoals: 

* Gedeelde installatie kopieën beheren wanneer een bedrijf een andere verkrijgt en de Azure-infra structuur is verdeeld over afzonderlijke tenants. 
* Azure-partners beheren Azure-infra structuur namens hun klanten. Aanpassing van installatie kopieën wordt uitgevoerd binnen de partner van de partners, maar de implementatie van de infra structuur vindt plaats in de Tenant van de klant. 


## <a name="create-the-app-registration"></a>De app-registratie maken

Maak een toepassings registratie die door beide tenants wordt gebruikt om de afbeeldingen galerie-resources te delen.
1. Open de [app-registraties (preview) in de Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Selecteer **nieuwe registratie** in het menu boven aan de pagina.
1. Typ *myGalleryApp* in **naam**.
1. In **ondersteunde account typen** selecteert u **accounts in een organisatorische map en persoonlijke micro soft-accounts**.
1. Typ in de **omleidings-URI** *https://www.microsoft.com* en selecteer vervolgens **registreren**. Nadat de app-registratie is gemaakt, wordt de overzichts pagina geopend.
1. Op de pagina overzicht kopieert u de **toepassings-id** en slaat u deze later op.   
1. Selecteer **certificaten & geheimen** en selecteer vervolgens **Nieuw client geheim**.
1. Typ in **Beschrijving** de *Galerie multi tenant-app-geheim voor gedeelde installatie kopieën*.
1. In **Expires** laat u de standaard waarde van **in één jaar** staan en selecteert u vervolgens **toevoegen**.
1. Kopieer de waarde van het geheim en sla het op een veilige plaats op. U kunt deze niet ophalen nadat u de pagina verlaat.


Geef de app-registratie toestemming om de galerie met gedeelde afbeeldingen te gebruiken.
1. Selecteer in de Azure Portal de galerie met gedeelde afbeeldingen die u wilt delen met een andere Tenant.
1. Selecteer **toegangs beheer (IAM) selecteren** en klik onder roltoewijzing **toevoegen** op *toevoegen*. 
1. Selecteer onder **rol** **lezer**.
1. Onder **toegang toewijzen aan:**, moet u dit als **Azure AD-gebruiker,-groep of-Service-Principal** laten staan.
1. Onder **selecteren** typt u *myGalleryApp* en selecteert u dit wanneer het wordt weer gegeven in de lijst. Selecteer **Opslaan** wanneer u klaar bent.


## <a name="give-tenant-2-access"></a>Toegang tot Tenant 2 verlenen

Tenant 2 toegang verlenen tot de toepassing door een aanmelding aan te vragen met behulp van een browser. Vervang door *\<Tenant2 ID>* de Tenant-id voor de Tenant waarmee u de galerie met installatie kopieën wilt delen. Vervang door *\<Application (client) ID>* de toepassings-id van de app-registratie die u hebt gemaakt. Wanneer u de vervangingen hebt uitgevoerd, plakt u de URL in een browser en volgt u de aanmeldings prompts om u aan te melden bij Tenant 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

In de [Azure Portal](https://portal.azure.com) meldt u zich aan als Tenant 2 en geeft u de app-registratie toegang tot de resource groep waar u de virtuele machine wilt maken.

1. Selecteer de resource groep en selecteer vervolgens **toegangs beheer (IAM)**. Onder roltoewijzing **toevoegen** selecteert u **toevoegen**. 
1. Typ onder **rol** **Inzender**.
1. Onder **toegang toewijzen aan:**, moet u dit als **Azure AD-gebruiker,-groep of-Service-Principal** laten staan.
1. Selecteer bij type *MyGalleryApp* **selecteren** de waarde in de lijst. Selecteer **Opslaan** wanneer u klaar bent.

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.

