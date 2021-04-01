---
title: De Active Directory verbonden service gebruiken (Visual Studio)
description: Een Azure Active Directory toevoegen met behulp van het dialoog venster verbonden services van Visual Studio toevoegen
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a1ba7db72743ac122a697bf271e783ec64e041e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165478"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Een Azure Active Directory toevoegen met behulp van verbonden services in Visual Studio

Met Azure Active Directory (Azure AD) kunt u één Sign-On (SSO) ondersteunen voor ASP.NET MVC-webtoepassingen of Active Directory verificatie in Web API-services. Met Azure AD-verificatie kunnen uw gebruikers hun accounts gebruiken van Azure Active Directory om verbinding te maken met uw webtoepassingen. De voor delen van Azure AD-verificatie met Web API bevatten verbeterde gegevens beveiliging bij het weer geven van een API van een webtoepassing. Met Azure AD hoeft u geen afzonderlijk verificatie systeem te beheren met een eigen account en gebruikers beheer.

In dit artikel en de bijbehorende aanvullende artikelen vindt u informatie over het gebruik van de functie van Visual Studio Connected service voor Active Directory. De mogelijkheid is beschikbaar in Visual Studio 2015 en hoger.

Op dit moment biedt de Active Directory verbonden service geen ondersteuning voor ASP.NET Core toepassingen.

## <a name="prerequisites"></a>Vereisten

- Azure-account: als u geen Azure-account hebt, kunt u [zich aanmelden voor een gratis proef versie](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) of [de voor delen van uw Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Visual Studio 2015** of hoger. [Down load Visual Studio nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Verbinding maken met Azure Active Directory met behulp van het dialoog venster verbonden services

1. Maak of open in Visual Studio een ASP.NET MVC-project of een ASP.NET Web API-project. U kunt de MVC-, Web-API-, Single-Page-toepassing, de Azure API-app, Azure Mobile App en Azure Mobile Service-sjablonen gebruiken.

1. Selecteer de menu opdracht **Project > verbonden service toevoegen...** of dubbel klik op het knoop punt **verbonden services dat is** gevonden onder het project in Solution Explorer.

1. Selecteer op de pagina **verbonden services** **verificatie met Azure Active Directory**.

    ![Pagina verbonden services](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Selecteer op de pagina **Inleiding** de optie **volgende**. Als er fouten op deze pagina worden weer geven, raadpleegt u [fouten diagnosticeren met de Azure Active Directory Connected-service](vs-active-directory-error.md).

    ![Introductie pagina](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Selecteer op de pagina **eenmalige aanmelding** een domein in de vervolg keuzelijst **domein** . De lijst bevat alle domeinen die toegankelijk zijn voor de accounts die worden vermeld in het dialoog venster account instellingen van Visual Studio (**instellingen voor bestands > account...**). Als alternatief kunt u een domein naam invoeren als u deze niet hebt gevonden, zoals `mydomain.onmicrosoft.com` . U kunt de optie kiezen om een Azure Active Directory-app te maken of de instellingen van een bestaande Azure Active Directory-app te gebruiken. Selecteer **volgende** als u klaar bent.

    ![Pagina met eenmalige aanmelding](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Selecteer op de pagina **Directory-toegang** de optie **Directory gegevens lezen** , zoals gewenst. Ontwikkel aars bevatten doorgaans deze optie.

    ![Pagina Directory toegang](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Selecteer **volt ooien** om de wijzigingen in uw project te starten om Azure AD-verificatie in te scha kelen. Tijdens deze periode wordt de voortgang van Visual Studio weer gegeven:

    ![Voortgang van Active Directory verbonden service](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Wanneer het proces is voltooid, opent Visual Studio uw browser in een van de volgende artikelen, afhankelijk van het project type:

    - [Aan de slag met .NET MVC-projecten](vs-active-directory-dotnet-getting-started.md)
    - [Aan de slag met WebAPI-projecten](vs-active-directory-webapi-getting-started.md)

1. U kunt ook het Active Directory domein zien op de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Hoe uw project is gewijzigd

Wanneer u de wizard Connected service toevoegt, voegt Visual Studio Azure Active Directory en gekoppelde verwijzingen naar uw project toe. Configuratie bestanden en code bestanden in uw project zijn ook gewijzigd om ondersteuning toe te voegen voor Azure AD. De specifieke wijzigingen die Visual Studio maakt, zijn afhankelijk van het project type. Raadpleeg de volgende artikelen voor meer informatie:

- [Wat is er gebeurd met mijn .NET MVC-project?](vs-active-directory-dotnet-what-happened.md)
- [Wat is er gebeurd met mijn web API-project?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Volgende stappen

- [Verificatie scenario's voor Azure Active Directory](./authentication-vs-authorization.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)