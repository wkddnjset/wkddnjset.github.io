---
title: (Python) P2P 자동투자 프로그램, 크롤링으로 구현하기
comments: true
description: 투게더펀딩이라는 사이트에서 자동으로 투자해주는 크롤링 프로그램을 제작해보도록하겠습니다.
categories:
 - Python
tags: python develop crawling
---

## 크롤링 방법

- [[투게더 펀딩](https://www.together.co.kr)] 사이트에서 자동으로 투자하는 프로그램을 만들어보겠습니다!!!
- [[Selenium](https://beomi.github.io/2017/02/27/HowToMakeWebCrawler-With-Selenium/)]을 사용하도록 하겠습니다!!!

> Selenium은 동적웹에서 크롤링을 하거나, 웹에서 액션(스크롤, 클릭)이 필요한 경우 사용됩니다!

## 프로그램 설치

{% highlight python linenos %}
(Together) C:\Users\wk647\Desktop\Project\Together> pip install selenium
(Together) C:\Users\wk647\Desktop\Project\Together> pip install bs4
{% endhighlight %}

가상환경을 만들고 **Selenium**과 **BeautifulSoup** 라이브러리를 설치합니다. **Selenium**을 사용하기 위해서는 **WebDriver**을 따로 설치해야합니다. 

- [[Chrome WebDriver 다운로드 링크](https://sites.google.com/a/chromium.org/chromedriver/downloads)]

설치한 파일은 가상환경 내부에 `src` 파일을 만들고 해당 위치에 저장합니다.

## 로그인하기

{% highlight python linenos %}
from selenium import webdriver
from bs4 import BeautifulSoup
import getpass

# 로그인 URL
url = "https://www.together.co.kr/menu/index.php?menu=login"
driver = webdriver.Chrome('./chromedriver')
driver.get(url)

userid = input("이메일을 입력하세요.")
# 비밀번호를 안보이게 하기 위해서 getpass 라이브러리를 사용했다.
password = getpass.getpass("비밀번호를 입력하세요")

driver.find_element_by_name('user_id').send_keys(userid)
driver.find_element_by_name('password').send_keys(password)
# 로그인 버튼 누르기
driver.find_element_by_xpath('//input[@class="btn_darkblue bold"]').click()
{% endhighlight %}

웹크롤링 같은 경우 **Html**, **CSS** 구조를 알고 있으면 쉽게 응용할 수 있습니다.

xpath('//테그[@속성="속성값"]/태그[@속성="속성값"]/태그')

> xpath를 잘 사용하면 매우 편합니다.



