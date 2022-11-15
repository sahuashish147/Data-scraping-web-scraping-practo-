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
    
## Scrolling automation ([Selenium](https://pypi.org/project/selenium/)):-
## Here, I've done the data extraction from [practo](https://www.practo.com/) website which stored all the information different kind of doctors in different cities and there consultaion charge. But the website is kind of infinite scrolling; that means if we move down to the page our information about doctor also increases. So if we directly extract the data with help of beautifulsoup it will only extract around 10-15 doctor's information but here we want all the doctors information about doctors that belongs to particular cities. Selenium  with python is helpful to automatic scroll our website till the last page.

    driver = webdriver.Chrome(executable_path=r"C:\Windows\chromedriver.exe") #here provide chormedriver path in your system

    driver.get("https://www.practo.com/mumbai/doctors") #Website link for data scraping, here i provided the practo website link for mumbai city only
    time.sleep(3) #allow 3 seconds for the web page to open
    scroll_height = driver.execute_script("return document.body.scrollHeight") #this will give the page height
    new_height = 0 

    while True:
        driver.execute_script("window.scrollTo(0, document.body.scrollHeight-1500);") #scroll to the last page 
        #here i subracted with 1500 beacuse I don't want to go the bottom of the page, I just want to scroll just above the bottom of the page
        #(if you want to scroll down to the bottom, then remove -1500 from the code)
        
        time.sleep(3) #scroll time; 3 seconds

        new_height = driver.execute_script("return document.body.scrollHeight")

        if new_height == scroll_height:
            break #breaks the loop if the previous page height is same as the new height after scrolling and refreshing
        scroll_height = new_height #assigning 


## Web scraping ([beautifulsoup](https://beautiful-soup-4.readthedocs.io/en/latest/)):-
## Now our page scrolled down at the bottom; now we have to extract data from that scrolled page with the help of beautifulsoup module from python. You need to inspect the page and provide the class name and elements by inspection of HTML code of the page.

    #I've created the list variable to store our data by appending each doctors information in the list.
    link = [] 
    doc_name = []
    exp = []
    info = []
    fee = []
    types =[]
    city =[]
    local =[]
    rating=[]
    feedback =[]
    
    #provided that page link to the beautifulsoup. Page link was stored in driver while working on selenium, that page link given beautifulsoup like driver.page_source
    soup = BeautifulSoup(driver.page_source, "html.parser")
    for parent in soup.find_all("div", class_="u-border-general--bottom"): #this will run the loop for class which stored doctors information and select each element.
        #appending each doctors information in each type of lists 
        second_half = parent.find('a')['href']
        link.append("https://www.practo.com/" + second_half) #links of all doctors
        doc_name.append(parent.select(".doctor-name")[0].get_text()) #Doctor name
        exp.append(parent.select(".uv2-spacer--xs-top")[0].get_text().split()[0]) #doctor experience
        info.append(parent.find("div", class_ = "info-section").text)
        fee.append(parent.find('span', attrs= {'data-qa-id':'consultation_fee'}).text)
        local.append(parent.find('span', attrs={'data-qa-id':'practice_locality'}).text)
        city.append(parent.find('span', attrs={'data-qa-id':'practice_city'}).text)
        types.append(parent.find("div", class_ = "u-grey_3-text").find("div", class_ = "u-d-flex").span.text)
        try:
            st2=parent.find('span', attrs={'data-qa-id':'doctor_recommendation'}).text
        except:
            st2=""
        rating.append(st2)

        try:
            st1=parent.find('span', attrs={'data-qa-id':'total_feedback'}).text
        except:
            st1=""
        feedback.append(st1)

## Database by [pandas](https://pandas.pydata.org/):-
## Information stored in the lists and then this list are used to create our database by the help of pandas module. In pandas DataFrame is used to create 2d table so that we can store, analyse and manipulate the database easily. Pandas is very handy module. 
Here I provided the dictionary of column name as keys and lists as values;
{variable_name2(column1 name) : [list of values] ,  variable_name2(column2 name) : [list of values],.....n number of columns can be created like this}

    df_bglr = pd.DataFrame({"Name":doc_name, "experience" : exp, 'fee': fee, 'types':types, 'city':city, 'rating':rating,
                  "feedback": feedback})
    df_bglr.info() #this gives the information about database; number of columns, rows, null values, datatype 
