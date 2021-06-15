# Web Scraping de apartamentos na Loft

Este notebook é parte das tarefas do curso de Data Science da [Awari](https://awari.com.br/). **O objetivo deste projeto é fazer o web scraping de apartamentos em São Paulo no site da Loft e criar um data set com os dados coletados**.

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


```python
#abrir a página da Loft
driver = webdriver.Chrome(ChromeDriverManager().install())
driver.implicitly_wait(10)
driver.get("https://loft.com.br/apartamentos/sao-paulo-sp/")

#dados_ap = pd.DataFrame(columns=['preço','endereço','área','quartos','vagas'])

#pegar número total de bairros
bairros_botão = driver.find_elements_by_xpath('//*[@id="__next"]/section/div/div/div[1]/div[2]/div/div/div[2]/div[2]/div')[0]
bairros_botão.click()
bairros = driver.find_elements_by_class_name('MuiTypography-root.MuiFormControlLabel-label.MuiTypography-body1')
tot_bairros = len(bairros)
print(tot_bairros)
driver.quit()
```


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
 
```


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
