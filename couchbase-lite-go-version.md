# How to build couchbase-lite-c embedded database for golang?

Recently I am working on a supershop's erp development. But this is an exception from all other projects because the consumer que at the point of sale/POS continues to grow in every minute.

On top of that there are problems with internet, local area network etc.

So the owner wants the software to work offline, that is, without an internet connection, and whenever the network problem is solved, all the data will be synced online.

I have never worked in offline first approach before
So I keep thinking about how I will solve this problem
Since I chose golang as the programming language, I was worried about which database would work better with golang, NoSQL or SQL ??


After two or three days of research I found couchbase

I'm glad to see big companies like ebay and staples in customer case studies on their website.

Even after that, some questions kept popping up in my mind

* Does couchbase has the sdk for golang?
* Does couchbase work together offline and online?


As soon as I searched the internet, I get to know.

* Couchbase has an open source version
* It supports offline first software development as it has a Couchbase Lite/Sync Gateway

Now I will discuss how I set up embedded NoSQL syncable database for golang.

For this I used https://github.com/couchbaselabs/couchbase-lite-C
It has a third party golang bindings https://github.com/svr4/couchbase-lite-cgo

**Step-1:**\
I am using the following command to clone the repo from github:
> git clone -b fix/ci_windows_etc https://github.com/couchbaselabs/couchbase-lite-C.git

**Step-2:**\
There is a power shell script to produce sub-module update recursively\
I am using that script.\
At first opening Windows PowerShell as an administrator\
Then cd into the repository downloaded using step-1\
inside the repo there is a folder called **jenkins**\

**Step-3:**\
cd into jenkins using following\
> cd jenkins_win.ps1

**Step-4:**\
running the powershell script using following:\
> .\jenkins_win.ps1


It failed to run Cmake because I am using visual studio 2019
so trying another way to generate solution file using cmake (using command line as administrator)

**Step-5:**\
creating a folder called build_cmake\
> mkdir build_cmake

**Step-6:** (Only if you use Visual Studio 2019)\
Find a file **BuiltInWebSocket.cc** into the following location\
> ~couchbase-lite-C/vendor/couchbase-lite-core/Networking/BuiltInWebSocket.cc

###### Open with notepad or any of your favorite text editor and add after `#include <string>`
> `#include <functional>`

After that execute the following command:\
> cmake -G "Visual Studio 16 2019" -B "C:\Users\mostain\Desktop\couchbase\newdb\couchbase-lite-C\build_cmake"


###### If my build is successful then I will get the following 4 files in Release folder

1. CouchbaseLiteC.dll
2. CouchbaseLiteC.exp
3. CouchbaseLiteC.lib
4. CouchbaseLiteCStatic.lib

![image](https://user-images.githubusercontent.com/17635146/76381770-97163480-6380-11ea-9737-c2583fd6ccbb.png)


### If you face any issue or unable to build don'w worry still you can download my release build from ::
<https://github.com/mateors/tutorial/tree/main/couchbase-lite-c>


### Now how will i implement couchbase-lite-c embedded database with golang?
To embed couchbsae-lite-c database with my golang project I have done the following steps...\

1. Created a project folder ***embed-lite-db*** on my golang working directory **E:\GOLANG\src\mateors** then Copy and pasted the following code:

```go

package main

import (
	cblcgo "github.com/svr4/couchbase-lite-cgo"
	"fmt"
)

func main() {
	var config cblcgo.DatabaseConfiguration

	// Optional encryption key.
	var encryption_key cblcgo.EncryptionKey
	encryption_key.Algorithm = cblcgo.EncryptionNone
	encryption_key.Bytes = make([]byte, 0)

	config.Directory = "./"
	config.EncryptionKey = encryption_key
	config.Flags = cblcgo.Database_Create

	if db, err := cblcgo.Open("my_db", &config); err == nil {
		// Create a doc
		doc := cblcgo.NewDocumentWithId("doc1")
		doc.Props["first_name"] = "Mostain"
		doc.Props["last_name"] = "Billah"
		doc.Props["age"] = 36
		doc.Props["email"] = "bill.rassel@gmail.com"
		doc.Props["type"] = "contacts"

		if _, err2 := db.Save(doc, cblcgo.LastWriteWins); err2 == nil {
			// Retrieve the saved doc
			if savedDoc, e := db.GetMutableDocument("test"); e == nil {
				for k, v := range savedDoc.Props {
					if savedDoc.Props[k] != v {
						fmt.Println("Saved document and retrieved document are different.")
					}
				}
				savedDoc.Release()
			} else {
				fmt.Println(e)
			}
		} else {
			fmt.Println(err2)
		}

		if !db.Close() {
			fmt.Println("Couldn't close the database.")
		}

	} else {
		fmt.Println(err)
	}
	
}
```

2. In the root of my project i ran the following command (E:\GOLANG\src\mateors\embed-lite-db)
> go mod init

Wait a bit and you will notice one file ***go.mod*** generated\


3. Now Run the following command
> go build

### output:
```
go: finding module for package github.com/svr4/couchbase-lite-cgo
go: found github.com/svr4/couchbase-lite-cgo in github.com/svr4/couchbase-lite-cgo v0.2.5
# mateors/embed-lite-db
c:\go\pkg\tool\windows_amd64\link.exe: running gcc failed: exit status 1
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
C:/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/8.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: cannot find -lCouchbaseLiteC
collect2.exe: error: ld returned 1 exit status
```

beside above output you will notice another file ***go.sum*** generated in the same directory\

4. Now you need to take two following action

    1. set the full path of your gcc for the golang environtment using following command.
    >set CC=C:\mingw64\bin\gcc.exe

    2. copy paste **CouchbaseLiteC.dll** file into two places.
    > copy CouchbaseLiteC.dll file $GOPATH\pkg\mod\github.com\svr4\couchbase-lite-cgo@v0.2.5 
    > copy CouchbaseLiteC.dll file and paste into your project root directory 

    (My golang working directory is E:\GOLANG)
    

5. Now run the go build command again to build your project .exe file (as i am on windows)
> go build

If the above code ran successfully you get an .exe on your project root directory \
(in my case its E:\GOLANG\src\mateors\embed-lite-db)

Now you may run .exe file to check everything working perfectly.
in my case ./embed-lite-db.exe ran successfully and got the following output:

```
E:\GOLANG\src\mateors\embed-lite-db>embed-lite-db.exe
09:04:30.104520| [DB]: {Shared#1}==> class litecore::DataFile::Shared 0000000000AF88F0 @0000000000AF88F0
09:04:30.105469| [DB]: {Shared#1} instantiated on E:\GOLANG\src\mateors\embed-lite-db\my_db.cblite2\db.sqlite3
09:04:30.106056| [DB]: {DB#2}==> class litecore::SQLiteDataFile .\my_db.cblite2\db.sqlite3 @0000000000AFCCF0
09:04:30.106241| [DB]: {DB#2} Opening database
09:04:30.107560| [Actor]: Starting Scheduler<0000000000B2BC70> with 12 threads
09:04:30.108185| [DB]: {DB#3}==> class litecore::SQLiteDataFile .\my_db.cblite2\db.sqlite3 @0000000000B2CEB0
09:04:30.108513| [DB]: {DB#3} Opening database
09:04:30.236115| [DB]: {DB#2} Committing transaction took 0.126 sec
09:04:30.240980| [DB]: {DB#3} Closing database
09:04:30.311529| [DB]: {DB#2} Closing database
```

If you see the above output **congratulations!** you have done everything successfully.
Thank you, if you like the post please give me a thumbs up/star on my github repo.
if you need assistance send me an email <bill.rassel@gmail.com>

## Tools you required:
1. http://sourceforge.net/projects/mingw-w64/files/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe/download
