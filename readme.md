# Comunicação de Acidente de Trabalho

Comunicação de Acidente de Trabalho (CAT) é o documento emitido para reconhecer um acidente de trabalho ou de trajeto, bem como uma doença ocupacional.

<details>

<summary>Clique para ver o sumário</summary>

## 📜 Sumário

- [Iniciando o projeto](#-iniciando-o-projeto)
    - [Pré-requisitos](#-pré-requisitos)
    - [Links úteis](#-links-úteis)
- [Entendendo os dados](#-entendendo-os-dados)
    - [Objetivo: Catweb](#-objetivo-catweb)
    - [Complemento: Dimensões](#-complemento-dimensões)
- [Etapas do projeto](#-etapas-do-projeto)
    - [Obtenção dos dados](#-obtenção-dos-dados)
    - [Primeiro tratamento](#️-primeiro-tratamento)
    - [Trabalhando as dimensões](#-trabalhando-as-dimensões)
    - [Segundo tratamento](#️-segundo-tratamento)
    - [Criando visualizações](#-criando-visualizações)

</details>

## 🌟 Iniciando o projeto

Para começar este projeto de Comunicação de Acidente de Trabalho, é ncessário configurar um ambiente Python e ter o Power BI instalado para criar as visualizações.

O projeto foi desenvolvido para fornecer insights sobre acidentes de trabalho e doenças ocupacionais, utilizando o plano de dados abertos do governo.

### 📋 Pré-requisitos

Antes de começar, certifique-se de ter o seguinte:

<details>

<summary>Veja a lista de pré-requisitos</summary><br>

1. Acesso a internet para download dos arquivos;
2. Ambiente de desenvolvimento, recomendo o uso de:
    - [VS Code](https://code.visualstudio.com/) (com extensão do Jupyter Notebook); ou
    - [Google Colab](https://colab.research.google.com) (vem com Jupyter integrado e roda na nuvem);
3. Python instalado na máquina, caso utilize o VS Code. Versão 3.7 ou superior;
4. Bibliotecas Python utilizadas:
    - pandas: Para manipulação e análise dos dados;
    - re: Para criação de expressão regular;
    - xlrd: Para trabalhar com arquivos .xls;
5. [Power BI Desktop](https://apps.microsoft.com/detail/9ntxr16hnw1t?hl=pt-br&gl=BR): Para criar e visualizar painéis;
6. [Power BI Online](https://app.powerbi.com): Conta para publicar e compartilhar o dashboard;
7. [Git](https://git-scm.com/downloads): Para clonar este repositório;
8. [Github](https://github.com/): Para criar seu repositório online.

</details>

Com os pré-requisitos acima atendidos, pode-se prosseguir para as próximas etapas do projeto.

### 🔗 Links úteis

Para ter acesso aos dados utilizados, confira a tabela abaixo:

| Fonte | Link |
| ----- | ---- |
| Comunicação de Acidente de Trabalho (2016-2018) | [dados.gov.br/cat_pt1](https://dados.gov.br/dados/conjuntos-dados/inss-comunicacao-de-acidente-de-trabalho-cat1) |
| Comunicação de Acidente de Trabalho (2023-2025) | [dados.gov.br/cat_pt2](https://dados.gov.br/dados/conjuntos-dados/comunicacoes-de-acidente-de-trabalho-cat-plano-de-dados-abertos-jun-2023-a-jun-2025) |
| Classificação Brasileira de Ocupações (CBO) | [mte.gov.br/cargos](https://cbo.mte.gov.br/cbosite/pages/home.jsf) |
| Classificações Estatísticas Nacionais (CNAE) | [ibge.gov.br/atividades](https://cnae.ibge.gov.br/classificacoes/download-concla/8265-download) |
| Códigos de Municípios do IBGE | [ibge.gov.br/municipios](https://www.ibge.gov.br/explica/codigos-dos-municipios.php) |
| Códigos Internacionais de Doenças (CID) | [datasus.gov.br/cid_10](http://www2.datasus.gov.br/cid10/V2008/cid10.htm)|
| Documentação Técnica do eSocial (Tabelas) | [esocial.gov.br/acidente](https://www.gov.br/esocial/pt-br/documentacao-tecnica/manuais/leiautes-esocial-v-1-1-beta/tabelas.html) |

Outros links úteis para dados do CID-10:

- [Cremesp](https://www.cremesp.org.br/pdfs/cid10_ultimaversaodisponivel_2012.pdf) - Arquivo .pdf com as subcategorias
- [Tabnet](http://tabnet.datasus.gov.br/cgi/sih/mxcid10lm.htm) - Lista de tabulação para morbidade
- [Dr. Jorge Assunção](https://docs.google.com/spreadsheets/d/e/2PACX-1vQG13ojbnTnmoF_UF69QVA5OjOOjB57m-xam6Ac1RhsMkOnsLPCn57xcVqsZ33ZFdP17gD38z7M_m5o/pubhtml) - Tabela no google sheets

> [!IMPORTANT]
> Os dados da CAT utilizados no projeto são apenas de 2024.  
> As tabelas do eSocial utilizadas foram: 13, 14, 15 e 17.

🔼 [Voltar ao Sumário](#-sumário)

## 📁 Entendendo os dados

Para iniciar uma **Comunicação de Acidente de Trabalho** é preciso cadastrar as informações na página da [Previdência Social](https://cadastro-cat.inss.gov.br/CATInternet/faces/pages/cadastramento/cadastramentoCat.xhtml), informando: tipo da comunicação, tipo do empregador, cnpj do empregador, cpf do empregado e data do acidente.

### 📝 Objetivo: Catweb

Quando baixamos os dados públicos dessas comunicações, obtemos ainda:

- Onde aconteceu o acidente
- De que forma aconteceu
- O que causou e a lesão sofrida
- O benefício recebido e por quem
- Se houve óbito ou não

### 📝 Complemento: Dimensões

As dimensões utilizadas se relacionam com os dados da CAT a partir dos códigos disponibilizados e servem como base para complementação dos dados.

Como tem limitação de caracteres nas colunas de texto da CAT, utilizei as dimensões e as tabelas do eSocial para completar e corrigir os textos abaixo:

- Espécie do benefício (ajuste ortográfico e de sigla)
- Agende causador do acidente (tabelas 14 e 15 do eSocial)
- Natureza da lesão (tabela 17 do eSocial)
- Parte do corpo atingida (tabela 13 do eSocial)
- Categorias do CID-10 (tabela com as categorias)

Para isso, exportei as colunas separadamente e, no Excel, alinhei os dados, criando um dicionário *(ref. ft_catweb_v2.ipynb)*.

#### ***Exemplo 1 - Exportação das colunas:***

```py
# Armazena as colunas selecionadas, dropando valores duplicados
verifica_cid = catweb_2024[['cd_cid', 'nm_cid']].drop_duplicates()

# Exporta a tabela para uso posterior
verifica_cid.to_csv('cid-10.csv', index=False, encoding='latin1')
```

#### ***Exemplo 2 - Aplicação do dicionário:***

```py
# Cria o dicionário com os valores da coluna e seus ajustes
beneficio = {
    'Pa': 'Auxílio-acidente',
    'Auxílio Doenca por A': 'Auxílio-doença acidentário',
    'Pensão por Morte Aci': 'Pensão por morte acidental'
}

# Aplica o dicionário na coluna, substituindo os valores
catweb_2024['tipo_beneficio'] = catweb_2024['tipo_beneficio'].replace(beneficio)
```

🔼 [Voltar ao Sumário](#-sumário)

## 👣 Etapas do projeto

Optei por realizar duas transformações nos dados coletados. Primeiro, um tratamento mais simples, padronizando os valores e normalizando os dados. Depois um mais complexo, que consistiu em substituir valores existentes em relação a ortografia, e cortes nos textos, deixando os valores mais legíveis ao carrregar no Power BI.

### 🔎 Obtenção dos dados

Pesquisei pelos dados da CAT no [Portal Brasileiro de Dados Abertos](https://dados.gov.br/home) e baixei os arquivos .zip diretamente, armazenando na pasta do projeto (já que escolhi usar o VS Code na execução).

### ✂️ Primeiro tratamento

Iniciei essa etapa, comparando os arquivos extraídos, para saber se eles têm as mesmas colunas e se elas estão na mesma ordem, para então empilhá-los.

***Verificação dos dataframes:***

```py
# Salva as colunas de cada dataframe em uma variável
colunas_jan = set(catweb_jan.columns)
colunas_fev = set(catweb_fev.columns)
colunas_mar = set(catweb_mar.columns)
colunas_abr = set(catweb_abr.columns)
colunas_mai = set(catweb_mai.columns)

# Verifica se as colunas são iguais em todos os dataframes
verifica_colunas = (colunas_jan == colunas_fev == colunas_mar == colunas_abr == colunas_mai)

# Verifica se as colunas estão na mesma ordem em todos os dataframes
verifica_ordem = (colunas_jan == colunas_fev == colunas_mar == colunas_abr == colunas_mai)

# Imprime o resultado (retornou True para ambos)
print("Todos os dataframes têm as mesmas colunas:", verifica_colunas)
print("Todos os dataframes têm colunas na mesma ordem:", verifica_ordem)
```

Outras verificações realizadas:

- Remoção de espaços em branco `strip()`
- Substituição de valores nulos `replace('x','y')`
- Padronização da quantidade de caracteres `zfill(x)`
- Extração de valores pela quantidade de caracteres `str[:y]`
- Padronização dos valores de data, veja abaixo

Como o `.unique()` abstrai valores quando em grande quantidade, fiz uma segunda verificação dos campos de data para ter certeza de que todas tinham o mesmo formato, após empilhar os dataframes e antes de converter a coluna.

***Verificação dos campos de data:***

```py
import re

# Expressão regular para verificar o formato da data
regex = re.compile(r'^\d{2}/\d{2}/\d{4}$')

# Verifica se todos os valores correspondem ao formato
formatos_invalidos = catweb_2024['dt_acidente'].apply(lambda x: not bool(regex.match(x))).sum()

# Imprime o resultado (retornou True para todos)
if formatos_invalidos > 0:
    print(f'Há {formatos_invalidos} datas que não correspondem ao formato dd/mm/yyyy.')
else:
    print('Todas as datas estão no formato dd/mm/yyyy.')
```

### 📚 Trabalhando as dimensões

Após concluir o tratamento, comecei a busca pelas dimensões e adaptei elas apenas para este projeto, mas o ideal é ter dimensões que possam ser utilizadas em projetos distintos, ou seja, mais completas e com mais informações.

- **dim_cnae** - atividades econômicas no nível de classe;
- **dim_cbo** - estrutura do cbo no nível de ocupações;
- **dim_mun** - relação dos estados e municípios do Brasil;
- **dim_cid** - tabela de morbidade no nível de categoria;

> [!TIP]
> A pasta **arquivos** tem as tabelas do eSocial compiladas em único aquivo, bem como, as tabelas exportadas no primeiro tratamento para ajustes realizados no segundo tratamento, a seguir.

### ✏️ Segundo tratamento

Assim como o [exemplo 2](#exemplo-2---aplicação-do-dicionário) acima, no segundo tratamento foram aplicadas várias substituições de valores incorretos gramaticalmente e/ou incompletos pela limitação da quantidade de caracteres da coluna.

O trabalho foi minuncioso e contou com apoio de tabelas do eSocial. Foi realizado no Excel, colocando os valores lado a lado e depois passado para o dicionário em Python.

Isso porque os códigos das tabelas do eSocial não constam nos dados da CAT para aplicar um relacionamento direto.

> [!NOTE]
> Além disso, optei por substituir a coluna com a data de nascimento do acidentado pela idade, calculando a idade na data do acidente.

### 💡 Criando visualizações

Para o painel, no Power BI, fiz as páginas por tema. A primeira página mostra uma visão geral das Comunicações de Acidentes de Trabalho; a segunda possui dados relacionados ao acidentado; a última conta com as causas das lesões sofridas.

Acesse o painel online, no link: [Painel CAT](https://app.powerbi.com/view?r=eyJrIjoiYTY4NzVjOGMtODVmZS00NzMzLWFkNTUtMTgwNmJiZTMzYTdmIiwidCI6ImEzZTU3Zjc1LTU5YTktNDFkOS05ZGIwLTA0YmM0ODg2YWY3NyJ9)  e veja uma prévia abaixo:

![Pagina 1]('https://github.com/andreadcsousa/projeto_acidentes-trabalho_python_pbi/imagens/pagina1.jpg')

<br>

🔼 [Voltar ao Sumário](#-sumário)