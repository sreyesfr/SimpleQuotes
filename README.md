Part 1: Getting Started
===========

1. Create a new rails application called "SimpleQuotes" and initialize a blank git repository inside the app:

        rails new SimpleQuotes
        cd SimpleQuotes
        git init .

1. Add all the files generated by rails to the repo and verify that all files are staged with the command `git status`. Commit these files by typing to git and verify again with `git status`.

        git add .
        git commit -m 'Base rails app'

1. Checkout a new branch called "quote". We will merge this back into `master` later.

        git checkout -b quote

1. Create the `Quote` model and scaffolding using the rails generator. (Substitute the correct values):

        rails generate scaffold <Model> <attribute>:<data type>

Your model should be `Quote`, with `body:string` and `source:string`. When generating the scaffold, multiple attributes (with colon and data type) are separated by a single space.

1. Visit the [Rails API documentation](http://apidock.com) from your browser for reference.

1. Open your rails project in your preferred editor.

1. Open the `config/database.yml` file and change the name of the development database to match the following:

    ```yaml
    development:
      # ... other stuff
      database: db/quotes_dev.sqlite3
    ```

  **Do not change anything else in the file!**

1. Go to the `db/migrate` directory and verify there is a file named `<somenumber>_create_quotes.rb`. Open this file and make sure the content is accurate.

  Set the default value of `source` to "Anonymous".  To do this, change the line with `:source` to

  ```ruby
  t.string :source, default: "Anonymous"
  ```

1. Migrate the database:

        bundle exec rails db:migrate

1. Commit your work with git. First type `git status` to see what needs to be staged. You will notice that in addition to the `config/database.yml` change you made, rails automatically edited your `routes.rb` file to include routing information for the new model you created.

        git status

  In addition, there are a number of new files related to the quotes scaffolding that need to be added to the project. Since these are all related, we will add them all at once to the staging level with `git add .`. 

        git add .

  Now commit these changes from staging to the repository by typing. Type git log to see a history of the repository changes.

        git commit -m "Quote model scaffolded; db minor edits"

1. Start up the development web server

        bundle exec rails server

1. Open up your browser and go to [localhost:3000](http://localhost:3000) to view the application.  

1. Add a `/quotes` to the end of the URL to get to the main quotes page.

1. Use the web ui to enter in at least three quotes from the [famous quotes database](http://67272.cmuis.net/files/67272/famous_quotes.pdf). Update one quote. Destroy one quote.

1. Now that the basic CRUD functionality is working and the app is populated with test data, let's merge it back to the master branch. 

        git checkout master
        git merge quote

- - -
# <span class="mega-icon mega-icon-issue-opened"></span> Stop
Show a TA that you have the Rails app set up, that the CRUD functionality of the app is working, and have properly saved the code to git. Make sure the TA initials your sheet.
- - -

Part 2: Model, View, and Controller
===========

1. Download the [additional lab materials](https://github.com/profh/67272_lab4_starter) from github.com.

1. Unzip the file and familiarize yourself with the contents. Go back to the command line and create a new git branch called 'styling' and check out the code to that branch so we can experiment with some styling.

        git checkout -b styling

1. Add the image `quips_n_quotes.png` to your `app/assets/images` directory.

1. Add the `my_styles.css` file to your `app/assets/stylesheets` directory.

1. Edit the `my_styles.css` stylesheet so that the base font is Verdana and font-size is 14px:

    ```css
    html, body {
      font-family: Verdana;
      font-size: 14px;
    }
    ```
1. Edit the `h1` tags display text in bold and at a font-size 180% of the base size:

    ```css
    h1 {
      font-size: 180%;
      line-height: 1.5;
    }
    ```

1. Now we want to edit the application layout (`app/views/layouts/application.html.erb`).

  Use the following as a template to get you started. Make sure to look at the API documentation before asking a TA for help.

    ```erb
    <html>
      <head>
        <meta http-equiv="Content-type" content="text/html; charset=utf-8">
        <title>Quips and Quotes :: <%= controller.controller_name.capitalize %></title>
        <link rel="shortcut icon" href="/favicon.ico" type="image/x-icon" />
      
        <!-- #stylesheet: add my_styles using rails helper function# -->
        <!-- #javascript: add defaults using rails helper function# -->

        <%= csrf_meta_tag %>
      </head>

      <body>
        <div id="wrap">
          <div id="header">
            <%= link_to(image_tag('quips_n_quotes.png'), home_path) %>
           </div>
           <br />

           <div id="body">
             <%= yield %>
           </div>

           <!-- #footer partial should go here later (but not yet...)# -->

        </div>
      </body>
    </html>
    ```

1. Make sure Rails can find the new home page by adding the code below to the `config/routes.rb` file.  Add this code right after the `resources :quotes` line.  (You can delete the comments after you read them and clean up the file as well.) 

    ```ruby
    resources :quotes

    get 'home/index' => 'home#index', as: :home
    root to: 'home#index'
    ```

1. Promote these changes to the staging level in git and then commit them to the repository's styling branch.

1. Start the rails server and in browser click on the logo at the top of the page.

  Solve this error on your own.  (Just kidding...)

  The problem is that the layout references a 'home_path'  to a controller that we have not yet created.

1. Go back to master and then create a new branch off master called 'home'.

  ```bash
  git checkout master
  git checkout -b home
  ```

  Stop for a minute and look at your `app/assets/images` directory – the image you added is gone as are a number of other files you added! (Not really, it just isn't part of this branch so it looks as if it were gone - we will bring it back shortly.) 

  In the meantime, let’s continue on and create the new `home` branch.

1. Now we can create a home controller with an index action:

  ```bash
  bundle exec rails generate controller Home index
  ```

  This will create a controller called 'home_controller' with an index action as well as the associated view. 

1. Go to the `app/controllers/home_controller.rb` file and add the following line to the `index` method:

    ```ruby
    def index
      # ... existing code
      @quote = Quote.get_random_quote
    end
    ```

1. Replace the generic contents of `app/views/home/index.html.erb` with the sample code below.  Of course, replace each of the commented erb tags (`<%= %>`) with the appropriate line of code.

    ```erb
    <p>This site is an eclectic set of (somewhat) famous quotes. Today's quote of the day is as follows:</p>
    <p>&nbsp;</p>

    <h1><%= # QUOTE BODY GOES HERE %></h1><br>

    <h2><%= # SOURCE GOES HERE %></h2>

    <p>&nbsp;</p>
    <%= link_to 'Add a new quote to our list', new_quote_path %>
    ```

1. Open the `Quote` model in `app/models/quote.rb` and add in the method `get_random_quote` below.  

    
    ```ruby
    # Method to get a random quote for home page
    # 
    # @return [Quote]
    #   the random quote 
    def self.get_random_quote
      self.all.to_a[rand(self.all.length)]
    end
    ```
1. Run your server and reload a few times to see the ranndom quotes generating. But wait! You should see an error pop up after a few tries. This is a good time to put your ByeBug skills to use to figure out what went wrong. Try your best to figure it out before reading the next line.

1. You might have discovered that the random function is incorrect, since it is generating a random number that might be up to the index of the length, which would be out of bounds. You can quickly fix it as follows if you haven't already.

    ```ruby
    # Method to get a random quote for home page
    # 
    # @return [Quote]
    #   the random quote 
    def self.get_random_quote
      self.all.to_a[rand(self.all.length) - 1]
    end
    ```

1. What's even better, as it turns out you don't even need to generate the number yourself. In fact, Rails provides us with the useful function select, which gets a random element from a relation. Great! You can just replace the inside of the method with the following:

    ```ruby
      self.all.sample
    ```

  Add in some basic validation by requiring a body for the quote.  The appropriate code would be:
  
    ```ruby
        validates_presence_of :body
    ```

1. Commit all these changes to git.

1. Now we need to merge these changes onto the master branch. Switch back to the master branch now.  Using the command `git merge home` to pull the contents of the home branch into the master branch.  

        git checkout master
        git merge home

  Commit these changes to git.

1. When we restart the server, we see the correct information displayed on `home/index`, but no styling or image can be seen. That’s because we haven’t merged the contents of the styling branch into the master branch. Do that now and see that the appropriate styling exists.

1. Finally, it's time to add a statement of ownership to the footer of every page.

  In the `app/views` directory create a new directory called `partials`.

1. Create a new file in that directory called `_footer.html.erb` and add the contents shown below.  

    ```erb
    <div id="footer">
      <div id="copy">
        Webmaster: <%= # your mail_to link goes here; encode with javascript %> 
        Copyright &copy; 2016
      </div>
    </div>
    ```

  Note that you need to edit the [mail_to link](http://apidock.com/rails/ActionView/Helpers/UrlHelper/mail_to) so that it displays your name, references your email address and is encoded with Javascript (to prevent spammers from harvesting it).

1. Go to the `application.html.erb` file in the layouts directory and replace the #footer partial# comment with an erb tag that renders the partial footer you just created.

    ```ruby
    <%= render partial: 'partials/footer' %>
    ```

  Note that we don't include the `_` (underscore) or extension (`.html.erb`).

1. Restart the web server and view the site. Reload the home page several times to see new quotes appear.

1. Add new quotes (try once to skip the body and see the error message that is given) in the quotes section.

1. Commit the changes to git.

- - -
# <span class="mega-icon mega-icon-issue-opened"></span> Stop
Show a TA that your website is working, has a footer and random quote, and that you have committed your changes to git. Make sure the TA initials your sheet.
- - -  

Part 3: Submitting to a Remote Repository
==========
There is a great tutorial online for using remote repositories that I encourage you to check out later at [atlassian.com](https://www.atlassian.com/git/tutorial/remote-repositories).  Our goal here is to get you to submit a project to a remote repository today so we know you are prepared to do so when we start phase 2.  We will be submitting the lab you finished and have stored in a local git repository.

1. Go to [http://darkknight.hss.cmu.edu/users/password/new](http://darkknight.hss.cmu.edu/users/password/new) and type in your andrew email address (e.g. andrewid@andrew.cmu.edu). Check your Andrew Email and you should see an link to reset your DarkKnight password. Once you have done that login with your new password. Ask a TA if you need help.

2. You will have to upload your SSH key to the gitlab server.  There is a [document](http://67272.cmuis.net/files/67272/GitLab_Instructions.pdf) that will walk you through this process and TAs can help you with this as well.   (_As you go through this tutorial, please remember that you are not Sally User, so replace 'suser' with your own Andrew ID._)

3. Once your key is saved to gitlab, switch to the home directory of your project.  Be sure this is where your git repository is by typing `ls -a` and seeing that you have a hidden `.git` directory there.

4. Verify that you currently have no remote repository specified by typing `git remote -v`.  The -v flag tells us to be verbose and print out the remote url, if it exists.  You should get no results from this command.

5. Add a connection to the remote repository called 'origin' by typing the command below (replacing your Andrew ID where appropriate).  The reason this is phase_1.git is that this portion of the lab counts as credit for part of Phase 1.

  ```bash
  git remote add origin git@darkknight.hss.cmu.edu:YOURANDREWID/SimpleQuotes.git
  ```

6. Verify that the connection to the remote repository exists by retyping `git remote -v`.  Now you should see two urls listed for origin -- one to push and the other to fetch.

7. Push your code out to the remote repository with the command:

  ```bash
  git push origin master
  ```

8. Go to [darkknight.hss.cmu.edu](http://darkknight.hss.cmu.edu/) and verify your project and all its files are in the remote repository.


- - -
# <span class="mega-icon mega-icon-issue-opened"></span> Stop
Show a TA that you have completed the first part. Make sure the TA initials your sheet.
- - -  

Part 4: SQLite Practice
===========

__Note: If you are out of time, you may finish this last part outside of lab. You will get full credit for this lab.__

1. Now we will practice interacting with the database directly. Using the SQLite Manager in Firefox, [Base](http://menial.co.uk/base/), or the command line, open the `db/quotes_dev.sqlite3` database. 

If you want to use the command line, you can type `rails db` as a shortcut to open the database. Typing `.quit` will return you to the command line. Typing `.mode column` and `.header on` will make the output more readable.

1. Experiment with the interface by adding, deleting and searching for quotes using SQL. When you are ready, demonstrate to a TA that you can perform all three of these operations in SQLite Manager or on the command line.

- - -
# <span class="mega-icon mega-icon-issue-opened"></span> Stop
Show a TA that you can work with a sqlite3 database. Make sure the TA initials your sheet. You are now finished with the lab.
- - -
