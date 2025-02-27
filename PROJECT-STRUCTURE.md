# Flutter Project Structure

## Introduction

This project follows **BLoC (Business Logic Component) architecture** and adheres to **Flutter’s recommended Clean Architecture**, ensuring **scalability**, **maintainability**, and **testability**. Below is a detailed explanation of the directory structure.

## Directory Structure

```markdown
│──assets/
│  │── icons/
│  │   │── svg/
│  │   │── png/
│  │── images/
│  │── lottie/
│  │── vectors/
│──config/
│──fonts/
│──lib/
│  │── src/
│  │   │── app/
│  │   │   │── routes/
│  │   │   ├── app.dart
│  │   │── core/
│  │   │   ├── base/
│  │   │   ├── dependency/
│  │   │   ├── environment/
│  │   │   ├── logic/
│  │   │   ├── core.dart
│  │   │   ├── core_dependency.dart
│  │   │── data/
│  │   │   ├── model/
│  │   │   ├── repository/
│  │   │   ├── services/
│  │   │   ├── data.dart
│  │   │   ├── data_dependency.dart
│  │   │── presentation/
│  │   │   ├── ui/
│  │   │   ├── logic/
│  │   │   ├── components/
│  │   │   ├── resources/
│  │   │   ├── presentation.dart
│  │   │── utils/
│  │── bootstrap.dart
│  │── main.dart
│── analysis_options.yaml
│── build.yaml
│── pubspec.yaml
```

### Root Directories And Files

The root directories define the foundational structure of the Flutter project, managing assets, configurations, dependencies, and the main entry point of the application. Below is a detailed explanation of each root-level directory and file.

- **Overview**

```markdown
│── assets/
│── config/
│── fonts/
│── lib/
│── analysis_options.yaml
│── build.yaml
│── pubspec.yaml
```

1. **`assets/` (Static Resources)**
    
    The `assets/` directory stores all static assets required in the app, such as images, icons, animations, and vectors. These assets are referenced in Flutter widgets and are declared in `pubspec.yaml`.
    
    - Subdirectories
        - `icons/` → Contains app icons in different formats
        - `svg/` → Scalable Vector Graphics (SVG format)
        - `png/` → PNG image icons
        - `images/` → Stores static images used throughout the app
        - `lottie/` → Contains Lottie JSON animations for dynamic UI elements
        - `vectors/` → Holds vector graphics and illustrations

1. **`config/` (Configuration Files)**
    
    The `config/` directory is used for application-wide configuration settings, such as environment variables, API endpoints, and feature toggles. This helps separate environment-specific data from the main application logic.
    
    - Typical Files in `config/`
        - `config_dev.json` → Configurations for the development environment
        - `config_stag.json` → Configurations for the staging environment
        - `config_prod.json` → Configurations for the production environment

1. **`fonts/` (Custom Fonts)**
    
    The `fonts/` directory contains custom font files (TTF, OTF) used in the app. Fonts defined here must be registered in `pubspec.yaml` to be usable within the app.
    
    - Example `pubspec.yaml` Registration:
    
    ```yaml
    fonts:
    - family: Roboto
      fonts:
        - asset: assets/fonts/Roboto-Regular.ttf
        - asset: assets/fonts/Roboto-Bold.ttf
          weight: 700
    ```
    

1. **`lib/` (Main App Code)**
    
    The `lib/` directory contains all the Dart source code and follows a clean architecture structure using BLoC for state management.
    
    > This will be explained in detail in a separate section
    > 
    
2. **`analysis_options.yaml` (Code Linter Rules)**
    
    Defines Dart analysis rules for enforcing code quality, best practices, and style consistency. This file helps catch errors early and maintain a clean codebase.
    
    - Example Configuration:
    
    ```yaml
    include: package:flutter_lints/flutter.yaml
    
    linter:
      rules:
        prefer_const_constructors: true
        avoid_print: true
        unnecessary_null_checks: true
    ```
    
3. **`build.yaml` (Build Configuration)**
    
    This file defines custom build rules and configurations for Flutter’s build system, used for code generation and automation. It is mostly used for tools like `build_runner` in Dart.
    
    - Use Case Example:
        - Generating JSON models from annotations (`json_serializable`)
        - Generating dependency injection setup (`injectable`)
    
4. **`pubspec.yaml` (Package & Asset Management)**
    
    The `pubspec.yaml` file is the manifest file for Flutter projects. It manages dependencies, assets, fonts, and metadata for the project.
    
    - Example Sections in `pubspec.yaml`:
    
    ```yaml
    name: my_flutter_app
    description: A new Flutter project following clean architecture.
     
    dependencies:
      flutter:
        sdk: flutter
      flutter_bloc: ^8.1.2
      http: ^0.13.5
    
    dev_dependencies:
      flutter_lints: ^2.0.0
      build_runner: ^2.3.3
     
    flutter:
      assets:
        - assets/images/
        - assets/icons/svg/
    ```
    

> The root directories and files provide essential configuration, dependencies, and assets that define the app's structure.
> 

---

### `lib/` Directory Documentation

The `lib/` directory is the core of the Flutter application, containing all the Dart source code responsible for UI, business logic, and data management. It follows a clean architecture approach with BLoC for state management, ensuring scalability and maintainability.

- **Overview**

```markdown
│── lib/
│   │── src/  👈 (Main source code, covered separately)
│   │── bootstrap.dart
│   │── main.dart
```

1. **`main.dart` (Application Entry Point)**
    
    The `main.dart` file is the starting point of the Flutter application. It initializes dependencies, configurations, and runs the app.
    
    - Common Responsibilities:
        - Runs the Flutter application
        - Initializes dependency injection (DI)
        - Handles environment setup
    - Example `main.dart` Implementation:
    
    ```dart
    import 'package:flutter/material.dart';
    import 'bootstrap.dart';
    import 'src/app/app.dart';
    
    void main() {
      bootstrap(
        () => MyApp(),
      ); 
    }
    ```
    
2. **`bootstrap.dart` (App Initialization)**
    
    The `bootstrap.dart` file is responsible for setting up core dependencies and environment configurations before the app starts.
    
    - Common Responsibilities:
        - Initializes dependency injection
        - Configures BLoC observers (for debugging state changes)
        - Handles error reporting
    - Example `bootstrap.dart` Implementation:
    
    ```dart
    import 'package:flutter/material.dart';
    import 'src/core/dependency/dependency_injection.dart';
    import 'src/app/app.dart';
    
    typedef AsyncAppBuilder = FutureOr<Widget> Function();
    
    Future<void> bootstrap(AsyncAppBuilder builder) {
      return runZonedGuarded(
        () async {
          WidgetsFlutterBinding.ensureInitialized();
    
          final dependencyHelper = DependencyHelper.instance;
          await dependencyHelper.initialize();
          // initialize other services 
    
          final app = await builder();
          runApp(
            MultiRepositoryProvider(
              providers: [
                RepositoryProvider(create: (context) => AuthRepository()),
                // other repositories here
              ],
              child: MultiBlocProvider(
                providers: [
                  BlocProvider(create: (context) => AppSettingsBloc()),
                  // other global bloc providers here
                ],
                child: app,
              ),
            ),
          );
        },
        (error, stackTrace) {
          Log.debug(error);
          Log.debug(stackTrace);
        },
      );
    }
    ```
    

> The `lib/` directory serves as the foundation of the Flutter app. It contains:
> 
> - `main.dart` → Entry point of the app
> - `bootstrap.dart` → Initializes dependencies before app launch

---

### `src/` Directory Documentation

The `src/` directory is the main source code container of the application. It follows Flutter’s Clean Architecture with BLoC (Business Logic Component) for state management, ensuring a well-organized, scalable, and maintainable codebase.

- **Overview**

```markdown
│── src/
│   │── app/
│   │── core/
│   │── data/
│   │── presentation/
│   │── utils/
```

Each of these subdirectories has a specific responsibility within the architecture.

1. **`app/` (Application Configuration & Routing)**
    
    Handles the main application setup and navigation.
    
    - Contains:
        - `routes/` → Defines and manages app navigation (e.g., `RouteGenerator`)
        - `app.dart` → The main widget that initializes the app structure
    - Responsibilities:
        - Manages navigation and routing
        - Initializes the main MaterialApp
    
2. **`core/` (Core Business Logic & Dependency Injection)**
    
    Contains base classes, dependency injection, and essential configurations.
    
    - Contains:
        - `base/` → Abstract classes and common base implementations
        - `dependency/` → Dependency injection setup
        - `environment/` → Manages environment configurations (Dev, Prod, Staging)
        - `logic/` → Core business logic independent of UI
        - `core.dart` → Aggregates core modules
        - `core_dependency.dart` → Manages dependency injection for core components
    - Responsibilities:
        - Implements dependency injection
        - Defines common business logic
        - Configures environment variables
    
3. **`data/` (Data Layer: Models, Repositories, & Services)**
    
    Handles data flow, storage, and external interactions such as APIs and local databases.
    
    - Contains:
        - `model/` → Defines data models (JSON structures, database schemas)
        - `repository/` → Implements repositories to fetch and cache data
        - `services/` → Manages API calls, Firebase, and local database services
        - `data.dart` → Aggregates data-related modules
        - `data_dependency.dart` → Handles DI for data-related components
    - Responsibilities:
        - Manages API calls, local storage, and caching
        - Implements repository pattern for data abstraction
        
4. **`presentation/` (UI & State Management)**
    
    Handles UI components, pages, and state management using BLoC.
    
    - Contains:
        - `ui/` → Screens and widgets
        - `logic/` → BLoC-based state management
        - `components/` → Reusable UI components
        - `resources/` → Themes, styles, localization files
        - `presentation.dart` → Aggregates presentation modules
    - Responsibilities:
        - Manages UI layout and components
        - Implements state management (BLoC pattern)
    
5. **`utils/` (Utilities & Helpers)**
    
    Contains helper functions, extensions, and utilities that assist different parts of the app.
    
    - Common Utilities:
        - Date formatters
        - String utilities
        - Network helpers
        - Logger implementations

> The `src/` directory organizes the app into well-defined modules, ensuring clean architecture principles.
> 
> - `app/` → Navigation & App Config
> - `core/` → Dependency Injection & Core Logic
> - `data/` → Repository Pattern for APIs & Storage
> - `presentation/` → UI & State Management (BLoC)
> - `utils/` → Helper Functions & Utilities

---

### `app/` Directory Documentation

The `app/` directory is responsible for application-level configurations, including navigation, global state observation, and core app setup. This is where the app starts and where global logic is managed.

- **Overview**

```markdown
│── app/
│   │── routes/
│   │── app.dart
│   │── bloc_observer.dart
│   │── (Other app-specific files)
```

Each of these subdirectories has a specific responsibility within the architecture.

1. **`routes/` (Navigation Management)**
    
    This directory manages app-wide navigation using either named routes or GoRouter.
    
    - Responsibilities:
        - Defines screen-to-screen navigation
        - Handles route guards (authentication, onboarding, etc.)
        - Supports deep linking and dynamic routes
    - Common Files:
        - `route_generator.dart` → Generates app routes dynamically
        - `app_routes.dart` → Defines static route names
    
2. **`app.dart` (Main App Widget)**
    
    This is the root widget of the app, where MaterialApp (or CupertinoApp) is initialized.
    
    - Responsibilities:
        - Initializes themes, localization, and navigation
        - Sets up the home screen or initial route
        - Configures global app settings
    
3. **`bloc_observer.dart` (Global BLoC Observer)**
    
    This file is used to monitor global BLoC state changes for debugging and logging.
    
    - Responsibilities:
        - Logs state transitions (e.g., when a page loads or a network call completes)
        - Helps debug unexpected state changes
    
4. **Other App-Specific Files**
    
    Other files in `app/` could include:
    
    - `flavor_config.dart` → Handles multiple environments (Dev, Staging, Production)
    - `app_initializer.dart` → Runs initial app configurations (like fetching remote config)
    - `error_handler.dart` → Centralized error handling

> The `app/` directory orchestrates the app’s core logic, including navigation, state observation, and initialization.
> 
> - `routes/` → Manages navigation
> - `app.dart` → Main application widget
> - `bloc_observer.dart` → Global BLoC state monitoring
> - `Other files` → Handles environment setup, initialization, and error handling

---

### **`core/` Directory Documentation**

The `core/` directory contains the fundamental logic, configurations, and shared functionalities that power the app. It serves as the foundation of the app’s architecture, ensuring modularity and maintainability.

- **Overview**

```markdown
│── core/
│   │── base/
│   │── dependency/
│   │── environment/
│   │── logic/
│   │── core.dart
│   │── core_dependency.dart
```

Each subdirectory and file plays a key role in managing the app's business logic, dependency injection, and configurations.

1. **`base/` (Abstract Classes & Common Implementations)**
    
    The `base/` directory provides abstract classes and generic implementations used across the app.
    
    - Responsibilities:
        - Defines base classes for BLoC, Repositories, and Use Cases
        - Implements common patterns
    - Common Files in `base/`:
        - `base_bloc.dart` → Common BLoC logic
        - `base_repository.dart` → Abstract repository for data access
    
2. **`dependency/` (Dependency Injection Setup)**
    
    This directory manages dependency injection (DI) to ensure proper service and object management.
    
    - Responsibilities:
        - Configures service locator (e.g., `get_it`, `injectable`)
        - Ensures modular dependency management
    
3. **`environment/` (App Environment Configurations)**
    
    This file is used to monitor global BLoC state changes for debugging and logging.
    
    - Responsibilities:
        - Defines environment variables (API keys, base URLs)
        - Allows switching between Dev, Staging, and Prod
    - Common Files in `environment/`
        - `env_config.dart` → Base class for environment configurations
        - `env_dev.dart` → Configurations for development
        - `env_prod.dart` → Configurations for production
    
4. **`logic/` (Core Business Logic)**
    
    This directory contains application-wide business logic that does not belong to a specific feature.
    
    - Responsibilities:
        - Implements global app logic
        - Handles global state changes
    - Common Files in `logic/`
        - `theme_cubit.dart` → Manages app-wide theme settings
        - `auth_cubit.dart` → Handles authentication state
        
5. **`core.dart` (Aggregates Core Modules)**
    
    This file imports and exposes all core components to ensure clean imports throughout the app.
    
    ```dart
    export 'base/base_use_case.dart';
    export 'dependency/dependency_injection.dart';
    export 'environment/env_config.dart';
    export 'logic/theme_cubit.dart';
    ```
    

1. **`core_dependency.dart` (Dependency Injection for Core)**
    
    Manages dependency injection for all core services.
    
    ```dart
    @module
    abstract class CoreDependency {
      @singleton
      AppEnvironment getEnvironment() {
        return AppEnvironment.fromDartEnvironment();
      }
    }
    ```
    

> The `core/` directory ensures a solid foundation by managing dependencies, configurations, and business logic.
> 
> - `base/` → Abstract classes & reusable logic
> - `dependency/` → Dependency Injection (DI) setup
> - `environment/` → Manages different app environments
> - `logic/` → Global state and business logic
> - `core.dart` → Exports core modules for easy imports
> - `core_dependency.dart` → Registers core dependencies

---

### **`data/` Directory Documentation**

The `data/` directory is responsible for managing all data-related operations in the application. It follows the Repository Pattern to separate data sources (APIs, local databases, services) from the business logic.

- **Overview**

```markdown
│── data/
│   │── model/
│   │── repository/
│   │── services/
│   │── data.dart
│   │── data_dependency.dart
```

Each subdirectory handles a specific data-related responsibility.

1. **`model/` (Data Models & Entities)**
    
    The `model/` directory defines data structures used across the app, including API response models and local database entities.
    
    - Responsibilities:
        - Defines data models for API responses
        - Implements JSON serialization/deserialization
        - Represents database entities
    - Common Files in `model/`:
        - `user_model.dart` → Represents a user
        - `post_model.dart` → Represents a post
    
2. **`repository/` (Data Abstraction Layer)**
    
    The `repository/` directory acts as a bridge between data sources and business logic.
    
    - Responsibilities:
        - Fetches data from remote APIs or local storage
        - Implements caching mechanisms
        - Abstracts data retrieval logic
    - Common Files in `repository/`:
        - `user_repository.dart` → Manages user data
        - `post_repository.dart` → Manages posts
    
3. **`services/` (API & Local Data Services)**
    
    The `services/` directory manages external data sources, including REST APIs, Firebase, and databases.
    
    - Responsibilities:
        - Handles network requests (HTTP, Firebase, GraphQL)
        - Manages local storage (Hive, SQLite, SharedPreferences)
    - Common Files in `services/`
        - `api_service.dart` → Handles HTTP requests
        - `local_storage_service.dart` → Manages local data storage
    
4. **`data.dart` (Aggregates Data Modules)**
    
    This file exports all data-related modules for cleaner imports across the app.
    
    ```dart
    export 'model/user_model.dart';
    export 'repository/user_repository.dart';
    export 'services/api_service.dart';
    ```
    

1. **`data_dependency.dart` (Dependency Injection for Data)**
    
    Registers repositories and services in the dependency injection system.
    
    ```dart
    @module
    abstract class DataDependency {
      @lazySingleton
      ApiClientService providesApiClientService(
        AppEnvironment environment, 
        LocalStorageService localStorageService
      ) {
        final BaseOptions baseOptions = BaseOptions(
          baseUrl: environment.apiBaseUrl,
          connectTimeout: const Duration(minutes: 1),
          receiveTimeout: const Duration(minutes: 1),
          sendTimeout: const Duration(minutes: 1),
          headers: {
            Headers.acceptHeader: Headers.jsonContentType, 
            Headers.contentTypeHeader: Headers.jsonContentType
          },
        );
    
        final dioClient = Dio(baseOptions);
    
        dioClient.interceptors.addAll([
          ApiInterceptor(
            logEnabled: kDebugMode,
            localStorageService: localStorageService,
          ),
        ]);
    
        return ApiClientService(dioClient);
      }
    }
    ```
    

> The `data/` directory ensures clean data management by separating API calls, local storage, and data models.
> 
> - `model/` → Defines data structures (User, Post, etc.)
> - `repository/` → Abstracts data fetching logic
> - `services/` → Handles APIs, databases, and storage
> - `data.dart` → Exports data-related modules
> - `data_dependency.dart` → Registers dependencies for repositories and services

---

### `presentation/` Directory Documentation

The `presentation/` directory is responsible for UI and state management in the application. It follows a structured approach to separate screens, logic (BLoC), and reusable widgets, ensuring a scalable and maintainable UI architecture.

- **Overview**

```markdown
│── presentation/
│   │── logic/              👈 (Global state & logic shared across pages)
│   │── ui/                 👈 (UI screens & components)
│   │   │── [screenName]/   👈 (Folder for each screen)
│   │   │   │── logic/      👈 (BLoC or Cubit for this screen)
│   │   │   │── widgets/    👈 (Reusable widgets for this screen)
│   │   │   │── name_screen.dart  👈 (Main screen widget)
│   │── components/         👈 (Global reusable widgets)
│   │── resources/          👈 (Themes, styles, colors, etc.)
│   │── presentation.dart   👈 (Exports presentation modules)
```

1. **`components/` (Global Reusable Widgets)**
    
    Contains UI components that are shared across multiple screens (e.g., buttons, text fields, cards).
    
    - Common Files in `components/`
        - `custom_button.dart` → A styled button
        - `custom_textfield.dart` → A reusable text input field
    
2. **`resources/` (App Themes & Styles)**
    
    Contains styling resources such as colors, themes, and fonts.
    
    - Common Files in `resources/`
        - `app_theme.dart` → Manages app-wide themes
        - `colors.dart` → Defines color constants
    
3. **`logic/` (Global Logic Shared Across Screens)**
    
    The `logic/` directory contains state management logic that is shared across multiple screens or affects the entire app (e.g., authentication, theme, or app-wide settings).
    
    - Responsibilities:
        - Manages global states such as theme, authentication, or internet connectivity
        - Stores Cubits/BLoCs that are used in multiple places
        - Handles global user actions and settings
    - Common Files in `logic/`
        - `user_bloc.dart` → Manages current user state
        - `authentication_bloc.dart` → Manages authentication state
    
4. **`ui/` (Screens & Page-Specific Logic)**
    
    The `ui/` directory contains screen-specific UI logic and components. Each screen has its own dedicated folder, following this structure:
    
    ```markdown
    │── ui/
    │   │── home/             👈 (Folder for Home screen)
    │   │   │── logic/        👈 (State management for this screen)
    │   │   │── widgets/      👈 (Reusable widgets for this screen)
    │   │   │── home_screen.dart  👈 (Main screen UI)
    ```
    
    - [screenName]/ (Folder for Each Screen):
        - Each screen gets its own folder containing:
            - logic/ → Each screen has its own **state management logic** using BLoC or Cubit.
            - widgets/ → Contains **small UI components** that are only used within this screen.
            - name_screen.dart → This file contains the **primary layout** for the screen.
    
5. **`presentation.dart` (Exports Presentation Modules)**
    
    This file exports all presentation-related modules for clean imports.
    
    ```dart
    export 'logic/authentication_bloc.dart';
    export 'ui/home/home_screen.dart';
    export 'components/custom_button.dart';
    ```
    

> The `presentation/` directory manages the UI and state management in a structured way.
> 
> - `logic/` → Global state management (Auth, Theme, etc.)
> - `ui/` → Screens structured into logic, widgets, and main screen files
> - `components/` → Reusable global widgets
> - `resources/` → Themes, colors, styles
> - `presentation.dart` → Exports presentation modules

---

### `utils/` Directory Documentation

The `utils/` directory contains helper functions, extensions, and utility classes that provide common functionalities used across the application. These utilities help reduce code duplication and improve code organization.

- **Overview**

```markdown
│── utils/
│   │── extensions/
│   │── helpers/
│   │── validators.dart
│   │── logger.dart
│   │── constants.dart
```

Each file or subdirectory serves a specific purpose in enhancing the app's functionality.

1. **`extensions/` (Dart & Flutter Extensions)**
    
    The `extensions/` folder contains extension methods to improve Dart and Flutter’s built-in classes.
    
    - Common Extensions in `extensions/`
        - `string_extensions.dart` → Enhances `String` class
        - `context_extensions.dart` → Provides shortcuts for `BuildContext`
        - `date_extensions.dart` → Adds helper methods for `DateTime`
    
2. **`helpers/` (Utility Functions for Common Tasks)**
    
    The `helpers/` folder contains standalone functions that simplify repetitive tasks.
    
    - Common Helpers in `helpers/`
        - `date_helper.dart` → Handles date formatting
        - `network_helper.dart` → Checks internet connection
        - `storage_helper.dart` → Manages shared preferences
    
3. **`validators.dart`** (Form Input Validation)
    
    The `validators.dart` file contains functions to validate user input.
    
4. **`logger.dart` (Logging Utility)**
    
    The `logger.dart` file provides a centralized logging system for debugging.
    
    ```dart
    import 'package:logger/logger.dart';
    
    class AppLogger {
      static final Logger _logger = Logger();
    
      static void info(String message) => _logger.i(message);
      
      static void error(String message, [dynamic error]) { 
        _logger.e(message, error);
      }
    }
    ```
    

1. **`constants.dart` (Global Constants)**
    
    The `constants.dart` file stores global static constants.
    
    ```dart
    class AppConstants {
      static const String apiBaseUrl = "https://api.example.com";
      static const int timeoutDuration = 30; // seconds
    }
    ```
    

> The `utils/` directory provides essential helper functions to enhance app development.
> 
> - `extensions/` → Extends Flutter & Dart classes
> - `helpers/` → Standalone utility functions
> - `validators.dart` → Form validation helpers
> - `logger.dart` → Centralized logging system
> - `constants.dart` → Stores global constants

---

## Final Review: Flutter Project Structure

We've gone through the entire **Flutter Clean Architecture** project structure in detail. Below is a final **overview and key takeaways** for reference.

- **Final Directory Structure Overview**

```markdown
│── assets/              👈 (Static assets: images, icons, animations)
│── config/              👈 (Environment configuration files)
│── fonts/               👈 (Custom fonts used in the app)
│── lib/                 👈 (Main application code)
│   │── src/             👈 (Application core following Clean Architecture)
│   │   │── app/         👈 (Application setup, navigation, global state)
│   │   │── core/        👈 (Dependency injection, environment, base logic)
│   │   │── data/        👈 (Repositories, services, API & database handling)
│   │   │── presentation/👈 (UI, BLoC logic, reusable components)
│   │   │── utils/       👈 (Helpers, validators, logging, extensions)
│   │── bootstrap.dart   👈 (App initialization & dependency setup)
│   │── main.dart        👈 (Entry point for the Flutter app)
│── analysis_options.yaml 👈 (Linter rules & code style settings)
│── build.yaml           👈 (Flutter build system configurations)
│── pubspec.yaml         👈 (Dependencies, assets, and package management)
```

- **Key Takeaways**
    - Separation of Concerns: The project is structured into app, core, data, presentation, and utils, ensuring modularity and maintainability.
    - State Management with BLoC: The `logic/` directories in both `presentation/` and `app/` handle state management effectively using Cubit/BLoC.
    - Repository Pattern for Data: The `data/` directory follows a repository pattern to abstract API calls, database interactions, and local storage.
    - Global Configuration & Dependency Injection:
        - `core/` manages **dependency injection** and **environment setup**.
        - `data_dependency.dart` and `core_dependency.dart` ensure **modular service injection** using **GetIt**.
    - Scalable UI Design:
        - Each screen in `presentation/ui/` has **logic (BLoC), widgets (components), and the main screen file**.
        - **Global reusable components** live in `presentation/components/`.
    - Utility Functions for Code Reusability:
        - `utils/` contains **extensions, helpers, validation, logging, and constants**.
    - Navigation System:
        - `app/routes/` handles **named routes** and **dynamic navigation**.

- **Final Thoughts & Next Steps**
    - This structured architecture ensures:
        - Scalability → Easy to add new features
        - Maintainability → Clean separation of concerns
        - Testability → Modular & isolated components
