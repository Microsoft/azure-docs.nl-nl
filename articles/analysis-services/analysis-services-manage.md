---
title: Azure Analysis Services beheren | Microsoft Docs
description: In dit artikel worden de hulpprogram ma's beschreven voor het beheren van beheer-en beheer taken voor een Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 91ffcac98b2b919a8fc131d235e699aad4fa215d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93078897"
---
# <a name="manage-analysis-services"></a>Analysis Services beheren
Zodra u een Analysis Services server hebt gemaakt in azure, zijn er mogelijk een aantal beheer-en beheer taken die u direct moet uitvoeren, of ergens anders in de weg. U kunt bijvoorbeeld de verwerking uitvoeren op de gegevens vernieuwen, bepalen wie toegang heeft tot de modellen op uw server of de status van uw server controleren. Sommige beheer taken kunnen alleen worden uitgevoerd in Azure Portal, andere in SQL Server Management Studio (SSMS) en sommige taken kunnen worden uitgevoerd in een van beide.

## <a name="azure-portal"></a>Azure Portal
Met [Azure Portal](https://portal.azure.com/) kunt u servers maken en verwijderen, Server bronnen bewaken, de grootte wijzigen en beheren wie toegang heeft tot uw servers.  Als u enkele problemen hebt, kunt u ook een ondersteunings aanvraag indienen.

![Scherm opname van de Azure Portal waar u servers kunt maken en verwijderen, Server bronnen bewaken, de grootte wijzigen en beheren wie toegang heeft tot uw servers.](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
U kunt verbinding maken met uw server in azure, net als bij het maken van verbinding met een Server exemplaar in uw eigen organisatie. Vanuit SSMS kunt u veel dezelfde taken uitvoeren, zoals het verwerken van gegevens of het maken van een verwerkings script, het beheren van rollen en het gebruik van Power shell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>SSMS downloaden en installeren
Zorg ervoor dat u de nieuwste versie van SSMS gebruikt om alle nieuwste functies en de meest vloeiende ervaring te krijgen bij het maken van verbinding met uw Azure Analysis Services-server. 

[Down load SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Verbinding maken met SSMS
 Wanneer u SSMS gebruikt voordat u de eerste keer verbinding maakt met uw server, moet u ervoor zorgen dat uw gebruikers naam is opgenomen in de Analysis Services beheerders groep. Zie [Server beheerders en database gebruikers](#server-administrators-and-database-users) verderop in dit artikel voor meer informatie.

1. Voordat u verbinding maakt, moet u de server naam ophalen. In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
    ![Servernaam bepalen in Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Klik in SSMS > **Objectverkenner** op **Verbinding maken** > **Analysis Services**.
3. Plak in het dialoog venster **verbinding maken met server** de naam van de server en kies vervolgens bij **verificatie** een van de volgende verificatie typen:   
    > [!NOTE]
    > Verificatie type, **Active Directory-Universal met MFA-ondersteuning**, wordt aanbevolen.

    > [!NOTE]
    > Als u zich aanmeldt met een micro soft-account, Live ID, Yahoo, Gmail, enzovoort, laat u het veld wacht woord leeg. U wordt gevraagd om een wacht woord nadat u op verbinding maken hebt geklikt.

    **Windows-verificatie** voor het gebruik van uw referenties voor Windows domein\gebruikersnaam en wacht woord.

    **Active Directory wachtwoord verificatie** om een organisatie account te gebruiken. Wanneer u bijvoorbeeld verbinding maakt vanaf een computer die niet lid is van een domein.

    **Active Directory-Universal met MFA-ondersteuning** voor het gebruik van [niet-interactieve of multi-factor Authentication](../azure-sql/database/authentication-mfa-ssms-overview.md). 
   
    ![Verbinding maken in SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Server beheerders en database gebruikers
In Azure Analysis Services zijn er twee soorten gebruikers: Server beheerders en database gebruikers. Beide typen gebruikers moeten zich in uw Azure Active Directory bevallen en moeten worden opgegeven door het e-mail adres of de UPN van de organisatie. Raadpleeg voor meer informatie [Verificatie en gebruikersmachtigingen](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Verbindings problemen oplossen
Als u verbinding maakt met behulp van SSMS en u problemen ondervindt, moet u mogelijk de aanmeldings cache wissen. Er wordt niets in de cache opgeslagen op schijf. Als u de cache wilt wissen, sluit u het verbindings proces en start u het opnieuw. 

## <a name="next-steps"></a>Volgende stappen
Als u nog geen tabellair model op uw nieuwe server hebt geïmplementeerd, is nu een goed moment. Zie [Implementeren naar Azure Analysis Services](analysis-services-deploy.md) voor meer informatie.

Als u een model op uw server hebt geïmplementeerd, kunt u er verbinding mee maken met behulp van een client of browser. Zie [gegevens ophalen van Azure Analysis Services-server](analysis-services-connect.md)voor meer informatie.