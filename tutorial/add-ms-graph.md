<!-- markdownlint-disable MD002 MD041 -->

Dans cet exercice, vous allez incorporer Microsoft Graph dans l’application. Pour cette application, vous allez utiliser la [bibliothèque cliente Microsoft Graph pour .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) pour effectuer des appels à Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtenir des événements de calendrier à partir d’Outlook

Commencez par mettre à jour le fichier **CalendarPage. Xaml** dans le projet **GraphTutorial** . Ouvrez le fichier et remplacez son contenu par ce qui suit.

```xml
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

Ouvrez `CalendarPage.xaml.cs` et ajoutez les instructions `using` suivantes en haut du fichier.

```cs
using Newtonsoft.Json;
using Microsoft.Graph;
using System.ComponentModel;
using System.Collections.ObjectModel;
```

Ajoutez ensuite le code suivant pour obtenir les événements de l’utilisateur et afficher la réponse JSON.

```cs
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Get the events
    var events = await App.GraphClient.Me.Events.Request()
        .Select("subject,organizer,start,end")
        .OrderBy("createdDateTime DESC")
        .GetAsync();

    // Temporary
    JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
}
```

Examinez le contenu du `OnAppearing` code.

- L’URL qui sera appelée est `/v1.0/me/events`.
- La `Select` fonction limite les champs renvoyés pour chaque événement à ceux que l’affichage utilise réellement.
- La `OrderBy` fonction trie les résultats en fonction de la date et de l’heure de leur création, avec l’élément le plus récent en premier.

Vous pouvez maintenant exécuter l’application, vous connecter et cliquer sur l’élément de navigation **calendrier** dans le menu. Vous devriez voir un dump JSON des événements sur le calendrier de l’utilisateur.

## <a name="display-the-results"></a>Afficher les résultats

À présent, vous pouvez remplacer le vidage JSON par un texte pour afficher les résultats de manière conviviale. Commencez par créer un [convertisseur de valeur de liaison](/xamarin/xamarin-forms/xaml/xaml-basics/data-binding-basics#binding-value-converters) pour convertir les valeurs [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) renvoyées par Microsoft Graph dans les formats de date et d’heure attendus par l’utilisateur. Cliquez avec le bouton **** droit sur le dossier Models dans le projet **GraphTutorial** , puis choisissez **Add**, **Class..**.. Nommez la `GraphDateTimeTimeZoneConverter` classe, puis choisissez **Ajouter**. Remplacez tout le contenu du fichier par ce qui suit.

```cs
using Microsoft.Graph;
using System;
using System.Globalization;
using Xamarin.Forms;

namespace GraphTutorial.Models
{
    class GraphDateTimeTimeZoneConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
        {
            if (value is DateTimeTimeZone date)
            {
                // Resolve the time zone
                var timezone = TimeZoneInfo.FindSystemTimeZoneById(date.TimeZone);
                // Parse method assumes local time, which may not be the case
                var parsedDateAsLocal = DateTimeOffset.Parse(date.DateTime);
                // Determine the offset from UTC time for the specific date
                // Making this call adjusts for DST as appropriate
                var tzOffset = timezone.GetUtcOffset(parsedDateAsLocal.DateTime);
                // Create a new DateTimeOffset with the specific offset from UTC
                var correctedDate = new DateTimeOffset(parsedDateAsLocal.DateTime, tzOffset);
                // Return the local date time string
                return $"{correctedDate.LocalDateTime.ToShortDateString()} {correctedDate.LocalDateTime.ToShortTimeString()}";
            }

            return string.Empty;
        }

        public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
}
```

Ensuite, remplacez tout le contenu de `CalendarPage.xaml` par par ce qui suit.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:GraphTutorial.Models"
             Title="Calendar"
             x:Class="GraphTutorial.CalendarPage">
    <ContentPage.Resources>
        <local:GraphDateTimeTimeZoneConverter x:Key="DateConverter" />
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout>
            <ListView x:Name="CalendarList"
                      HasUnevenRows="true"
                      Margin="10,10,10,10">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Margin="10,10,10,10">
                                <Label Text="{Binding Path=Subject}"
                                       FontAttributes="Bold"
                                       FontSize="Medium" />
                                <Label Text="{Binding Path=Organizer.EmailAddress.Name}"
                                       FontSize="Small" />
                                <StackLayout Orientation="Horizontal">
                                    <Label Text="{Binding Path=Start, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                    <Label Text="to"
                                           FontSize="Micro" />
                                    <Label Text="{Binding Path=End, Converter={StaticResource DateConverter}}"
                                       FontSize="Micro" />
                                </StackLayout>
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Cela remplace le `Editor` par un `ListView`. Le `DataTemplate` utilisé pour afficher chaque élément utilise l `GraphDateTimeTimeZoneConverter` 'pour convertir `Start` les `End` propriétés et de l’événement en chaîne. Maintenant, `CalendarPage.xaml.cs` ouvrez et supprimez les lignes suivantes `OnAppearing` de la fonction.

```cs
// Temporary
JSONResponse.Text = JsonConvert.SerializeObject(events.CurrentPage, Formatting.Indented);
```

À leur place, ajoutez le code suivant.

```cs
// Add the events to the list view
CalendarList.ItemsSource = events.CurrentPage.ToList();
```

Exécutez l’application, connectez-vous, puis cliquez sur l’élément de navigation **calendrier** . Vous devriez voir la liste des événements dont les valeurs de **début** et de **fin** sont mises en forme.

![Capture d’écran du tableau des événements](./images/calendar-page.png)
