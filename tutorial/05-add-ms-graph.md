<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6cf17-101">Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application.</span><span class="sxs-lookup"><span data-stu-id="6cf17-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="6cf17-102">Pour cette application, vous allez utiliser la bibliothèque [cliente Microsoft Graph pour .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="6cf17-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="6cf17-103">Récupérer les événements de calendrier à partir d’Outlook</span><span class="sxs-lookup"><span data-stu-id="6cf17-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="6cf17-104">Ouvrez **CalendarPage.xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="6cf17-104">Open **CalendarPage.xaml** in the **GraphTutorial** project and replace its contents with the following.</span></span>

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

1. <span data-ttu-id="6cf17-105">Ouvrez **CalendarPage.xaml.cs** et ajoutez les `using` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="6cf17-105">Open **CalendarPage.xaml.cs** and add the following `using` statements at the top of the file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. <span data-ttu-id="6cf17-106">Ajoutez la fonction suivante à la classe pour obtenir le début de la semaine en cours dans le `CalendarPage` fuseau horaire de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6cf17-106">Add the following function to the `CalendarPage` class to get the start of the current week in the user's time zone.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml.cs" id="GetStartOfWeekSnippet":::

1. <span data-ttu-id="6cf17-107">Ajoutez la fonction suivante à la classe pour obtenir les `CalendarPage` événements de l’utilisateur et afficher la réponse JSON.</span><span class="sxs-lookup"><span data-stu-id="6cf17-107">Add the following function to the `CalendarPage` class to get the user's events and display the JSON response.</span></span>

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        // Get start and end of week in user's time zone
        var startOfWeek = GetUtcStartOfWeekInTimeZone(DateTime.Today, App.UserTimeZone);
        var endOfWeek = startOfWeek.AddDays(7);

        var queryOptions = new List<QueryOption>
        {
            new QueryOption("startDateTime", startOfWeek.ToString("o")),
            new QueryOption("endDateTime", endOfWeek.ToString("o"))
        };

        // Get the events
        var events = await App.GraphClient.Me.CalendarView.Request(queryOptions)
            .Header("Prefer", $"outlook.timezone=\"{App.UserTimeZone.DisplayName}\"")
            .Select(e => new
            {
                e.Subject,
                e.Organizer,
                e.Start,
                e.End
            })
            .OrderBy("start/DateTime")
            .Top(50)
            .GetAsync();

        // Temporary
        JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    }
    ```

    <span data-ttu-id="6cf17-108">Réfléchissez à ce que fait `OnAppearing` le code.</span><span class="sxs-lookup"><span data-stu-id="6cf17-108">Consider what the code in `OnAppearing` is doing.</span></span>

    - <span data-ttu-id="6cf17-109">L’URL qui sera appelée est `/v1.0/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="6cf17-109">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="6cf17-110">Les `startDateTime` `endDateTime` paramètres définissent le début et la fin de l’affichage Calendrier.</span><span class="sxs-lookup"><span data-stu-id="6cf17-110">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
        - <span data-ttu-id="6cf17-111">`Prefer: outlook.timezone`L’en-tête entraîne le retour des événements dans `start` le fuseau horaire de `end` l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6cf17-111">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
        - <span data-ttu-id="6cf17-112">La fonction limite les champs renvoyés pour chaque événement à ceux que `Select` l’application utilisera réellement.</span><span class="sxs-lookup"><span data-stu-id="6cf17-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
        - <span data-ttu-id="6cf17-113">La `OrderBy` fonction trie les résultats par date et heure de début.</span><span class="sxs-lookup"><span data-stu-id="6cf17-113">The `OrderBy` function sorts the results by the start date and time.</span></span>
        - <span data-ttu-id="6cf17-114">La `Top` fonction demande au maximum 50 événements.</span><span class="sxs-lookup"><span data-stu-id="6cf17-114">The `Top` function requests at most 50 events.</span></span>

1. <span data-ttu-id="6cf17-115">Exécutez l’application, connectez-vous et cliquez **sur** l’élément de navigation Calendrier dans le menu.</span><span class="sxs-lookup"><span data-stu-id="6cf17-115">Run the app, sign in, and click the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="6cf17-116">Vous devriez voir un vidage JSON des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="6cf17-116">You should see a JSON dump of the events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="6cf17-117">Afficher les résultats</span><span class="sxs-lookup"><span data-stu-id="6cf17-117">Display the results</span></span>

<span data-ttu-id="6cf17-118">Vous pouvez désormais remplacer le vidage JSON par un autre qui permet d’afficher les résultats de manière conviviale.</span><span class="sxs-lookup"><span data-stu-id="6cf17-118">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span>

<span data-ttu-id="6cf17-119">Commencez par créer un [convertisseur](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) de valeur de liaison pour convertir les valeurs [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) renvoyées par Microsoft Graph dans les formats de date et d’heure que l’utilisateur attend.</span><span class="sxs-lookup"><span data-stu-id="6cf17-119">Start by creating a [binding value converter](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) to convert the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) values returned by Microsoft Graph into the date and time formats the user expects.</span></span>

1. <span data-ttu-id="6cf17-120">Cliquez avec le bouton droit sur le dossier **Modèles** dans le projet **GraphTutorial** et sélectionnez **Ajouter,** puis **Classe...**. Nommez la classe `GraphDateTimeTimeZoneConverter` et sélectionnez **Ajouter.**</span><span class="sxs-lookup"><span data-stu-id="6cf17-120">Right-click the **Models** folder in the **GraphTutorial** project and select **Add**, then **Class...**. Name the class `GraphDateTimeTimeZoneConverter` and select **Add**.</span></span>

1. <span data-ttu-id="6cf17-121">Remplacez tout le contenu du fichier par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="6cf17-121">Replace the entire contents of the file with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. <span data-ttu-id="6cf17-122">Remplacez tout le contenu **de CalendarPage.xaml** par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="6cf17-122">Replace the entire contents of **CalendarPage.xaml** with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    <span data-ttu-id="6cf17-123">Cela remplace le `Editor` par `ListView` un .</span><span class="sxs-lookup"><span data-stu-id="6cf17-123">This replaces the `Editor` with a `ListView`.</span></span> <span data-ttu-id="6cf17-124">`DataTemplate`L’élément utilisé pour restituer chaque élément utilise pour convertir les propriétés et `GraphDateTimeTimeZoneConverter` les `Start` `End` propriétés de l’événement en chaîne.</span><span class="sxs-lookup"><span data-stu-id="6cf17-124">The `DataTemplate` used to render each item uses the `GraphDateTimeTimeZoneConverter` to convert the `Start` and `End` properties of the event to a string.</span></span>

1. <span data-ttu-id="6cf17-125">Ouvrez **CalendarPage.xaml.cs** et supprimez les lignes suivantes de la `OnAppearing` fonction.</span><span class="sxs-lookup"><span data-stu-id="6cf17-125">Open **CalendarPage.xaml.cs** and remove the following lines from the `OnAppearing` function.</span></span>

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. <span data-ttu-id="6cf17-126">À la place, ajoutez le code suivant.</span><span class="sxs-lookup"><span data-stu-id="6cf17-126">In their place, add the following code.</span></span>

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. <span data-ttu-id="6cf17-127">Exécutez l’application, connectez-vous et cliquez sur **l’élément de** navigation Calendrier.</span><span class="sxs-lookup"><span data-stu-id="6cf17-127">Run the app, sign in, and click the **Calendar** navigation item.</span></span> <span data-ttu-id="6cf17-128">Vous devez voir la liste des événements avec les valeurs **Début** **et** Fin formatées.</span><span class="sxs-lookup"><span data-stu-id="6cf17-128">You should see the list of events with the **Start** and **End** values formatted.</span></span>

    ![Capture d’écran du tableau des événements](./images/calendar-page.png)
