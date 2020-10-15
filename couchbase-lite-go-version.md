Recently I am working on a supershop's erp development

But this is an exception from all other projects because the consumer que at the pos continues to grow every minute

On top of that there are problems with internet, local area network etc.

So the owner wants the software to work offline, that is, without an internet connection, and whenever the network problem is solved, all the data will be synced online.

I have never worked in offline first approach before
So I keep thinking about how I will solve this problem
Since I chose golang as the programming language, I was worried about which database would work better with golang, NoSQL or SQL.


After two or three days of research I found couchbase

I'm glad to see big companies like ebay and staples in customer case studies on their website.

Even after that, some questions kept popping up in my mind

### Does couchbase has the sdk for golang?
### Does couchbase work together offline and online?


As soon as I searched the internet, I saw the

Couchbase has an open source version
It supports offline  first software development as it has a Couchbase Lite/Sync Gateway

Now I will discuss how I set up embedded NoSQL syncable database for golang

For this i used https://github.com/couchbaselabs/couchbase-lite-C
it has a third party golang bindings https://github.com/svr4/couchbase-lite-cgo

**Step-1:**
I am using the following command to clone the repo from github:
> git clone -b fix/ci_windows_etc https://github.com/couchbaselabs/couchbase-lite-C.git

**Step-2:**
There is a power shell script to produce sub-module update recursively i am using that script using below:
opening Windows PowerShell as administrative mode
cd into the repository downloaded using step-1
inside my repo there is a folder called jenkins

**Step-3:**
cd into jenkins using following
>cd jenkins_win.ps1

**Step-4:**
running the powershell script using following:
> .\jenkins_win.ps1


It failed to run Cmake because i am using visual studio 2019
so i am trying to generate solution file using cmake following command: (using command line as administrator)

**Step-5:**
creating a folder called build_cmake
> mkdir build_cmake

**Step-6:** (Only if you use Visual Studio 2019)
>Find  BuiltInWebSocket.cc into the following location
>~couchbase-lite-C/vendor/couchbase-lite-core/Networking/BuiltInWebSocket.cc

open with notepad or any of your favorite text editor and add after #include <string>
> #include <functional>

After that executing the following command:
> cmake -G "Visual Studio 16 2019" -B "C:\Users\mostain\Desktop\couchbase\newdb\couchbase-lite-C\build_cmake"


###### If my build is successful then I will get the following 4 files in Release folder

1. CouchbaseLiteC.dll
2. CouchbaseLiteC.exp
3. CouchbaseLiteC.lib
4. CouchbaseLiteCStatic.lib

![image](https://user-images.githubusercontent.com/17635146/76381770-97163480-6380-11ea-9737-c2583fd6ccbb.png)

done!
Thank you.