## Using the Build.Util Project

### New Tools

### [Paket](https://fsprojects.github.io/Paket/)

Paket is a dependency manager for .NET and mono projects, which is designed to work well with NuGet packages and also enables referencing files directly from GitHub repositories.

### [FAKE](http://fsharp.github.io/FAKE/)

Fake (F# Make) is a domain-specific language, written in F#, to help developers easily automate build tasks in the .NET stack.

## Getting Started

***NOTE - Run git BASH as Administrator***

- First, you need to use Paket to restore the needed packages.

  - Download the latest paket.exe

  ````bash
  Mosaic(develop)$ .paket/paket.bootstrapper.exe
  ````

  - Restore the packages in the paket.lock file

  ````bash
  Mosaic(develop)$ .paket/paket.exe restore
  ````

##  A New Way to initialize your local development environment

  ````bash
  Mosaic(develop)$ fake Build.Util/InitalizeLocalEnvironment.fsx
  ````  
  - This command, ensures host entries, IIS Sites and IIS Application Pools exists for:
      - local.mosaic.sherwin.com
      - local.mosservices.sherwin.com
      - local.appservices.sherwin.com

##  A New Way to Publish to Parallel

  ````bash
  Mosaic(develop)$ fake Build.Util/PublishLocalParallel.fsx
  ````
  - This command
    - Ensures registry entry to set TCPTimedWait to 30 seconds exists.
    - Ensures host entries, IIS Sites and IIS Application Pools exists for all needed parallel sites.
   - Publishes all parallel sites to IIS sites.

## Coming soon to a theater near you
  - The ability to have the migrations ran on parallel databases from the PublishLocalParallel script.  
