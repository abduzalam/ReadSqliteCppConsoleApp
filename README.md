# ReadSqliteCppConsoleApp

# Introduction
This is a C++ Console Application that reads from a local SQLite database table and displays the table contents in the console.
## Prerequisites

- Visual Studio 2022 with C++ language support.
- Download the `sqlite3.h` header file and `sqlite3.dll` from the [SQLite Download Page](https://www.sqlite.org/download.html).
  - For the header file, choose the `sqlite-amalgamation-XXXXX.zip` file.
  - For `sqlite3.dll`, under "Precompiled Binaries for Windows", download the 32-bit or 64-bit library based on your requirement.

Once the Precompiled Binaries for Windows are downloaded, you will find a `sqlite.def` file in the zip folder. This "module-definition" file is used in the Windows operating system when creating a Dynamic Link Library (DLL). It provides the linker with information about the exports, attributes, and other directives for the DLL.

To generate `sqlite3.lib`, which is required to be referenced in the C++ solution to make SQLite database calls (as the linker requires a `.lib` file at link time), follow these steps:

1. Open the Visual Studio command prompt.
2. Go to the directory containing `sqlite3.def` (probably the same one containing `sqlite3.dll`).
3. Run the following command:

   ```shell
   lib /def:sqlite3.def /out:sqlite3.lib
   ```

With the `.lib` file generated, you can now set up your C++ Console Application:

1. Create a new C++ Console Application in Visual Studio.
2. Open Project Properties, then add the header file directory path (that you initially downloaded) under "Configuration Properties -> VC++ Directories -> Include Directories".

     ![image](https://github.com/abduzalam/ReadSqliteCppConsoleApp/assets/32676744/2a29e5b6-1177-4657-8765-ccd4063d3385)


3. Include the lib directory path under "Configuration Properties -> VC++ Directories -> Library Directories".

     ![image](https://github.com/abduzalam/ReadSqliteCppConsoleApp/assets/32676744/f05630bf-66d8-44e6-bad2-3d25dcc61fc4)

4. Under "Linker -> Input -> Additional Dependencies", add the absolute path of the `.lib` file (including the filename).

     ![image](https://github.com/abduzalam/ReadSqliteCppConsoleApp/assets/32676744/5e895700-7d03-498e-829f-4d4f8ae04739)


After completing the above setup, create a test SQLite database, add a new table, and insert some records into it.

In your `.cpp` file, add the following code:

```cpp
// ReadSqliteCppConsoleApp.cpp : This file contains the 'main' function. Program execution begins and ends there.

#include <iostream>
#include <sqlite3.h>

static int callback(void* data, int argc, char** argv, char** azColName)
{
    for (int i = 0; i < argc; i++)
    {
		std::cout << azColName[i] << " = " << (argv[i] ? argv[i] : "NULL") << std::endl;
	}
    std::cout << std::endl;
	return 0;
}

int main()
{
   sqlite3* db;
   char* zErrMsg = 0;
   int rc;
   const char* sql;
   const char* data = "Callback function called";

   rc = sqlite3_open("C:\\temp\\Routine.db", &db);
   if (rc) {
       std::cerr << "Can't open database: " << sqlite3_errmsg(db) << std::endl;
       return 0;
   } else {
       std::cout << "Opened database successfully" << std::endl;
   }

   // Create SQL statement
   sql = "SELECT * from MyRoutine";

   // Execute SQL statement
   rc = sqlite3_exec(db, sql, callback, (void*)data, &zErrMsg);

   if (rc != SQLITE_OK) {
       std::cerr << "SQL error: " << zErrMsg << std::endl;
       sqlite3_free(zErrMsg);
   } else {
       std::cout << "Operation done successfully" << std::endl;
   }

   sqlite3_close(db);
   return 0;
}
```

Compile and run the program, and it should read and display the contents from the SQLite database table.
