<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la bibliothèque [cliente Microsoft Graph pour .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Ouvrez **CalendarPage.xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.

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

1. Ouvrez **CalendarPage.xaml.cs** et ajoutez les `using` instructions suivantes en haut du fichier.

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. Ajoutez la fonction suivante à la classe pour obtenir les `CalendarPage` événements de l’utilisateur et afficher la réponse JSON.

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

    Réfléchissez à ce que fait `OnAppearing` le code.

    - L’URL qui sera appelée est `/v1.0/me/calendarview`.
        - Les `startDateTime` `endDateTime` paramètres et les paramètres définissent le début et la fin de l’affichage Calendrier.
        - `Prefer: outlook.timezone`L’en-tête entraîne le retour des événements dans `start` le fuseau horaire de `end` l’utilisateur.
        - La fonction limite les champs renvoyés pour chaque événement à ceux que `Select` l’application utilisera réellement.
        - La `OrderBy` fonction trie les résultats par date et heure de début.
        - La `Top` fonction demande au maximum 50 événements.

1. Exécutez l’application, connectez-vous et cliquez **sur** l’élément de navigation Calendrier dans le menu. Vous devriez voir un vidage JSON des événements sur le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

Vous pouvez désormais remplacer le vidage JSON par un autre qui permet d’afficher les résultats de manière conviviale.

Commencez par créer un [convertisseur](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) de valeur de liaison pour convertir les valeurs [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) renvoyées par Microsoft Graph dans les formats de date et d’heure que l’utilisateur attend.

1. Cliquez avec le bouton droit sur le dossier **Modèles** dans le projet **GraphTutorial** et sélectionnez **Ajouter,** puis **Classe...**. Nommez la classe `GraphDateTimeTimeZoneConverter` et sélectionnez **Ajouter.**

1. Remplacez tout le contenu du fichier par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. Remplacez tout le contenu **de CalendarPage.xaml** par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml" id="CalendarPageXamlSnippet":::

    Cela remplace le `Editor` par `ListView` un . `DataTemplate`L’élément utilisé pour restituer chaque élément utilise pour convertir les propriétés et `GraphDateTimeTimeZoneConverter` les `Start` `End` propriétés de l’événement en chaîne.

1. Ouvrez **CalendarPage.xaml.cs** et supprimez les lignes suivantes de la `OnAppearing` fonction.

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. À la place, ajoutez le code suivant.

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. Exécutez l’application, connectez-vous et cliquez sur **l’élément de** navigation Calendrier. Vous devez voir la liste des événements avec les valeurs **Début** **et** Fin formatées.

    ![Capture d’écran du tableau des événements](./images/calendar-page.png)
