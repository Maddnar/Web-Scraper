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
The first step in the process is to create the Scrapy project, which sets up a directory with our project name, as pictured below:

<img width="288" height="303" alt="image" src="https://github.com/user-attachments/assets/aeaeeeb6-3d2c-4b4e-b691-34503718e44b" />
<br/>

- books/ is the root directory, where our entire project is housed.
- books/spiders/ is where we will build and define the guidelines of our web crawler.
- books/items.py is where we will tell the scraper what kind of information we want to collect, as well as how to organize it.
- books/middlewares.py is where you can add extra rules or tools that change how the scraper behaves while it runs.
- books/pipelines.py is where scraped data gets cleaned up, organized, and prepared before being stored.
- books/settings.py is where we can adjust settings like speed, limits, or other options to fine-tune how the scraper runs.

### Building out the Spider

In books/spiders/ we find book.py, the program where our spider is actually defined:

<img width="444" height="398" alt="image" src="https://github.com/user-attachments/assets/71792a22-f757-4a10-9ddf-c173f9a0ef68" />


A lot is going on here, but the parse() function is the only thing we really need to turn our attention to. In this function, we loop through each book on the page, collecting URLs, titles, and prices - storing them in Items (explained more later). The function also handles pagination, which means it continues to the next page as long as one exists. And last but not least, the function logs information and includes error handling to make sure our scraper runs reliably. 

### Defining Items
In Books/items.py, we create a definition for BooksItem, which tells the scraper what kind of information to collect and how to organize it: 

```python
class BooksItem(scrapy.Item):
    _id = scrapy.Field()
    url = scrapy.Field()
    title = scrapy.Field()
    price = scrapy.Field()
```
This code defines the fields we are trying to collect, which are a unique ID (_id_), the book's URL, title, and price. This ensures that all scraped data is structured consistently, making it much easier to process and store. Overall, items.py serves as the blueprint for the pipeline, ensuring that each piece of data flows through the scraper in an organized manner. 

### Processing Data and Storing Data in MongoDB

In books/pipelines.py, we define how the scraped data is cleaned, organized, and stored in our database:

```python
class MongoPipeline:
    COLLECTION_NAME = "books"
    
    def __init__(self, mongo_uri, mongo_db):
        self.mongo_uri = mongo_uri
        self.mongo_db = mongo_db
    
    @classmethod
    def from_crawler(cls, crawler):
        return cls(
            mongo_uri=crawler.settings.get("MONGO_URI"),
            mongo_db=crawler.settings.get("MONGO_DATABASE"),
        )
    
    def open_spider(self, spider):
        self.client = pymongo.MongoClient(self.mongo_uri)
        self.db = self.client[self.mongo_db]

    def close_spider(self, spider):
        self.client.close()

    def process_item(self, item, spider):
        item_id = self.compute_item_id(item)
        item_dict = ItemAdapter(item).asdict()

        self.db[self.COLLECTION_NAME].update_one(
            filter={"_id": item_id},
            update={"$set": item_dict},
            upsert=True
        )

        return item

    def compute_item_id(self, item):
        url = item["url"]
        return hashlib.sha256(url.encode("utf-8")).hexdigest()
```

In the code above, the three main sections to examine are open_spider(), process_item(), and close_spider (). open_spider() initializes a connection to our MongoDB server, process_item() normalizes our data and inserts it into mongoDB, and close_spider() closes our connection to mongoDB. The connection and interaction between our spider and our database is established using PyMongo.

### Running the Scraper

Running the scraper from the command line is simple. First, make sure you are in the correct directory:

```powershell
PS C:\VSC>
PS C:\VSC\books> 
PS C:\VSC\books\books> 
```
Next, use the scrapy command to run your spider:

```powershell
PS C:\\VSC\books\books> scrapy crawl book
```
For a while, nothing will appear in your console, but eventually your console will reset, meaning your spider has finished crawling the website. To verify that your spider was successful in extracting the correct information, you can check your MongoDB database: 
