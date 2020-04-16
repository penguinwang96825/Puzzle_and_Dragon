# Puzzle and Dragon
A note to build a crawler for P&D.

## Import packages
```python
import pandas as pd
import numpy as np
import time
import requests
import bs4
from selenium import webdriver
from bs4 import BeautifulSoup
from tqdm.notebook import tqdm
```

## Get dataframe
Get P&D card infromation from this [website](https://pad.skyozora.com/pets/statistics/).

```python
def get_pnd_dataframe():
    url = "https://pad.skyozora.com/pets/statistics/"
    r = requests.get(url, timeout=20).text
    soup = BeautifulSoup(r, "html.parser")

    pnd = []
    table = soup.find_all(name="table")[3]
    for i in range(20):
        trs = table.find_all(name="tr")[i+1]
        tds = trs.find_all(name="td")

        pet_name = tds[1].find(name="a").get("title")
        pet_name = pet_name.split("- ")[1]

        link = tds[1].find(name="a").get("href")
        link = "https://pad.skyozora.com/" + link

        review = tds[2].find_all(text=True)
        review = "".join(review)

        pnd.append([i+1, pet_name, link, review])
        
    df = pd.DataFrame(pnd)
    df.columns = ["rank", "pet_name", "link", "review"]
    
    return df
```

## Take a look at dataframe
