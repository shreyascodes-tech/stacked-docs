---
id: stacked-cli
sidebar_label: "Stacked CLI"
sidebar_position: 5
---


# Stacked CLI [![Pub Version](https://img.shields.io/pub/v/stacked_cli)](https://pub.dev/packages/stacked_cli)

The Stacked CLI is a part of the `stacked_cli` package. The CLI was created to speed up development using the Stacked framework.


## Getting Started

To get started, install the `stacked_cli` package on your machine:

```shell
dart pub global activate stacked_cli
```

### Creating a Stacked App

To create your first Stacked app, all you need to do is run:

```shell
stacked create app my_app
```

This will create a new flutter app based on your flutter version, add all the code required for a Stacked app and generate the required code. When the command completes navigate into the my_app folder and run the app.

### Add a New View

From the root folder of your Stacked application, run the command:

```shell
stacked create view login
```

This will create a new View called `LoginView` with its associated ViewModel in the `ui/views` folder. This will also create the ViewModel tests file, as well as add the View to the available routes in `app.dart` file.

### Add a New Service

From the root folder of your Stacked application, run the command:

```shell
stacked create service stripe
```

This will create a new Service called `StripeService` in the `services` folder and add it to the dependencies in the `app.dart` file.

### Generate Stacked Code

When you've changed something manually, or added a new model, instead of executing the command `flutter pub run build_runner build --delete-conflicting-outputs` you can simply run `stacked generate`.

### Update CLI

When you want to update a `stacked_cli` app, instead of executing the command `dart pub global activate stacked_cli` you can simply run `stacked update`.


## Use the CLI with Existing Project

Using the Stacked CLI with an existing project takes a little bit more effort than above. There are a few things that you have to ensure:

1. You need to have your app file in `lib/app/app.dart`
2. If you have tests setup as presented in the [unit testing videos](https://youtu.be/5BFlo9k3KNU) your helper file should be `test/helpers/test_helpers.dart`
3. You tell Stacked where to make the modifications

The way we know where to add modifications into your code is by reading what we call a **template identifier**. This tells our tools, "At this position you can make a modification". For now, we only have two scaffolding commands:

### Create View

This command creates all the scaffolding to add a new View into the project:

1. Creates a new folder with the View name in `lib/ui/views/`
2. Creates the new View and ViewModel files in `lib/ui/views/view_name/`
3. Creates the ViewModel tests file in the `test/viewmodel_tests/` folder
4. Adds the route to the `lib/app/app.dart` file

For us to achieve #4, we need to know where to add the route and its import. To indicate that we use the **template identifiers**, open your `lib/app/app.dart` file and under the last import, add:

```dart
// @stacked-import
```

And underneath your last route add:

```dart
// @stacked-route
```

Now if you run `stacked create view profile` you'll see that all the files are generated AND we also add the route into your `app.dart` file. The modifications are optional so if you don't have the template identifiers, Stacked will still generate the necessary files but won't automatically add the route to your `app.dart` file. Everything else will still work though.

### Create Service

This command creates all the scaffolding to add a new Service into the project:

1. Creates a new Service file in `lib/services/`
2. Creates the unit tests file in `test/services/`
3. Registers the Service with your `StackedApp`
4. Adds the Service Mock into the test_helpers and registers it

For us to achieve #3, we need to know where to add the dependency registration. Open your `lib/app/app.dart` file and under the last dependency registration, add:

```dart
// @stacked-service
```

For us to achieve #4, we need to know where to add the Service Mock. Open your `test_helpers.dart` file and under all the imports, add:

```dart
// @stacked-import
```

Under your last `MockSpec<T>()`, add:

```dart
// @stacked-mock-spec
```

Under your last `getAndRegisterService` that creates a Mock and returns it, add:

```dart
// @stacked-mock-create
```

Under your last Service registration in `registerServices`, add:

```dart
// @stacked-mock-register
```

Now, when you run `stacked create service user`, you'll see the files created for the `UserService` and all the registration will happen automatically.


## Config

If you want to use `stacked_cli` in a package that doesn't fit the structure that the CLI expects, then you can configure Stacked to look in the correct places. Create a new file in the root folder of your package called `stacked.json`. Inside the file, create a JSON body with the following properties:

- `views_path`: The relative path where Views and ViewModels will be generated. The default value is: `ui/views`
- `services_path`: The relative path where Services will be generated. The default value is: `services`
- `stacked_app_path`: The relative path to the file that contains the `StackedApp` setup. The default value is: `app/app.dart`
- `test_helpers_path`: The relative path to the file that contains the test_helpers (mocks, registerService, etc). Default: `helpers/test_helpers.dart`
- `test_services_path`: The relative path to where the Services' unit tests will be generated. Default: `services`
- `test_views_path`: The relative path to where the ViewModels' unit tests will be generated. Default: `viewmodels`
- `locator_name`: The name of the locator that Services are registered with. This is used when creating a new Service using the `create service` command. Default: `locator`
- `register_mocks_function`: The name of the function that registers all the Mocks when running a test. This is used when generating a test file during `create service` command. Default: `registerServices`

Only include the paths you want to customize. If you exclude a path, the default value will be used for it.

### Example

```json
{
    "stacked_app_file_path" : "app/app.dart",
    "services_path" : "services",
    "views_path" : "ui/views",
    "test_helpers_file_path" : "helpers/test_helpers.dart",
    "test_services_path" : "services",
    "test_views_path" : "viewmodels",
    "locator_name" : "locator",
    "register_mocks_function" : "registerServices",
    "v1": false, // Indicates whether you want to use ViewModelBuilder(v1) or the the new StackedView (v2)
    "line_length": 80 // Passed into the flutter formatter when running CLI commands
}
```