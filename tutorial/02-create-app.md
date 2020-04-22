<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="2608b-101">Ouvrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="2608b-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="2608b-102">Dans la boîte de dialogue **créer un nouveau projet** , choisissez **application mobile (Xamarin. Forms)**, puis cliquez sur **suivant**.</span><span class="sxs-lookup"><span data-stu-id="2608b-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Boîte de dialogue créer un nouveau projet dans Visual Studio 2019](images/new-project-dialog.png)

1. <span data-ttu-id="2608b-104">Dans la boîte de dialogue **configurer un nouveau projet** , entrez `GraphTutorial` le **nom du projet** et le nom de la **solution**, puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="2608b-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="2608b-105">Assurez-vous d’entrer exactement le même nom pour le projet Visual Studio spécifié dans ces instructions d’atelier.</span><span class="sxs-lookup"><span data-stu-id="2608b-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="2608b-106">Le nom du projet Visual Studio fait alors partie de l’espace de noms dans le code.</span><span class="sxs-lookup"><span data-stu-id="2608b-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="2608b-107">Le code figurant dans ces instructions dépend de l’espace de noms qui correspond au nom de projet Visual Studio spécifié dans celles-ci.</span><span class="sxs-lookup"><span data-stu-id="2608b-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="2608b-108">Si vous utilisez un autre nom de projet, celui-ci n’est pas compilé sauf si vous ajustez tous les espaces de noms pour qu’ils correspondent au nom de projet Visual Studio saisi à la création du projet.</span><span class="sxs-lookup"><span data-stu-id="2608b-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Boîte de dialogue Configurer un nouveau projet de Visual Studio 2019](images/configure-new-project-dialog.png)

1. <span data-ttu-id="2608b-110">Dans la boîte de dialogue **nouvelle application** multiplateforme, sélectionnez le modèle **vierge** , puis sélectionnez les plateformes que vous souhaitez créer sous **plateformes**.</span><span class="sxs-lookup"><span data-stu-id="2608b-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="2608b-111">Sélectionnez **OK** pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="2608b-111">Select **OK** to create the solution.</span></span>

    ![Nouvelle boîte de dialogue d’application multiplateforme Visual Studio 2019](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="2608b-113">Installer des packages</span><span class="sxs-lookup"><span data-stu-id="2608b-113">Install packages</span></span>

<span data-ttu-id="2608b-114">Avant de poursuivre, installez des packages NuGet supplémentaires que vous utiliserez plus tard.</span><span class="sxs-lookup"><span data-stu-id="2608b-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="2608b-115">[Microsoft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client/) pour gérer l’authentification Azure ad et la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="2608b-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="2608b-116">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) pour effectuer des appels à Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="2608b-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

1. <span data-ttu-id="2608b-117">Sélectionnez **Outils > Gestionnaire de package NuGet > Console Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="2608b-117">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="2608b-118">Dans le menu Console du Gestionnaire de package, saisissez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="2608b-118">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.10.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 3.0.1 -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="2608b-119">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="2608b-119">Design the app</span></span>

<span data-ttu-id="2608b-120">Commencez par mettre à jour `App` la classe pour ajouter des variables afin de suivre l’état d’authentification et l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="2608b-120">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="2608b-121">Dans l' **Explorateur de solutions**, développez le projet **GraphTutorial** , puis développez le fichier **app. Xaml** .</span><span class="sxs-lookup"><span data-stu-id="2608b-121">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="2608b-122">Ouvrez le fichier **app.Xaml.cs** et ajoutez les instructions `using` suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="2608b-122">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="2608b-123">Ajoutez l' `INotifyPropertyChanged` interface à la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="2608b-123">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="2608b-124">Ajoutez les propriétés suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="2608b-124">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="2608b-125">Ajoutez les fonctions suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="2608b-125">Add the following functions to the `App` class.</span></span> <span data-ttu-id="2608b-126">Les `SignIn`fonctions `SignOut`, et `GetUserInfo` sont simplement des espaces réservés pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="2608b-126">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

    ```csharp
    public async Task SignIn()
    {
        await GetUserInfo();

        IsSignedIn = true;
    }

    public async Task SignOut()
    {
        UserPhoto = null;
        UserName = string.Empty;
        UserEmail = string.Empty;
        IsSignedIn = false;
    }

    private async Task GetUserInfo()
    {
        UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
        UserName = "Adele Vance";
        UserEmail = "adelev@contoso.com";
    }

    private Stream GetUserPhoto()
    {
        // Return the default photo
        return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
    }
    ```

1. <span data-ttu-id="2608b-127">La `GetUserPhoto` fonction renvoie une photo par défaut pour le moment.</span><span class="sxs-lookup"><span data-stu-id="2608b-127">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="2608b-128">Vous pouvez fournir votre propre fichier ou télécharger celui utilisé dans l’exemple à partir de [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span><span class="sxs-lookup"><span data-stu-id="2608b-128">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="2608b-129">Si vous utilisez votre propre fichier, renommez-le en **no-Profile-pic. png**.</span><span class="sxs-lookup"><span data-stu-id="2608b-129">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="2608b-130">Copiez le fichier dans le répertoire **./GraphTutorial/GraphTutorial** .</span><span class="sxs-lookup"><span data-stu-id="2608b-130">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="2608b-131">Cliquez avec le bouton droit sur le fichier dans l' **Explorateur de solutions** , puis sélectionnez **Propriétés**.</span><span class="sxs-lookup"><span data-stu-id="2608b-131">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="2608b-132">Dans la fenêtre **Propriétés** , modifiez l’action de **génération** en **ressource incorporée**.</span><span class="sxs-lookup"><span data-stu-id="2608b-132">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![Capture d’écran de la fenêtre Propriétés pour le fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="2608b-134">Navigation de l’application</span><span class="sxs-lookup"><span data-stu-id="2608b-134">App navigation</span></span>

<span data-ttu-id="2608b-135">Dans cette section, vous allez remplacer la page principale de l’application par une [page maître/détails](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span><span class="sxs-lookup"><span data-stu-id="2608b-135">In this section, you'll change the application's main page to a [Master-Detail page](/xamarin/xamarin-forms/app-fundamentals/navigation/master-detail-page).</span></span> <span data-ttu-id="2608b-136">Cela permet de fournir un menu de navigation permettant de basculer entre l’affichage dans l’application.</span><span class="sxs-lookup"><span data-stu-id="2608b-136">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="2608b-137">Ouvrez le fichier **MainPage. Xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="2608b-137">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a><span data-ttu-id="2608b-138">Implémenter le menu</span><span class="sxs-lookup"><span data-stu-id="2608b-138">Implement the menu</span></span>

1. <span data-ttu-id="2608b-139">Cliquez avec le bouton droit sur le projet **GraphTutorial** et sélectionnez **Ajouter**, puis **nouveau dossier**.</span><span class="sxs-lookup"><span data-stu-id="2608b-139">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="2608b-140">Nommer le dossier `Models`.</span><span class="sxs-lookup"><span data-stu-id="2608b-140">Name the folder `Models`.</span></span>

1. <span data-ttu-id="2608b-141">Cliquez avec le bouton droit sur le dossier **modèles** , sélectionnez **Ajouter**, puis **classe..**.. Nommez la `NavMenuItem` classe et sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="2608b-141">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="2608b-142">Ouvrez le fichier **NavMenuItem.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="2608b-142">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="2608b-143">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `MenuPage`la page.</span><span class="sxs-lookup"><span data-stu-id="2608b-143">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="2608b-144">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="2608b-144">Select **Add**.</span></span>

1. <span data-ttu-id="2608b-145">Ouvrez le fichier **MenuPage. Xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="2608b-145">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. <span data-ttu-id="2608b-146">Développez **MenuPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MenuPage.Xaml.cs** .</span><span class="sxs-lookup"><span data-stu-id="2608b-146">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="2608b-147">Remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="2608b-147">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="2608b-148">Visual Studio signale les erreurs dans **MenuPage.Xaml.cs**.</span><span class="sxs-lookup"><span data-stu-id="2608b-148">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="2608b-149">Ces erreurs seront résolues au cours d’une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="2608b-149">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="2608b-150">Implémenter la page d’accueil</span><span class="sxs-lookup"><span data-stu-id="2608b-150">Implement the welcome page</span></span>

1. <span data-ttu-id="2608b-151">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `WelcomePage`la page.</span><span class="sxs-lookup"><span data-stu-id="2608b-151">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="2608b-152">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="2608b-152">Select **Add**.</span></span> <span data-ttu-id="2608b-153">Ouvrez le fichier **WelcomePage. Xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="2608b-153">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. <span data-ttu-id="2608b-154">Développez **WelcomePage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **WelcomePage.Xaml.cs** .</span><span class="sxs-lookup"><span data-stu-id="2608b-154">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="2608b-155">Ajoutez la fonction suivante à la classe `WelcomePage`.</span><span class="sxs-lookup"><span data-stu-id="2608b-155">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-page"></a><span data-ttu-id="2608b-156">Ajouter une page de calendrier</span><span class="sxs-lookup"><span data-stu-id="2608b-156">Add calendar page</span></span>

<span data-ttu-id="2608b-157">Maintenant, ajoutez une page de calendrier.</span><span class="sxs-lookup"><span data-stu-id="2608b-157">Now add a calendar page.</span></span> <span data-ttu-id="2608b-158">Il s’agit uniquement d’un espace réservé pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="2608b-158">This will just be a placeholder for now.</span></span>

1. <span data-ttu-id="2608b-159">Cliquez avec le bouton droit sur le projet **GraphTutorial** , puis sélectionnez **Ajouter**, puis **nouvel élément..**.. Choisissez **page de contenu** et nommez `CalendarPage`la page.</span><span class="sxs-lookup"><span data-stu-id="2608b-159">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="2608b-160">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="2608b-160">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="2608b-161">Mettre à jour le code MainPage-behind</span><span class="sxs-lookup"><span data-stu-id="2608b-161">Update MainPage code-behind</span></span>

<span data-ttu-id="2608b-162">Maintenant que toutes les pages sont en place, mettez à jour le code-behind pour **MainPage. Xaml**.</span><span class="sxs-lookup"><span data-stu-id="2608b-162">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="2608b-163">Développez **MainPage. Xaml** dans l' **Explorateur de solutions** et ouvrez le fichier **MainPage.Xaml.cs** et remplacez l’intégralité de son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="2608b-163">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="2608b-164">Enregistrez toutes vos modifications.</span><span class="sxs-lookup"><span data-stu-id="2608b-164">Save all of your changes.</span></span> <span data-ttu-id="2608b-165">Cliquez avec le bouton droit sur le projet que vous souhaitez exécuter (Android, iOS ou UWP) et sélectionnez **définir comme projet de démarrage**.</span><span class="sxs-lookup"><span data-stu-id="2608b-165">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="2608b-166">Appuyez sur **F5** ou sélectionnez **déboguer > démarrer le débogage** dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2608b-166">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![Captures d’écran des versions Android, iOS et UWP de l’application](./images/welcome-page.png)
