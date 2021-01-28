---
title: 'Procedure: een geregistreerde app verwijderen uit Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: In deze instructiegids leert u hoe u een toepassing kunt verwijderen die bij Microsoft Identity Platform is geregistreerd.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 12074b6dc5d27c0d28ceea5ab6d266d1f02ecbe2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938178"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Een toepassing verwijderen die bij het Microsoft Identity Platform is geregistreerd

Bedrijfs ontwikkelaars en SaaS-providers (Software-as-a-Service) die toepassingen hebben geregistreerd bij het micro soft Identity-platform, moeten mogelijk de registratie van een toepassing verwijderen.

In de volgende secties leert u het volgende:

* Een toepassing verwijderen die is geschreven door u of uw organisatie
* Een toepassing verwijderen die is geschreven door een andere organisatie

## <a name="prerequisites"></a>Vereisten

* Een [toepassing die in uw Azure Active Directory-tenant is geregistreerd](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Een toepassing verwijderen die is geschreven door u of uw organisatie

Toepassingen die u of uw organisatie hebben geregistreerd, worden vertegenwoordigd door een toepassingsobject en een service-principalobject in uw tenant. Zie [Toepassingsobjecten en service-principalobjecten](./app-objects-and-service-principals.md) voor meer informatie.

Als u een toepassing wilt verwijderen, moet u worden vermeld als eigenaar van de toepassing of over beheerders rechten beschikken.

1. Meld u bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal<span class="docon docon-navigate-external x-hidden-focus"></span></a> aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
1. Selecteer in het navigatiedeelvenster aan de linkerkant de **Azure Active Directory**-service en selecteer vervolgens **App-registraties**. Zoek en selecteer de toepassing die u wilt configureren. Wanneer u de app hebt geselecteerd, ziet u de pagina **Overzicht**.
1. Selecteer **Verwijderen** op de pagina **Overzicht**.
1. Selecteer **Ja** om te bevestigen dat u de app wilt verwijderen.

## <a name="remove-an-application-authored-by-another-organization"></a>Een toepassing verwijderen die is geschreven door een andere organisatie

Als u **App-registraties** bekijkt binnen de context van een tenant, dan is een subset van de toepassingen die onder **Alle apps** wordt weergegeven van een andere tenant en zijn deze tijdens het toestemmingsproces bij uw tenant geregistreerd. Om precies te zijn, worden deze toepassingen alleen vertegenwoordigd door een service-principal-object in uw tenant, zonder bijbehorend toepassingsobject. Zie [Toepassings- en service-principal-objecten in Azure Active Directory](./app-objects-and-service-principals.md) voor meer informatie over de verschillen tussen toepassings- en service-principal-objecten.

Als u de toegang van een toepassing tot uw directory wilt verwijderen (nadat u toestemming hebt verleend), moet de bedrijfsbeheerder de service-principal van de toepassing verwijderen. De beheerder moet globale beheerderstoegang hebben en kan de toepassing verwijderen via de Azure-portal of de [Azure Active Directory PowerShell-cmdlets](/previous-versions/azure/jj151815(v=azure.100)) gebruiken om de toegang op te heffen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [toepassings- en service-principalobjecten](app-objects-and-service-principals.md) in Microsoft Identity Platform.
