# Web Scraping de vagas de Data Science no Linkedin

Este notebook é parte das tarefas do curso de Data Science da [Awari](https://awari.com.br/). **O objetivo deste projeto é fazer o web scraping de vagas de Cientista de Dados em São Paulo no Linkedin e criar uma nuvem de palavras**.

Foram recuperados 16380 apartamentos em 99 bairros de São Paulo.


```python
!pip install selenium
!pip install webdriver-manager
!pip install wordcloud

from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
from time import sleep
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
import matplotlib.pyplot as plt
import pandas as pd
```

    Requirement already satisfied: selenium in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (3.141.0)
    Requirement already satisfied: urllib3 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from selenium) (1.25.11)
    Requirement already satisfied: webdriver-manager in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (3.4.2)
    Requirement already satisfied: crayons in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from webdriver-manager) (0.4.0)
    Requirement already satisfied: configparser in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from webdriver-manager) (5.0.2)
    Requirement already satisfied: requests in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from webdriver-manager) (2.24.0)
    Requirement already satisfied: colorama in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from crayons->webdriver-manager) (0.4.4)
    Requirement already satisfied: idna<3,>=2.5 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from requests->webdriver-manager) (2.10)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from requests->webdriver-manager) (1.25.11)
    Requirement already satisfied: certifi>=2017.4.17 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from requests->webdriver-manager) (2020.6.20)
    Requirement already satisfied: chardet<4,>=3.0.2 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from requests->webdriver-manager) (3.0.4)
    Requirement already satisfied: wordcloud in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (1.8.1)
    Requirement already satisfied: matplotlib in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from wordcloud) (3.3.2)
    Requirement already satisfied: numpy>=1.6.1 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from wordcloud) (1.19.2)
    Requirement already satisfied: pillow in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from wordcloud) (8.0.1)
    Requirement already satisfied: kiwisolver>=1.0.1 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from matplotlib->wordcloud) (1.3.0)
    Requirement already satisfied: certifi>=2020.06.20 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from matplotlib->wordcloud) (2020.6.20)
    Requirement already satisfied: pyparsing!=2.0.4,!=2.1.2,!=2.1.6,>=2.0.3 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from matplotlib->wordcloud) (2.4.7)
    Requirement already satisfied: python-dateutil>=2.1 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from matplotlib->wordcloud) (2.8.1)
    Requirement already satisfied: cycler>=0.10 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from matplotlib->wordcloud) (0.10.0)
    Requirement already satisfied: six>=1.5 in /Users/leuzinger/opt/anaconda3/lib/python3.8/site-packages (from python-dateutil>=2.1->matplotlib->wordcloud) (1.15.0)



```python
#abrir a página da Loft
driver = webdriver.Chrome(ChromeDriverManager().install())
driver.implicitly_wait(10)
driver.get("https://loft.com.br/apartamentos/sao-paulo-sp/")

'''
#filtrar aps em SP
SP_botão = driver.find_elements_by_xpath('//*[@id="__next"]/section/div[1]/article/div/div[2]/div[2]/form/div/div[1]')[0]
SP_botão.click()
buscar_botão = driver.find_element_by_xpath('//*[@id="__next"]/section/div[1]/article/div/div[2]/div[2]/form/button')
driver.execute_script("arguments[0].click();", buscar_botão)


try:
    buscar_botão = WebDriverWait(driver, 20).until(EC.element_to_be_clickable((By.XPATH,'//*[@id="__next"]/section/div[1]/article/div/div[2]/div[2]/form/button')))
finally:                                            
    buscar_botão = driver.find_elements_by_xpath('//*[@id="__next"]/section/div[1]/article/div/div[2]/div[2]/form/button')[0]
    print(buscar_botão)
    buscar_botão.click()
'''

#dados_ap = pd.DataFrame(columns=['preço','endereço','área','quartos','vagas'])

#pegar número total de bairros
bairros_botão = driver.find_elements_by_xpath('//*[@id="__next"]/section/div/div/div[1]/div[2]/div/div/div[2]/div[2]/div')[0]
bairros_botão.click()
bairros = driver.find_elements_by_class_name('MuiTypography-root.MuiFormControlLabel-label.MuiTypography-body1')
tot_bairros = len(bairros)
print(tot_bairros)
driver.quit()
```

    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    99



```python
dados_ap = pd.DataFrame(columns=['preço','endereço','área','quartos','vagas'])

for i in range(tot_bairros):

    #abrir a página da Loft
    driver = webdriver.Chrome(ChromeDriverManager().install())
    driver.implicitly_wait(10)
    driver.get("https://loft.com.br/apartamentos/sao-paulo-sp/")
    
    #filtrar primeiro bairro bairro
    bairros_botão = driver.find_elements_by_xpath('//*[@id="__next"]/section/div/div/div[1]/div[2]/div/div/div[2]/div[2]/div')[0]
    bairros_botão.click()
    sleep(1)
    
    bairros = driver.find_elements_by_class_name('MuiTypography-root.MuiFormControlLabel-label.MuiTypography-body1')
    botão_target = bairros[i]
    actions = ActionChains(driver)
    actions.move_to_element(botão_target)
    actions.perform()
    sleep(1)    
    
    bairros[i].click()
    print(i+1,bairros[i].text)
    sleep(1)
    
    filtro = driver.find_elements_by_xpath('/html/body/div[4]/div[3]/div/div[2]/button[2]')[0]
    filtro.click()
    
    #pegar número de aps no bairro
    aps = driver.find_element_by_xpath('//*[@id="__next"]/section/div/div/div[2]/section[1]/div/div[1]/div[1]/div[1]/div/span')
    while aps.text == '':
        aps = driver.find_element_by_xpath('//*[@id="__next"]/section/div/div/div[2]/section[1]/div/div[1]/div[1]/div[1]/div/span')
        if aps.text !='':
            break
    aps_text = aps.text
    aps_len = int(aps_text)
    print(aps_len)
    resultados = []
    
    #loop para navegar pela página
    while len(resultados)<aps_len:
        try:
            resultados = driver.find_elements_by_class_name('MuiCardContent-root')
            try:
                element = WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CLASS_NAME,'ScrollTracker')))
            finally:
                target = driver.find_element_by_class_name('ScrollTracker')
                actions = ActionChains(driver)
                actions.move_to_element(target)
                actions.perform()
        except:
            print('erro resultados')
            print('Resultados(Erro): ',len(resultados))
            break
    
    print('Resultados: ',len(resultados))
    
    #salvar dados no df
    for k in range(len(resultados)):
        try:
            ap = resultados[k].text.split('\n')
            #print(len(ap))
            if len(ap)>5:
                del ap[-1]
                ap_ = pd.Series(ap, index = dados_ap.columns)
                dados_ap = dados_ap.append(ap_, ignore_index=True)
            else:
                ap_ = pd.Series(ap, index = dados_ap.columns)
                dados_ap = dados_ap.append(ap_, ignore_index=True)
        except: 
            print(k,ap) 
            pass
    
    driver.quit()
    
    '''
    #filtrar próximo bairro
    bairros_botão = driver.find_elements_by_xpath('//*[@id="__next"]/section/div/div/div[1]/div[2]/div/div/div[2]/div[2]/div')[0]
    bairros_botão.click()
    
    sleep(1)
    bairros = driver.find_elements_by_class_name('MuiTypography-root.MuiFormControlLabel-label.MuiTypography-body1')
    #print(bairros[i-1].text)
    bairros[i-1].click()
    
    #sleep(1)
    print(i+1,bairros[i].text)
    bairros[i].click()
    
    sleep(1)
    filtro = driver.find_elements_by_xpath('/html/body/div[4]/div[3]/div/div[2]/button[2]')[0]
    filtro.click()
    #sleep(1)

#pegar número de aps no último bairro
aps = driver.find_element_by_xpath('//*[@id="__next"]/section/div/div/div[2]/section[1]/div/div[1]/div[1]/div[1]/div/span')
while aps.text == '':
    aps = driver.find_element_by_xpath('//*[@id="__next"]/section/div/div/div[2]/section[1]/div/div[1]/div[1]/div[1]/div/span')
    if aps.text !='':
        break
aps_text = aps.text
aps_len = int(aps_text)
print(aps_text)
resultados = []

#loop para navegar pelas páginas com os aps
while len(resultados)<aps_len:
    try:
        resultados = driver.find_elements_by_class_name('MuiCardContent-root.jss493')
        try:
            element = WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.CLASS_NAME,'ScrollTracker')))
        finally:
            target = driver.find_element_by_class_name('ScrollTracker')
            actions = ActionChains(driver)
            actions.move_to_element(target)
            actions.perform()
            #sleep(3)
    except:
        print('erro')
        pass
#print(len(resultados))
    
for k in range(len(resultados)):
    try:
        ap = resultados[k].text.split('\n')
        #print(len(ap))
        if len(ap)>5:
            del ap[-1]
            ap_ = pd.Series(ap, index = dados_ap.columns)
            dados_ap = dados_ap.append(ap_, ignore_index=True)
        else:
            ap_ = pd.Series(ap, index = dados_ap.columns)
            dados_ap = dados_ap.append(ap_, ignore_index=True)
    except: 
        print(ap) 
        pass'''
```

    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    1 Aclimação
    306
    Resultados:  311
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    2 Água Rasa
    184
    Resultados:  186
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    3 Alto Da Lapa
    39
    Resultados:  41
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    4 Alto De Pinheiros
    66
    Resultados:  68
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    5 Aricanduva
    80
    Resultados:  82
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    6 Artur Alvim
    4
    Resultados:  5
    4 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    7 Barra Funda
    66
    Resultados:  68
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    8 Bela Vista
    563
    Resultados:  568
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    9 Belém
    90
    Resultados:  92
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    10 Bom Retiro
    405
    Resultados:  410
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    11 Brás
    121
    Resultados:  123
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    12 Brasilândia
    28
    Resultados:  30
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    13 Brooklin
    244
    Resultados:  246
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    14 Butantã
    76
    Resultados:  78
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    15 Cachoeirinha
    115
    Resultados:  117
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    16 Cambuci
    211
    Resultados:  213
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    17 Campo Belo
    391
    Resultados:  396
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    18 Campo Grande
    227
    Resultados:  229
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    19 Campo Limpo
    178
    Resultados:  180
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    20 Cangaíba
    89
    Resultados:  91
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    21 Capão Redondo
    54
    Resultados:  56
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    22 Carrão
    117
    Resultados:  119
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    23 Casa Verde
    118
    Resultados:  120
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    24 Chacara Klabin
    170
    Resultados:  172
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    25 Cidade Ademar
    168
    Resultados:  170
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    26 Cidade Dutra
    36
    Resultados:  38
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    27 Cidade Líder
    95
    Resultados:  97
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    28 Cursino
    320
    Resultados:  325
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    29 Ermelino Matarazzo
    48
    Resultados:  50
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    30 Freguesia do Ó
    151
    Resultados:  153
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    31 Guaianases
    1
    Resultados:  5
    1 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    32 Higienópolis
    279
    Resultados:  284
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    33 Ipiranga
    266
    Resultados:  268
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    34 Itaim Bibi
    177
    Resultados:  179
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    35 Itaim Nobre
    11
    Resultados:  16
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    36 Jabaquara
    510
    Resultados:  515
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    37 Jaçanã
    31
    Resultados:  33
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    38 Jaguara
    22
    Resultados:  24
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    39 Jaguaré
    102
    Resultados:  104
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    40 Jaraguá
    1
    Resultados:  5
    1 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    41 Jardim América
    254
    Resultados:  254
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    42 Jardim Ângela
    21
    Resultados:  23
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    43 Jardim Europa
    8
    Resultados:  13
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    44 Jardim Helena
    2


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======


    Resultados:  3
    2 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    45 Jardim Paulista
    264
    erro resultados
    Resultados(Erro):  254
    Resultados:  254
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    46 Jardim Paulistano
    14
    Resultados:  19
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    47 Jardim São Luís
    75
    Resultados:  77
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    48 José Bonifácio
    2
    Resultados:  6
    2 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    49 Lajeado
    1
    Resultados:  5
    1 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    50 Lapa
    44
    Resultados:  46
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    51 Liberdade
    115
    Resultados:  120
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    52 Limão
    79
    Resultados:  81
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    53 Mandaqui
    214
    erro resultados
    Resultados(Erro):  200
    Resultados:  200
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    54 Moema Índios
    221
    Resultados:  226
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    55 Moema Pássaros
    197
    Resultados:  202
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    56 Mooca
    276
    Resultados:  281
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    57 Morumbi
    249
    Resultados:  254
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    58 Paraíso
    190
    erro resultados
    Resultados(Erro):  182
    Resultados:  182
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    59 Pari
    9
    Resultados:  14
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    60 Parque do Carmo
    24
    Resultados:  26
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    61 Pedreira
    10
    Resultados:  15
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    62 Penha
    126
    Resultados:  131
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    63 Perdizes
    475
    Resultados:  480
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    64 Pinheiros
    288
    Resultados:  290
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    65 Pirituba
    136
    erro resultados
    Resultados(Erro):  128
    Resultados:  128
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    66 Ponte Rasa
    37
    erro resultados
    Resultados(Erro):  38
    Resultados:  38
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    67 Raposo Tavares
    65
    Resultados:  67
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    68 República
    433
    erro resultados
    Resultados(Erro):  434
    Resultados:  434
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    69 Rio Pequeno
    158
    Resultados:  160
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    70 Sacomã
    535
    Resultados:  537
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    71 Santa Cecília
    269
    Resultados:  271
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    72 Santana
    484
    Resultados:  489
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    73 Santo Amaro
    289
    erro resultados
    Resultados(Erro):  290
    Resultados:  290
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    74 São Domingos
    64
    Resultados:  66
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    75 São Lucas
    120
    Resultados:  122
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    76 São Mateus
    84
    Resultados:  86
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    77 Sapopemba
    3
    Resultados:  7
    3 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    78 Saúde
    352
    Resultados:  355
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    79 Sé
    99
    Resultados:  101
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    80 Socorro
    24
    Resultados:  26
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    81 Sumaré
    116
    erro resultados
    Resultados(Erro):  110
    Resultados:  110
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    82 Tatuapé
    342
    Resultados:  347
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    83 Tremembé
    48
    Resultados:  50
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    84 Tucuruvi
    166
    Resultados:  171
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    85 Vila Andrade
    964
    Resultados:  969
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    86 Vila Clementino
    90
    Resultados:  92
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    87 Vila Formosa
    130
    Resultados:  135
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    88 Vila Guilherme
    120
    Resultados:  122
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    89 Vila Leopoldina
    124
    Resultados:  126
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    90 Vila Madalena
    212
    Resultados:  217
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    91 Vila Maria
    86
    Resultados:  88
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    92 Vila Mariana
    148
    Resultados:  150
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    93 Vila Matilde
    98
    Resultados:  100
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    94 Vila Medeiros
    61
    Resultados:  63
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    95 Vila Nova Conceição
    78
    Resultados:  80
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    96 Vila Olímpia
    315
    Resultados:  320
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    97 Vila Prudente
    174
    erro resultados
    Resultados(Erro):  164
    Resultados:  164
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    98 Vila Romana
    282
    Resultados:  284
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']


    
    
    INFO:WDM:
    
    ====== WebDriver manager ======
    INFO:WDM:====== WebDriver manager ======
    Current google-chrome version is 91.0.4472
    INFO:WDM:Current google-chrome version is 91.0.4472
    Get LATEST driver version for 91.0.4472
    INFO:WDM:Get LATEST driver version for 91.0.4472
    Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache
    INFO:WDM:Driver [/Users/leuzinger/.wdm/drivers/chromedriver/mac64/91.0.4472.101/chromedriver] found in cache


    99 Vila Sônia
    332
    Resultados:  337
    7 ['Quer descobrir seu potencial de compra?', 'Simular financiamento']
    8 ['Imóveis escolhidos a dedo pra você.', 'Saber mais detalhes']



```python
dados_ap.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 16380 entries, 0 to 16379
    Data columns (total 5 columns):
     #   Column    Non-Null Count  Dtype 
    ---  ------    --------------  ----- 
     0   preço     16380 non-null  object
     1   endereço  16380 non-null  object
     2   área      16380 non-null  object
     3   quartos   16380 non-null  object
     4   vagas     16380 non-null  object
    dtypes: object(5)
    memory usage: 640.0+ KB



```python
dados_ap.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>preço</th>
      <th>endereço</th>
      <th>área</th>
      <th>quartos</th>
      <th>vagas</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>R$ 1.295.000</td>
      <td>Rua Paula Ney, Aclimação</td>
      <td>94 m²</td>
      <td>3 quartos</td>
      <td>2 vagas</td>
    </tr>
    <tr>
      <th>1</th>
      <td>R$ 1.410.000</td>
      <td>Rua Castro Alves, Aclimação</td>
      <td>195 m²</td>
      <td>4 quartos</td>
      <td>3 vagas</td>
    </tr>
    <tr>
      <th>2</th>
      <td>R$ 899.000</td>
      <td>Rua Urano, Aclimação</td>
      <td>118 m²</td>
      <td>3 quartos</td>
      <td>2 vagas</td>
    </tr>
    <tr>
      <th>3</th>
      <td>R$ 540.000</td>
      <td>Rua Álvaro Guimarães, Aclimação</td>
      <td>29 m²</td>
      <td>Studio</td>
      <td>1 vaga</td>
    </tr>
    <tr>
      <th>4</th>
      <td>R$ 1.175.000</td>
      <td>Rua Bartolomeu de Gusmão, Aclimação</td>
      <td>135 m²</td>
      <td>4 quartos</td>
      <td>2 vagas</td>
    </tr>
  </tbody>
</table>
</div>




```python
dados = dados_ap.copy()
dados[['endereço','bairro']] = dados_ap.endereço.str.split(",",expand=True)
dados.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>preço</th>
      <th>endereço</th>
      <th>área</th>
      <th>quartos</th>
      <th>vagas</th>
      <th>bairro</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>R$ 1.295.000</td>
      <td>Rua Paula Ney</td>
      <td>94 m²</td>
      <td>3 quartos</td>
      <td>2 vagas</td>
      <td>Aclimação</td>
    </tr>
    <tr>
      <th>1</th>
      <td>R$ 1.410.000</td>
      <td>Rua Castro Alves</td>
      <td>195 m²</td>
      <td>4 quartos</td>
      <td>3 vagas</td>
      <td>Aclimação</td>
    </tr>
    <tr>
      <th>2</th>
      <td>R$ 899.000</td>
      <td>Rua Urano</td>
      <td>118 m²</td>
      <td>3 quartos</td>
      <td>2 vagas</td>
      <td>Aclimação</td>
    </tr>
    <tr>
      <th>3</th>
      <td>R$ 540.000</td>
      <td>Rua Álvaro Guimarães</td>
      <td>29 m²</td>
      <td>Studio</td>
      <td>1 vaga</td>
      <td>Aclimação</td>
    </tr>
    <tr>
      <th>4</th>
      <td>R$ 1.175.000</td>
      <td>Rua Bartolomeu de Gusmão</td>
      <td>135 m²</td>
      <td>4 quartos</td>
      <td>2 vagas</td>
      <td>Aclimação</td>
    </tr>
  </tbody>
</table>
</div>




```python
dados.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 16380 entries, 0 to 16379
    Data columns (total 6 columns):
     #   Column    Non-Null Count  Dtype 
    ---  ------    --------------  ----- 
     0   preço     16380 non-null  object
     1   endereço  16380 non-null  object
     2   área      16380 non-null  object
     3   quartos   16380 non-null  object
     4   vagas     16380 non-null  object
     5   bairro    16380 non-null  object
    dtypes: object(6)
    memory usage: 767.9+ KB



```python
dados.nunique()
```




    preço       2765
    endereço    3352
    área         413
    quartos        8
    vagas          8
    bairro        99
    dtype: int64




```python
dados['bairro'].value_counts()
```




     Vila Andrade      987
     Bela Vista        567
     Sacomã            535
     Jabaquara         510
     Santana           484
                      ... 
     José Bonifácio      2
     Jardim Helena       2
     Guaianases          1
     Lajeado             1
     Jaraguá             1
    Name: bairro, Length: 99, dtype: int64




```python
dados.to_csv(r'/Users/leuzinger/Dropbox/Data Science/Awari/Web Scraping/Dados_ap.csv',index=False)
```
