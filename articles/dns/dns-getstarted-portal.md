---
title: 'Quickstart: Een DNS-zone en -record maken - Azure-portal'
titleSuffix: Azure DNS
description: Gebruik deze stapsgewijze quickstart om te leren hoe u een Azure DNS-zone en -record maakt met behulp van de Azure-portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: be76a7902c2747c7a8dc1bfc21d58ae88f3ff343
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92282147"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>Quickstart: Een Azure DNS-zone en -record maken met de Azure-portal

U kunt Azure DNS configureren voor het omzetten van hostnamen in uw openbare domein. Als u bijvoorbeeld de domeinnaam *contoso.xyz* hebt gekocht bij een domeinnaamregistrar, kunt u Azure DNS configureren om het domein *contoso.xyz* te hosten en *`www.contoso.xyz`* om te zetten in het IP-adres van uw webserver of web-app.

In deze quickstart maakt u een testdomein en maakt u vervolgens een adresrecord om *www* om te zetten in het IP-adres *10.10.10.10*.

>[!IMPORTANT]
>Alle namen en IP-adressen in deze quickstart zijn voorbeelden en geen weerspiegelingen van praktijkscenario’s.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voor alle portal-stappen meldt u zich aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

Een DNS-zone bevat de DNS-vermeldingen voor een domein. Als u uw domein wilt hosten in Azure DNS, maakt u een DNS-zone voor deze domeinnaam. 

**De DNS-zone maken:**

1. Selecteer linksboven **Een resource maken**, en vervolgens **Netwerken** en **DNS-zone**.

1. Typ of selecteer op de pagina **DNS-zone maken** de volgende waarden:

   - **Naam**: typ *contoso.xyz* voor het voorbeeld in deze quickstart. De naam van de DNS-zone mag elke waarde zijn die nog niet is geconfigureerd op de Azure DNS-servers. Een werkelijke waarde zou een domein zijn dat u hebt gekocht via een domeinnaamregistrar.
   - **Resourcegroep**: Selecteer **Nieuwe maken**, voer *MyResourceGroup* in en selecteer **OK**. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement. 

1. Selecteer **Maken**.

   ![DNS-zone](./media/dns-getstarted-portal/openzone650.png)

Het maken van de zone kan een paar minuten duren.

## <a name="create-a-dns-record"></a>Een DNS-record maken

DNS-vermeldingen of -records voor het domein maakt u binnen de DNS-zone. Maak een nieuwe adresrecord of A-record om een hostnaam om te zetten in een IPv4-adres.

**Een A-record maken:**

1. Open in de Azure-portal onder **Alle resources** de DNS-zone **contoso.xyz** in de resourcegroep **MyResourceGroup**. U kunt *contoso.xyz* invoeren in het vak **Filteren op naam** om deze eenvoudiger te vinden.

1. Selecteer boven aan de pagina **DNS-zone** de optie **+ Recordset**.

1. Typ of selecteer op de pagina **Recordset toevoegen** de volgende waarden:

   - **Naam**: typ *www*. De recordnaam is de hostnaam die u wilt omzetten in het opgegeven IP-adres.
   - **Type**: Selecteer **A**. A-records worden het meest gebruikt, maar er zijn andere recordtypen voor e-mailservers (MX), IPv6-adressen (AAAA), enzovoort. 
   - **TTL**: typ *1*. *Time-to-live* van de DNS-aanvraag geeft aan hoelang DNS-servers en -clients een antwoord kunnen opslaan in de cache.
   - **TTL-eenheid**: selecteer **Uren**. Dit is de tijdseenheid voor de **TTL**-waarde. 
   - **IP-adres**: typ voor het voorbeeld in deze quickstart *10.10.10.10*. Deze waarde is het IP-adres waarin de recordnaam wordt omgezet. In een praktijkscenario voert u het openbare IP-adres voor uw webserver in.

Omdat deze quickstart alleen bedoeld is voor testdoeleinden, hoeft u de Azure DNS-naamservers niet te configureren bij de registrar van uw domeinnaam. Bij een echt productiedomein wilt u dat iedereen op internet de hostnaam omzet, zodat ze verbinding kunnen maken met uw webserver of web-app. U gaat naar uw domeinnaamregistrar om de naamserverrecords te vervangen door de Azure DNS-naamservers. Zie [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md#delegate-the-domain) voor meer informatie.

## <a name="test-the-name-resolution"></a>De naamomzetting testen

Nu u een testzone hebt met daarin een DNS-record, kunt u de naamomzetting testen met het hulpprogramma *nslookup*. 

**DNS-naamomzetting testen:**

1. Open in de Azure-portal onder **Alle resources** de DNS-zone **contoso.xyz** in de resourcegroep **MyResourceGroup**. U kunt *contoso.xyz* invoeren in het vak **Filteren op naam** om deze eenvoudiger te vinden.

1. Kopieer een van de namen van de naamservers uit de lijst op de pagina **Overzicht**. 

   ![zone](./media/dns-getstarted-portal/viewzonens500.png)

1. Open een opdrachtprompt en voer de volgende opdracht uit:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Bijvoorbeeld:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Er verschijnt een scherm dat er ongeveer als volgt uitziet:

   ![In de schermafbeelding ziet u een opdrachtpromptvenster met een n s lookup-opdracht en waarden voor Server, Adres, Naam en Adres.](media/dns-getstarted-portal/nslookup.PNG)

De hostnaam **www\.contoso.xyz** wordt overeenkomstig uw configuratie omgezet in **10.10.10.10**. Met dit resultaat wordt gecontroleerd of de naamomzetting juist werkt. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u in deze quickstart hebt gemaakt, niet meer nodig hebt, verwijdert u ze door de resourcegroep **MyResourceGroup** te verwijderen. Open de resourcegroep **MyResourceGroup** en selecteer **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)
