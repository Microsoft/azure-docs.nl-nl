---
title: Vereisten om programmatisch toegang te krijgen tot Analytics-gegevens
description: Meer informatie over de vereisten die u moet ontmoeten voordat u programmatisch toegang kunt krijgen tot gegevens van Analytics-commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: b61608c0cb53ab808c5d3d789ec5ddc318c6923d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106799"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>Vereisten om programmatisch toegang te krijgen tot Analytics-gegevens

Voordat u programmatisch toegang kunt krijgen tot gegevens van de commerciële Marketplace, moet u voldoen aan de volgende vereisten.

## <a name="commercial-marketplace-enrollment"></a>Inschrijving voor commerciële Marketplace

Als u via een programma toegang wilt krijgen tot commerciële Marketplace Analytics-gegevens, moet u worden inge schreven in het Commercial Marketplace-programma en beschikken over een partner centrum-account. Zie [een commercieel Marketplace-account maken in Partner Center](create-account.md)voor meer informatie.

## <a name="create-azure-active-directory-application"></a>Azure Active Directory-toepassing maken

Reguliere gebruikers referenties kunnen niet worden gebruikt voor toegang via een commerciële Marketplace-analyse gegevens. Er moet een Azure Active Directory (Azure AD)-toepassing worden gemaakt, samen met een geheim voor toegang tot de analyse-Api's. Voor meer informatie over het maken van een Azure AD-toepassing en-geheim raadpleegt u [Quick Start: een toepassing registreren bij het micro soft Identity-platform](../active-directory/develop/quickstart-register-app.md).

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>De Azure AD-toepassing koppelen aan de Partner Center-Tenant

De Azure AD-toepassing die u hebt gemaakt in Azure Portal moet worden gekoppeld aan uw partner centrum-account. De stappen zijn als volgt:

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard).
1. Selecteer in de rechter bovenhoek het tandwiel pictogram en selecteer vervolgens **account instellingen**.
1. Selecteer in het menu **account instellingen** de optie **gebruikers beheer**.
1. Selecteer **Azure AD-toepassingen** en selecteer **+ Azure AD-toepassing maken**.
1. Selecteer de Azure AD-toepassing die u hebt gemaakt op Azure Portal en selecteer **volgende**.
1. Selecteer het selectie vakje **Manager (Windows)** en selecteer vervolgens **toevoegen**.

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="Illustreert de pagina Azure AD-toepassing maken met de selectie vakjes voor het selecteren van rollen.":::

## <a name="generate-an-azure-ad-token"></a>Een Azure AD-token genereren

U moet een Azure AD-token genereren met behulp van de toepassings-ID (client). Met deze ID kunt u uw client toepassing op unieke wijze identificeren in het micro soft Identity-platform en het client geheim van de vorige stap. Zie [service-to-service-aanroepen met client referenties (gedeeld geheim of certificaat)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)voor de stappen voor het genereren van een Azure AD-token.

> [!NOTE]
> Het token is één uur geldig.

## <a name="next-steps"></a>Volgende stappen

- [Paradigma voor toegang tot software](analytics-programmatic-access.md)