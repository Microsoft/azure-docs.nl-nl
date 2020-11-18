---
title: Beheer hulpprogramma implementeren met een Azure Resource Manager sjabloon-Azure
description: Een hulp programma voor gebruikers interface installeren met een Azure Resource Manager sjabloon voor het beheren van klassieke Windows-resources (virtueel bureau blad).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f96365bcdf64d19dc0b894f2f1230233b3137bc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842648"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Een beheer hulpprogramma voor virtueel bureau blad (klassiek) van Windows met een Azure Resource Manager-sjabloon implementeren

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

In de instructies in dit artikel wordt uitgelegd hoe u de gebruikers interface implementeert met behulp van een Azure Resource Manager sjabloon.

## <a name="important-considerations"></a>Belangrijke overwegingen

Omdat de app toestemming nodig heeft om te communiceren met het virtuele bureau blad van Windows, ondersteunt dit hulp programma geen Business-to-Business (B2B)-scenario's. Elk Azure Active Directory (AAD)-abonnement van de Tenant heeft een eigen afzonderlijke implementatie van het beheer programma nodig.

Dit beheer programma is een voor beeld. Micro soft zal belang rijke updates voor de beveiliging en kwaliteit bieden. De [bron code is beschikbaar in github](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Klanten en partners worden aangemoedigd het hulp programma aan te passen aan de behoeften van hun bedrijf.

De volgende browsers zijn compatibel met het beheer programma:
- Google Chrome 68 of hoger
- Micro soft Edge 40,15063 of hoger
- Mozilla Firefox 52,0 of hoger
- Safari 10 of hoger (alleen macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Wat u nodig hebt om het beheer programma te implementeren

Voordat u het beheer programma implementeert, hebt u een Azure Active Directory-gebruiker (Azure AD) nodig voor het maken van een app-registratie en het implementeren van de beheer GEBRUIKERSINTERFACE. Deze gebruiker moet:

- Azure AD Multi-Factor Authentication (MFA) is uitgeschakeld
- Machtiging voor het maken van resources in uw Azure-abonnement
- Toestemming voor het maken van een Azure AD-toepassing. Volg deze stappen om te controleren of uw gebruiker over de vereiste machtigingen beschikt door de instructies in de [vereiste machtigingen](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)te volgen.

Nadat u het beheer programma hebt geïmplementeerd en geconfigureerd, raden we u aan een gebruiker te vragen de beheer GEBRUIKERSINTERFACE te starten om ervoor te zorgen dat alles werkt. De gebruiker die de beheer GEBRUIKERSINTERFACE start, moet beschikken over een roltoewijzing waarmee ze de Windows Virtual Desktop-Tenant kunnen weer geven of bewerken.

## <a name="deploy-the-management-tool"></a>Hulpprogramma voor beheer implementeren

Voordat u begint, moet u ervoor zorgen dat de server-en client-apps toestemming hebben op de [Windows-pagina toestemming voor virtueel bureau blad](https://rdweb.wvd.microsoft.com) voor de Azure Active Directory (Aad) die wordt weer gegeven.

Volg deze instructies voor het implementeren van de Azure resource management-sjabloon:

1. Ga naar de [github Azure RDS-Templates-pagina](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. De sjabloon implementeren in Azure.
    - Als u in een bedrijfs abonnement implementeert, schuift u omlaag en selecteert u **implementeren naar Azure**.
    - Als u in een Cloud Solution Provider-abonnement implementeert, volgt u deze instructies om te implementeren in Azure:
        1. Schuif omlaag en klik met de rechter muisknop op **implementeren naar Azure**, en selecteer vervolgens **koppelings locatie kopiëren**.
        2. Open een tekst editor zoals Klad blok en plak de koppeling daar.
        3. Voer direct na <https://portal.azure.com/> en vóór de hashtag (#) een apen staartje (@) in, gevolgd door de domein naam van de Tenant. Hier volgt een voor beeld van de notatie: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/> .
        4. Meld u aan bij de Azure Portal als gebruiker met beheerders-en Inzender machtigingen voor het Cloud Solution Provider-abonnement.
        5. Plak de koppeling die u naar de tekst editor hebt gekopieerd naar de adres balk.
3. Ga als volgt te werk bij het invoeren van de para meters:
    - Selecteer **Onwaar** voor de para meter **isServicePrincipal** .
    - Voer voor de referenties uw Azure AD-referenties in als multi-factor Authentication is uitgeschakeld. Deze referenties worden gebruikt om de Azure AD-toepassing en Azure-resources te maken. Zie [wat u nodig hebt om het beheer programma te implementeren](#what-you-need-to-deploy-the-management-tool)voor meer informatie.
    - Voor de **ApplicationName** gebruikt u een unieke naam voor uw app die in uw Azure Active Directory wordt geregistreerd. Deze naam wordt ook gebruikt voor de web-app-URL. U kunt bijvoorbeeld een naam gebruiken zoals ' Apr3UX '.
4. Wanneer u de para meters hebt opgegeven, accepteert u de voor waarden en selecteert u **kopen**.

## <a name="provide-consent-for-the-management-tool"></a>Toestemming geven voor het beheer programma

Nadat de GitHub-Azure Resource Manager sjabloon is voltooid, vindt u een resource groep met twee app-Services samen met één app service-plan in de Azure Portal.

Voordat u zich aanmeldt en het beheer programma gebruikt, moet u toestemming geven voor de nieuwe Azure AD-toepassing die aan het beheer programma is gekoppeld. Door toestemming te geven, kunt u met het beheer programma Windows virtueel-bureaublad beheer aanroepen namens de gebruiker die momenteel is aangemeld bij het hulp programma.

> [!div class="mx-imgBorder"]
> ![Een scherm opname met de machtigingen die worden gegeven wanneer u akkoord gaat met het beheer programma voor de gebruikers interface.](../media/management-ui-delegated-permissions.png)

Als u wilt bepalen welke gebruiker u kunt gebruiken om u aan te melden bij het hulp programma, gaat u naar de [pagina met gebruikers instellingen van Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) en noteert u de waarde voor **gebruikers kan toestemming geven om toegang te krijgen tot Bedrijfs gegevens** namens hen.

> [!div class="mx-imgBorder"]
> ![Een scherm opname waarin wordt getoond of gebruikers toestemming kunnen geven voor toepassingen alleen voor hun gebruiker.](../media/management-ui-user-consent-allowed.png)

- Als de waarde is ingesteld op **Ja**, kunt u zich aanmelden met een gebruikers account in de Azure Active Directory en alleen toestemming geven voor die gebruiker. Als u zich echter op een later tijdstip bij het beheer programma aanmeldt met een andere gebruiker, moet u dezelfde toestemming opnieuw uitvoeren.
- Als de waarde is ingesteld op **Nee**, moet u zich aanmelden als globale beheerder in de Azure Active Directory en toestemming geven voor de beheerder voor alle gebruikers in de Directory. Geen van de andere gebruikers wordt gevraagd om toestemming te vragen.


Nadat u hebt bepaald welke gebruiker u gaat gebruiken om toestemming te geven, volgt u deze instructies om toestemming te geven voor het hulp programma:

1. Ga naar uw Azure-resources, selecteer de Azure-app Services-resource met de naam die u in de sjabloon hebt gegeven (bijvoorbeeld Apr3UX) en navigeer naar de URL die eraan is gekoppeld. bijvoorbeeld  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Meld u aan met het juiste Azure Active Directory gebruikers account.
3. Als u een globale beheerder hebt geverifieerd, kunt u nu het selectie vakje inschakelen om toestemming te geven namens **uw organisatie**. Selecteer **accepteren** om toestemming te geven.

   > [!div class="mx-imgBorder"]
   > ![Een scherm opname waarin de volledige toestemming pagina wordt weer gegeven die de gebruiker of beheerder ziet.](../media/management-ui-consent-page.png)

Hiermee gaat u nu naar het beheer programma.

## <a name="use-the-management-tool"></a>Het beheer programma gebruiken

Nadat u toestemming hebt gegeven voor de organisatie of voor een opgegeven gebruiker, kunt u op elk gewenst moment toegang krijgen tot het beheer programma.

Volg deze instructies om het hulp programma te starten:

1. Selecteer de Azure-app Services-resource met de naam die u in de sjabloon hebt gegeven (bijvoorbeeld Apr3UX) en navigeer naar de URL die eraan is gekoppeld. bijvoorbeeld  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Meld u aan met de referenties van uw Windows-virtueel bureau blad.
3. Wanneer u wordt gevraagd om een Tenant groep te kiezen, selecteert u **standaard Tenant groep** in de vervolg keuzelijst.
4. Wanneer u **standaard Tenant groep** selecteert, wordt er een menu aan de linkerkant van het venster weer gegeven. Zoek in dit menu de naam van uw Tenant groep en selecteer deze.

  > [!NOTE]
  > Als u een aangepaste Tenant groep hebt, voert u de naam hand matig in in plaats van te kiezen in de vervolg keuzelijst.

## <a name="report-issues"></a>Problemen melden

Als u problemen ondervindt met het beheer programma of andere virtuele bureau blad-hulpprogram ma's van Windows, volgt u de instructies in [Azure Resource Manager sjablonen voor extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md) om ze te rapporteren op github.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het beheer programma implementeert en er verbinding mee maakt, kunt u leren hoe u de Help van Azure-service gebruikt om service problemen en status adviezen te bewaken. Zie onze [zelf studie Service waarschuwingen instellen](set-up-service-alerts-2019.md)voor meer informatie.