# Capybara

## Setting up the Environment

Follow [this guide](https://everythingtech.dev/2023/06/step-by-step-guide-on-how-to-get-started-with-drogon-on-windows-with-visual-studio/) to set up the environment. A few things that differs from the tutorial:
* Step 2.7: Along with the tutorial. Run this command vcpkg install nlohmann-json:
* Step 3.2: Don't follow the tutorial. Instead:
  * Create a **CMake** C++ project with VS. Call the project Capybara.
  * Copy the `.gitignore` file on GitHub to the root directory, exactly placed as shown on the repo. We will hard pull the code on the repo, but we want to keep the config files generated by VS, so we need this to let git untrack them.
  * In terminal, go to project root directory:
  ```
  git init
  git status //make sure .vs/ and out/ does not appear
  git remote add origin https://github.com/mm6234/Capybara.git
  git remote -v
  // origin  https://github.com/mm6234/Capybara.git (fetch)
  // origin  https://github.com/mm6234/Capybara.git (push)
  git fetch --all
  git reset --hard origin/tsai00150-api
  ```
* Step 3.4: Change the file named `CMakePresets.json` at the root directory. Change `toolchainFile`'s directory to `vcpkg.cmake` (see appendix). Use two slashes `\\`. 
* Step 3.5 and 3.6: Do not need to modify; the change should be reflected in the repo. 
* If you encounter issues in step 2.7, try uninstall `cmake` and try again. 

After the tutorial, compile the code and start the exe file. Use tools such as postman or curl to test the program. 

## API documentation

### Get the doctor's info based on the doctor's id
```
GET http://capybara.com/api/doctor-info/{DoctorId}
```
Example return body (includes all possible fields):
```
{
  "status": 200,
  "data": {
    "id": 3,
    "doctorName": "Capybara",
    "rating": 3.5,
    "submissions": 1834,
    "location": {
      "latitude": 3.222,
      "longitude": 78.43
    },
    "practiceKeywords": [
      "Ear",
      "Nose",
      "Throat"
    ],
    "languagesSpoken": [
      "English"
    ],
    "Insurance": [
      "Aetna"
    ],
    "Other": {
      "streetAddress": "NYC"
    }
  }
}
```
### Create/update doctor information
```
POST http://capybara.com/api/update
```
If `id` is not included, we create a new doctor record and return the id.

Example request body to create doctor record:
```
{
  "doctorName": "Capybara",
  "location": {
    "latitude": 3.222,
    "longitude": 78.43
  },
  "practiceKeywords": [
    "Ear",
    "Nose",
    "Throat"
  ]
}
```

Example return body to create doctor record:
```
{
  "status": 200,
  "data": {
    "id": 3
  }
}
```
If `id` is included, then we update the existing doctor record. 

Example request body to update doctor record:
```
{
  "id": 3,
  "languagesSpoken": [
    "English"
  ]
}
```
Example return body to update doctor record:
```
{
  "status": 200
}
```
### Get recommendations based on field weights
```
GET http://capybara.com/api/query?field1=value1&field2=value2&...
```
Example return body:
```
{
  "query": [
    {
      "id": 3,
      "doctorName": "Capybara",
      "rating": 3.5, 
      ...more info...
    },
    {
      "id": 4,
      "doctorName": "Capybara2",
      "rating": 3.4, 
      ...more info...
    }
  ]
}
```
## Install MySQL Server

Visit the [MySQL Downloads page](mysql.com/downloads)
* Go to MySQL Community (GPL) Downloads
* MySQL Installer for Windows
* Download the larger file of the two shown (i.e. mysql-installer-community-8.0.27.1.msi)

Open the MySQL Installer file
* Click 'Custom' install
* Include both MySQL Server _(MySQL Servers->MySQL Server 8.0->MySQL Server 8.0.27 -X64)_ and MySQL Client _(Applications->MySQL Workbench->MySQL Workbench 8.0->MySQL Workbench 8.0.27 – X64)_
* Click ‘Next’, then ‘Execute’, then ‘Next’, ‘Next’, ‘Next’, ‘Next’, choose a Root password, ‘Next’, ‘Next’, ‘Execute’, ‘Finish’, ‘Next’, ‘Finish’

## Linking System Environment Variable to MySQL Library

1. Right click on the bottom left Windows icon
2. Choose 'System'
3. Select 'Advanced System Settings'
4. Click 'Environment Variables'
5. Within the bottom 'System Variables' listing, select the 'Path' variable and press 'Edit...'
6. At the very beginning of the field within the popup window, add the MySQL Server directory (i.e. %ProgramFiles%\MySQL\MySQL Server 8.0\bin) and append a semicolon
7. Click 'Ok' on every window to exit

## Opening the Database in MySQL Workbench

Open the MySQL Workbench program
* Select the '+' button to add a new MySQL Connection
* Enter the database credentials provided in the Discord server

## Setting up the Visual Studio Environment

Download the Windows (x86, 64-bit) ZIP archive from the [MySQL downloads page](https://dev.mysql.com/downloads/connector/cpp/)
* Un-zip the 'mysql-connector-c++' folder
* Open the Capybara project in Visual Studio
* Change the project Solution Configuration setting within the menu bar from 'Debug' to 'Release'
* Click the 'Project' menu item, and select 'Project' properties at the bottom of the listing
* In the popup window, ensure 'Configuration' is set to 'Release', and 'Platform' is 'x64'
* Click 'C/C++' > General > Additional Include Directories > Select the dropdown icon > Click 'Edit'
  * Select the yellow create icon in the popup window, then the '...' button that appears
  * Copy and paste the following directory in the field: [Directory you stored the Un-Zipped MySQL Connector Library]/include/jdbc
* Click 'C/C++' > Preprocessor > Preprocessor Definitions
  * Add `STATIC_CONCPP;` to the beginning of the field value
* Click 'C/C++' > Code Generation > Runtime Library
  * Select `Multi-Threaded DLL (/MD)`
* Click 'Linker' > Input > Additional Dependencies
  * Select the dropdown icon > Click 'Edit'
  * Input `mysqlcppcon-static.lib` in the white box
 
Download and Run the solution.

## Unit test
We use Google Test; in Visual Studio, simply "start debugging" on `Unittest.cpp`.

## Acknowledgement
* `.gitignore` file template from github's [gitignore repo](https://github.com/github/gitignore/blob/main/VisualStudio.gitignore). Used for best practices. 

## Appendix
`CMakePresets.json`: Change `toolchainFile` to the approptiate directory. For example: `"C:\\Users\\iamyo\\projects\\vcpkg\\scripts\\buildsystems\\vcpkg.cmake"`
```
{
    "version": 3,
    "configurePresets": [
        {
            "name": "windows-base",
            "hidden": true,
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "cacheVariables": {
                "CMAKE_C_COMPILER": "cl.exe",
                "CMAKE_CXX_COMPILER": "cl.exe"
            },
            "condition": {
                "type": "equals",
                "lhs": "${hostSystemName}",
                "rhs": "Windows"
            }
        },
      {
        "name": "x64-debug",
        "displayName": "x64 Debug",
        "inherits": "windows-base",
        "architecture": {
          "value": "x64",
          "strategy": "external"
        },
        "cacheVariables": {
          "CMAKE_BUILD_TYPE": "Debug"
        },
        "toolchainFile": "<Insert your directory to vcpkg.cmake>"
      },
        {
            "name": "x64-release",
            "displayName": "x64 Release",
            "inherits": "x64-debug",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Release"
            }
        },
        {
            "name": "x86-debug",
            "displayName": "x86 Debug",
            "inherits": "windows-base",
            "architecture": {
                "value": "x86",
                "strategy": "external"
            },
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            }
        },
        {
            "name": "x86-release",
            "displayName": "x86 Release",
            "inherits": "x86-debug",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Release"
            }
        },
        {
            "name": "linux-debug",
            "displayName": "Linux Debug",
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            },
            "condition": {
                "type": "equals",
                "lhs": "${hostSystemName}",
                "rhs": "Linux"
            },
            "vendor": {
                "microsoft.com/VisualStudioRemoteSettings/CMake/1.0": {
                    "sourceDir": "$env{HOME}/.vs/$ms{projectDirName}"
                }
            }
        },
        {
            "name": "macos-debug",
            "displayName": "macOS Debug",
            "generator": "Ninja",
            "binaryDir": "${sourceDir}/out/build/${presetName}",
            "installDir": "${sourceDir}/out/install/${presetName}",
            "cacheVariables": {
                "CMAKE_BUILD_TYPE": "Debug"
            },
            "condition": {
                "type": "equals",
                "lhs": "${hostSystemName}",
                "rhs": "Darwin"
            },
            "vendor": {
                "microsoft.com/VisualStudioRemoteSettings/CMake/1.0": {
                    "sourceDir": "$env{HOME}/.vs/$ms{projectDirName}"
                }
            }
        }
    ]
}

```
