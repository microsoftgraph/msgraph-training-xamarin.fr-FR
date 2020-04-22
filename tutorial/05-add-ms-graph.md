<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la [bibliothèque cliente Microsoft Graph pour .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Récupérer les événements de calendrier à partir d’Outlook

1. Ouvrez **CalendarPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.

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

1. Ouvrez **CalendarPage.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.

    ```csharp
    using Microsoft.Graph;
    using Newtonsoft.Json;
    using System.Collections.ObjectModel;
    using System.ComponentModel;
    ```

1. Ajoutez la fonction suivante à la `CalendarPage` classe pour obtenir les événements de l’utilisateur et afficher la réponse JSON.

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

    Examinez le contenu du `OnAppearing` code.

    - L’URL qui sera appelée est `/v1.0/me/events`.
    - La fonction `Select` limite les champs renvoyés pour chaque événement à ceux que la vue utilise réellement.
    - La fonction `OrderBy` trie les résultats en fonction de la date et de l’heure de création, avec l’élément le plus récent en premier.

1. Exécutez l’application, connectez-vous, puis cliquez sur l’élément de navigation **calendrier** dans le menu. Vous devriez voir un dump JSON des événements sur le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale.

Commencez par créer un [convertisseur de valeur de liaison](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) pour convertir les valeurs [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) renvoyées par Microsoft Graph dans les formats de date et d’heure attendus par l’utilisateur.

1. Cliquez avec le bouton droit sur le dossier des **modèles** dans le projet **GraphTutorial** et sélectionnez **Ajouter**, puis **classe...**. Nommez la `GraphDateTimeTimeZoneConverter` classe et sélectionnez **Ajouter**.

1. Remplacez tout le contenu du fichier par ce qui suit.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/GraphDateTimeTimeZoneConverter.cs" id="DateTimeConverterSnippet":::

1. Remplacez l’intégralité du contenu de **CalendarPage. Xaml** par le code suivant.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/CalendarPage.xaml":::

    Cela remplace le `Editor` par un `ListView`. Le `DataTemplate` utilisé pour afficher chaque élément utilise l `GraphDateTimeTimeZoneConverter` 'pour convertir `Start` les `End` propriétés et de l’événement en chaîne.

1. Ouvrez **CalendarPage.Xaml.cs** et supprimez les lignes suivantes de `OnAppearing` la fonction.

    ```csharp
    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
    ```

1. À leur place, ajoutez le code suivant.

    ```csharp
    // Add the events to the list view
    CalendarList.ItemsSource = events.CurrentPage.ToList();
    ```

1. Exécutez l’application, connectez-vous, puis cliquez sur l’élément de navigation **calendrier** . Vous devriez voir la liste des événements dont les valeurs de **début** et de **fin** sont mises en forme.

    ![Capture d’écran du tableau des événements](./images/calendar-page.png)
