---
title: Verificatie-Data Lake Storage Gen1 met Azure AD
description: Meer informatie over het verifiëren met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6fc09f9145b7a1652b621ed38a8bf9af7c4c82a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106566"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Verificatie met Azure Data Lake Storage Gen1 met behulp van Azure Active Directory

Azure Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie. Voordat u een toepassing ontwerpt die met Data Lake Storage Gen1 werkt, moet u bepalen hoe u uw toepassing wilt verifiëren met Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Verificatieopties

* **Verificatie van de eind gebruiker** : de Azure-referenties van een eind gebruiker worden gebruikt voor verificatie met data Lake Storage gen1. De toepassing die u maakt om te werken met Data Lake Storage Gen1 vraagt om deze gebruikers referenties. Als gevolg hiervan is het verificatie mechanisme *interactief* en wordt de toepassing uitgevoerd in de context van de aangemelde gebruiker. Zie voor meer informatie en instructies de [verificatie van eind gebruikers voor data Lake Storage gen1](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Service-naar-service-verificatie** : gebruik deze optie als u wilt dat een toepassing zichzelf verifieert met data Lake Storage gen1. In dergelijke gevallen maakt u een Azure Active Directory-toepassing (AD) en gebruikt u de sleutel van de Azure AD-toepassing om u te verifiëren met Data Lake Storage Gen1. Als gevolg hiervan is dit verificatie mechanisme *niet-interactief*. Zie [service-to-service-verificatie voor data Lake Storage gen1](data-lake-store-service-to-service-authenticate-using-active-directory.md)voor meer informatie en instructies.

In de volgende tabel ziet u hoe eind gebruikers en service-naar-service-verificatie mechanismen worden ondersteund voor Data Lake Storage Gen1. U leest de tabel als volgt.

* Met het symbool ✔ * wordt aangegeven dat de verificatie optie wordt ondersteund en wordt een koppeling naar een artikel beschreven waarin wordt getoond hoe u de verificatie optie gebruikt. 
* Het ✔-symbool geeft aan dat de verificatie optie wordt ondersteund. 
* De lege cellen duiden aan dat de verificatie optie niet wordt ondersteund.


|Gebruik deze verificatie optie met...                   |.NET         |Java     |PowerShell |Azure CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Eind gebruiker (zonder MFA * *)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(afgeschaft)     |    **[✔ *](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Eind gebruiker (met MFA)                           |    **[✔ *](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔ *](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔ *](data-lake-store-get-started-cli-2.0.md)**      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Service-naar-service (met client sleutel)         |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔ *](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔ *](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔ *](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Service-naar-service (met behulp van client certificaat) |    **[✔ *](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klik op <b>het \* ✔</b> symbool. Het is een koppeling.</i><br>
<i>* * MFA staat voor multi-factor Authentication</i>

Zie  [verificatie scenario's voor Azure Active Directory](../active-directory/develop/authentication-vs-authorization.md) voor meer informatie over het gebruik van Azure Active Directory voor verificatie.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie van de eindgebruiker](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Verificatie van service-tot-service](data-lake-store-service-to-service-authenticate-using-active-directory.md)