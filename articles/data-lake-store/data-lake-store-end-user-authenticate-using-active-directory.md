---
title: Verificatie van de eind gebruiker-Data Lake Storage Gen1 met Azure AD
description: Meer informatie over het bezorgen van de verificatie van eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref
ms.openlocfilehash: a5272f7d580a3f8a68afda9150a1e95c1807eba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103778"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Verificatie door eind gebruikers met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory
> [!div class="op_single_selector"]
> * [Verificatie van de eindgebruiker](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Verificatie van service-tot-service](data-lake-store-service-to-service-authenticate-using-active-directory.md)
>
>

Azure Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie. Voordat u een toepassing ontwerpt die werkt met Data Lake Storage Gen1 of Azure Data Lake Analytics, moet u bepalen hoe u uw toepassing verifieert met Azure Active Directory (Azure AD). De twee belangrijkste beschik bare opties zijn:

* Verificatie door eind gebruikers (dit artikel)
* Service-naar-service-verificatie (Kies deze optie in de vervolg keuzelijst hierboven)

Beide opties hebben als resultaat dat uw toepassing wordt geleverd met een OAuth 2,0-token dat wordt gekoppeld aan elke aanvraag die wordt gedaan aan Data Lake Storage Gen1 of Azure Data Lake Analytics.

In dit artikel wordt uitgelegd hoe u een **systeem eigen Azure AD-toepassing maakt voor verificatie door eind gebruikers**. Zie [service-to-service-verificatie met data Lake Storage gen1 met behulp van Azure Active Directory](./data-lake-store-service-to-service-authenticate-using-active-directory.md)voor instructies over de configuratie van de Azure AD-toepassing voor service-naar-service-verificatie.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* Uw abonnements-ID. U kunt deze ophalen uit de Azure Portal. Het is bijvoorbeeld beschikbaar op de Blade Data Lake Storage Gen1-account.

    ![Abonnements-ID ophalen](./media/data-lake-store-end-user-authenticate-using-active-directory/get-subscription-id.png)

* De naam van uw Azure AD-domein. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. In de onderstaande scherm afbeelding is de domein naam **contoso.onmicrosoft.com** en de GUID tussen haakjes is de Tenant-id.

    ![AAD-domein ophalen](./media/data-lake-store-end-user-authenticate-using-active-directory/get-aad-domain.png)

* De ID van uw Azure-Tenant. Voor instructies over het ophalen van de Tenant-ID raadpleegt u [de Tenant-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="end-user-authentication"></a>Verificatie van de eindgebruiker
Dit verificatie mechanisme is de aanbevolen benadering als u wilt dat een eind gebruiker zich bij uw toepassing aanmeldt via Azure AD. Uw toepassing kan vervolgens toegang krijgen tot Azure-resources met hetzelfde toegangs niveau als de eind gebruiker die zich heeft aangemeld. Uw eind gebruiker moet uw referenties regel matig opgeven om de toegang tot de toepassing te behouden.

Het resultaat van het afmelden van de eind gebruiker is dat uw toepassing een toegangs token en een vernieuwings token krijgt. Het toegangs token wordt gekoppeld aan elke aanvraag van Data Lake Storage Gen1 of Data Lake Analytics en is standaard één uur geldig. Het vernieuwings token kan worden gebruikt om een nieuw toegangs token op te halen en is standaard Maxi maal twee weken geldig. U kunt twee verschillende benaderingen gebruiken voor aanmelding door de eind gebruiker.

### <a name="using-the-oauth-20-pop-up"></a>De OAuth 2,0 pop-up gebruiken
Uw toepassing kan een OAuth 2,0-autorisatie pop-up activeren, waarbij de eind gebruiker de referenties kan invoeren. Deze pop-up werkt ook met het Azure AD-proces voor twee ledige verificatie (twee ledige), indien nodig.

> [!NOTE]
> Deze methode wordt nog niet ondersteund in de Azure AD-verificatie bibliotheek (ADAL) voor python of Java.
>
>

### <a name="directly-passing-in-user-credentials"></a>Rechtstreeks door geven in gebruikers referenties
Uw toepassing kan rechtstreeks gebruikers referenties opgeven voor Azure AD. Deze methode werkt alleen met gebruikers accounts voor organisatie-id's; het is niet compatibel met gebruikers accounts voor persoonlijk/' Live ID ', met inbegrip van de accounts die eindigen op @outlook.com of @live.com . Bovendien is deze methode niet compatibel met gebruikers accounts waarvoor Azure AD Two-Factor Authentication (twee ledige) is vereist.

### <a name="what-do-i-need-for-this-approach"></a>Wat heb ik nodig voor deze aanpak?
* Azure AD-domein naam. Deze vereiste is al opgenomen in de vereisten van dit artikel.
* ID van Azure AD-Tenant. Deze vereiste is al opgenomen in de vereisten van dit artikel.
* **Systeem eigen** Azure AD-toepassing
* Toepassings-ID voor de systeem eigen Azure AD-toepassing
* Omleidings-URI voor de systeem eigen Azure AD-toepassing
* Gedelegeerde machtigingen instellen


## <a name="step-1-create-an-active-directory-native-application"></a>Stap 1: een Active Directory systeem eigen toepassing maken

Maak en configureer een systeem eigen Azure AD-toepassing voor verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van Azure Active Directory. Zie [een Azure AD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md)voor instructies.

Zorg dat u bij het volgen van de instructies in de koppeling **systeem eigen** voor toepassings type selecteert, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Een web-app maken](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-create-native-app.png "Systeem eigen app maken")

## <a name="step-2-get-application-id-and-redirect-uri"></a>Stap 2: toepassings-ID en omleidings-URI ophalen

Zie [de toepassings-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) om de toepassings-id op te halen.

Voer de volgende stappen uit om de omleidings-URI op te halen.

1. Selecteer **Azure Active Directory** in het Azure Portal, klik op **app-registraties** en zoek en klik vervolgens op de systeem eigen Azure AD-toepassing die u hebt gemaakt.

2. Klik op de Blade **instellingen** voor de toepassing op **omleidings-uri's**.

    ![Omleidings-URI ophalen](./media/data-lake-store-end-user-authenticate-using-active-directory/azure-active-directory-redirect-uri.png)

3. Kopieer de waarde die wordt weer gegeven.


## <a name="step-3-set-permissions"></a>Stap 3: machtigingen instellen

1. Selecteer **Azure Active Directory** in het Azure Portal, klik op **app-registraties** en zoek en klik vervolgens op de systeem eigen Azure AD-toepassing die u hebt gemaakt.

2. Klik op de Blade **instellingen** voor de toepassing op **vereiste machtigingen** en klik vervolgens op **toevoegen**.

    ![Scherm afbeelding van de Blade instellingen met de optie U R I omleiden en de Blade omleiden U R I met de daad werkelijke U R.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-1.png)

3. Klik op de Blade **API-toegang toevoegen** op **een API selecteren**, klik op **Azure data Lake** en klik vervolgens op **selecteren**.

    ![Scherm opname van de Blade API-toegang toevoegen met de optie een API selecteren en de Blade een API selecteren met de optie Azure Data Lake en de optie selecteren.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-2.png)

4.  Klik op de Blade **API-toegang toevoegen** op **machtigingen selecteren**, schakel het selectie vakje in als u **volledige toegang tot data Lake Store wilt** geven en klik vervolgens op **selecteren**.

    ![Scherm opname van de Blade API-toegang toevoegen met de optie machtigingen selecteren en de Blade toegang inschakelen met de optie volledige toegang tot de Azure Data Lake-service en de optie selecteren.](./media/data-lake-store-end-user-authenticate-using-active-directory/aad-end-user-auth-set-permission-3.png)

    Klik op **Gereed**.

5. Herhaal de laatste twee stappen om machtigingen voor **Windows Azure Service Management-API** ook toe te kennen.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een systeem eigen Azure AD-toepassing gemaakt en de informatie verzameld die u nodig hebt in uw client toepassingen die u hebt geschreven met behulp van .NET SDK, Java SDK, REST API, enzovoort. U kunt nu door gaan met de volgende artikelen over het gebruik van de Azure AD-webtoepassing om eerst te verifiëren met Data Lake Storage Gen1 en vervolgens andere bewerkingen uit te voeren in de Store.

* [Eind gebruiker-verificatie met Data Lake Storage Gen1 met behulp van Java SDK](data-lake-store-end-user-authenticate-java-sdk.md)
* [Verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
* [Verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van python](data-lake-store-end-user-authenticate-python.md)
* [Verificatie door eind gebruikers met Data Lake Storage Gen1 met behulp van REST API](data-lake-store-end-user-authenticate-rest-api.md)