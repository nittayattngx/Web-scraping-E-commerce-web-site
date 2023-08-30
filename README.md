# Web-scraping-E-commerce-website
Scraping specific products from an E-commerce website (shopee)

<p>&emsp;
การ scraping website เพื่อให้ได้ข้อมูลที่เราต้องการมาใช้ ก็เป็นอีกหนึ่ง project ที่น่าสนใจ เลยศึกษาทั้งจากใน blog ต่าง ๆ รวมถึงวิธีการทำที่มากมายจาก youtube (แหล่งขุมทรัพย์ความรู้ที่ใช้บ่อยเลย ฮา) จากนั้นก็ตัดสินใจว่า scraping products จากเว็บ E-commerce สีส้มชื่อดัง
<br>&emsp;
สำหรับ blog นี้ก็เป็นบันทึกการเรียนรู้ผ่าน project อีกครั้ง
</p>

### Code ใน jupyter notebook

# Scraping
<b>Install and import</b>

```
!pip install selenium
!pip install bs4

from selenium import webdriver
from selenium.webdriver.chrome.service import Service 
from selenium.webdriver.common.by import By 
from selenium.webdriver.common.keys import Keys 
from bs4 import BeautifulSoup as bs 
import pandas as pd
import time 

```

<p>&emsp;
อันดับแรกก็โหลด web driver มาไว้ในไฟล์ project ก่อน ซึ่งตอนนี้ (29/8/2023) ตัวเบราเซอร์ที่ใช้เป็น version 115.0.5790.102 สามารถโหลดได้จาก
</p>
(https://edgedl.me.gvt1.com/edgedl/chrome/chrome-for-testing/115.0.5790.102/win32/chromedriver-win32.zip)

<p>&emsp;
ก่อนหน้านี้ตอนที่สนใจการทำ web scraping ก็ทำให้รู้จัก beautifulSoup อยู่แล้ว แต่เพิ่งมารู้จักเจ้า selenium ที่ถ้าอยาก scraping จากเว็บประเภท e-commerce ก็ต้องรัน web driver ขึ้นมาใหม่เลยนั่นเอง 
<br>&emsp;
รัน web driver แล้วเข้าไปที่หน้าเว็บที่เราต้องการ
</p>

```
service = Service(executable_path = r'C:\Users\your username\Desktop\shopee\chromedriver-win32\chromedriver.exe')
options = webdriver.ChromeOptions()
driver = webdriver.Chrome(service=service, options=options)

driver.get('https://shopee.co.th/')

```

<p align = "center">
&nbsp;<img src = "https://github.com/nittayattngx/Web-scraping-E-commerce-web-site/blob/main/img/Screenshot%202023-08-28%20163036.png" width = "500"/>
</p>

<p>&emsp;
พอเข้ามาแล้ว ก็จะมีเจ้าสิ่งนี้เด้งขึ้นมา ‘เลือกภาษา’
<br>&emsp;
ถ้าเราต้องการเลือกภาษาไทย เราก็ต้องสั่งให้มันเลือกปุ่มบนใช่ไหม? เราเลยโค้ดให้ web driver หาปุ่มภาษาไทยจากหน้าเว็บปัจจุบัน โดยใช้ XPATH ชี้เป้าให้มันว่าตรงนี้คือปุ่มที่อยากกดนะ แล้วสั่งให้มันคลิกที่ปุ่มนั้น
</p>

```
choose_thailang = driver.find_element(By.XPATH, '/html/body/div[2]/div[1]/div[1]/div/div[3]/div[1]/button')
choose_thailang.click()

```

<p>&emsp;
พอกดเลือกภาษาแล้วก็จะมี pop up โฆษณาเด้งขึ้นมาต่อทันที ในตอนที่กำลังหา XPATH อยู่นั้นก็ไปเจอกับสิ่งนี้เข้า
</p>

<p align = "center">
&nbsp;<img src = "https://github.com/nittayattngx/Web-scraping-E-commerce-web-site/blob/main/img/Screenshot%202023-08-28%20164337.png" width = "500"/>
</p>

<p>&emsp;
‘shadow root’ เจ้าสิ่งนี้จะทำให้โปรแกรมเข้าไปไม่ถึงส่วนที่เราต้องการเพราะดันอยู่ข้างในมันอีกที แต่โชคดีที่เป็น case study ที่น่าสนใจ ทำให้มีคนสอนวิธีรับมือกับสิ่งนี้ด้วย 
<br>&emsp;
นั่นคือการ execute java script แล้วใช้คำสั่งของ java script query โหนดที่เก็บเจ้า shadow root ไว้ จากนั้นก็เข้าไปที่ shadow root และ query ตำแหน่งที่เราต้องการออกมาโดยใช้เป็น class แทนที่จะเป็น XPATH เก็บมาไว้ในตัวแปรแล้วคลิกไป เท่านี้ก็ปิด pop up โฆษณาได้แล้ว
</p>


```
close_ad = driver.execute_script('return document.querySelector("shopee-banner-popup-stateful").shadowRoot.querySelector("div.shopee-popup__close-btn")')
close_ad.click()

```

<p>&emsp;
จากนั้นกรอก product ที่เราสนใจเข้าไปในช่องค้นหา อย่างเช่น ‘โต๊ะทำงาน’ แล้วสั่งให้ enter
</p>

```
search = driver.find_element(By.XPATH, '/html/body/div[1]/div/header/div[2]/div/div[1]/div[1]/div/form/input')
search.send_keys('โต๊ะทำงาน')

search.send_keys(Keys.ENTER)
```

<p>&emsp;
พอกด enter แล้วแทนที่จะมุ่งสู่หน้า products แต่ทางเว็บต้องการให้เราล็อกอินเข้าไปก่อน
</p>

<p align = "center">
&nbsp;<img src = "https://github.com/nittayattngx/Web-scraping-E-commerce-web-site/blob/main/img/Screenshot%202023-08-28%20165712.png" width = "500"/>
</p>

<p>&emsp;
ใช้วิธีการเหมือนเดิม แต่ทีนี้ XPATH ที่ก็อปมา ดันทำให้โค้ดกรอก username และ password ช่องเดียวกัน เลยเปลี่ยนวิธีระบุตำแหน่งใหม่ 
</p>

```
login_user = driver.find_element(By.NAME, "loginKey")
login_user.send_keys('your username')

```

<p>&emsp;
ทำเหมือนกันทั้ง 2 ช่อง แล้วเพิ่มโค้ดให้กด ‘เข้าสู่ระบบ’ ไป หากเข้ามาเป็นครั้งแรกเว็บจะขอให้ยืนยันตัวตน จากนั้นเว็บก็จะพาเข้าสู่หน้า products ที่เรากรอกไปก่อนหน้านี้
&emsp;
<br>ก่อนอื่นต้องดูก่อนว่าอยากได้ข้อมูลอะไรบ้าง แล้วสร้าง list ว่างไว้เตรียมใส่ข้อมูลนั้น ๆ
</p>

```
all_products = []
all_price = []
all_soldAmount = []
all_province = []

```

<p>&emsp;
ที่หน้าเว็บของ shopee เป็นแบบต้องเลื่อนดูก่อนถึงจะโหลดข้อมูลให้ ทำให้มี 2 ทางให้เลือกใช้ คือจะเลื่อนเมาส์ลงแล้วเก็บข้อมูล หรือจะซูมหน้าเว็บออกให้เห็น products ทั้งหมดเพื่อโหลดทีเดียว เราเลือกวิธีซูมออก
</p>

```
driver.execute_script("document.body.style.zoom='10%' ") 
    data = driver.page_source 
    soup = bs(data) #ใช้ beautifulsoup 

```

<p>&emsp;
เก็บข้อมูลในหน้าเว็บทั้งหมดไว้ในตัวแปร จากนั้นใช้เจ้า beautifulSoup สำหรับการช่วยดึงข้อมูลที่เราต้องการ
</p>

```
products = soup.find_all('div', {'class':'ie3A+n bM+7UW Cve6sh'})
```

<p>&emsp;
syntax การดึงข้อมูลโดยใช้ class และ tag ที่อยู่ของข้อมูลนั้นมาเก็บไว้ในตัวแปร จากนั้นสร้าง loop ขึ้นมาเพื่อนำข้อมูลนั้นไปใส่ไว้ใน list ที่เราสร้างไว้ก่อนหน้านี้
</p>

```
for i in products:
        all_products.append(i.text)

```

<p>&emsp;
จากนั้นสร้าง loop อีกอันมาครอบไว้เพื่อให้เก็บข้อมูลครบทุกหน้า เพราะเว็บแสดง 60 products ในหนึ่งหน้าเท่านั้น ถ้าเราอยากได้ข้อมูลทั้งหมดก็ต้องเปิดหน้าถัดไปเรื่อย ๆ จนหมด
</p>

- Total_pages เก็บเลขที่แสดงจำนวนหน้า products ทั้งหมดเพื่อนำมาใช้ใน loop
- next_page_btn สำหรับกดปุ่มไปหน้าถัดไป

```
total_pages = driver.find_element(By.XPATH, '/html/body/div[1]/div/div[2]/div[1]/div/div[2]/div[3]/div[1]/div[2]/div/span[2]')
total_pages = total_pages.text 
total_pages = int(total_pages)

for i in range(total_pages): 
    
    driver.execute_script("document.body.style.zoom='10%' ") 
    data = driver.page_source 
    soup = bs(data) 
   
    products = soup.find_all('div', {'class':'ie3A+n bM+7UW Cve6sh'})
    price = soup.find_all('div', {'class': 'vioxXd rVLWG6'})
    sold_amount = soup.find_all('div', {'class': 'ZnrnMl'})
    province = soup.find_all('div', {'class': 'zGGwiV'})
    

    
    for i in products:
        all_products.append(i.text)

    for i in price:
        all_price.append(i.text)

    for i in sold_amount:
        all_soldAmount.append(i.text)

    for i in province:
        all_province.append(i.text)
    
    
    time.sleep(3)
    next_page_btn = driver.find_element(By.XPATH, '/html/body/div[1]/div/div[2]/div/div/div[2]/div[3]/div[1]/div[2]/button[2]')
    driver.execute_script("arguments[0].scrollIntoView();", next_page_btn)
    driver.execute_script("arguments[0].click();", next_page_btn)
    
    time.sleep(3)

```

<p>&emsp;
<b>ตัวอย่างข้อมูลที่ได้ใน list<b>
</p>

<p align = "center">
&nbsp;<img src = "https://github.com/nittayattngx/Web-scraping-E-commerce-web-site/blob/main/img/Screenshot%202023-08-29%20153507.png" width = "500"/>
</p>

<p>&emsp;
จากนั้นทำการรวม list ทั้งหมดเข้าไว้ด้วยกันใน list ใหญ่ และเปลี่ยนให้เป็น data frame โดยคำสั่ง pd.DataFrame ของ pandas จะได้ข้อมูลหน้าตาแบบนี้
</p>

<p align = "center">
&nbsp;<img src = "https://github.com/nittayattngx/Web-scraping-E-commerce-web-site/blob/main/img/Screenshot%202023-08-29%20153730.png" width = "500"/>
</p>

<p>&emsp;
Save ไฟล์เป็น excel จบการฝึกทำ web scraping แล้วแต่เรามี 2 ไฟล์นะ อีกไฟล์คือเอา data ที่ scraping มาไป cleaning data สามารถดู code ได้ในไฟล์ clean_data.ipynb
</p>

## อธิบายการ cleaning data โดยมี step ดังนี้

- ลบ emoji และ สัญลักษณ์พิเศษต่าง ๆ ออกจากชื่อของ products
- ลบ stop word คำที่ไม่มีความหมายหรือคำที่เราไม่ต้องการออกไป 
- split word ให้คำแยกออกจากกัน ตรงนี้คือการเตรียมสำหรับนำไปทำ word cloud ได้ เพื่อดูว่า products ‘โต๊ะทำงาน’ ส่วนมากใช้คำอะไรในการตั้งชื่อกันบ้าง
- ราคาที่อยู่ใน format ฿xxx – ฿xxx นำมาแยกออกจากกันแล้วสร้าง column ใหม่ เป็น minimum price และ maximum price
- ลบ column ที่ไม่ได้ใช้ออก แล้วสั่ง save เป็นไฟล์ excel
- Sold amount และ province เนื่องจาก data ไม่เยอะมาก ทำให้ cleaning data ใน excel ต่อได้ ใช้การ replace ข้อความที่ไม่จำเป็นด้วยค่าว่าง และ replace ‘พัน’ ด้วย ‘00’ เท่านี้ data set ก็พร้อมใช้งานแล้ว

- <p align = "center">
&nbsp;<img src = "https://github.com/nittayattngx/Web-scraping-E-commerce-web-site/blob/main/img/Screenshot%202023-08-29%20161055.png" width = "500"/>
</p>

---
