# Web-Scraper

## Objective
My primary objective when starting this project was to gain a basic understanding of what it entails to extract data from a website and store that data effectively. This was achieved through the use of the Python library Scrapy for data extraction, MongoDB for data storage and organization, and a controlled website serving as our target. 
<br/>
<a href="https://realpython.com/web-scraping-with-scrapy-and-mongodb/#prepare-the-scraper-scaffolding"> Link to Original Project Guide</a>



### Skills Learned

- Setting up and working with different Python virtual environments
- Ability to recognize CSS selectors to identify desired web content
- MongoDB integration for simplified data management
- Data pipeline design from extraction -> transformation -> storage
- Ethical data scraping practices like respecting robots.txt files

### Tools Used

- Scrapy for an efficient all-in-one data scraping framework
- MongoDB for a scalable and easy-to-work-with database for storing and querying scraped data.
- PyMongo as a way to interact with my MongoDB database from Python programs.

## Workflow Outline

### Creating the Scrapy Project
The first step in the process is to create the Scrapy project, which sets up a directory with our project name, as pictured below.

<img width="288" height="303" alt="image" src="https://github.com/user-attachments/assets/aeaeeeb6-3d2c-4b4e-b691-34503718e44b" />
<br/>

- books/ is the root directory, where our entire project is housed.
- books/spiders/ is where we will build and define the guidelines of our web crawler.
- books/items.py is where we will tell the scraper what kind of information we want to collect, as well as how to organize it.
- books/middlewares.py is where you can add extra rules or tools that change how the scraper behaves while it runs.
- books/pipelines.py is where scraped data gets cleaned up, organized, and prepared before being stored.
- books/settings.py is where we can adjust settings like speed, limits, or other options to fine-tune how the scraper runs.

### Building the Spider
