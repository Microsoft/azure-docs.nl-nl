---
title: Azure Active Directory toegang configureren-Azure Block Chain-Service
description: Azure Block Chain service configureren met Azure Active Directory toegang
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85208770"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Azure Active Directory toegang configureren voor de Azure Block Chain-Service

In dit artikel leert u hoe u toegang verleent en verbinding maakt met Azure Block Chain Service-knoop punten met behulp van de gebruikers-, groeps-of toepassings-Id's van Azure Active Directory (Azure AD).

Azure AD biedt identiteits beheer op basis van de Cloud en stelt u in staat om één identiteit te gebruiken voor een hele onderneming en toegang te krijgen tot toepassingen in Azure. De Azure Block Chain-service is geïntegreerd met Azure AD en biedt voor delen zoals ID-Federatie, eenmalige aanmelding en multi-factor Authentication.

## <a name="prerequisites"></a>Vereisten

* [Een Block Chain-lid maken met behulp van de Azure Portal](create-member.md)

## <a name="grant-access"></a>Toegang verlenen

U kunt zowel het lidniveau als het knooppunt niveau toegang verlenen. Het verlenen van toegangs rechten op het niveau van de gebruiker verleent toegang tot alle knoop punten onder het lid.

### <a name="grant-member-level-access"></a>Toegang op leden niveau verlenen

Toegangs rechten verlenen op het niveau van de gebruiker.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar **toegangs beheer (IAM) > voeg > roltoewijzing** toe.
1. Selecteer de rol **toegang tot Block Chain (preview-node)** en voeg het Azure ad-id-object toe waaraan u toegang wilt verlenen. Het Azure AD-ID-object kan zijn:

    | Azure AD-object | Voorbeeld |
    |-----------------|---------|
    | Azure AD-gebruiker   | `kim@contoso.onmicrosoft.com` |
    | Azure AD-groep  | `sales@contoso.onmicrosoft.com` |
    | Toepassings-id  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Roltoewijzing toevoegen](./media/configure-aad/add-role-assignment.png)

1. Selecteer **Opslaan**.

### <a name="grant-node-level-access"></a>Toegang verlenen op knooppunt niveau

U kunt toegang verlenen op knooppunt niveau door te navigeren naar knoop punt beveiliging en te klikken op de naam van het knoop punt dat u toegang wilt verlenen.

Selecteer de rol toegang tot Block Chain (preview-node) en voeg het Azure AD-ID-object toe waaraan u toegang wilt verlenen.

Zie [service transactie knooppunten van Azure Block Chain configureren](configure-transaction-nodes.md#azure-active-directory-access-control)voor meer informatie.

## <a name="connect-using-azure-blockchain-connector"></a>Verbinding maken met behulp van de Azure Block Chain-connector

Down load of kloon de [Azure Block Chain-connector van github](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Volg de sectie Quick Start in het **Leesmij-bestand** om de connector te bouwen op basis van de bron code.

### <a name="connect-using-an-azure-ad-user-account"></a>Verbinding maken met behulp van een Azure AD-gebruikers account

1. Voer de volgende opdracht uit om te verifiëren met behulp van een Azure AD-gebruikers account. Vervang door \<myAADDirectory\> een Azure AD-domein. Bijvoorbeeld `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD vraagt om referenties.
1. Meld u aan met uw gebruikers naam en wacht woord.
1. Bij een geslaagde verificatie maakt uw lokale proxy verbinding met uw Block Chain-knoop punt. U kunt nu uw Geth-client aan het lokale eind punt koppelen.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Verbinding maken met behulp van een toepassings-ID

Veel toepassingen verifiëren met Azure AD met behulp van een toepassings-ID in plaats van een Azure AD-gebruikers account.

Als u verbinding wilt maken met uw knoop punt met behulp van een toepassings-ID, vervangt u **aadauthcode** door **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parameter | Beschrijving |
|-----------|-------------|
| Tenant-id | Azure AD-domein, bijvoorbeeld `yourdomain.onmicrosoft.com`
| client-id | Client-ID van de geregistreerde toepassing in azure AD
| client-geheim | Client geheim van de geregistreerde toepassing in azure AD

Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken voor toegang tot resources](../../active-directory/develop/howto-create-service-principal-portal.md) voor meer informatie over het registreren van een toepassing in azure AD.

### <a name="connect-a-mobile-device-or-text-browser"></a>Een mobiel apparaat of een tekst browser verbinden

Voor een mobiel apparaat of een tekst-gebaseerde browser waarbij het pop-upvenster Azure AD-verificatie niet mogelijk is, genereert Azure AD een eenmalige wachtwoord code. U kunt de wachtwoord code kopiëren en door gaan met Azure AD-verificatie in een andere omgeving.

Als u de wachtwoord code wilt genereren, vervangt u **aadauthcode** door **aaddevice**. Vervang door \<myAADDirectory\> een Azure AD-domein. Bijvoorbeeld `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Block Chain Service Security](data-security.md)(Engelstalig) voor meer informatie over de beveiliging van gegevens in de Azure Block Chain-service.