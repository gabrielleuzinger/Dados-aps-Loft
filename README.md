Este repositório é parte das tarefas do curso de Data Science da Awari. O objetivo deste projeto é fazer uma calculadora de preço de apartamentos. Usamos como base os preços no site da [Loft](https://loft.com.br/). **O projeto ainda está em desenvolvimento.**

Este projeto foi montado seguindo as premissas de [Pesquisas Reprodutíveis](https://pt.coursera.org/learn/reproducible-research), de modo que qualquer pessoa consiga chegar aos mesmos resultados que eu utilizando os passos que segui no Jupyter Notebook.

# Dependências do projeto

Todas as dependências podem ser encontradas no arquivo `requirements.txt`, mas abaixo estão listadas:
* Selenium
* Jupyter Notebook

Para instalar as dependências execute na pasta raiz do projeto: `pip install -r requirements.txt`. 

Para acessar o Jupyter Notebook que criei, execute na pasta raiz do projeto `jupyter notebook`. Logo em seguida seu browser será aberto e basta selecionar o arquivo `Web Scraping na Loft.ipynb`. 

É importante frisar que os dados utilizados para este desafio não foram adicionados a este projeto. 

# Estrutura do projeto

```{sh}
  .
  |-reports
  |  |- markdown
  |  |  |- Web Scraping na Loft.md
  |-data
  |- Web Scraping na Loft.ipynb
  |- Data Cleaning dados Loft.ipynb
  |- requirements.txt
```

A pasta `report` contém um arquivo html com uma versão do relatório gerado a partir do estudo feito nesse projeto. Esse arquivo contém **todos os insights e estudos feitos, bem como uma descrição detalhada de como foi elaborado o projeto**.

 **Todas as referências utilizadas para a criação desse projeto estão descritas no report**.
