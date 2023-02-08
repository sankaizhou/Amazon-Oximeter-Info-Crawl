# Amazon-Oximeter-Info-Crawl
Crawl Oximeter Info including product title, price, rating for data analysis
# Import required libraries
```
from bs4 import BeautifulSoup  
import requests  
import pandas as pd  
```
# Define a function to get the titles of products
```
def get_title(new_soup):
    try:
        title = new_soup.find("span", attrs={"id":'productTitle'}).text.strip()
        return title
    except AttributeError:
        title_string = ""
        return title_string
```
# Define a function to get the prices of products
```
def get_price(new_soup):
    try:
        price = new_soup.find("span", attrs={"class":'a-price aok-align-center reinventPricePriceToPayMargin priceToPay'}).find("span", attrs={"class": "a-offscreen"}).text.strip()
        return price
    except AttributeError:
        return
```
# Define a function to get the ratings of products
```
def get_rating(new_soup):
    try:
        rating = new_soup.find("span", attrs={"class":'a-icon-alt'}).text
    except:
        rating=""
    return rating
```
# Define a function to get the ratings of products
```
def get_rating(new_soup):
    try:
        rating = new_soup.find("span", attrs={"class":'a-icon-alt'}).text
    except:
        rating=""
    return rating
```
# Start condictional statement
```
if __name__=='__main__':
    URL= "https://www.amazon.com/s?k=%E8%A1%80%E6%B0%A7%E4%BB%AA&crid=3DMNBW0O40O1Q&sprefix=xue%27yang%27yi%2Caps%2C139&ref=nb_sb_noss"

    ''''Headers for request'''
    HEADERS = ({'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.0.0 Safari/537.36',
               'Accept-Language': 'en-US, en;q=0.5'
               })
    
    ''''HTTP Request'''
    webpage = requests.get(URL, headers=HEADERS)
    
    #print(webpage.content)
    
    #print(type(webpage.content))
    
    '''Soup object containing all data'''
    soup = BeautifulSoup(webpage.content, "html.parser")
    
    #print(soup)
    
    '''Fetch links as list of Tag objects'''
    links = soup.find_all("a", attrs={'class': 'a-link-normal s-underline-text s-underline-link-text s-link-style a-text-normal'})
    
    links_list = []
    
    for link in links:
        links_list.append(link.get('href'))
        
    d={"title":[], "price":[], "rating":[]}
    
    length_link_list=len(links_list)
    for link in links_list:
        new_webpage = requests.get("https://amazon.com" + link, headers=HEADERS)

        new_soup = BeautifulSoup(new_webpage.content, "html.parser")
        
        d['title'].append(get_title(new_soup))
        d['price'].append(get_price(new_soup))
        d['rating'].append(get_rating(new_soup))
        print('urls left:',length_link_list-links_list.index(link))
    
    amazon_df = pd.DataFrame.from_dict(d)

    amazon_df.to_csv("amazon_data.csv", header=True, index=False)
    
```
