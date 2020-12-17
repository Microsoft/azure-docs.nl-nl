---
title: Beveiligings filters voor het afkappen van resultaten met behulp van Active Directory
titleSuffix: Azure Cognitive Search
description: Meer informatie over het implementeren van beveiligings bevoegdheden op document niveau voor Azure Cognitive Search Zoek resultaten, met behulp van beveiligings filters en Azure Active Directory van AD-identiteiten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629507"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Beveiligings filters voor het verkleinen van Azure Cognitive Search-resultaten met behulp van Active Directory-identiteiten

In dit artikel wordt beschreven hoe u met behulp van Azure Active Directory (AD)-beveiligings identiteiten samen met filters in azure Cognitive Search Zoek resultaten kunt bijsnijden op basis van lidmaatschap van de gebruikers groep.

Dit artikel behandelt de volgende taken:
> [!div class="checklist"]
> - Azure AD-groepen en-gebruikers maken
> - De gebruiker koppelen aan de groep die u hebt gemaakt
> - De nieuwe groepen in de cache opslaan
> - Documenten indexeren met gekoppelde groepen
> - Een zoek opdracht met groeps-id's filteren
> 
> [!NOTE]
> Voorbeeld code fragmenten in dit artikel zijn geschreven in C#. U vindt de volledige broncode [op GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Vereisten

Uw index in azure Cognitive Search moet een [beveiligings veld](search-security-trimming-for-azure-search.md) bevatten voor het opslaan van de lijst met groeps-id's met lees toegang tot het document. In dit voor beeld wordt uitgegaan van een een-op-een-correspondentie tussen een beveiligbaar item (zoals een college van een persoon) en een beveiligings veld dat bepaalt wie toegang heeft tot dat item (Admissions).

U moet beschikken over Azure AD-beheerders machtigingen die zijn vereist in dit overzicht voor het maken van gebruikers, groepen en koppelingen. 

Uw toepassing moet ook worden geregistreerd bij Azure AD als een multi tenant-app, zoals beschreven in de volgende procedure.

### <a name="register-your-application-with-azure-active-directory"></a>Uw toepassing registreren bij Azure Active Directory

Deze stap integreert uw toepassing met Azure AD voor het accepteren van aanmeldingen van gebruikers-en groeps accounts. Als u geen Tenant beheerder bent in uw organisatie, moet u mogelijk [een nieuwe Tenant maken](../active-directory/develop/quickstart-create-new-tenant.md) om de volgende stappen uit te voeren.

1. Zoek in [Azure Portal](https://portal.azure.com)de Azure Active Directory resource voor uw abonnement.

1. Selecteer aan de linkerkant onder **beheren** de optie **app-registraties** en selecteer vervolgens **nieuwe registratie**.

1. Geef de registratie een naam, mogelijk een naam die overeenkomt met de naam van de zoek toepassing. Selecteer **Registreren**.

1. Wanneer de app-registratie is gemaakt, kopieert u de toepassings-ID. U moet deze teken reeks opgeven voor uw toepassing.

   Als u de [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)stapsgewijs doorloopt, plakt u deze waarde in het **app.config** -bestand.

   Herhaal dit voor de Tenant-ID.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Toepassings-ID in de sectie Essentials":::

1. Selecteer aan de linkerkant **API-machtigingen** en selecteer vervolgens **een machtiging toevoegen**. 

1. Selecteer **Microsoft Graph** en selecteer vervolgens **gedelegeerde machtigingen**.

1. Zoek de volgende gedelegeerde machtigingen en voeg deze toe:

   - **Map. ReadWrite. all**
   - **Group.ReadWrite.All**
   - **User. ReadWrite. all**

Microsoft Graph biedt een API die programmatische toegang tot Azure AD via een REST API mogelijk maakt. In het code voorbeeld voor deze walkthrough worden de machtigingen gebruikt voor het aanroepen van de Microsoft Graph-API voor het maken van groepen, gebruikers en koppelingen. De Api's worden ook gebruikt om groeps-id's in de cache op te slaan voor snellere filtering.

## <a name="create-users-and-groups"></a>Gebruikers en groepen maken

Als u een zoek opdracht toevoegt aan een vastgelegde toepassing, hebt u mogelijk bestaande gebruikers-en groeps-id's in azure AD. In dit geval kunt u de volgende drie stappen overs Laan. 

Als u echter geen bestaande gebruikers hebt, kunt u Microsoft Graph-Api's gebruiken om de beveiligings-principals te maken. De volgende code fragmenten laten zien hoe u id's kunt genereren, die gegevens waarden worden voor het veld beveiliging in uw Azure Cognitive Search index. In onze hypothetische toepassing voor colleges is dit de beveiligings-id's voor admission medewerkers.

Gebruikers-en groepslid maatschappen kunnen zeer onvoorzichtig zijn, met name voor grote organisaties. Code die gebruikers-en groeps-id's bouwt, moet vaak genoeg worden uitgevoerd om wijzigingen in het lidmaatschap van de organisatie op te halen. Op dezelfde manier moet uw Azure Cognitive Search-index een vergelijk bare update planning hebben om de huidige status van toegestane gebruikers en resources weer te geven.

### <a name="step-1-create-group"></a>Stap 1: [groep maken](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>Stap 2: [gebruiker maken](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>Stap 3: een gebruiker en groep koppelen

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>Stap 4: de groeps-id's in de cache opslaan

Als u de netwerk latentie wilt beperken, kunt u de groeps associaties van de gebruiker in de cache opslaan, zodat er groepen worden geretourneerd uit de cache, waarbij een retour naar Azure AD wordt opgeslagen. U kunt [Azure AD batch-API](/graph/json-batching) gebruiken om één HTTP-aanvraag met meerdere gebruikers te verzenden en de cache te bouwen.

Microsoft Graph is ontworpen om een groot aantal aanvragen af te handelen. Als er sprake is van een overweldigend aantal aanvragen, mislukt de aanvraag met de HTTP-status code 429 van Microsoft Graph. Zie [Microsoft Graph beperking](/graph/throttling)voor meer informatie.

## <a name="index-document-with-their-permitted-groups"></a>Document indexeren met de toegestane groepen

Query bewerkingen in azure Cognitive Search worden uitgevoerd via een Azure Cognitive Search-index. In deze stap importeert een indexerings bewerking Doorzoek bare gegevens in een index, inclusief de id's die als beveiligings filters worden gebruikt. 

Azure Cognitive Search verifieert geen gebruikers identiteiten of biedt logica om te bepalen welke inhoud een gebruiker mag weer geven. Bij het gebruik van beveiligings beperking wordt ervan uitgegaan dat u de koppeling tussen een gevoelig document en de groeps-id die toegang heeft tot het document, hebt geïmporteerd in een zoek index. 

In het hypothetische voor beeld bevat de hoofd tekst van de PUT-aanvraag in een Azure Cognitive Search index het College of transcript van een sollicitant samen met de groeps-id die gemachtigd is om die inhoud weer te geven. 

In het algemene voor beeld dat in het code voorbeeld voor dit scenario wordt gebruikt, kan de index actie er als volgt uitzien:

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>Een zoek opdracht uitgeven

Voor beveiligings beperking zijn de waarden in uw beveiligings veld in de index statische waarden die worden gebruikt voor het opnemen of uitsluiten van documenten in Zoek resultaten. Als de groeps-id voor Admissions bijvoorbeeld "A11B22C33D44-E55F66G77-H88I99JKK" is, worden alle documenten in een Azure Cognitive Search index met de id in het beveiligings document opgenomen (of uitgesloten) in de zoek resultaten die worden teruggestuurd naar de aanvrager.

Als u documenten wilt filteren die zijn geretourneerd in de zoek resultaten op basis van groepen van de gebruiker die de aanvraag heeft verzonden, raadpleegt u de volgende stappen.

### <a name="step-1-retrieve-users-group-identifiers"></a>Stap 1: de groeps-id's van de gebruiker ophalen

Als de groepen van de gebruiker niet al zijn opgeslagen in de cache of als de cache is verlopen, geeft u de aanvraag voor de [groep](/graph/api/directoryobject-getmembergroups) op.

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>Stap 2: de zoek opdracht opstellen

Ervan uitgaande dat u het lidmaatschap van de groep van de gebruiker hebt, kunt u de zoek opdracht uitgeven met de juiste filter waarden.

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>Stap 3: de resultaten verwerken

Het antwoord bevat een gefilterde lijst met documenten, die bestaan uit degene die de gebruiker mag bekijken. Afhankelijk van hoe u de pagina met zoek resultaten bouwt, wilt u mogelijk visuele aanwijzingen toevoegen aan de gefilterde resultatenset.

## <a name="next-steps"></a>Volgende stappen

In dit scenario hebt u een patroon geleerd voor het gebruik van Azure AD-aanmeldingen voor het filteren van documenten in azure Cognitive Search resultaten, waardoor de resultaten van documenten die niet overeenkomen met het filter dat is opgegeven in de aanvraag, worden afgekapt. Raadpleeg de volgende koppelingen voor een ander patroon dat eenvoudiger kan zijn of om andere beveiligings functies opnieuw te bezoeken.

- [Beveiligings filters voor het verkleinen van resultaten](search-security-trimming-for-azure-search.md)
- [Beveiliging in azure Cognitive Search](search-security-overview.md)