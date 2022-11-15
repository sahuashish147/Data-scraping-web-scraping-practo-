# Infinite-Scroll-web-scraping
Web scraping for the sites which includes infinite scrolling.
Here I used Python selenium for automatic scrolling till the end of the page and then used beautiful soup module for extraction of data from that scrolled page.

Python - 3
complete the certain task like:- pip install selenium, bs4, pandas, requests (in your command prompt)

## python IDE:-
## Import below mentioned python modules

    import pandas as pd
    import time
    from selenium import webdriver
    from bs4 import BeautifulSoup
    import requests

## Here, I've done the data extraction from [practo](https://www.practo.com/) website which stored all the information different kind of doctors in different cities and there consultaion charge. 
    driver = webdriver.Chrome(executable_path=r"C:\Windows\chromedriver.exe") #here provide chormedriver path in your system

    driver.get("https://www.practo.com/mumbai/doctors")
    time.sleep(3) #allow 3 second for the web page to open
    scroll_height = driver.execute_script("return document.body.scrollHeight")
    new_height = 0 

    while True:
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight-1500);") #scroll to the last page
        time.sleep(3) #scroll time

        new_height = driver.execute_script("return document.body.scrollHeight")

        if new_height == scroll_height:
            break #breaks the loop if the previous page height is same as the new height after scrolling and refreshing
        scroll_height = new_height #assigning 

