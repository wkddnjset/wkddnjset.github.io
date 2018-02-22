---
title: (Python) 부동산 데이터 수집, 크롤러 제작하기
comments: true
description: KB리브온이라는 KB국민은행에서 운영하는 부동산 관련 정보를 제공해주는 사이트에서 데이터를 수집해오는 크롤러를 제작해보도록 하겠습니다.
categories:
 - Python
tags: python develop crawling
---

## 크롤링 방법

- [[KB리브온](http://nland.kbstar.com/)]이라는 사이트에서 아파트 부동산에 대한 정보를 크롤링 해보도록 하겠습니다.
- 주로 [[Selenium](https://beomi.github.io/2017/02/27/HowToMakeWebCrawler-With-Selenium/)]이라는 라이브러리를 자주 사용합니다!!

> Selenium은 동적웹에서 크롤링을 하거나, 웹에서 액션(스크롤, 클릭)이 필요한 경우 사용됩니다!

## 프로그램 설치

{% highlight python linenos %}
(Together) C:\Users\wk647\Desktop\Project\Together> pip install selenium
(Together) C:\Users\wk647\Desktop\Project\Together> pip install bs4
{% endhighlight %}

가상환경을 만들고 **Selenium**과 **BeautifulSoup** 라이브러리를 설치합니다. **Selenium**을 사용하기 위해서는 **WebDriver**을 따로 설치해야합니다. 

- [[Chrome WebDriver 다운로드 링크](https://sites.google.com/a/chromium.org/chromedriver/downloads)]

설치한 파일은 가상환경 내부에 `src` 파일을 만들고 해당 위치에 저장합니다.

## 아파트 검색하기

### Selenium 실행

{% highlight python linenos %}
def start(url):
    driver = webdriver.Chrome("chromedriver.exe")
    driver.set_window_size(1920, 1080)
    driver.get(url)
    time.sleep(0.5)
    return driver
{% endhighlight %}

저는 **이미지 데이터**를 얻고 싶어서 **selenium**을 **전체화면**으로 실행시키는 함수를 만들었습니다.
> **이미지 데이터**를 얻기 위해서는 캡쳐를 한 다음 **pillow 라이브러리**를 사용해서 이미지는 **crop**하는 작업을 실행해야 합니다. 그러기 위해서는 각자 컴퓨터에 맞는 **해상도**도로 **selenium**을 실행시켜야합니다.

{% highlight python linenos %}
url = "http://nland.kbstar.com/quics?page=B046948"
driver = start(url)
{% endhighlight %}

**KB리브온**에서 매물정보를 확인하는 페이지의 **url**로 바로 들어갑니다.

![live-on-01](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/live_on_01.png)

크롬창이 뜨면서 위와 같은 화면을 볼 수 있을 겁니다. 이제 주소별로 검색하는 함수를 구현해보겠습니다.

### Input

{% highlight python linenos %}
address = input("상세주소를 입력해주세요")
# 상세주소를 입력해주세요 : 경기도 부천시 심곡본동 태경삼익
address_list = address.split(" ")
{% endhighlight %}

**address**로 상세주소를 입력받습니다. 입력받은 주소를 띄어쓰기로 나눠서 **list**로 나눠줍니다.

{% highlight python linenos %}
def search(_id, search_keyword, error_message):
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    _list = []
    # select 명령어로 li태그의 정보를 가져와 _list에 append 합니다.
    for li in soup.select("#"+_id+" li"):
        _list.append(li.text)
    print(search_keyword in _list)
    # _list에서 search_keyword 존재 여부를 확인하고  True일 경우 해당 인덱스를 클릭합니다. 
    if (search_keyword in _list):
        index = _list.index(search_keyword)
        driver.find_element_by_xpath("//ul[@id='"+_id+"']/li["+str(index+1)+"]").click()
        time.sleep(0.5)
    else:
        print(error_message)
{% endhighlight %}

어떤 항목을 검색할지 확인하는 `_id`, 검색어에 해당하는 `search_keyword` 해당 함수가 오류가 날 경우 실행되는 에러메세지, 이렇게 3개의 인자를 포함하는 `search` 라는 함수를 만들었습니다.

{% highlight python linenos %}
_id_list = "sido", "siGunGu", "eupMyeonDong", "danji"
error_message_list = [   "시/도를 잘못 입력하셨습니다.",
                 "시/군/구를 잘못 입력하셨습니다.",
                 "읍/면/동을 잘못 입력하셨습니다.",
                 "단지를 잘못 입력하셨습니다."]
# 검색창을 한번 클릭해서 활성화 시킨 다음 search 함수를 실행합니다.
driver.find_element_by_xpath("//a[@title = '시/도 선택']").click()
for i in range(4):
    search(_id_list[i], search_list[i], error_message_list[i])
{% endhighlight %}

`_id`, `error_message`를 시/도, 시/군/구, 읍/면/동, 단지 에 따라 각각의 값을 list에 넣어줍니다. 그다음 For문을 통해 serach 함수를 실행시킵니다.

![live-on-02](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/live_on_02.png)

이제 검색결과에서 필요한 정보를 **캡쳐**해보도록 하겠습니다.

## 시세 캡쳐하기

### 시세 탭 클릭

{% highlight python linenos %}
driver.find_element_by_xpath('//div[@class="tab_type"]/ul/li[2]').click()
{% endhighlight %}

위 명령어는 탭 형태로 디자인된 **div태그**에서 2번째 **li태그**를 클릭한 것이다.

![live-on-03](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/live_on_03.png)

2번째 탭에 위치한 시세를 클릭하기 위해 **index**를 사용한 것이다.

### 평수 선택

![live-on-04](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/live_on_04.png)

시세변동 추이 그래프 부분에 **평수를 선택**할 수 있게 되어있습니다. 정확한 데이터 수집을 위해 평수 선택을 하고 **캡쳐**를 하도록 하겠습니다.

{% highlight python linenos %}
def select_space(space):
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')
    option_list = soup.select("option")
    # find 값이 -1 이아니면 해당 인덱스를 프린트
    for index, option_ele in enumerate(option_list):
        option = option_ele.text
        print(option)
        if option.find(space)!=-1:
            print(index)
            driver.find_element_by_xpath('//option[@value="'+str(index+1)+'"]').click()
{% endhighlight %}

**select_space** 함수는 평수를 입력받아 **html소스**에서 **option**에 있는 값들과 비교 후 **인덱스**를 이용해 해당 태그를 클릭하는 방식으로 구현되었습니다.

> *`if option.find(space)!=-1:`* find 값이 -1이라는 소리는 option에서 space에 해당하는 값을 찾지 못했다는 것을 의미합니다.

{% highlight python linenos %}
space = input("평수를 입력해주세요")
# 평수를 입력해주세요 : 105 
select_space(space)
{% endhighlight %}

소수점까지 적지않아도 **find** 명령어가 스스로 찾아서 해당 인덱스를 가져와서 클릭합니다.
> 다만 중복될 경우 에러가 발생할 수 있습니다.

![live-on-05](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/live_on_05.png)

이제 **캡쳐**를 해보도록 하겠습니다.

### 캡쳐

{% highlight python linenos %}
# 맨 아래로 이동
element = driver.find_element_by_class_name("chat")
actions = ActionChains(driver)
actions.move_to_element(element).perform()
{% endhighlight %}

캡쳐를 하기 위해서는 해당 element로 스크롤을 이동해야합니다. 이동하기 전에 스크롤을 맨아래로 내려줍니다.

> 이건 개인적인 노하우 인데, **actions** 명령어가 아래서 위로 올려가며 해당 **element**를 찾기 때문에 **맨아래**로 내려서 스크롤 이동을 하게 되면 캡쳐 후 **crop**이 용이합니다. 

{% highlight python linenos %}
# 시세 div으로 스크롤 이동
element = driver.find_element_by_class_name("tbl_col")
actions = ActionChains(driver)
actions.move_to_element(element).perform()

location = element.location
size = element.size
# 전체화면을 캡쳐 후 output 폴더에 저장합니다.
driver.save_screenshot('output/screenshot.png')
# 저장된 이미지를 다시 불러온다음
im = Image.open('output/screenshot.png')
# 상,하,좌,우 값을 사전에 detect한 태그의 크기로 정해줍니다.
left = location['x']
top = 0
right = location['x'] + size['width']
bottom = location['y']
# 이미지 crop!
im = im.crop((left, top, right, bottom))
im.save('output/시세정보.png')
{% endhighlight %}

이렇게 하게 되면 **output** 폴더에 이미지가 생성됩니다!

### Result

![live-on-06](https://raw.githubusercontent.com/wkddnjset/wkddnjset.github.io/master/_posts/images/2018-02-12/live_on_06.png)

시세관련 테이블과 그래프를 corp한 결과입니다.