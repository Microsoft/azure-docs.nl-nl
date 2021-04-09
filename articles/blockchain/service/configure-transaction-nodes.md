---
title: Azure Block Chain Service-transactie knooppunten configureren
description: Service transactie knooppunten van Azure Block Chain configureren
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 0b659fa9f15cd41e9e55de526747d470046cafa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091271"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Azure Block Chain Service-transactie knooppunten configureren

Trans actie knooppunten worden gebruikt voor het verzenden van Block Chain-trans acties naar de Azure Block Chain-service via een openbaar eind punt. Het standaard transactie knooppunt bevat de persoonlijke sleutel van het Ethereum-account dat is geregistreerd bij de Block Chain, en kan niet worden verwijderd.

Details van het standaard transactie knooppunt weer geven:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Navigeer naar het lid van Azure Block Chain Service. Selecteer **transactie knooppunten**.

    ![Standaardtransactieknooppunt selecteren](./media/configure-transaction-nodes/nodes.png)

    Overzichts gegevens zijn open bare eindpunt adressen en open bare sleutel.

## <a name="create-transaction-node"></a>Transactie knooppunt maken

U kunt Maxi maal negen extra transactie knooppunten toevoegen aan uw Block Chain-lid, voor een totaal van 10 transactie knooppunten. Door transactie knooppunten toe te voegen, kunt u de schaal baarheid verg Roten of de belasting verdelen. U kunt bijvoorbeeld een eind punt voor een trans actie-knoop punt hebben voor verschillende client toepassingen.

Een transactie knooppunt toevoegen:

1. Ga in het Azure Portal naar het lid van de Azure Block Chain-service en selecteer **transactie knooppunten > toevoegen**.
1. Voltooi de instellingen voor het nieuwe trans actie-knoop punt.

    ![Transactie knooppunt toevoegen](./media/configure-transaction-nodes/add-node.png)

    | Instelling | Beschrijving |
    |---------|-------------|
    | Name | Naam van het transactie knooppunt. De naam wordt gebruikt voor het maken van het DNS-adres voor het eind punt van het transactie knooppunt. Bijvoorbeeld `newnode-myblockchainmember.blockchain.azure.com`. De naam van het knoop punt kan niet worden gewijzigd nadat deze is gemaakt. |
    | Wachtwoord | Stel een sterk wacht woord in. Gebruik het wacht woord voor toegang tot het knoop punt van het transactie knooppunt met basis verificatie.

1. Selecteer **Maken**.

    Het inrichten van een nieuw transactie knooppunt duurt ongeveer 10 minuten. Extra transactie knooppunten zijn kosten in rekening gebracht. Zie [Azure-prijzen](https://aka.ms/ABSPricing)voor meer informatie over de kosten.

## <a name="endpoints"></a>Eindpunten

Transactie knooppunten hebben een unieke DNS-naam en open bare eind punten.

De eindpunt Details van een transactie knooppunt weer geven:

1. Ga in het Azure Portal naar een van uw trans actie knooppunten van de Azure Block Chain-service en selecteer **overzicht**.

    ![Scherm opname toont het overzicht van transactie knooppunten voor een Block Chain-lid.](./media/configure-transaction-nodes/endpoints.png)

De eind punten van het transactie knooppunt zijn veilig en vereisen authenticatie. U kunt verbinding maken met een trans actie-eind punt met behulp van Azure AD-verificatie, HTTPS-basis verificatie en een toegangs sleutel via HTTPS of WebSocket over TLS gebruiken.

### <a name="azure-active-directory-access-control"></a>Toegangs beheer Azure Active Directory

Azure Block Chain Service Trans Action node-eind punten ondersteunen Azure Active Directory-verificatie (Azure AD). U kunt Azure AD-gebruikers,-groepen en-Service-Principal toegang verlenen tot uw eind punt.

Toegangs beheer voor Azure AD aan uw eind punt verlenen:

1. Ga in het Azure Portal naar het lid van de Azure Block Chain-service en selecteer **transactie knooppunten > toegangs beheer (IAM) > voeg > roltoewijzing** toe.
1. Maak een nieuwe roltoewijzing voor een gebruiker, groep of Service-Principal (toepassings rollen).

    ![IAM-rol toevoegen](./media/configure-transaction-nodes/add-role.png)

    | Instelling | Bewerking |
    |---------|-------------|
    | Rol | Selecteer **eigenaar**, **bijdrager** of **lezer**.
    | Toegang toewijzen aan | Selecteer **Azure AD-gebruiker,-groep of-Service-Principal**.
    | Selecteer | Zoek naar de gebruiker, groep of service-principal die u wilt toevoegen.

1. Selecteer **Opslaan** om de roltoewijzing toe te voegen.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie over Azure AD-toegangs beheer

Zie [verbinding maken met uw knoop punt met behulp van Aad-verificatie](configure-aad.md)voor meer informatie over het maken van verbinding met Azure AD-verificatie.

### <a name="basic-authentication"></a>Basisverificatie

Voor HTTPS-basis verificatie worden gebruikers naam-en wachtwoord referenties door gegeven in de HTTPS-header van de aanvraag voor het eind punt.

U kunt de eindpunt Details van de basis verificatie van een transactie knooppunt weer geven in de Azure Portal. Navigeer naar een van uw trans actie knooppunten van de Azure Block Chain-service en selecteer **basis verificatie** in instellingen.

![Basisverificatie](./media/configure-transaction-nodes/basic.png)

De gebruikers naam is de naam van het knoop punt en kan niet worden gewijzigd.

Als u de URL wilt gebruiken, vervangt u door \<password\> de wachtwoordset wanneer het knoop punt is ingericht. U kunt het wacht woord bijwerken door **wacht woord opnieuw instellen** te selecteren.

### <a name="access-keys"></a>Toegangssleutels

Voor toegangs sleutel verificatie is de toegangs sleutel opgenomen in de eind punt-URL. Wanneer het transactie knooppunt is ingericht, worden er twee toegangs sleutels gegenereerd. De toegangs sleutel kan worden gebruikt voor verificatie. Met twee sleutels kunt u sleutels wijzigen en draaien.

U kunt de toegangs sleutel gegevens van een transactie knooppunt bekijken en eindpunt adressen kopiëren die de toegangs sleutels bevatten. Ga naar een van de trans actie knooppunten van uw Azure Block Chain service-lid en selecteer **toegangs sleutels** in instellingen.

### <a name="firewall-rules"></a>Firewall-regels

Met firewall regels kunt u de IP-adressen beperken waarmee wordt geprobeerd om zich te verifiëren bij uw transactie knooppunt.  Als er geen firewall regels zijn geconfigureerd voor uw transactie knooppunt, is deze niet toegankelijk voor een partij.  

Als u de firewall regels van een transactie knooppunt wilt weer geven, gaat u naar een van uw Azure Block Chain Service-trans actie knooppunten en selecteert u **firewall regels** in instellingen.

U kunt Firewall regels toevoegen door de naam van de regel, het eerste IP-adres en het laatste IP-adres in het raster van de **firewall regels** in te voeren.

![Firewall-regels](./media/configure-transaction-nodes/firewall-rules.png)

In te scha kelen:

* **Eén IP-adres:** Configureer hetzelfde IP-adres voor de begin-en eind-IP-adressen.
* **IP-adres bereik:** Het begin-en eind-IP-adres bereik configureren. Bijvoorbeeld: een bereik vanaf 10.221.34.0 en eindigend op 10.221.34.255 zou het hele 10.221.34.xxx-subnet mogelijk maken.
* **Alle IP-adressen toestaan:** Configureer het begin-IP-adres naar 0.0.0.0 en het laatste IP-adres in 255.255.255.255.

## <a name="connection-strings"></a>Verbindingsreeksen

De syntaxis van de verbindings reeks voor uw transactie knooppunt wordt verschaft voor basis verificatie of het gebruik van toegangs sleutels. Verbindings reeksen met toegangs sleutels via HTTPS en websockets worden meegeleverd.

U kunt de verbindings reeksen van een transactie knooppunt weer geven en eindpunt adressen kopiëren. Navigeer naar een van uw trans actie knooppunten van de Azure Block Chain-service en selecteer **verbindings reeksen** in instellingen.

![Verbindingsreeksen](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Voorbeeldcode

De voorbeeld code is beschikbaar om snel verbinding te maken met uw transactie knooppunt via Web3, Nethereum, Web3js en Truffle.

U kunt de voorbeeld verbindings code van een transactie knooppunt bekijken en deze kopiëren voor gebruik met populaire ontwikkel hulpprogramma's. Ga naar een van de trans actie knooppunten van een Azure Block Chain-service en selecteer **voorbeeld code** in instellingen.

Kies het tabblad Web3, Nethereum, Truffle of Web3j om het code voorbeeld te bekijken dat u wilt gebruiken.

![Voorbeeldcode](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Transactie knooppunten configureren met behulp van Azure CLI](manage-cli.md)
