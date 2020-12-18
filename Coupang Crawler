# 해당 프로젝트의 주요 사항 : 크롤러와 mySQL을 연결 + ActionChains
# 사전에 mySQL 서버가 설치 및 설정되어 있어야 합니다.

# 필요한 모듈과 라이브러리를 로딩
from bs4 import BeautifulSoup
from selenium import webdriver
import pandas as pd    
import time, os, math, random

import pymysql
from selenium.webdriver.common.action_chains import ActionChains

# 사용자에게 크롤링 방식 묻기
print("=" *80)
print("         키워드 검색 쿠팡 크롤러입니다.")
print("=" *80)

#검색할 키워드와 크롤링할 건수 입력받기
keyword = str(input("1.크롤링할 키워드를 지정해주세요 : "))
cnt = int(input('2.크롤링 할 건수는 몇건입니까?: '))

#직접 검색 방식으로 크롤링할 경우에는 한 페이지당 최대 게시물이 72개.
#동시에 72개씩 볼때 가장 많은 게시물을 크롤링 가능
page_cnt = math.ceil(cnt/72)
f_dir = ''
# input("3.파일을 저장할 폴더명을 지정해주세요(기본경로:c:\\crawlbot\\):")
if f_dir == '' :
    f_dir = "c:\\crawlbot\\"    

print("=" *80)
print("         데이터 크롤링을 시작합니다.")
print("=" *80)

# 작업 시간과 고유 dir 등 생성
n = time.localtime()
s = '%04d-%02d-%02d' % (n.tm_year, n.tm_mon, n.tm_mday)
s1 = '%02d-%02d' % (n.tm_hour, n.tm_min)
s_time = time.time( )

# HEADLESS MODE
options = webdriver.ChromeOptions()
options.add_argument('headless')
options.add_argument('window-size=1920x1080') 
options.add_argument("--disable-gpu")
# 쿠팡 헤들리스 모드 크롤러 막혀있는 걸 뚫기위해 위장
options.add_argument("user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.121 Safari/537.36")
args = ["hide_console", ]

# 웹사이트 접속 후 해당 메뉴로 이동
chrome_path = "c:/temp/python/chromedriver.exe"
driver = webdriver.Chrome(chrome_path,options=options,service_args=args)  
# driver = webdriver.Chrome(chrome_path)
query_url= ('https://www.coupang.com/np/search?component=&q={}&channel=user'. format(keyword))
driver.get(query_url)
driver.implicitly_wait(5)


# 키워드 검색의 경우 게시물 보기 방식이 클릭이 아닌 가져다대는 방식으로 actionchains 사용
# 72개씩 보기로 변경 - 액션 체인으로 마우스 가져다대고 클릭
try : 
    time.sleep(random.randint(1,3))
    from selenium.webdriver.common.action_chains import ActionChains
    action = ActionChains(driver)
    selectbox = driver.find_element_by_class_name('selected')
    action.move_to_element(selectbox).perform()
    time.sleep(0.5)
    driver.find_element_by_xpath('''//*[@id="searchSortingList"]/ul/li[4]/label''').click()
    driver.implicitly_wait(3)
    time.sleep(random.randint(1,3))
except :
    driver.refresh()
    time.sleep(1)

    from selenium.webdriver.common.action_chains import ActionChains
    action = ActionChains(driver)
    selectbox = driver.find_element_by_class_name('selectbox-options')
    action.move_to_element(selectbox).perform()
    time.sleep(0.5)
    driver.find_element_by_xpath('''//*[@id="searchSortingList"]/ul/li[4]/label''').click()
    driver.implicitly_wait(3)
    time.sleep(random.randint(1,3))


# 저장될 파일 경로와 이름을 지정
html = driver.page_source
soup = BeautifulSoup(html, 'html.parser')
sec_name = str(keyword)
query_txt='쿠팡'

try : 
    os.makedirs(f_dir+s+'\\'+query_txt+'\\'+sec_name)
except : pass
os.chdir(f_dir+s+'\\'+query_txt+'\\'+sec_name)

# 이미지/txt/csv/xls 이름 지정
ff_dir=f_dir+s+'\\'+query_txt+'\\'+sec_name
ff_name=f_dir+s+'\\'+query_txt+'\\'+sec_name+'\\'+s+'-'+query_txt+'-'+sec_name+'.txt'
fc_name=f_dir+s+'\\'+query_txt+'\\'+sec_name+'\\'+s+'-'+query_txt+'-'+sec_name+'.csv'


# 내용을 수집
print("\n")
print("========== 곧 수집된 결과를 출력합니다 ========== ")
print("\n")

# 크롤링 데이터가 들어갈 리스트 생성
ranking2=[]        #제품의 판매순위 저장
title2=[]          #제품 정보 저장
p_price2=[]        #현재 판매가 저장
original2 = []     #원가 저장
discount2 = []     #할인율 저장
rocket2 = []       #로켓배송여부
out2 = []          #품절여부
sat_count2=[]      #상품평 수 저장
stars2 = []        #상품평점 저장
category2 = []     #크롤링 검색어

count = 1     # 총 게시물 건수 카운트 변수


#각 페이지별 소스를 파싱해서 게시글 단위로 크롤링
for x in range(1,page_cnt + 1) :
    html = driver.page_source
    soup = BeautifulSoup(html, 'html.parser')

    item_result = soup.find('div','search-content search-content-with-feedback')
    item_result2 = item_result.find_all('li')
    item_result2 = item_result2[10:]

    for li in item_result2:

        if cnt < count :
            break

        #제품 내용 추출
        f = open(ff_name, 'a',encoding='UTF-8')
        f.write("-----------------------------------------------------"+"\n")
        print("-" *70)

        ranking = count
        print("1.판매순위:",ranking)
        f.write('1.판매순위:'+ str(ranking) + "\n")

        try :
            t = li.find('div',class_='name').get_text().replace("\n","")
        except AttributeError :
            title = '제품소개가 없습니다'
            print(title.replace("\n",""))
            f.write('2.제품소개:'+ title + "\n")
        else :
            title = t.replace("\n","").strip()
            print("2.제품소개:", title.replace("\n","").strip())                  
            f.write('2.제품소개:'+ title + "\n")

        try :
            p_price = li.find('strong','price-value').get_text()
        except :
            p_price = '0'
            print("3.판매가격:", p_price.replace("\n",""))
            f.write('3.판매가격:'+ p_price + "\n")
        else :
            print("3.판매가격:", p_price.replace("\n",""))
            f.write('3.판매가격:'+ p_price + "\n")

        try :
            original = li.find('del','base-price').get_text()
        except  :
            original = p_price
            print("4:원래가격:", original.replace("\n",""))
            f.write('4.원래가격:'+ original + "\n")
        else :
            print("4:원래가격:", original.replace("\n",""))
            f.write('4.원래가격:'+ original + "\n")

        # 할인율을 가져오되, 할인율이 명시되어 있지 않은 경우 실제 가격과 비교해서 할인율을 산출하는 식을 추가
        try :
            discount = li.find('span','instant-discount-rate').get_text().replace("\n","")
        except  :
            try : 
                discount = (((original - p_price)/original))*100 
                discount = str(round(discount,1))
            except :
                discount = '0 %'
            print("5:할인율:", discount)
            f.write('5.할인율:'+ discount + "\n")
        else :
            print("5:할인율:", discount)
            f.write('5.할인율:'+ discount + "\n")

        try :
            rocket = li.find('span', class_="badge rocket").find('img')['alt']
            rocket = str(rocket)
        except  AttributeError :
            rocket= "일반배송"
            print('6.로켓배송여부: ',rocket)
            f.write('6. 로켓배송여부:'+ rocket + "\n")
        else :
            print('6.로켓배송여부:',rocket)
            f.write('6. 로켓배송여부:'+ rocket + "\n")

        try :
            out = li.find('div','out-of-stock').get_text()
        except AttributeError :
            out= "재고있음"
            print('7.품절여부: ',out)
            f.write('7.품절여부:'+ out + "\n")
        else :
            out = "품절"
            print('7.품절여부:',out)
            f.write('7.품절여부:'+ out + "\n")

        try :
            sat_count_1 = li.find('span','rating-total-count').get_text()
            sat_count_2 = sat_count_1.replace("(","").replace(")","")
        except  :
            sat_count_2='0'
            print('8.상품평 수: ',sat_count_2)
            f.write('8.상품평 수:'+ sat_count_2 + "\n")
        else :
            print('8.상품평 수:',sat_count_2)
            f.write('8.상품평 수:'+ sat_count_2 + "\n")

        try :
            stars1 = li.find('em','rating').get_text()
        except  :
            stars1='0'
            print('9.상품평점: ',stars1)
            f.write('9.상품평점:'+ stars1 + "\n")
        else :
            print('9.상품평점:',stars1)
            f.write('9.상품평점:'+ stars1 + "\n")

        category = sec_name
        print("10.카테고리:",category)
        f.write('10.판매순위:'+ str(category) + "\n")


        print("-" *70)

        #상단광고의 경우 구분(각 페이지별 상위 4개 항목에 대한 리뷰 수가 50개 이하인 경우 상단노출 광고로 간주)
        #이를 통해 추후 상단 광고로 노출된 상품이 어느정도의 광고효과가 있었는지 추적 가능
        if count % 72 < 6 and int(sat_count_2) < 51 :
            title = title + '   *** 상단광고 ***'

        f.close( )             
        time.sleep(0.5)

        #추출한 데이터를 리스트화
        ranking2.append(ranking)
        title2.append(title.replace("\n",""))

        p_price2.append(p_price.replace("\n",""))
        original2.append(original.replace("\n",""))
        discount2.append(discount.replace("\n",""))

        rocket2.append(rocket.replace("\n",""))
        out2.append(out)

        try :   
            sat_count2.append(sat_count_2)
        except IndexError :
            sat_count2.append(0)

        try :   
            stars2.append(stars1)
        except IndexError :
            stars2.append(0)

        category2.append(str(category))

        count += 1

    # 페이지 번호를 넘기고, 다음 페이지 번호 클릭
    x += 1          
    try :
        driver.find_element_by_class_name("btn-page").find_element_by_link_text('%s' %x).click() # 다음 페이지번호 클릭
    except :
        pass

    time.sleep(2)  

# 크롤링한 데이터를 pandas를 이용해 DataFrame 형태로 저장             
coupang_df = pd.DataFrame()
coupang_df['판매순위']=ranking2
coupang_df['제품소개']=pd.Series(title2)
coupang_df['제품판매가']=pd.Series(p_price2)
coupang_df['원래 가격']=pd.Series(original2)
coupang_df['할인율']=pd.Series(discount2)
coupang_df['로켓배송여부']=pd.Series(rocket2)
coupang_df['품절여부']=pd.Series(out2)
coupang_df['상품평수']=pd.Series(sat_count2)
coupang_df['상품평점']=pd.Series(stars2)
coupang_df['분류']=pd.Series(category2)

# csv 형태로 저장하기
coupang_df.to_csv(fc_name,encoding="utf-8-sig",index=False)

e_time = time.time( )
t_time = e_time - s_time

count -= 1

print("\n")
print("=" *80)
print("1.요청된 총 %s 건의 리뷰 중에서 실제 크롤링 된 리뷰수는 %s 건입니다" %(cnt,count))
print("2.총 소요시간은 %s 초 입니다 " %round(t_time,1))
print("3.파일 저장 완료: txt 파일명 : %s " %ff_name)
print("4.파일 저장 완료: csv 파일명 : %s " %fc_name)
print("=" *80)
print('\n')

driver.close( )

# mySQL과 연결하기 위한 필수값 설정
host_name = "localhost"
username = "root"
password = "oracle"
database_name = "mysql"

# mySQL과 연결
con = pymysql.connect(host=host_name,port=3306,user=username, passwd=password, db=database_name,charset='utf8')
cur = con.cursor()

# # # # # 데이터베이스 및 테이블 구성 # # # # #
# STEP1. IF EXISTS 구문을 활용해 같은 이름의 DB가 존재하면 없애고(DROP), 추후(STEP2) 새로 만든다.

# STEP2. 데이터베이스 생성
try : 
    SQL_QUERY = """ CREATE DATABASE bot ; """
    cur.execute(SQL_QUERY)
except : pass

# STEP3. 생성한 데이터 베이스를 사용하는 데이터 베이스로 지정
SQL_QUERY=""" USE bot ; """
cur.execute(SQL_QUERY)

# STEP4. 활성화된 데이터 베이스 안에 지정한 이름의 테이블이 중복 존재하는 경우 삭제(DROP)
# 지정형식은 DB.TABLE, 여기서는 test_db 아래의 test라는 이름의 테이블을 조회한 후 중복존재하면 삭제하는 것

# STEP5. 테이블 생성
# VARCHAR2는 VARCHAR로, NUMBER는 정수의 경우 INT로, 소수점이 있는 경우 DECIMAL로 바꿔줍니다.
# CRAWLED 값의 경우엔 추후에 따로 SYSDATE 값을 넣지 않고 테이블 생성 과정에서 DATETIME과 DEFAULT 값으로 설정해줍니다.
SQL_QUERY=""" 
    CREATE TABLE bot.cp (
    RANKING INT, 
    TITLE VARCHAR(256) ,
    P_PRICE VARCHAR(10) ,
    O_PRICE VARCHAR(10) , 
    DISCOUNT VARCHAR(4) ,
    DELIVERY VARCHAR(8) ,
    STOCK VARCHAR(8) ,
    REVIEW INT ,
    STARS DECIMAL(2,1) ,
    CATEGORY VARCHAR(20) ,
    CRAWLED DATE DEFAULT (CURRENT_DATE)
) DEFAULT CHARSET=utf8 COLLATE=utf8_bin 
"""
try : 
    cur.execute(SQL_QUERY)
    con.commit()
except : pass

# STEP6. 테이블에 들어갈 값을 지정합니다.
# 첫 괄호에는 각각의 항목에 부여할 열의 이름을, 그 다음에는 그 데이터에 걸맞은 형식을 지정해줍니다.
# mySQL의 경우에는 VALUE 값을 일일히 순서로 지정하지 않고, %s로 적은 뒤 for구문과 executemany를 이용해 하나씩 대입시킵니다.
sql = ''' INSERT INTO bot.cp (RANKING, TITLE, P_PRICE, O_PRICE,DISCOUNT, DELIVERY,STOCK, REVIEW, STARS, CATEGORY,CRAWLED)
         VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s,%s,CURDATE()) ''' 

rows = [tuple(x) for x in coupang_df.values]
cur.executemany(sql,rows)
con.commit()

print(" 작업이 완료되었습니다. ")
print('\n')
print(" 작업 폴더를 엽니다. ")
print("=" *80)

# 작업 완료 후 작업 폴더 열기
os.startfile(f_dir)
