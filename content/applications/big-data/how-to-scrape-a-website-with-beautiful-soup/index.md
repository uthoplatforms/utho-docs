---
slug: Scraping-website-beautiful-soup-guide
description: "Mastering Data Collection Over Time: A Guide to Utilizing Beautiful Soup in Python for Continuous Scraping and Spreadsheet Export"
keywords: ['beautiful soup', 'python', 'scraping', 'tinydb', 'data']
modified: 2024-05-15
modified_by:
  name: Utho
published: 2024-05-15
title: "Scrape a Website with Beautiful Soup"
title_meta: "How to Scrape a Website with Beautiful Soup"
dedicated_cpu_link: true
authors: ["Pawan Kumar"]
---

How to Scrape a Website with BeautifulSoup

## What is Beautiful Soup?

[Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/) simplifies the task of parsing HTML or XML documents in Python, enabling easy extraction of desired data. Widely employed for web scraping, it boasts a user-friendly interface and seamless encoding conversion.

With the structured nature of web pages, Beautiful Soup facilitates navigation through this complexity, empowering users to extract pertinent information. This guide illustrates crafting a Python script to scrape motorcycle prices from Craigslist. Configured to execute at intervals via a cron job, the extracted data is exported to an Excel spreadsheet for trend analysis. Adaptation to diverse websites or search queries is effortless by substituting URLs and adjusting the script accordingly.

## Install Beautiful Soup

### Install Python

               {{< content "install_python_miniconda" >}}

### Install Beautiful Soup and Dependencies

Ensure your system is up to date.

        sudo apt update && sudo apt upgrade

Utilize pip to install the latest version of Beautiful Soup.

        pip install beautifulsoup4

Install necessary dependencies:

        pip install tinydb urllib3 xlsxwriter lxml

## Build a Web Scraper

### Required Modules

`BeautifulSoup` from bs4 facilitates webpage parsing. datetime module handles date manipulation. `Tinydb` offers a NoSQL database API, while urllib3 manages HTTP requests. Additionally, `xlsxwriter` API assists in creating Excel spreadsheets.


Open `craigslist.py` in a text editor and include essential import statements.

                {{< file "craigslist.py" python >}}
                from bs4 import BeautifulSoup
                import datetime
                from tinydb import TinyDB, Query
                import urllib3
                import xlsxwriter
                {{< /file >}}

### Add Global Variables

Following imports, set global variables and configure options:

                {{< file "craigslist.py" python >}}
                urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

                url = 'https://elpaso.craigslist.org/search/mcy?sort=date'
                total_added = 0
                {{< /file >}}

`url` stores the target webpage's URL, while `total_added` tracks the total results added to the database. `urllib3.disable_warnings()` ignores SSL certificate warnings.

### Retrieve the Webpage

The make_soup function sends a GET request to the designated URL and converts resulting HTML into a BeautifulSoup object.

             {{< file "craigslist.py" python >}}
             def make_soup(url):
              http = urllib3.PoolManager()
              r = http.request("GET", url)
              return BeautifulSoup(r.data,'lxml')
              {{< /file >}}

If `make_soup` encounters errors, refer to [urllib3 docs](https://urllib3.readthedocs.io/en/latest/)  docs for detailed troubleshooting.

Beautiful Soup offers various parsers, each with different strictness levels regarding webpage structure. For this guide's example script, the lxml parser suffices. Explore other options in the [official documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/). based on your requirements.

### Process the Soup Object

`BeautifulSoup` objects are structured in a tree format. To access desired data, familiarize yourself with the original HTML's organization. Inspect the webpage's source by right-clicking and selecting **View page source** (or **Inspect**) in your browser:

                {{< file "https://elpaso.craigslist.org/search/mcy?sort=date" html >}}
                <li class="result-row" data-pid="6370204467">
                <a href="https://elpaso.craigslist.org/mcy/d/ducati-diavel-dark/6370204467.html" class="result-image gallery" data-ids="1:01010_8u6vKIPXEsM,1:00y0y_4pg3Rxry2Lj,1:00F0F_2mAXBoBiuTS">
                <span class="result-price">$12791</span>
                </a>
                <p class="result-info">
                <span class="icon icon-star" role="button">
                <span class="screen-reader-text">favorite this post</span>
                </span>
                <time class="result-date" datetime="2017-11-01 19:38" title="Wed 01 Nov 07:38:13 PM">Nov  1</time>
                <a href="https://elpaso.craigslist.org/mcy/d/ducati-diavel-dark/6370204467.html" data-id="6370204467" class="result-title hdrlnk">Ducati Diavel | Dark</a>
                <span class="result-meta">
                <span class="result-price">$12791</span>
                <span class="result-tags">
                pic
                <span class="maptag" data-pid="6370204467">map</span>
                </span>
                <span class="banish icon icon-trash" role="button">
                <span class="screen-reader-text">hide this posting</span>
                </span>
                <span class="unbanish icon icon-trash red" role="button" aria-hidden="true"></span>
                <a href="#" class="restore-link">
                <span class="restore-narrow-text">restore</span>
                <span class="restore-wide-text">restore this posting</span>
                    </a>
                 </span>
               </p>
             </li>
               {{< /file >}}

Filter webpage snippets by selecting li HTML tags, specifically those with a class of `result-row. results` variable stores snippets matching this criterion.


                 results = soup.find_all("li", class_="result-row")

Attempt to create a record conforming to the target snippet's structure. If it doesn't match, Python will raise an exception, skipping this record and snippet.


                 {{< file "craigslist.py" python >}}
                 rec = {
                'pid': result['data-pid'],
                'date': result.p.time['datetime'],
                'cost': clean_money(result.a.span.string.strip()),
                'webpage': result.a['href'],
                'pic': clean_pic(result.a['data-ids']),
                'descr': result.p.a.string.strip(),
                'createdt': datetime.datetime.now().isoformat()
                 }
                 {{< /file >}}

Utilize Beautiful Soup's array notation to access HTML element attributes.

            'pid': result['data-pid']

Additional data attributes may be nested within the HTML structure and can be accessed using a combination of dot and array notation. For example, the posting date of a result is stored in datetime, a data attribute of the time element, which is nested within a p tag, itself a child of result. Access this value using the following format:

             'date': result.p.time['datetime']

Occasionally, the required information is the content between the start and end tags. BeautifulSoup offers the string method to access this content.

              <span class="result-price">$12791</span>

Access it by:

             'cost': clean_money(result.a.span.string.strip())

The value here is further processed by using the Python `strip()` function, as well as a custom function `clean_money` that removes the dollar sign.

Many Craigslist listings feature images of the item for sale. The custom function clean_pic assigns the URL of the first picture to pic:

              'pic': clean_pic(result.a['data-ids'])

Enhance the record with metadata. For instance, you can include a field to indicate the creation time of a particular record.

        'createdt': datetime.datetime.now().isoformat()

Utilize the Query object to verify whether a record already exists in the database before inserting it, preventing duplicate entries.

             {{< file "craigslist.py" python >}}
             Result = Query()
             s1 = db.search(Result.pid == rec["pid"])

             if not s1:
             total_added += 1
             print ("Adding ... ", total_added)
             db.insert(rec)
             {{< /file >}}

### Error Handling

It's crucial to address two types of errors, not stemming from the script itself but rather from snippet structure, which can trigger Beautiful Soup's API to throw an error.

An AttributeError arises when the dot notation fails to find a sibling tag for the current HTML tag. For instance, if a specific snippet lacks the anchor tag, accessing the cost key will result in an error, as it traverses and thus necessitates the anchor tag.

The other error, a KeyError, occurs when a mandatory HTML tag attribute is absent. For instance, without a data-pid attribute in a snippet, accessing the pid key will raise an error.

If either of these errors occurs during result parsing, the respective result will be skipped to prevent insertion of malformed snippets into the database.

                    {{< file "craigslist.py" python >}}
                     except (AttributeError, KeyError) as ex:
                     pass
                     {{< /file >}}

### Cleaning Functions

These are two short custom functions to clean up the snippet data. The `clean_money` function strips any dollar signs from its input:

                     {{< file "craigslist.py" python >}}
                     def clean_money(amt):
                     return int(amt.replace("$",""))
                     {{< /file >}}

The `clean_pic` function generates a URL for accessing the first image in each search result:

                     {{< file "craigslist.py" python >}}
                     def clean_pic(ids):
                     idlist = ids.split(",")
                     first = idlist[0]
                     code = first.replace("1:","")
                     return "https://images.craigslist.org/%s_300x300.jpg" % code
                     {{< /file >}}

The function extracts and cleans the id of the first image, then adds it to the base URL.

### Write Data to an Excel Spreadsheet

The `make_excel` function is responsible for transferring data from the database to an Excel spreadsheet.

Define spreadsheet-related variables.

                     {{< file "craigslist.py" python >}}
                     Headlines = ["Pid", "Date", "Cost", "Webpage", "Pic", "Desc", "Created Date"]
                     row = 0
                     {{< /file >}}

The **Headlines** variable consists of titles for the spreadsheet columns. The row variable monitors the current position within the spreadsheet.

Employ `xlsxwriter` to initiate a workbook and append a worksheet to accommodate the data.

            {{< file "craigslist.py" python >}}
            workbook = xlsxwriter.Workbook('motorcycle.xlsx')
            worksheet = workbook.add_worksheet()
            {{< /file >}}

Set up the worksheet.

               {{< file "craigslist.py" python >}}
               worksheet.set_column(0,0, 15) # pid
               worksheet.set_column(1,1, 20) # date
               worksheet.set_column(2,2, 7)  # cost
               worksheet.set_column(3,3, 10)  # webpage
               worksheet.set_column(4,4, 7)  # picture
               worksheet.set_column(5,5, 60)  # Description
               worksheet.set_column(6,6, 30)  # created date
               {{< /file >}}

The first two parameters remain constant in the `set_column` method. These parameters define a range of columns from the first specified column to the next. The last parameter indicates the width of the column in characters.

Input the column headers into the worksheet.

             {{< file "craigslist.py" python >}}
             for col, title in enumerate(Headlines):
             worksheet.write(row, col, title)
            {{< /file >}}

Record the data into the database.

           {{< file "craigslist.py" python >}}
           for item in db.all():
           row += 1
           worksheet.write(row, 0, item['pid'] )
           worksheet.write(row, 1, item['date'] )
           worksheet.write(row, 2, item['cost'] )
           worksheet.write_url(row, 3, item['webpage'], string='Web Page')
           worksheet.write_url(row, 4, item['pic'], string="Picture" )
           worksheet.write(row, 5, item['descr'] )
           worksheet.write(row, 6, item['createdt'] )
           {{< /file >}}

Most fields within each row are written using `worksheet.write`; however, `worksheet.write_url` is employed for listing and image URLs, rendering the resulting links clickable in the final spreadsheet.

Conclude the Excel workbook session.

         {{< file "craigslist.py" python >}}
         workbook.close()
         {{< /file >}}

### Main Routine

The core routine will loop through each page of search results, executing the `soup_process` function on each page. It maintains a tally of the total database entries added in the global variable total_added, which is updated within the soup_process function and showcased once the scraping concludes. Ultimately, it establishes a `TinyDB` database named db.json to store the parsed data. After the scraping is finished, the database is handed over to the make_excel function to generate a spreadsheet.

               {{< file "craigslist.py" python >}}
               def main(url):
               total_added = 0
               db = TinyDB("db.json")

              while url:
                     print ("Web Page: ", url)
                     soup = soup_process(url, db)
                     nextlink = soup.find("link", rel="next")

               url = False
               if (nextlink):
               url = nextlink['href']

              print ("Added ",total_added)

              make_excel(db)
              {{< /file >}}

  An example run could resemble the following. Observe that each page contains an embedded index in the URL, enabling Craigslist to determine the starting point of the next data page.

                 $ python3 craigslist.py
                 Web Page:  https://elpaso.craigslist.org/search/mcy?sort=date
                 Adding ...  1
                 Adding ...  2
                 Adding ...  3
                 Web Page:  https://elpaso.craigslist.org/search/mcy?s=120&sort=date
                 Web Page:  https://elpaso.craigslist.org/search/mcy?s=240&sort=date
                 Web Page:  https://elpaso.craigslist.org/search/mcy?s=360&sort=date
                 Web Page:  https://elpaso.craigslist.org/search/mcy?s=480&sort=date
                 Web Page:  https://elpaso.craigslist.org/search/mcy?s=600&sort=date
                 Added  3

## Set up Cron to Scrape Automatically

This section will configure a cron job to automatically execute the scraping script at specified intervals.

        ssh normaluser@<Utho Public IP>

Ensure that the entire `craigslist.py` script is located in the home directory.

           {{< file "craigslist.py" python >}}
           from bs4 import BeautifulSoup
           import datetime
           from tinydb import TinyDB, Query
           import urllib3
           import xlsxwriter

           urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

           url = 'https://elpaso.craigslist.org/search/mcy?sort=date'
           total_added = 0

           def make_soup(url):
              http = urllib3.PoolManager()
              r = http.request("GET", url)
              return BeautifulSoup(r.data,'lxml')

           def main(url):
              global total_added
            db = TinyDB("db.json")

             while url:
                print ("Web Page: ", url)
                soup = soup_process(url, db)
                nextlink = soup.find("link", rel="next")

             url = False
                  if (nextlink):
                  url = nextlink['href']

            print ("Added ",total_added)

            make_excel(db)

            def soup_process(url, db):
            global total_added

            soup = make_soup(url)
            results = soup.find_all("li", class_="result-row")

            for result in results:
                  try:
                   rec = {
                    'pid': result['data-pid'],
                    'date': result.p.time['datetime'],
                    'cost': clean_money(result.a.span.string.strip()),
                    'webpage': result.a['href'],
                    'pic': clean_pic(result.a['data-ids']),
                    'descr': result.p.a.string.strip(),
                    'createdt': datetime.datetime.now().isoformat()
                     }

                    Result = Query()
                    s1 = db.search(Result.pid == rec["pid"])

                    if not s1:
                    total_added += 1
                    print ("Adding ... ", total_added)
                    db.insert(rec)

                     except (AttributeError, KeyError) as ex:
                     pass

                    return soup

                   def clean_money(amt):
                    return int(amt.replace("$",""))

                  def clean_pic(ids):
                  idlist = ids.split(",")
                  first = idlist[0]
                  code = first.replace("1:","")
                  return "https://images.craigslist.org/%s_300x300" % code

                def make_excel(db):
                Headlines = ["Pid", "Date", "Cost", "Webpage", "Pic", "Desc", "Created Date"]
                 row = 0

            workbook = xlsxwriter.Workbook('motorcycle.xlsx')
            worksheet = workbook.add_worksheet()

             worksheet.set_column(0,0, 15) # pid
             worksheet.set_column(1,1, 20) # date
             worksheet.set_column(2,2, 7)  # cost
             worksheet.set_column(3,3, 10)  # webpage
             worksheet.set_column(4,4, 7)  # picture
             worksheet.set_column(5,5, 60)  # Description
             worksheet.set_column(6,6, 30)  # created date

             for col, title in enumerate(Headlines):
             worksheet.write(row, col, title)

             for item in db.all():
             row += 1
            worksheet.write(row, 0, item['pid'] )
            worksheet.write(row, 1, item['date'] )
            worksheet.write(row, 2, item['cost'] )
            worksheet.write_url(row, 3, item['webpage'], string='Web Page')
            worksheet.write_url(row, 4, item['pic'], string="Picture" )
            worksheet.write(row, 5, item['descr'] )
            worksheet.write(row, 6, item['createdt'] )

            workbook.close()

            main(url)
            {{< /file >}}

Insert a cron tab entry as the user:

                      crontab -e

This cron entry will execute the Python program daily at 6:30 a.m.

                      30 6 * * * /usr/bin/python3 /home/normaluser/craigslist.py

The Python program will generate the `motorcycle.xlsx` spreadsheet in the `/home/normaluser/` directory.

## Retrieve the Excel Report

**On Linux**

Utilize scp to transfer `motorcycle.xlsx` from the remote machine executing your Python program to this machine:

           scp normaluser@<Utho Public IP>:/home/normaluser/motorcycle.xlsx .

**On Windows**

Use Firefox's built-in sftp capabilities. Type the following URL in the address bar and it will request a password. Choose the spreadsheet from the directory listing that appears.

    sftp://normaluser@<Utho Public IP>/home/normaluser
