---
title: Gebruikers gegevens beheren in Azure Active Directory B2C | Microsoft Docs
description: Meer informatie over het verwijderen of exporteren van gebruikers gegevens in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/06/2018
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d9bdf7258296b82e65e03f6b8af8021b9a7be0f0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952468"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Gebruikers gegevens beheren in Azure Active Directory B2C

 In dit artikel wordt beschreven hoe u de gebruikers gegevens in Azure Active Directory B2C (Azure AD B2C) kunt beheren door gebruik te maken van de bewerkingen die worden gegeven door de [Microsoft Graph API](/graph/use-the-api). Het beheren van gebruikers gegevens omvat het verwijderen of exporteren van gegevens uit audit Logboeken.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Gebruikers gegevens verwijderen

Gebruikers gegevens worden opgeslagen in de map Azure AD B2C en in de audit Logboeken. Alle gebruikers controle gegevens worden 7 dagen in Azure AD B2C bewaard. Als u gebruikers gegevens binnen die periode van 7 dagen wilt verwijderen, kunt u de bewerking [een gebruiker verwijderen](/graph/api/user-delete) gebruiken. Er is een Verwijder bewerking vereist voor elk van de Azure AD B2C tenants waarin gegevens zich kunnen bevinden.

Aan elke gebruiker in Azure AD B2C is een object-ID toegewezen. De object-ID biedt een ondubbelzinnige id die u kunt gebruiken om gebruikers gegevens in Azure AD B2C te verwijderen. Afhankelijk van uw architectuur kan de object-ID een handige correlatie-id zijn tussen andere services, zoals financiële, marketing-en data bases voor relatie beheer van klanten.

De meest nauw keurige manier om de object-ID voor een gebruiker op te halen, is deze te verkrijgen als onderdeel van een verificatie traject met Azure AD B2C. Als u een geldige aanvraag voor gegevens van een gebruiker ontvangt door andere methoden te gebruiken, is het mogelijk dat u een offline proces, zoals een zoek opdracht door een ondersteunings agent van Customer Service, nodig hebt om de gebruiker te vinden en de bijbehorende object-ID te noteren.

In het volgende voor beeld ziet u een mogelijke stroom voor het verwijderen van gegevens:

1. De gebruiker meldt zich aan en selecteert **mijn gegevens verwijderen**.
2. De toepassing biedt een optie voor het verwijderen van de gegevens in een beheer sectie van de toepassing.
3. De toepassing dwingt een verificatie af Azure AD B2C. Azure AD B2C levert een token met de object-ID van de gebruiker terug naar de toepassing.
4. Het token wordt ontvangen door de toepassing en de object-ID wordt gebruikt om de gebruikers gegevens te verwijderen via een aanroep van de Microsoft Graph-API. De Microsoft Graph-API verwijdert de gebruikers gegevens en retourneert de status code 200 OK.
5. De toepassing organiseert het verwijderen van gebruikers gegevens in andere organisatie systemen, indien nodig, met behulp van de object-ID of andere id's.
6. De toepassing bevestigt het verwijderen van gegevens en geeft de volgende stappen aan de gebruiker.

## <a name="export-customer-data"></a>Klant gegevens exporteren

Het proces voor het exporteren van klant gegevens uit Azure AD B2C is vergelijkbaar met het verwijderings proces.

Azure AD B2C gebruikers gegevens beperkt zijn tot:

- **Gegevens die zijn opgeslagen in de Azure Active Directory**: u kunt gegevens ophalen in een Azure AD B2C-gebruikers traject voor verificatie met behulp van de object-id of een aanmeldings naam, zoals een e-mail adres of gebruikers naam.
- **Rapport met gebruikersspecifieke controle gebeurtenissen**: u kunt gegevens indexeren met behulp van de object-id.

In het volgende voor beeld van een gegevens stroom exporteren, de stappen die worden beschreven als uitgevoerd door de toepassing, kunnen ook worden uitgevoerd door een back-end-proces of een gebruiker met een beheerdersrol in de map:

1. De gebruiker meldt zich aan bij de toepassing. Azure AD B2C wordt zo nodig verificatie afgedwongen met Azure AD Multi-Factor Authentication.
2. De toepassing gebruikt de gebruikers referenties om een Microsoft Graph API-bewerking aan te roepen om de gebruikers kenmerken op te halen. De Microsoft Graph-API biedt de kenmerk gegevens in JSON-indeling. Afhankelijk van het schema, kunt u de inhoud van het ID-token zo instellen dat alle persoonlijke gegevens over een gebruiker worden meegenomen.
3. De toepassing haalt de controle activiteit voor gebruikers op. De Microsoft Graph-API levert de gebeurtenis gegevens aan de toepassing.
4. De toepassing aggregeert de gegevens en maakt deze beschikbaar voor de gebruiker.

## <a name="next-steps"></a>Volgende stappen

Zie [gebruikers toegang beheren](manage-user-access.md)voor meer informatie over het beheren van hoe gebruikers toegang krijgen tot uw toepassing.