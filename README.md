## Add full text search to your Umbraco website in 15 minutes flat

0.651.nupkg 
This version can be installed and works!

## Why Create USiteSearch?

I build websites using the Umbraco content management system, and one feature that is often necessary 
is a site search. While you can use Examine for this purpose, my sites often require a Google-like search 
rather than a search specific to the site's content. Additionally, I need a user-friendly interface for 
the search and syntax highlighting to make the results more readable. 

In the past, I have copied and pasted my site search code between projects, but this has not been 
efficient and has resulted in bugs and other issues. To improve the quality of my code and make it easier to 
use, I have decided to add unit and integration tests and turn it into a system that can be easily installed in just 15 minutes. 
This system is called USiteSearch.

Earlier versions of the code used in USiteSearch can be seen in action on <a href="https://mysteriousuniverse.org/" target="_blank">Mysterious Universe</a> which has a total of 20,000 blog posts.

## Need an Umbraco developer?

You can reach me at <a href="https://www.yart.com.au/capabilities/umbraco-developer/" target="_blank">my website</a>

## Support

Contact me if you need support, this is free :-)

## Installation

Let's get **USiteSearch** working in a sample Umbraco website so you can see exactly how it works. Installing on the sample
site will teach you how to install it in your own site. Set up should take no longer than 15 minutes.

Note: This help can also be viewed as a <a href="https://youtu.be/gf2Gx8i6vr8" target="_blank">YouTube tutorial</a> if 
you prefer watching a video. 

### Installation steps

1. Clone this basic <a href="https://github.com/petrassurna/umbraco10samplesite.git" target="_blank">Umbraco 10 website</a>. 
The website comes with an embedded database so it should run without further modifications. 
The back office login for the website is:

	Username: admin@admin.com  
	Password: admin12345

	Alternatively clone the same website in <a href="https://github.com/petrassurna/umbraco11samplesite.git" target="_blank">Umbraco 11</a> if you are using Umbraco 11. Check the website runs and you can log in to the backoffice.

2. Install the latest nuget USiteSearch package via the NuGet package manager:

	<img src="https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/nuget-install.jpg" style="height:500px">


3. Install https://github.com/petrassurna/usitesearch/raw/main/nuget/USiteSearchProjectFiles-0.651.zip  
   Unzip the files in the *project folder*.

	This installs some css, images, javascript and a partial view. The files it installs are show below:

	<img src="https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/setup-sample.jpg" style="height:500px">

4. Run the project to view the sample website. Now let's add *USiteSearch*.
   'Open startup.cs and add these three using statements at the top of the file:

	```
	using Umbraco.Cms.Core.Notifications;
	using UmbracoExtensions;

	//add this line
	using USiteSearch.Notifications;
	```

	Add *AddUSiteSearch* to *ConfigureServices*:

	```
	public void ConfigureServices(IServiceCollection services)
	{
		services.AddUmbraco(_env, _config)
			.AddBackOffice()
			.AddWebsite()
			.AddComposers()
			.AddNotificationHandler<UmbracoApplicationStartingNotification, Bundling>()
			.AddUSiteSearch(services, "app_data/USiteSearch", 9) //add this
			.Build();
	}
	```

	This configures the USiteSearch to a LuceneProvider search provider:

	*app_data/USiteSearch* - is the relative path where Lucene will store data in the application.

	*9* - When displaying search results, the number of words to display each side of a word match.

	Note the line *AddNotificationHandler* which adds bundling via *Bundling.cs*. 
	This is an addition to the base Umbraco project but is something you should probably be adding for all css and javascript.
	Read more about [Umbraco Bundling](https://docs.umbraco.com/umbraco-cms/fundamentals/design/stylesheets-javascript#bundling-and-minification-for-javascript-and-css)


5. Next we need to enable the css and javascript used by USiteSearch. 
Add these lines in *views/master.cshtml* in the *head* section:

	```
	<link rel="stylesheet" href="usitesearch-css-bundle" />
	<script src="usitesearch-js-bundle"></script>
	```

	This includes these files via bundling:
	
	*~/USiteSearch/css/usitesearch.min.css* and   
	*~/USiteSearch/scripts/usitesearch.js*


6.  Add this line in *Views/master.cshtml* under the *body* tag:

	```
	@Html.Partial("~/Views/Partials/USiteSearch/USiteSearch.cshtml")
	```

7. Next we need to index the site, to do this, log into the backoffice and save and publish each of the pages.  
   This will create the lucene index under the *app_data* folder as specified in *startup.cs*:

	![app_data folder](https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/app-data.jpg)

	This folder can be deleted to clear the search.

8. In order to engage the search, we need to give a front end element the attribute *id="site-search"*  
This has been done for you in *master.cshtml*:

	```
	<li>
		<a href="#" id="site-search" title="Search the site">Site search</a>
	</li>
	```

	Click on the element and you should see the search bar, enter the term *animals*. After typing a few characters you should 
	see these results:

	![app_data folder](https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/search-animals.jpg)

	Refine the search, search for *animals lions*. Note the results reduce and each search term is highlighted with 9 words either
	side of the word match. This is the variable we set earlier in *startup.cs*.

9. Search for the term *play close* and notice it matches all pages: 

	![Search for pay close](https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/search-pay-close.jpg)

	This is because this text is in the margin. In order to remove this text from the search, add this tag to html elements you want excluded from the site search:

	![Search exclusion](https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/search-exclude.jpg)

	Typically you would exclude the navigation, footer and common margins. You will need to save each page again to reindex these pages.

10. If you want to remove whole pages from the site search, add the property *blockFromSearch* to document types:

	![Search exclusion](https://raw.githubusercontent.com/petrassurna/usitesearch/main/USiteSearch/images/block-from-search.jpg)

	This is already present in the sample project and when you check it on, pages will be removed from the search if you save them after checking the option.