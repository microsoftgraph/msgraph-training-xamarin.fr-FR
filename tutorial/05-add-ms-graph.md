<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="bebab-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="bebab-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="bebab-102">Pour cette application, vous allez utiliser la [bibliothèque cliente Microsoft Graph pour .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="bebab-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="bebab-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="bebab-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="bebab-104">Ouvrez **CalendarPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="bebab-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 Title="Calendar"
                 x:Class="GraphTutorial.CalendarPage">
        <ContentPage.Content>
            <StackLayout>
                <Editor x:Name="JSONResponse"
                        IsSpellCheckEnabled="False"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="FillAndExpand"/>
            </StackLayout>
        </ContentPage.Content>
    </ContentPage>
    ```

1. <span data-ttu-id="bebab-105">Ouvrez **CalendarPage.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="bebab-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="bebab-106">Ajoutez la fonction suivante à la `CalendarPage` classe pour obtenir les événements de l’utilisateur et afficher la réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="bebab-106">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get the events
        var events = await App.GraphClient.Me.Events.Request()
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        // Temporary
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    <span data-ttu-id="bebab-107">Examinez le contenu du `OnAppearing` code.</span><span class="sxs-lookup"><span data-stu-id="bebab-107">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="bebab-108">L’URL qui sera appelée est `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="bebab-108">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="bebab-109">La fonction `Select` limite les champs renvoyés pour chaque événement à ceux que la vue utilise réellement.</span><span class="sxs-lookup"><span data-stu-id="bebab-109">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="bebab-110">La fonction `OrderBy` trie les résultats en fonction de la date et de l’heure de création, avec l’élément le plus récent en premier.</span><span class="sxs-lookup"><span data-stu-id="bebab-110">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="bebab-111">Exécutez l’application, connectez-vous, puis cliquez sur l’élément de navigation **calendrier** dans le menu.</span><span class="sxs-lookup"><span data-stu-id="bebab-111">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="bebab-112">Vous devriez voir un dump JSON des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="bebab-112">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="bebab-113">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="bebab-113">Display the results</span></span>

<span data-ttu-id="bebab-114">À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="bebab-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="bebab-115">Commencez par créer un [convertisseur de valeur de liaison](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) pour convertir les valeurs [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) renvoyées par Microsoft Graph dans les formats de date et d’heure attendus par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="bebab-115">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="bebab-116">Cliquez avec le bouton droit sur le dossier des **modèles** dans le projet **GraphTutorial** et sélectionnez **Ajouter**, puis **classe...**. Nommez la `GraphDateTimeTimeZoneConverter` classe et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="bebab-116">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="bebab-117">Remplacez tout le contenu du fichier par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="bebab-117">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="bebab-118">Remplacez l’intégralité du contenu de **CalendarPage. Xaml** par le code suivant.</span><span class="sxs-lookup"><span data-stu-id="bebab-118">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    <span data-ttu-id="bebab-119">Cela remplace le `Editor` par un `ListView`.</span><span class="sxs-lookup"><span data-stu-id="bebab-119">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="bebab-120">Le `DataTemplate` utilisé pour afficher chaque élément utilise l `GraphDateTimeTimeZoneConverter` 'pour convertir `Start` les `End` propriétés et de l’événement en chaîne.</span><span class="sxs-lookup"><span data-stu-id="bebab-120">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="bebab-121">Ouvrez **CalendarPage.Xaml.cs** et supprimez les lignes suivantes de `OnAppearing` la fonction.</span><span class="sxs-lookup"><span data-stu-id="bebab-121">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="bebab-122">À leur place, ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="bebab-122">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="bebab-123">Exécutez l’application, connectez-vous, puis cliquez sur l’élément de navigation **calendrier** .</span><span class="sxs-lookup"><span data-stu-id="bebab-123">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="bebab-124">Vous devriez voir la liste des événements dont les valeurs de **début** et de **fin** sont mises en forme.</span><span class="sxs-lookup"><span data-stu-id="bebab-124">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![Capture d’écran du tableau des événements](./images/calendar-page.png)
