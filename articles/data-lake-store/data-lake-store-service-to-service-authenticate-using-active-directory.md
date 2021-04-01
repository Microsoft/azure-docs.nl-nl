---
title: Service-naar-service-verificatie-Data Lake Storage Gen1-Azure
description: Meer informatie over het uitvoeren van service-to-service-verificatie met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576095"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Service-naar-service verificatie met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory
> [!div class="op_single_selector"]
> * [Verificatie van de eindgebruiker](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Verificatie van service-tot-service](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

Azure Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie. Voordat u een toepassing ontwerpt die met Data Lake Storage Gen1 werkt, moet u bepalen hoe u uw toepassing wilt verifiëren met Azure Active Directory (Azure AD). De twee belangrijkste beschik bare opties zijn:

* Verificatie van de eindgebruiker 
* Service-naar-service-verificatie (dit artikel) 

Beide opties hebben als resultaat dat uw toepassing wordt geleverd met een OAuth 2,0-token dat wordt gekoppeld aan elke aanvraag die wordt gedaan aan Data Lake Storage Gen1.

In dit artikel wordt uitgelegd hoe u een **Azure AD-webtoepassing maakt voor service-naar-service-verificatie**. Zie voor instructies voor de configuratie van de Azure AD-toepassing voor verificatie door eind gebruikers de [verificatie van eind gebruikers met data Lake Storage gen1 met behulp van Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>Stap 1: een Active Directory-webtoepassing maken

Een Azure AD-webtoepassing maken en configureren voor service-naar-service verificatie met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory. Zie [een Azure AD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md)voor instructies.

Controleer bij het volgen van de instructies op de voor gaande koppeling of u **Web-app/API** voor toepassings type selecteert, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Een web-app maken](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Een web-app maken")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>Stap 2: toepassings-ID, verificatie sleutel en Tenant-ID ophalen
Wanneer u zich programmatisch aanmeldt, hebt u de ID voor uw toepassing nodig. Als de toepassing wordt uitgevoerd onder eigen referenties, hebt u ook een verificatie sleutel nodig.

* Zie [toepassings-id en verificatie sleutel ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)voor instructies over het ophalen van de toepassings-id en verificatie sleutel (ook wel het client geheim genoemd) voor uw toepassing.

* Zie [Tenant-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)voor instructies over het ophalen van de Tenant-id.

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>Stap 3: de Azure AD-toepassing toewijzen aan het bestand of de map van het Azure Data Lake Storage Gen1-account


1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open het Data Lake Storage Gen1-account dat u wilt koppelen aan de Azure Active Directory toepassing die u eerder hebt gemaakt.
2. Klik op de Blade Data Lake Storage Gen1 account op **Data Explorer**.
   
    ![Mappen maken in Data Lake Storage Gen1-account](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Mappen maken in Data Lake-account")
3. Klik op de Blade **Data Explorer** op het bestand of de map waarvoor u toegang wilt verlenen tot de Azure AD-toepassing en klik vervolgens op **toegang**. Als u de toegang tot een bestand wilt configureren, klikt u op **toegang** op de Blade **Bestands voorbeeld** .
   
    ![Acl's op Data Lake bestands systeem instellen](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "Acl's op Data Lake bestands systeem instellen")
4. Op de Blade **toegang** worden de standaard toegang en aangepaste toegang weer gegeven die al aan de basis zijn toegewezen. Klik op het pictogram **toevoegen** om acl's op aangepaste niveau toe te voegen.
   
    ![Standaard-en aangepaste toegang weer geven](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Standaard-en aangepaste toegang weer geven")
5. Klik op het pictogram **toevoegen** om de Blade **aangepaste toegang toevoegen** te openen. Klik in deze Blade op **gebruiker of groep selecteren** en zoek in de Blade **gebruiker of groep** de Azure Active Directory toepassing op die u eerder hebt gemaakt. Als u veel groepen wilt doorzoeken, gebruikt u het tekstvak bovenaan om de groeps naam te filteren. Klik op de groep die u wilt toevoegen en klik vervolgens op **selecteren**.
   
    ![Een groep toevoegen](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Een groep toevoegen")
6. Klik op **machtigingen selecteren**, selecteer de machtigingen en geef aan of u de machtigingen wilt toewijzen als standaard-ACL, toegangs-ACL of beide. Klik op **OK**.
   
    ![Scherm opname van de Blade aangepaste toegang toevoegen met de optie machtigingen selecteren en de Blade machtigingen selecteren met de optie OK.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Machtigingen toewijzen aan groep")
   
    Zie [Access Control in data Lake Storage gen1](data-lake-store-access-control.md)voor meer informatie over machtigingen in data Lake Storage gen1 en standaard-acl's.
7. Klik op de Blade **aangepaste toegang toevoegen** op **OK**. De toegevoegde groepen, met de bijbehorende machtigingen, worden weer gegeven op de Blade **toegang** .
   
    ![Scherm afbeelding van de Access-Blade met de zojuist toegevoegde groep, die wordt genoemd in de sectie aangepaste toegang.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Machtigingen toewijzen aan groep")

> [!NOTE]
> Als u van plan bent om uw Azure Active Directory-toepassing te beperken tot een specifieke map, moet u ook dezelfde machtiging voor het **uitvoeren** van Azure Active Directory-toepassing voor de hoofdmap geven om toegang tot het maken van bestanden via de .NET SDK in te scha kelen.

> [!NOTE]
> Als u de Sdk's wilt gebruiken om een Data Lake Storage Gen1-account te maken, moet u de Azure AD-webtoepassing toewijzen als een rol aan de resource groep waarin u het Data Lake Storage Gen1-account maakt.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>Stap 4: het OAuth 2,0-token eindpunt ophalen (alleen voor Java-toepassingen)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en klik op Active Directory in het linkerdeel venster.

2. Klik in het linkerdeel venster op **app-registraties**.

3. Klik boven aan de Blade App-registraties op **eind punten**.

    ![Scherm opname van Active Directory met de optie App-registraties en de optie voor eind punten genoemd.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth-token eindpunt")

4. Kopieer het OAuth 2,0-token-eind punt uit de lijst met eind punten.

    ![Scherm opname van de Blade eind punten met het pictogram O-verificatie punt 0 punt O van het TOKEN EINDPUNT.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth-token eindpunt")   

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Azure AD-webtoepassing gemaakt en de informatie verzameld die u nodig hebt in uw client toepassingen die u hebt geschreven met behulp van .NET SDK, Java, Python, REST API, enzovoort. U kunt nu door gaan met de volgende artikelen over het gebruik van de systeem eigen Azure AD-toepassing om u eerst te verifiëren met Data Lake Storage Gen1 en vervolgens andere bewerkingen uit te voeren in de Store.

* [Service-naar-service verificatie met Data Lake Storage Gen1 met behulp van Java](data-lake-store-service-to-service-authenticate-java.md)
* [Service-naar-service verificatie met Data Lake Storage Gen1 met behulp van .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Service-naar-service verificatie met Data Lake Storage Gen1 met behulp van python](data-lake-store-service-to-service-authenticate-python.md)
* [Service-naar-service verificatie met Data Lake Storage Gen1 met behulp van REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


