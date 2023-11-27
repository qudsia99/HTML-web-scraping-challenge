# HTML-web-scraping-challenge : Analysis of Mars Data 

### General Info

This assignment first looks at Mars news extracted from a tutorial website (designed for web scraping purposes) in part_1_mars_news.ipynb utilizing BeautifulSoup and Browser to parse the site. In the following notebook, an analysis of real data is made, with over 1500 rows of data of Mars' listed here: 
  
  ##### id: the identification number of a single transmission from the Curiosity rover
  ##### terrestrial_date: the date on Earth
  ##### sol: the number of elapsed sols (Martian days) since Curiosity landed on Mars
  ##### ls: the solar longitude
  ##### month: the Martian month
  ##### min_temp: the minimum temperature, in Celsius, of a single Martian day (sol)
  ##### pressure: The atmospheric pressure at Curiosity's location

Using more python libraries such as matplotlib and pandas, to find significant insights in the data, which were later visualized in graphs. 

#### Brief overview of the mars_news notebook:

#Visit the Mars news site
```
browser = Browser('chrome')
url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
browser.visit(url)

#Create a Beautiful Soup object
html = browser.html
soup = soup(html,'html.parser')

#Extract all the text elements
print(html)
```

#Grabbing all the title's and previews using find_all()

  #Grabbing all the title's
  `titles = soup.find_all('div',class_='content_title')`

  #Grabbing all the preview text's
  `preview_texts = soup.find_all('div',class_='article_teaser_body')`

#Create an empty list to store the dictionaries

#First, collecting all the article info in a variable for easier access
`articles = soup.find_all('div',class_="col-md-8")`

```
for a in articles:
    #Grabbing all the title's
    titles = a.find(class_='content_title').text.strip()

    #Grabbing all the title preview's
    preview_texts = a.find(class_='article_teaser_body').text.strip()

    #Adding the findings inside our dictionary
    mars_dict = {
    'title':titles,
    'preview':preview_texts
    }
    
    # Append the dictionary, inside our list.
    mars_info.append(mars_dict)
```

### PART-2------- mars_weather.ipynb preview:

#Create an empty list
`row_list = []`

### Loop through the scraped data to create a list of rows
```
data = soup.find('tbody')

for row in data.find_all('tr',class_='data-row')[0:]:
    info_td = row.find_all('td')
    id = info_td[0].get_text()
    ter_date = info_td[1].get_text()
    sol = info_td[2].get_text()
    ls =info_td[3].get_text()
    month = info_td[4].get_text()
    min_temp = info_td[5].get_text()
    pressure = info_td[6].get_text()
```

    #Add all column data into a new dictionary with respective columns
   ```
  mars_diction = {
        'id': id,
        'terrestrial_date': ter_date,
        'sol': sol,
        'ls':ls,
        'month':month,
        'min_temp':min_temp,
        'pressure':pressure,
    }

    # Add dictionary to an empty list
     row_list.append(mars_diction)
```

#### Change data types for data analysis

```
mars_pd['terrestrial_date'] = mars_pd['terrestrial_date'].astype('datetime64')
mars_pd[['sol','ls','month']] = mars_pd[['sol','ls','month']].astype('int64')
mars_pd[['min_temp','pressure']] = mars_pd[['min_temp','pressure']].astype('float64')
```

#### Plot the average temperature by month

```
avg_temp_by_month = mars_pd.groupby('month')['min_temp'].mean()

avg_temp_by_month.plot(kind='bar',xlabel = 'Month',ylabel='Temperature')
```

#Identify the coldest and hottest months in Curiosity's location

```
ordered_temp = mars_pd.groupby('month')['min_temp'].mean().sort_values(ascending=True)

ordered_temp.plot(kind='bar',xlabel = 'Month',ylabel='Temperature')
```

#Plot the average pressure by month

```
pressure_by_month = mars_pd.groupby('month')['pressure'].mean().sort_values(ascending=True)

pressure_by_month.plot(kind='bar',xlabel='Month',ylabel = 'Atmoshperic Pressure')
```

#Write the data to a CSV

`mars_pd.to_csv('mars_data.csv',header=True, index=True)`

#quit browser
browser.quit()

