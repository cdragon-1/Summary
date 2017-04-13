##### Crawling, Beautifulsoup

가수 이름 및 타이틀을 가져오기 위해 구현해본 crawling code


import requests
from bs4 import BeautifulSoup


def get_title_top1000():
    url = 'https://www.letssingit.com/artists/popular/1'
    source_code = requests.get(url)
    plain_text = source_code.text
    soup = BeautifulSoup(plain_text, "html.parser")

    items = soup.find_all('a', 'high_profile')

    result = []
    for item in items:
        k = item.string
        result.append(k)
    return result
