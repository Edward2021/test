# Setting Up Your Windows Environment

##### NOTE: If you are *not* running Windows, follow the instructions on the Unix tutorial [here](/setup_development_mac+unix.md).

In this tutorial, you will be setting up your computer as a local web server, and transforming it into a development environment for the web app accessible at https://www.theglassfiles.com.

## Contents
  * [Prerequisite Setup Instructions](#prerequisite-setup-instructions)
  * [Cloning the Repository](#cloning-the-repository)
  * [Continuing Setup](#continuing-setup)
  * [Setting up the Databases and Amazon S3](#setting-up-the-databases-and-amazon-s3)
  * [Workflow](#workflow)

## Prerequisite Setup Instructions
All the commands in this setup doc **_MUST_** be run in PowerShell, **_NOT_** Command Prompt. On Windows 10 machines, you can open PowerShell if you `Shift + Right Click` anywhere within File Explorer and select the option to "Open PowerShell window here". Doing so will open a PowerShell pre-set to that directory. Alternativly, open Run `Windows Key + R`, type **powershell** and hit enter.

  * [Ruby 2.0.0-p648](https://rubyinstaller.org/downloads/archives/)
    * Under **RubyInstallers**, choose _Ruby 2.0.0-p648 (x64)_ for 64 bit systems and _Ruby 2.0.0-p648_ for 32 bit systems
    * _**NOTE:**_ During Ruby installation, when the installer asks for "Installation Destination and Optional Tasks", make sure you check "Add Ruby executables to your PATH". This step is critical as nothing will work without it.
  * [Ruby Development Kit](https://rubyinstaller.org/downloads/#development-kit-old)
    * **Make sure you are downloading the old Ruby Development Kit!** The section the download is in should be labeled `Development Kit (old)`.
    * Download the correct version of the DevKit for your system (x32 or x64) and open it.
    * When you run the file, a window will popup asking to extract. If you did not change any paths when installing Ruby, paste `C:\Ruby200-x64` (`C:\Ruby200` on 32 bit machines) and hit "Extract". Please verify that you have the correct for your computer before extracting.
    * Open PowerShell (`Windows Key + R` and enter **powershell**) and naviate to the path you entered above. The command should look something like `cd C:\Ruby200-x64`. Then run these commands:

      ```posh
        > ruby .\dk.rb init
        > ruby .\dk.rb install
      ```

  * [PostgreSQL 9.1+](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads#windows)
    * Choose PostgreSQL 9.6.9 (or the latest backward-compatible version) for your system architecture (x32 or x64)
    * **When installing PostgreSQL, make a note of the password you enter when prompted to create one. You will need this again late in setup.**
    * **Do NOT change anything from their default values besides the password you set.**
    * When it finishes, the installer will ask you if you want to run another program called _Stack Builder_. Do not run it.
    * Once PostgreSQL has installed, open Run `Windows Key + R`, type **services.msc**, and hit enter.
    * You should see a massive list of Windows Services. Scroll until you find the one starting with **postgresql**, right click it and click _Properties_. A window should pop up.
    * Navigate to the _Log On_ tab and check "Local System account". If "Allow service to interact with desktop" is not checked, check that as well. Apply those changes and return to the list of services.
    * Click the same PostgreSQL service and click _Restart_ on the left side of the screen. This will ensure that PostgreSQL uses your local account rather than a seperate one when interacting with files. This is a necessary step as the account PostgreSQL uses by default does not have the correct permissions while your local account does.
  * [ImageMagick 7.0.6-5+](https://www.imagemagick.org/script/download.php#windows)
    * Download the _dynamic_ 16 bits-per-pixel version for your x64 or x32 (x86) bit system.
    * **When you get to the "Install Additional Tasks" screen, make sure that you check "Install legacy utilities (e.g. convert)" option. This is a critical step!**
  * [MuPDF 0.9](https://mupdf.com/downloads/archive/mupdf-0.9-windows.zip)
    * The Glass Files uses a utility called MuPDF to process PDFs and convert them into images.
    * **Make sure you download version 0.9 of the software!**
    * Once you have download it, create a folder in your _Program Files (x86)_ directory (`C:\Program Files (x86)`) and call it MuPDF.
    * Drag and drop all the files from the `zip` you downloaded (`mupdf-0.9-windows.zip`) into that folder.
    * We now need to add MuPDF to the computer's PATH variable. If you know how to do that already, go ahead and add `C:\Program Files (x86)\MuPDF`. If not, follow these steps:
      * Go to the environment variables window by typing `Windows Key + R` and entering `rundll32 sysdm.cpl,EditEnvironmentVariables`.
      * In the top box, underneath _User variables_, scroll to the entry labeled `Path` and select it.
      * Click the upper of the two Edit buttons.
      * In the dialog box that pops up, click _New_ on the right side. A new field should appear at the bottom of the list. Paste `C:\Program Files (x86)\MuPDF` into the field, and click `Ok` in all the windows open.
  * [Git 2.13.4+](https://git-scm.com/)
  * A SFTP Client, like [WinSCP](https://winscp.net/eng/index.php) or [FileZilla](https://filezilla-project.org/).  

## Cloning the Repository
  * Clone this repository somewhere on your machine. [GitHub Desktop](https://desktop.github.com/) is a useful tool if you do not want to work directly with command line Git to do this, as it also alerts about merge errors and commit history.
    * When cloning the repository, make sure to use the recommended HTTPS remote URL rather than the SSH one. Configuring your system to generate and properly use SSH keypairs is a pain, so it is much easier to avoid it by simply using the HTTPS protocol. Follow **_ONE_** of these cloning options:
    * **Cloning this repository with GitHub Desktop (recommended):**
      * Download and install GitHub Desktop from the link above. When installation is complete, launch the application and login with your GitHub credentials.
      * To clone the repo, go to `File -> Clone repository` or via the keyboard shortcut `Ctrl + Shift + O`. In the spot for a "URL or username/repository", paste `https://github.com/sdbase/theglassfiles_com.git`. You can change where to clone the repository under "Local path", but make sure the end of the path always ends in "\theglassfiles_com". Click "Clone" and continue following these instructions.
    * **[OR] Cloning this repository via the command line:**
      * To clone this repository with the command line, simply enter the command below. Git will prompt you for your credentials and then it will clone the repository. Do note, however, that this will clone the repo into where you are executing the script from. So if your shell is running from your Desktop, the repository will be cloned to "...\Desktop\theglassfiles_com".

       ```posh
         > git clone https://github.com/sdbase/theglassfiles_com.git
       ```

## Continuing Setup
  * In a PowerShell prompt, navigate to the directory you cloned via the `cd` command, or by using `Shift + Right Click` trick described [above](#prerequisite-setup-instructions). If your name is John and you cloned the repository to your Desktop, the `cd` command would look something like this:

    ```posh
      > cd C:\Users\John\Desktop\theglassfiles_com
    ```

  * Now we need to create some files so that everything can run properly. We need to create two files, one called *database.yml* and the other called *secrets.yml*. These files allow us to easily interact with our application and our PostgreSQL database. To create the files type the following commands into the terminal:

    ```posh
      > copy config\database.example.yml config\database.yml
      > copy config\secrets.example.yml config\secrets.yml
    ```

    Our environment also uses a log file. Log files are extremely useful because they record everything that happens during development. If something does not go as expected, or you receive an error of any kind, you can usually check the log file to see exactly what went wrong. Typically, log files are generated automatically by the application. Unfortunately, however, Rails does not do this, so we need to create the file ourselves. You can do this by executing these commands:

    ```posh
      > mkdir log
      > ni log\development.log
    ```

### Updating OpenSSL [If you have done this before, skip this step]
OpenSSL is used by practically all network communications, from push commits with Git to communicating to our databases and Amazon S3. For whatever reason, the OpenSSL installation that's been downloaded for us is outdated. Because of this, communication with Amazon doesn't work. To fix this problem, we need to do a few things.
  * First, download the newest permissions file [here]( http://curl.haxx.se/ca/cacert.pem) and drop it into your project directory.
  * Run this command: `Start-Process powershell -Verb runas`. A new PowerShell window should pop up.
  * Within that new window, `cd` into your project directory like you did [above](#continuing-setup).
  * Finally, we need to tell our machine to use that recent permissions file. Fortunately, all we need to do is change a variable. To do this, run this command from the new window:

    ```posh
      > [Environment]::SetEnvironmentVariable("SSL_CERT_FILE", "$(Convert-Path .)\cacert.pem", "Machine")
    ```
  * **DO NOT DELETE THIS FILE. DOING SO WILL BREAK STUFF!**

### Updating RubyGems [If you have done this before, skip this step]
If you have not noticed by now, our development environment is using an old version of Ruby. Unfortunately for us, this means that some core functionality doesn't work quite right, the biggest being RubyGems. Ruby provides things called gems, which are essentially small snippets of code that provide functionality to your application. RubyGems is how gems are installed to your own machine. However, because we are using an older version of Ruby, and thus an old version of RubyGems, we run into the problem of not being able to download gems due to security updates and certificate discrepancies. In order to due so, we have to manually update RubyGems:
  * Go to https://rubygems.org/pages/download/ and download the GEM file. When it is finished downloading, drag it into your development folder.
  * In your terminal run the following commands. Note that the name of the .GEM file you downloaded might be different, so change the first command accordingly:

    ```posh
      > gem install --local --no-document rubygems-update-*.gem
      > update_rubygems --no-document
      > gem uninstall rubygems-update -x
    ```

  Once those commands have finished running, you can safely delete the .GEM file from your development folder. If you're really lazy and don't feel like deleting the file manually, delete it from PowerShell with:

  ```posh
      > del rubygems-update-*.gem
  ```

### Installing all the necessary gems
Now that RubyGems is up to date, we can install all the gems that we need. The first and super important one is called [Bundler](https://bundler.io/). Bundler basically handles installation of all the other gems we need. Install and setup bundler like so, but be warned that it may take some time:

  ```posh
      > gem install --no-document bundler
      > bundle install --jobs ((gwmi win32_computersystem | select -ExpandProperty NumberOfL*) - 1)
  ```

### Configuring the application
Our project requires things called *secrets* in order to run. The exact definition doesn't matter right now, just know that you need to create some. You can do so by running this command:

  ```posh
    > bundle exec rake secret
  ```

The command will output a long string of random letters and numbers. Copy it and paste it into your `config/secrets.yml` replacing `{DEV_SECRET}`. Your file should look like this:

  ```yml
    development:
      ...
      secret_key_base: SOME_LONG_STRING_OF_STUFF
  ```

You will need to run the command a second time and do the same as above for the test environment. However, now replace `{TEST_SECRET}`. Your file should now look something like this:

  ```yml
    development:
      ...
      secret_key_base: SOME_LONG_STRING_OF_STUFF

    test:
      ...
      secret_key_base: SOME_OTHER_LONG_STRING_OF_STUFF
  ```

### Forcing cross-platform operability
There are two enormous differences between running this app on Unix-based systems and Windows-based systems: operating-system-specific gems, and line endings.

For example, Windows must install native extensions to some of the gems we're using in order to get everything working. It is extremely tedious to remove these gems from `Gemfile.lock` every time you want to commit a change.

Use this set of scripts called [Neighborhood](https://github.com/thearchitector/Neighborhood) to automate this process, install it in the project root directory by running this command:

  ```posh
    > ruby -e "$(curl https://raw.githubusercontent.com/thearchitector/Neighborhood/master/install)"
  ```

## Setting up the Databases and Amazon S3
Please make sure you follow the initial setup steps above before continuing here.

### Configuring the database access
Now that we've setup the `config\secrets.yml` we can continue to setup the database. The first thing to do is to setup our database config file. In your `config\database.yml` file, replace `DATABASE_USERNAME` with `postgres`.

### Downloading the seed data
The first step is to download the seed data for our database. **As the file server is not port fowarded for public access, you can only continue setup at The Glass Files HQ**.
  * Using the SFTP client you downloaded earlier in setup, connect to the file server and dowload the file located at to `/sdb1/sdbase/clients/the_glass_files/database/dump/theglassfiles_com_production_20180603_data-only.sql`. If you don't have the hostname and password, ask someone for it.
  * Move the file to the `db` folder in your project directory.

### Downloading the environment file
Our site uses a host of third party utilities that enable us to do things like upload images and send emails. Each of these services requires authentication credentials to use. For security purposes and some sanity, you must download them from the file server rather than GitHub. Just like above, download the file located at `/sdb1/sdbase/clients/the_glass_files/security/dot-env_20180509.txt`. **You will need to change the filename to be just `.env`, without anything before the period.** However, your OS will probably not let you do this the normal way. The easiest way to do it is to open the `.txt` you downloaded in a text editor and then save it as `.env`. When you save the new file, move it to your project directory. **Make sure the file is just _.env_, not _.env.txt_ !!**

### Setting up the database
At this point in setup, we haven't actually created the databases yet. Fortunately, Rails has made this process extremely easy. With the following three commands, we create all the databases TGF uses, set up their schemas, and add in all the seed data. When prompted for your password, enter the exact same one as you did above. **As a security precaution, you will not see the password as you enter it. Be aware that _you are still_ entering the password.**

  ```posh
    > bundle exec rake db:drop:all
    > bundle exec rake db:create RAILS_ENV=development
    > bundle exec rake db:schema:load
    > psql -U postgres -d theglassfiles_com_development -f .\db\theglassfiles_com_production_20180603_data-only.sql
    > bundle exec rake data:update_sponsored_families
  ```
These commands _will_ spam your console with tons of messages. Just ignore them, that is normal.

To keep things neat, when all those commands have finished, delete the `.sql` file from your `db` folder.

### Active Record Migrations
When building any web app, there come times when it is necessary to alter the database structure. When using Rails, the creation of tables or the altering of columns are done with things called migrations. Active Record Migrations allow incremental updates to a database's structure without (when done correctly) having an effect on stored data.

You can read more about database migrations at http://guides.rubyonrails.org/v4.2/active_record_migrations.html.

#### Creating migrations
To create a database migration, run `rails generate migration {NAME}` where `{NAME}` is a descriptive phrase written in CamelCase, such as *AddDefaultItemThumbnailOffsets*. A new file will be generated in the directory at `\db\migrate` following the format `timestamp_add_default_item_thumbnail_offsets.rb`. You schema changes must be placed within that file. Read the [official documentation](guides.rubyonrails.org/v4.2/active_record_migrations.html#writing-a-migration) to learn more about writing your own migrations.

#### Running migrations
If there is a new migration that needs to be run, you can do so by running `bundle exec rake db:migrate` in your project's root directory. All pending migrations will be run and your database will be updated. Read the [official documentation](http://guides.rubyonrails.org/v4.2/active_record_migrations.html#running-migrations) to learn more about running migrations.

### Setting up content cloud storage on Amazon S3
This stage is super important and potentially dangerous, so we're delegating most of the tasks to an admin. Ask someone important to help setup your S3 bucket and they should know what to do. When they're done, they should tell you the name of something called a `bucket`, and it should look something like `dev-gabrie-media-theglassfiles-com`.

Open the `.env` file in your project's directory and change `dev-username-media-theglassfiles-com` to the bucket that they gave you.

### Downloading and placing the default media items directory
When a user creates an account, a 'default' media item is created for that user, which serves as a NUX feature.

  * you must use an SFTP client to download the directory file from the file server at scenyc
  * file location:  `sdb1/tectonyc/partners/the_glass_files/theglassfiles_com/content_management/default_media-items/default_media-items.zip`
  * unzip the archive
  * move the resulting directory, named `default_media-items` to the `public` folder in your project directory

# Workflow
To launch The Glass Files on your computer, open PowerShell in your project directory and type the following command:

  ```posh
    > rails s
  ```

This will start a locally hosted server on your machine, running The Glass Files website. Go to `http://localhost:3000` in your browser and the website should load.

_If you are finding that your computer takes a long time to refresh the page, open your `.env` file and, on a new line at the bottom of the file, add `ENABLE_ASSET_DEBUGGING=false`._

## Helpers
There are a couple of things that are useful to do when developing. It's best for each of these commands to be run in separate PowerShell windows:
  * `gc log\development.log -tail 10 -wait` - this will tail the development log so you can see what is happening as it happens
  * `rails c` - this will start the rails console in case you need to do something during testing
