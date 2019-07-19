

```python
import pandas as pd
import numpy as np
import wget
from os import listdir, remove, stat, mkdir
import os.path
import requests
import zipfile
import xml.etree.cElementTree as et


pd.set_option('display.max_columns', 500)
pd.options.display.float_format = '{:.2f}'.format
```


```python
def convert_bytes(num):
    for x in ['bytes', 'KB', 'MB', 'GB', 'TB']:
        if num < 1024.0:
            return "%3.1f %s" % (num, x)
        num /= 1024.0

def file_size(file_path):
    if os.path.isfile(file_path):
        file_info = os.stat(file_path)
        return convert_bytes(file_info.st_size)
    
def unzip_all(filename,temp_dir):
    with zipfile.ZipFile(filename,"r") as zip_ref:
        zip_ref.extractall(download_folder+temp_dir)
        
def unzip_file(file,filename,temp_dir):
    with zipfile.ZipFile(filename,"r") as zip_ref:
        zip_ref.extract(file,download_folder+temp_dir)
        
def unzip_list(filename):
    with zipfile.ZipFile(filename,"r") as zip_ref:
        return zip_ref.namelist()
    
def read_xml(file):
    root = et.parse(file).getroot()
    d  = dict()
    for count,elem in enumerate(root):
        d[count] = elem.attrib
    return pd.DataFrame.from_dict(d, orient='index')
```


```python
download_folder = 'd:\\publicidade_oficial\\download\\'
data_folder = 'd:\\publicidade_oficial\\data\\'
```

## Data from TSE


```python
for year in [2008,2012,2016]:
    df = pd.DataFrame([])
    filename = 'consulta_'+str(year)+'.zip'
    if filename not in listdir(download_folder):
        wget.download('http://agencia.tse.jus.br/estatistica/sead/odsele/consulta_cand/consulta_cand_'+str(year)+'.zip', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]
    unzip_all(download_folder+filename,temp_dir)
    files = [download_folder+temp_dir+'\\'+x for x in listdir(download_folder+temp_dir) if x.split('.')[-1] != 'pdf']
    if year < 2016:
        names = ['DT_GERACAO', 'HH_GERACAO', 'ANO_ELEICAO', 'NR_TURNO', 'DS_ELEICAO', 'SG_UF', 'SG_UE', 'NM_UE', 'CD_CARGO', 'DS_CARGO', 'NM_CANDIDATO', 'SQ_CANDIDATO', 'NR_CANDIDATO', 'NR_CPF_CANDIDATO', 'NM_URNA_CANDIDATO', 'CD_SITUACAO_CANDIDATURA', 'DS_DETALHE_SITUACAO_CAND', 'NR_PARTIDO', 'SG_PARTIDO', 'NM_PARTIDO', 'CODIGO_LEGENDA', 'SIGLA_LEGENDA', 'DS_COMPOSICAO_COLIGACAO', 'NM_COLIGACAO', 'CD_OCUPACAO', 'DS_OCUPACAO', 'DT_NASCIMENTO', 'NR_TITULO_ELEITORAL_CANDIDATO', 'NR_IDADE_DATA_POSSE', 'CD_GENERO', 'DS_GENERO', 'CD_GRAU_INSTRUCAO', 'DS_GRAU_INSTRUCAO', 'CD_ESTADO_CIVIL', 'DS_ESTADO_CIVIL', 'CD_NACIONALIDADE', 'DS_NACIONALIDADE', 'SG_UF_NASCIMENTO', 'CD_MUNICIPIO_NASCIMENTO', 'NM_MUNICIPIO_NASCIMENTO', 'NR_DESPESA_MAX_CAMPANHA', 'CD_SIT_TOT_TURNO', 'DS_SIT_TOT_TURNO']
        if year == 2012:
            names = names + ['NM_EMAIL']
        for f in files:
            df_uf = pd.read_csv(f, sep=';', encoding = "ISO-8859-1", names=names)
            df = df.append(df_uf, sort=False, ignore_index=True)
    else:
        for f in files:
            df_uf = pd.read_csv(f, sep=';', encoding = "ISO-8859-1")
            df = df.append(df_uf, sort=False)
    df = df[(df['DS_CARGO'] == 'PREFEITO') | (df['DS_CARGO'] == 'VICE-PREFEITO')]
    display(df.head())
    df.to_pickle(data_folder+'candidato_'+str(year)+'.pickle')
```

    2008
    consulta_2008.zip 23.6 MB
    


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
      <th>DT_GERACAO</th>
      <th>HH_GERACAO</th>
      <th>ANO_ELEICAO</th>
      <th>NR_TURNO</th>
      <th>DS_ELEICAO</th>
      <th>SG_UF</th>
      <th>SG_UE</th>
      <th>NM_UE</th>
      <th>CD_CARGO</th>
      <th>DS_CARGO</th>
      <th>NM_CANDIDATO</th>
      <th>SQ_CANDIDATO</th>
      <th>NR_CANDIDATO</th>
      <th>NR_CPF_CANDIDATO</th>
      <th>NM_URNA_CANDIDATO</th>
      <th>CD_SITUACAO_CANDIDATURA</th>
      <th>DS_DETALHE_SITUACAO_CAND</th>
      <th>NR_PARTIDO</th>
      <th>SG_PARTIDO</th>
      <th>NM_PARTIDO</th>
      <th>CODIGO_LEGENDA</th>
      <th>SIGLA_LEGENDA</th>
      <th>DS_COMPOSICAO_COLIGACAO</th>
      <th>NM_COLIGACAO</th>
      <th>CD_OCUPACAO</th>
      <th>DS_OCUPACAO</th>
      <th>DT_NASCIMENTO</th>
      <th>NR_TITULO_ELEITORAL_CANDIDATO</th>
      <th>NR_IDADE_DATA_POSSE</th>
      <th>CD_GENERO</th>
      <th>DS_GENERO</th>
      <th>CD_GRAU_INSTRUCAO</th>
      <th>DS_GRAU_INSTRUCAO</th>
      <th>CD_ESTADO_CIVIL</th>
      <th>DS_ESTADO_CIVIL</th>
      <th>CD_NACIONALIDADE</th>
      <th>DS_NACIONALIDADE</th>
      <th>SG_UF_NASCIMENTO</th>
      <th>CD_MUNICIPIO_NASCIMENTO</th>
      <th>NM_MUNICIPIO_NASCIMENTO</th>
      <th>NR_DESPESA_MAX_CAMPANHA</th>
      <th>CD_SIT_TOT_TURNO</th>
      <th>DS_SIT_TOT_TURNO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>26/04/2018</td>
      <td>19:31:19</td>
      <td>2008</td>
      <td>1</td>
      <td>Eleições 2008</td>
      <td>AC</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>CARLOS CÉSAR NUNES DE ARAÚJO</td>
      <td>667</td>
      <td>40</td>
      <td>73900443220</td>
      <td>CARLINHOS</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>40</td>
      <td>PSB</td>
      <td>PARTIDO SOCIALISTA BRASILEIRO</td>
      <td>76</td>
      <td>#NE#</td>
      <td>#NE#</td>
      <td>'UNIDOS VENCEREMOS'</td>
      <td>278</td>
      <td>VEREADOR</td>
      <td>17/07/1984</td>
      <td>3851432453</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>6</td>
      <td>ENSINO MÉDIO COMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-1</td>
      <td>PLÁCIDO DE CASTRO</td>
      <td>-1</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
    </tr>
    <tr>
      <th>1</th>
      <td>26/04/2018</td>
      <td>19:31:19</td>
      <td>2008</td>
      <td>1</td>
      <td>Eleições 2008</td>
      <td>AC</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>JOSÉ DONISETE DE MELO</td>
      <td>801</td>
      <td>15</td>
      <td>39642232120</td>
      <td>PROFESSOR DONISETE</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>15</td>
      <td>PMDB</td>
      <td>PARTIDO DO MOVIMENTO DEMOCRÁTICO BRASILEIRO</td>
      <td>90</td>
      <td>#NE#</td>
      <td>#NE#</td>
      <td>TODOS PELA MUNDANÇA</td>
      <td>265</td>
      <td>PROFESSOR DE ENSINO FUNDAMENTAL</td>
      <td>13/07/1967</td>
      <td>2803292410</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>8</td>
      <td>SUPERIOR COMPLETO</td>
      <td>1</td>
      <td>SOLTEIRO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>MS</td>
      <td>-1</td>
      <td>DOURADOS</td>
      <td>-1</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
    </tr>
    <tr>
      <th>2</th>
      <td>26/04/2018</td>
      <td>19:31:19</td>
      <td>2008</td>
      <td>1</td>
      <td>Eleições 2008</td>
      <td>AC</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>VILSEU FERREIRA DA SILVA</td>
      <td>637</td>
      <td>11</td>
      <td>27278913187</td>
      <td>VILSEU FERREIRA</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>11</td>
      <td>PP</td>
      <td>PARTIDO PROGRESSISTA</td>
      <td>71</td>
      <td>#NE#</td>
      <td>#NE#</td>
      <td>FRENTE POPULAR DE ACRELANDIA - FPA</td>
      <td>275</td>
      <td>PREFEITO</td>
      <td>18/04/1960</td>
      <td>411782470</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>5</td>
      <td>ENSINO MÉDIO INCOMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>PR</td>
      <td>-1</td>
      <td>PEROLA DO OESTE</td>
      <td>-1</td>
      <td>1</td>
      <td>ELEITO</td>
    </tr>
    <tr>
      <th>3</th>
      <td>26/04/2018</td>
      <td>19:31:19</td>
      <td>2008</td>
      <td>1</td>
      <td>Eleições 2008</td>
      <td>AC</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>CESALPINO FARIA DE ARAUJO</td>
      <td>638</td>
      <td>11</td>
      <td>77722914772</td>
      <td>PININHO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>13</td>
      <td>PT</td>
      <td>PARTIDO DOS TRABALHADORES</td>
      <td>71</td>
      <td>#NE#</td>
      <td>#NE#</td>
      <td>FRENTE POPULAR DE ACRELANDIA - FPA</td>
      <td>601</td>
      <td>AGRICULTOR</td>
      <td>03/05/1959</td>
      <td>2158792453</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>4</td>
      <td>ENSINO FUNDAMENTAL COMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>ES</td>
      <td>-1</td>
      <td>BARRA DE SAO FRANCISCO</td>
      <td>-1</td>
      <td>-1</td>
      <td>#NULO#</td>
    </tr>
    <tr>
      <th>4</th>
      <td>26/04/2018</td>
      <td>19:31:19</td>
      <td>2008</td>
      <td>1</td>
      <td>Eleições 2008</td>
      <td>AC</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>CLOVIS VALDIR MORETI</td>
      <td>668</td>
      <td>40</td>
      <td>45848106134</td>
      <td>CLOVIS</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>33</td>
      <td>PMN</td>
      <td>PARTIDO DA MOBILIZAÇÃO NACIONAL</td>
      <td>76</td>
      <td>#NE#</td>
      <td>#NE#</td>
      <td>'UNIDOS VENCEREMOS'</td>
      <td>602</td>
      <td>PECUARISTA</td>
      <td>11/04/1968</td>
      <td>3550702429</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>2</td>
      <td>LÊ E ESCREVE</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>PR</td>
      <td>-1</td>
      <td>LARANJEIRAS DO SUL</td>
      <td>-1</td>
      <td>-1</td>
      <td>#NULO#</td>
    </tr>
  </tbody>
</table>
</div>


    2012
    consulta_2012.zip 32.9 MB
    


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
      <th>DT_GERACAO</th>
      <th>HH_GERACAO</th>
      <th>ANO_ELEICAO</th>
      <th>NR_TURNO</th>
      <th>DS_ELEICAO</th>
      <th>SG_UF</th>
      <th>SG_UE</th>
      <th>NM_UE</th>
      <th>CD_CARGO</th>
      <th>DS_CARGO</th>
      <th>NM_CANDIDATO</th>
      <th>SQ_CANDIDATO</th>
      <th>NR_CANDIDATO</th>
      <th>NR_CPF_CANDIDATO</th>
      <th>NM_URNA_CANDIDATO</th>
      <th>CD_SITUACAO_CANDIDATURA</th>
      <th>DS_DETALHE_SITUACAO_CAND</th>
      <th>NR_PARTIDO</th>
      <th>SG_PARTIDO</th>
      <th>NM_PARTIDO</th>
      <th>CODIGO_LEGENDA</th>
      <th>SIGLA_LEGENDA</th>
      <th>DS_COMPOSICAO_COLIGACAO</th>
      <th>NM_COLIGACAO</th>
      <th>CD_OCUPACAO</th>
      <th>DS_OCUPACAO</th>
      <th>DT_NASCIMENTO</th>
      <th>NR_TITULO_ELEITORAL_CANDIDATO</th>
      <th>NR_IDADE_DATA_POSSE</th>
      <th>CD_GENERO</th>
      <th>DS_GENERO</th>
      <th>CD_GRAU_INSTRUCAO</th>
      <th>DS_GRAU_INSTRUCAO</th>
      <th>CD_ESTADO_CIVIL</th>
      <th>DS_ESTADO_CIVIL</th>
      <th>CD_NACIONALIDADE</th>
      <th>DS_NACIONALIDADE</th>
      <th>SG_UF_NASCIMENTO</th>
      <th>CD_MUNICIPIO_NASCIMENTO</th>
      <th>NM_MUNICIPIO_NASCIMENTO</th>
      <th>NR_DESPESA_MAX_CAMPANHA</th>
      <th>CD_SIT_TOT_TURNO</th>
      <th>DS_SIT_TOT_TURNO</th>
      <th>NM_EMAIL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>15/07/2016</td>
      <td>19:21:07</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1015</td>
      <td>CAPIXABA</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>RUBENS ALVES DA SILVA</td>
      <td>10000002537</td>
      <td>33</td>
      <td>1540734234</td>
      <td>ZÉ DO CÔCO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>25</td>
      <td>DEM</td>
      <td>DEMOCRATAS</td>
      <td>10000000206</td>
      <td>#NE#</td>
      <td>DEM / PMN</td>
      <td>PODEMOS MUDAR, MAS PRECISAMOS DE VOCÊ</td>
      <td>601</td>
      <td>AGRICULTOR</td>
      <td>22/02/1941</td>
      <td>372362461</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>3</td>
      <td>ENSINO FUNDAMENTAL INCOMPLETO</td>
      <td>7</td>
      <td>SEPARADO(A) JUDICIALMENTE</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-1</td>
      <td>XAPURI</td>
      <td>-1.0</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>#NULO#</td>
    </tr>
    <tr>
      <th>9</th>
      <td>15/07/2016</td>
      <td>19:21:07</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1015</td>
      <td>CAPIXABA</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>FRANCISCA LEIDA ARAUJO DA COSTA</td>
      <td>10000002536</td>
      <td>33</td>
      <td>22064834249</td>
      <td>LEIDA</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>33</td>
      <td>PMN</td>
      <td>PARTIDO DA MOBILIZAÇÃO NACIONAL</td>
      <td>10000000206</td>
      <td>#NE#</td>
      <td>DEM / PMN</td>
      <td>PODEMOS MUDAR, MAS PRECISAMOS DE VOCÊ</td>
      <td>169</td>
      <td>COMERCIANTE</td>
      <td>10/12/1961</td>
      <td>693592461</td>
      <td>-1</td>
      <td>4</td>
      <td>FEMININO</td>
      <td>8</td>
      <td>SUPERIOR COMPLETO</td>
      <td>1</td>
      <td>SOLTEIRO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-1</td>
      <td>RIO BRANCO</td>
      <td>300000.0</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>LEIIDAARAUJO@YAHOO.COM.BR</td>
    </tr>
    <tr>
      <th>28</th>
      <td>15/07/2016</td>
      <td>19:21:07</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1139</td>
      <td>FEIJÓ</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>ABNER TAVARES DOS SANTOS</td>
      <td>10000000440</td>
      <td>11</td>
      <td>36014095268</td>
      <td>ABNER</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>15</td>
      <td>PMDB</td>
      <td>PARTIDO DO MOVIMENTO DEMOCRÁTICO BRASILEIRO</td>
      <td>10000000051</td>
      <td>#NE#</td>
      <td>PP / PMDB</td>
      <td>RENOVA FEIJO</td>
      <td>257</td>
      <td>EMPRESÁRIO</td>
      <td>11/04/1972</td>
      <td>1812342437</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>4</td>
      <td>ENSINO FUNDAMENTAL COMPLETO</td>
      <td>1</td>
      <td>SOLTEIRO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-1</td>
      <td>FEIJO</td>
      <td>-1.0</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>AUDIOTROM@GMAIL.COM</td>
    </tr>
    <tr>
      <th>29</th>
      <td>15/07/2016</td>
      <td>19:21:07</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1139</td>
      <td>FEIJÓ</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>KIEFER ROBERTO CAVALCANTE LIMA</td>
      <td>10000000439</td>
      <td>11</td>
      <td>30870968220</td>
      <td>KIEFER</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>11</td>
      <td>PP</td>
      <td>PARTIDO PROGRESSISTA</td>
      <td>10000000051</td>
      <td>#NE#</td>
      <td>PP / PMDB</td>
      <td>RENOVA FEIJO</td>
      <td>257</td>
      <td>EMPRESÁRIO</td>
      <td>16/09/1968</td>
      <td>1555542453</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>6</td>
      <td>ENSINO MÉDIO COMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-1</td>
      <td>FEIJO</td>
      <td>500000.0</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>KIEFERLIMA11@HOTMAIL.COM</td>
    </tr>
    <tr>
      <th>50</th>
      <td>15/07/2016</td>
      <td>19:21:07</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1023</td>
      <td>PORTO ACRE</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>RAIMUNDO JERONIMO DOS ANJOS CHAVES</td>
      <td>10000000008</td>
      <td>13</td>
      <td>12896535268</td>
      <td>COCA</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>65</td>
      <td>PC do B</td>
      <td>PARTIDO COMUNISTA DO BRASIL</td>
      <td>10000000004</td>
      <td>#NE#</td>
      <td>PRB / PDT / PT / PTN / PSDC / PMN / PSB / PC do B</td>
      <td>FRENTE POPULAR DE PORTO ACRE-FPPA</td>
      <td>298</td>
      <td>SERVIDOR PÚBLICO MUNICIPAL</td>
      <td>18/09/1962</td>
      <td>1974152470</td>
      <td>-1</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>8</td>
      <td>SUPERIOR COMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-1</td>
      <td>RIO BRANCO</td>
      <td>-1.0</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>#NULO#</td>
    </tr>
  </tbody>
</table>
</div>


    2016
    consulta_2016.zip 124.0 MB
    


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
      <th>DT_GERACAO</th>
      <th>HH_GERACAO</th>
      <th>ANO_ELEICAO</th>
      <th>CD_TIPO_ELEICAO</th>
      <th>NM_TIPO_ELEICAO</th>
      <th>NR_TURNO</th>
      <th>CD_ELEICAO</th>
      <th>DS_ELEICAO</th>
      <th>DT_ELEICAO</th>
      <th>TP_ABRANGENCIA</th>
      <th>SG_UF</th>
      <th>SG_UE</th>
      <th>NM_UE</th>
      <th>CD_CARGO</th>
      <th>DS_CARGO</th>
      <th>SQ_CANDIDATO</th>
      <th>NR_CANDIDATO</th>
      <th>NM_CANDIDATO</th>
      <th>NM_URNA_CANDIDATO</th>
      <th>NM_SOCIAL_CANDIDATO</th>
      <th>NR_CPF_CANDIDATO</th>
      <th>NM_EMAIL</th>
      <th>CD_SITUACAO_CANDIDATURA</th>
      <th>DS_SITUACAO_CANDIDATURA</th>
      <th>CD_DETALHE_SITUACAO_CAND</th>
      <th>DS_DETALHE_SITUACAO_CAND</th>
      <th>TP_AGREMIACAO</th>
      <th>NR_PARTIDO</th>
      <th>SG_PARTIDO</th>
      <th>NM_PARTIDO</th>
      <th>SQ_COLIGACAO</th>
      <th>NM_COLIGACAO</th>
      <th>DS_COMPOSICAO_COLIGACAO</th>
      <th>CD_NACIONALIDADE</th>
      <th>DS_NACIONALIDADE</th>
      <th>SG_UF_NASCIMENTO</th>
      <th>CD_MUNICIPIO_NASCIMENTO</th>
      <th>NM_MUNICIPIO_NASCIMENTO</th>
      <th>DT_NASCIMENTO</th>
      <th>NR_IDADE_DATA_POSSE</th>
      <th>NR_TITULO_ELEITORAL_CANDIDATO</th>
      <th>CD_GENERO</th>
      <th>DS_GENERO</th>
      <th>CD_GRAU_INSTRUCAO</th>
      <th>DS_GRAU_INSTRUCAO</th>
      <th>CD_ESTADO_CIVIL</th>
      <th>DS_ESTADO_CIVIL</th>
      <th>CD_COR_RACA</th>
      <th>DS_COR_RACA</th>
      <th>CD_OCUPACAO</th>
      <th>DS_OCUPACAO</th>
      <th>NR_DESPESA_MAX_CAMPANHA</th>
      <th>CD_SIT_TOT_TURNO</th>
      <th>DS_SIT_TOT_TURNO</th>
      <th>ST_REELEICAO</th>
      <th>ST_DECLARAR_BENS</th>
      <th>NR_PROTOCOLO_CANDIDATURA</th>
      <th>NR_PROCESSO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>28/05/2019</td>
      <td>15:09:28</td>
      <td>2016</td>
      <td>2</td>
      <td>ELEIÇÃO ORDINÁRIA</td>
      <td>1</td>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>02/10/2016</td>
      <td>MUNICIPAL</td>
      <td>AC</td>
      <td>1538</td>
      <td>SENADOR GUIOMARD</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>10000002173</td>
      <td>45</td>
      <td>VANDERSON MOREIRA MACIEL</td>
      <td>VANDERSON MAMBIRA</td>
      <td>#NULO#</td>
      <td>87865173253</td>
      <td>MAMBIS1@HOTMAIL.COM</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>COLIGAÇÃO</td>
      <td>25</td>
      <td>DEM</td>
      <td>DEMOCRATAS</td>
      <td>10000000209</td>
      <td>QUINARI PODE MAIS</td>
      <td>PSDB / PTN / DEM</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-3</td>
      <td>SENADOR GUIOMARD</td>
      <td>10/12/1985</td>
      <td>31.0</td>
      <td>4497992461</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>8</td>
      <td>SUPERIOR COMPLETO</td>
      <td>1</td>
      <td>SOLTEIRO(A)</td>
      <td>1</td>
      <td>BRANCA</td>
      <td>125</td>
      <td>ADMINISTRADOR</td>
      <td>-1</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>S</td>
      <td>50192016</td>
      <td>2719020166010008</td>
    </tr>
    <tr>
      <th>3</th>
      <td>28/05/2019</td>
      <td>15:09:28</td>
      <td>2016</td>
      <td>2</td>
      <td>ELEIÇÃO ORDINÁRIA</td>
      <td>1</td>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>02/10/2016</td>
      <td>MUNICIPAL</td>
      <td>AC</td>
      <td>1511</td>
      <td>PLÁCIDO DE CASTRO</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000002912</td>
      <td>65</td>
      <td>CAMILO DA SILVA</td>
      <td>PROFESSOR CAMILO DA SILVA</td>
      <td>#NULO#</td>
      <td>18874665253</td>
      <td>CAMILO560@LIVE.COM</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>COLIGAÇÃO</td>
      <td>65</td>
      <td>PC do B</td>
      <td>PARTIDO COMUNISTA DO BRASIL</td>
      <td>10000000269</td>
      <td>NOSSA FRENTE</td>
      <td>PC do B / PT / PSOL</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-3</td>
      <td>RIO BRANCO</td>
      <td>30/04/1965</td>
      <td>51.0</td>
      <td>4862432402</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>8</td>
      <td>SUPERIOR COMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>1</td>
      <td>BRANCA</td>
      <td>266</td>
      <td>PROFESSOR DE ENSINO MÉDIO</td>
      <td>-1</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>S</td>
      <td>59682016</td>
      <td>4753720166010008</td>
    </tr>
    <tr>
      <th>29</th>
      <td>28/05/2019</td>
      <td>15:09:28</td>
      <td>2016</td>
      <td>2</td>
      <td>ELEIÇÃO ORDINÁRIA</td>
      <td>1</td>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>02/10/2016</td>
      <td>MUNICIPAL</td>
      <td>AC</td>
      <td>1007</td>
      <td>BUJARI</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000001495</td>
      <td>40</td>
      <td>JOÃO EDVALDO TELES DE LIMA</td>
      <td>PADEIRO</td>
      <td>#NULO#</td>
      <td>3051781215</td>
      <td>A.G.FORTES@HOTMAIL.COM</td>
      <td>3</td>
      <td>INAPTO</td>
      <td>14</td>
      <td>INDEFERIDO</td>
      <td>COLIGAÇÃO</td>
      <td>40</td>
      <td>PSB</td>
      <td>PARTIDO SOCIALISTA BRASILEIRO</td>
      <td>10000000152</td>
      <td>Frente Alternativa do Bujarí</td>
      <td>PSB / PROS</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-3</td>
      <td>TARAUACÁ</td>
      <td>27/10/1956</td>
      <td>60.0</td>
      <td>393432461</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>3</td>
      <td>ENSINO FUNDAMENTAL INCOMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>3</td>
      <td>PARDA</td>
      <td>999</td>
      <td>OUTROS</td>
      <td>-1</td>
      <td>-1</td>
      <td>#NULO#</td>
      <td>N</td>
      <td>S</td>
      <td>42282016</td>
      <td>657320166010009</td>
    </tr>
    <tr>
      <th>36</th>
      <td>28/05/2019</td>
      <td>15:09:28</td>
      <td>2016</td>
      <td>2</td>
      <td>ELEIÇÃO ORDINÁRIA</td>
      <td>1</td>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>02/10/2016</td>
      <td>MUNICIPAL</td>
      <td>AC</td>
      <td>1490</td>
      <td>XAPURI</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000002340</td>
      <td>13</td>
      <td>FRANCISCO UBIRACY MACHADO DE VASCONCELOS</td>
      <td>BIRA</td>
      <td>#NULO#</td>
      <td>21583900268</td>
      <td>BIRAXAPURI@HOTMAIL.COM</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>COLIGAÇÃO</td>
      <td>13</td>
      <td>PT</td>
      <td>PARTIDO DOS TRABALHADORES</td>
      <td>10000000220</td>
      <td>FRENTE POPULAR DE XAPURI</td>
      <td>PT / PC do B / PSB</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>AC</td>
      <td>-3</td>
      <td>XAPURI</td>
      <td>01/10/1964</td>
      <td>52.0</td>
      <td>371462470</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>8</td>
      <td>SUPERIOR COMPLETO</td>
      <td>9</td>
      <td>DIVORCIADO(A)</td>
      <td>3</td>
      <td>PARDA</td>
      <td>297</td>
      <td>SERVIDOR PÚBLICO ESTADUAL</td>
      <td>0</td>
      <td>1</td>
      <td>ELEITO</td>
      <td>N</td>
      <td>S</td>
      <td>52372016</td>
      <td>442120166010002</td>
    </tr>
    <tr>
      <th>42</th>
      <td>28/05/2019</td>
      <td>15:09:28</td>
      <td>2016</td>
      <td>2</td>
      <td>ELEIÇÃO ORDINÁRIA</td>
      <td>1</td>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>02/10/2016</td>
      <td>MUNICIPAL</td>
      <td>AC</td>
      <td>1015</td>
      <td>CAPIXABA</td>
      <td>12</td>
      <td>VICE-PREFEITO</td>
      <td>10000003077</td>
      <td>40</td>
      <td>LIBERATO RIBEIRO DA SILVA FILHO</td>
      <td>FILIM</td>
      <td>#NULO#</td>
      <td>81222734249</td>
      <td>FILIM27@GMAIL.COM</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>COLIGAÇÃO</td>
      <td>31</td>
      <td>PHS</td>
      <td>PARTIDO HUMANISTA DA SOLIDARIEDADE</td>
      <td>10000000288</td>
      <td>SIM, NÓS PODEMOS!</td>
      <td>PSB / PHS</td>
      <td>1</td>
      <td>BRASILEIRA NATA</td>
      <td>SP</td>
      <td>-3</td>
      <td>SAO MIGUEL PAULISTA</td>
      <td>21/08/1982</td>
      <td>34.0</td>
      <td>3850462437</td>
      <td>2</td>
      <td>MASCULINO</td>
      <td>7</td>
      <td>SUPERIOR INCOMPLETO</td>
      <td>3</td>
      <td>CASADO(A)</td>
      <td>3</td>
      <td>PARDA</td>
      <td>298</td>
      <td>SERVIDOR PÚBLICO MUNICIPAL</td>
      <td>-1</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>N</td>
      <td>61442016</td>
      <td>5463920166010008</td>
    </tr>
  </tbody>
</table>
</div>



```python
for year in [2012,2016]:
    filename = 'prestacao_'+str(year)+'.zip'
    if filename not in listdir(download_folder):
        wget.download('http://agencia.tse.jus.br/estatistica/sead/odsele/prestacao_contas/prestacao_contas_final_'+str(year)+'.zip', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]
    file_list = unzip_list(download_folder+filename)
    for file_zip in file_list:
        if file_zip.startswith('despesas_cand') and file_zip.endswith('brasil.txt'):
            unzip_file(file_zip,download_folder+filename,temp_dir)
            df = pd.read_csv(download_folder+temp_dir+'\\'+file_zip, sep=';', encoding = "ISO-8859-1")
            df = df[(df['Cargo'] == 'Prefeito') | (df['Cargo'] == 'Vice-prefeito')]
            display(df.head())
            df.to_pickle(data_folder+'prestacao_'+str(year)+'.pickle')
```

    prestacao_2012.zip 640.1 MB
    


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
      <th>Cód. Eleição</th>
      <th>Desc. Eleição</th>
      <th>Data e hora</th>
      <th>Sequencial Candidato</th>
      <th>UF</th>
      <th>Número UE</th>
      <th>Município</th>
      <th>Sigla  Partido</th>
      <th>Número candidato</th>
      <th>Cargo</th>
      <th>Nome candidato</th>
      <th>CPF do candidato</th>
      <th>Tipo do documento</th>
      <th>Número do documento</th>
      <th>CPF/CNPJ do fornecedor</th>
      <th>Nome do fornecedor</th>
      <th>Nome do fornecedor (Receita Federal)</th>
      <th>Cod setor econômico do doador</th>
      <th>Setor econômico do fornecedor</th>
      <th>Data da despesa</th>
      <th>Valor despesa</th>
      <th>Tipo despesa</th>
      <th>Descriçao da despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>127</th>
      <td>47</td>
      <td>Eleição Municipal 2012</td>
      <td>06/07/201614:40:59</td>
      <td>130000025650</td>
      <td>MG</td>
      <td>42994</td>
      <td>CASCALHO RICO</td>
      <td>PSL</td>
      <td>17</td>
      <td>Prefeito</td>
      <td>DÁRIO BORGES DE REZENDE</td>
      <td>76629325672</td>
      <td>Nota Fiscal</td>
      <td>326</td>
      <td>3.524968e+12</td>
      <td>LINHA BORD LTDA ME</td>
      <td>LINHA BORD LTDA ME</td>
      <td>1412601.0</td>
      <td>Confecção de peças do vestuário, exceto roupas...</td>
      <td>21/08/2012</td>
      <td>420</td>
      <td>Publicidade por materiais impressos</td>
      <td>PRESTAÇÃO DE SERVIÇOS REFERENTE CONFECÇÃO DE M...</td>
    </tr>
    <tr>
      <th>545</th>
      <td>47</td>
      <td>Eleição Municipal 2012</td>
      <td>06/07/201614:40:59</td>
      <td>140000007619</td>
      <td>PA</td>
      <td>4430</td>
      <td>CAPANEMA</td>
      <td>PTB</td>
      <td>14</td>
      <td>Prefeito</td>
      <td>DANIEL TRAVASSOS DA ROSA COSTA</td>
      <td>45148317787</td>
      <td>Recibo</td>
      <td>035</td>
      <td>8.985740e+10</td>
      <td>ANTONIO ROBSON DA SILVA</td>
      <td>ANTONIO ROBSON DA SILVA</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>11/08/2012</td>
      <td>80</td>
      <td>Serviços prestados por terceiros</td>
      <td>PRESTAÇÃO DE SERVIÇOS COMO AUTONOMO NA AREA DE...</td>
    </tr>
    <tr>
      <th>546</th>
      <td>47</td>
      <td>Eleição Municipal 2012</td>
      <td>06/07/201614:40:59</td>
      <td>140000007619</td>
      <td>PA</td>
      <td>4430</td>
      <td>CAPANEMA</td>
      <td>PTB</td>
      <td>14</td>
      <td>Prefeito</td>
      <td>DANIEL TRAVASSOS DA ROSA COSTA</td>
      <td>45148317787</td>
      <td>Recibo</td>
      <td>005</td>
      <td>8.985740e+10</td>
      <td>ANTONIO ROBSON DA SILVA</td>
      <td>ANTONIO ROBSON DA SILVA</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>04/08/2012</td>
      <td>75</td>
      <td>Serviços prestados por terceiros</td>
      <td>PRESTAÇÃO DE SERVIÇOS COMO AUTONOMO NA AREA DE...</td>
    </tr>
    <tr>
      <th>547</th>
      <td>47</td>
      <td>Eleição Municipal 2012</td>
      <td>06/07/201614:40:59</td>
      <td>140000007619</td>
      <td>PA</td>
      <td>4430</td>
      <td>CAPANEMA</td>
      <td>PTB</td>
      <td>14</td>
      <td>Prefeito</td>
      <td>DANIEL TRAVASSOS DA ROSA COSTA</td>
      <td>45148317787</td>
      <td>Recibo</td>
      <td>101</td>
      <td>9.573716e+10</td>
      <td>AURIANA NUNES DA SILVA</td>
      <td>AURIANA NUNES DA SILVA</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>25/08/2012</td>
      <td>50</td>
      <td>Serviços prestados por terceiros</td>
      <td>PRESTAÇÃO DE SERVIÇOS COMO AUTONOMO NA AREA DE...</td>
    </tr>
    <tr>
      <th>548</th>
      <td>47</td>
      <td>Eleição Municipal 2012</td>
      <td>06/07/201614:40:59</td>
      <td>140000007619</td>
      <td>PA</td>
      <td>4430</td>
      <td>CAPANEMA</td>
      <td>PTB</td>
      <td>14</td>
      <td>Prefeito</td>
      <td>DANIEL TRAVASSOS DA ROSA COSTA</td>
      <td>45148317787</td>
      <td>Recibo</td>
      <td>066</td>
      <td>9.573716e+10</td>
      <td>AURIANA NUNES DA SILVA</td>
      <td>AURIANA NUNES DA SILVA</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>18/08/2012</td>
      <td>80</td>
      <td>Serviços prestados por terceiros</td>
      <td>PRESTAÇÃO DE SERVIÇOS COMO AUTONOMO NA AREA DE...</td>
    </tr>
  </tbody>
</table>
</div>


    prestacao_2016.zip 1.0 GB
    


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
      <th>Cód. Eleição</th>
      <th>Desc. Eleição</th>
      <th>Data e hora</th>
      <th>CNPJ Prestador Conta</th>
      <th>Sequencial Candidato</th>
      <th>UF</th>
      <th>Sigla da UE</th>
      <th>Nome da UE</th>
      <th>Sigla  Partido</th>
      <th>Número candidato</th>
      <th>Cargo</th>
      <th>Nome candidato</th>
      <th>CPF do candidato</th>
      <th>CPF do vice/suplente</th>
      <th>Tipo de documento</th>
      <th>Número do documento</th>
      <th>CPF/CNPJ do fornecedor</th>
      <th>Nome do fornecedor</th>
      <th>Nome do fornecedor (Receita Federal)</th>
      <th>Cod setor econômico do fornecedor</th>
      <th>Setor econômico do fornecedor</th>
      <th>Data da despesa</th>
      <th>Valor despesa</th>
      <th>Tipo despesa</th>
      <th>Descriçao da despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29</th>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>04/08/2018 21:41:54</td>
      <td>25469279000188</td>
      <td>250000021381</td>
      <td>SP</td>
      <td>62219</td>
      <td>BEBEDOURO</td>
      <td>DEM</td>
      <td>25</td>
      <td>Prefeito</td>
      <td>FERNANDO GALVÃO MOURA</td>
      <td>10890650861</td>
      <td>07038953893</td>
      <td>Nota Fiscal</td>
      <td>46 - NFE</td>
      <td>10375188000106</td>
      <td>FERNANDO ROBERTO BARONI NETO - ME</td>
      <td>FERNANDO ROBERTO BARONI NETO - ME</td>
      <td>7739099</td>
      <td>Aluguel de outras máquinas e equipamentos come...</td>
      <td>19/09/201600:00:00</td>
      <td>4000</td>
      <td>Comícios</td>
      <td>FORNECIIMENTO DE MONTAGEM DE PALCO E SOM/LUZ P...</td>
    </tr>
    <tr>
      <th>30</th>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>04/08/2018 21:41:54</td>
      <td>25469279000188</td>
      <td>250000021381</td>
      <td>SP</td>
      <td>62219</td>
      <td>BEBEDOURO</td>
      <td>DEM</td>
      <td>25</td>
      <td>Prefeito</td>
      <td>FERNANDO GALVÃO MOURA</td>
      <td>10890650861</td>
      <td>07038953893</td>
      <td>Recibo</td>
      <td>059</td>
      <td>38746940898</td>
      <td>GLEISE NAYARA DE OLIVEIRA GOMES</td>
      <td>GLEISE NAYARA DE OLIVEIRA GOMES</td>
      <td>#NULO</td>
      <td>#NULO</td>
      <td>01/09/201600:00:00</td>
      <td>120</td>
      <td>Despesas com pessoal</td>
      <td>ASSISTENTE DE CAMPANHA</td>
    </tr>
    <tr>
      <th>31</th>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>04/08/2018 21:41:54</td>
      <td>25469279000188</td>
      <td>250000021381</td>
      <td>SP</td>
      <td>62219</td>
      <td>BEBEDOURO</td>
      <td>DEM</td>
      <td>25</td>
      <td>Prefeito</td>
      <td>FERNANDO GALVÃO MOURA</td>
      <td>10890650861</td>
      <td>07038953893</td>
      <td>Recibo</td>
      <td>92</td>
      <td>23432890850</td>
      <td>FRANCISLAINE ELOISA CORREIA</td>
      <td>FRANCISLAINE ELOISA CORREIA</td>
      <td>#NULO</td>
      <td>#NULO</td>
      <td>01/09/201600:00:00</td>
      <td>185</td>
      <td>Despesas com pessoal</td>
      <td>ASSISTENTE DE CAMPANHA</td>
    </tr>
    <tr>
      <th>32</th>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>04/08/2018 21:41:54</td>
      <td>25469279000188</td>
      <td>250000021381</td>
      <td>SP</td>
      <td>62219</td>
      <td>BEBEDOURO</td>
      <td>DEM</td>
      <td>25</td>
      <td>Prefeito</td>
      <td>FERNANDO GALVÃO MOURA</td>
      <td>10890650861</td>
      <td>07038953893</td>
      <td>Recibo</td>
      <td>254</td>
      <td>37082602871</td>
      <td>GISLENE FERREIRA DE SOUZA</td>
      <td>GISLENE FERREIRA DE SOUZA</td>
      <td>#NULO</td>
      <td>#NULO</td>
      <td>01/09/201600:00:00</td>
      <td>410</td>
      <td>Despesas com pessoal</td>
      <td>ASSISTENTE DE CAMPANHA</td>
    </tr>
    <tr>
      <th>33</th>
      <td>220</td>
      <td>Eleições Municipais 2016</td>
      <td>04/08/2018 21:41:54</td>
      <td>25469279000188</td>
      <td>250000021381</td>
      <td>SP</td>
      <td>62219</td>
      <td>BEBEDOURO</td>
      <td>DEM</td>
      <td>25</td>
      <td>Prefeito</td>
      <td>FERNANDO GALVÃO MOURA</td>
      <td>10890650861</td>
      <td>07038953893</td>
      <td>Recibo</td>
      <td>159</td>
      <td>37082602871</td>
      <td>GISLENE FERREIRA DE SOUZA</td>
      <td>GISLENE FERREIRA DE SOUZA</td>
      <td>#NULO</td>
      <td>#NULO</td>
      <td>01/09/201600:00:00</td>
      <td>133</td>
      <td>Despesas com pessoal</td>
      <td>ASSISTENTE DE CAMPANHA</td>
    </tr>
  </tbody>
</table>
</div>



```python
for year in [2012,2016]:
    filename = 'resultado_'+str(year)+'.zip'
    df = pd.DataFrame([])
    if filename not in listdir(download_folder):
        wget.download('http://agencia.tse.jus.br/estatistica/sead/odsele/votacao_candidato_munzona/votacao_candidato_munzona_'+str(year)+'.zip', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]
    unzip_all(download_folder+filename,temp_dir)
    files = [download_folder+temp_dir+'\\'+x for x in listdir(download_folder+temp_dir) if x.split('.')[-1] != 'pdf']
    for f in files:
        if year == 2012:
            df_uf = pd.read_csv(f, sep=';', encoding = "ISO-8859-1", names=['DT_GERACAO', 'HH_GERACAO', 'ANO_ELEICAO', 'NR_TURNO', 'DS_ELEICAO', 'SG_UF', 'SG_UE', 'CD_MUNICIPIO', 'NM_MUNICIPIO', 'NR_ZONA', 'CD_CARGO', 'NR_CANDIDATO', 'SQ_CANDIDATO', 'NM_CANDIDATO', 'NM_URNA_CANDIDATO', 'DS_CARGO', 'CD_SITUACAO_CANDIDATURA', 'DS_SITUACAO_CANDIDATURA', 'CD_DETALHE_SITUACAO_CAND', 'DESC_SIT_CANDIDATO', 'CODIGO_SIT_CAND_TOT', 'DESC_SIT_CAND_TOT', 'NR_PARTIDO', 'SG_PARTIDO', 'NM_PARTIDO', 'SQ_COLIGACAO', 'NM_COLIGACAO', 'DS_COMPOSICAO_COLIGACAO', 'QT_VOTOS_NOMINAIS'])
        else:
            df_uf = pd.read_csv(f, sep=';', encoding = "ISO-8859-1")
        df = df.append(df_uf, sort=False, ignore_index=True)
    df['DS_CARGO'] = df['DS_CARGO'].str.upper()
    df = df[(df['DS_CARGO'] == 'PREFEITO')]
    display(df.head())
    df.to_pickle(data_folder+'resultado_'+str(year)+'.pickle')
```

    resultado_2012.zip 23.4 MB
    


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
      <th>DT_GERACAO</th>
      <th>HH_GERACAO</th>
      <th>ANO_ELEICAO</th>
      <th>NR_TURNO</th>
      <th>DS_ELEICAO</th>
      <th>SG_UF</th>
      <th>SG_UE</th>
      <th>CD_MUNICIPIO</th>
      <th>NM_MUNICIPIO</th>
      <th>NR_ZONA</th>
      <th>CD_CARGO</th>
      <th>NR_CANDIDATO</th>
      <th>SQ_CANDIDATO</th>
      <th>NM_CANDIDATO</th>
      <th>NM_URNA_CANDIDATO</th>
      <th>DS_CARGO</th>
      <th>CD_SITUACAO_CANDIDATURA</th>
      <th>DS_SITUACAO_CANDIDATURA</th>
      <th>CD_DETALHE_SITUACAO_CAND</th>
      <th>DESC_SIT_CANDIDATO</th>
      <th>CODIGO_SIT_CAND_TOT</th>
      <th>DESC_SIT_CAND_TOT</th>
      <th>NR_PARTIDO</th>
      <th>SG_PARTIDO</th>
      <th>NM_PARTIDO</th>
      <th>SQ_COLIGACAO</th>
      <th>NM_COLIGACAO</th>
      <th>DS_COMPOSICAO_COLIGACAO</th>
      <th>QT_VOTOS_NOMINAIS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>87</th>
      <td>15/07/2016</td>
      <td>21:41:43</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1040</td>
      <td>1040</td>
      <td>MARECHAL THAUMATURGO</td>
      <td>4</td>
      <td>11</td>
      <td>45</td>
      <td>10000001428</td>
      <td>MAURICIO JOSÉ DA SILVA PRAXEDES</td>
      <td>MAURICIO PRAXEDES</td>
      <td>PREFEITO</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>45</td>
      <td>PSDB</td>
      <td>PARTIDO DA SOCIAL DEMOCRACIA BRASILEIRA</td>
      <td>10000000122</td>
      <td>ALIANÇA DEMOCRÁTICA</td>
      <td>PMDB / PSDC / PSDB / PSD</td>
      <td>2062</td>
    </tr>
    <tr>
      <th>105</th>
      <td>15/07/2016</td>
      <td>21:41:43</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1058</td>
      <td>1058</td>
      <td>BRASILÉIA</td>
      <td>6</td>
      <td>11</td>
      <td>13</td>
      <td>10000000582</td>
      <td>JOSE ALVANI LOPES</td>
      <td>ALVANI</td>
      <td>PREFEITO</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>13</td>
      <td>PT</td>
      <td>PARTIDO DOS TRABALHADORES</td>
      <td>10000000061</td>
      <td>FPB-FRENTE POPULAR DE BRASILEIA</td>
      <td>PT / PSB / PV</td>
      <td>5561</td>
    </tr>
    <tr>
      <th>106</th>
      <td>15/07/2016</td>
      <td>21:41:43</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1058</td>
      <td>1058</td>
      <td>BRASILÉIA</td>
      <td>6</td>
      <td>11</td>
      <td>27</td>
      <td>10000001296</td>
      <td>JOSE MESSIAS RIBEIRO</td>
      <td>MESSIAS RIBEIRO</td>
      <td>PREFEITO</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>27</td>
      <td>PSDC</td>
      <td>PARTIDO SOCIAL DEMOCRATA CRISTÃO</td>
      <td>10000000113</td>
      <td>BRASILEIA PODE MAIS</td>
      <td>PSDC / PC do B</td>
      <td>420</td>
    </tr>
    <tr>
      <th>154</th>
      <td>15/07/2016</td>
      <td>21:41:43</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1040</td>
      <td>1040</td>
      <td>MARECHAL THAUMATURGO</td>
      <td>4</td>
      <td>11</td>
      <td>13</td>
      <td>10000000721</td>
      <td>ALDEMIR DA SILVA LOPES</td>
      <td>ALDEMIR LOPES</td>
      <td>PREFEITO</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>1</td>
      <td>ELEITO</td>
      <td>13</td>
      <td>PT</td>
      <td>PARTIDO DOS TRABALHADORES</td>
      <td>10000000069</td>
      <td>FRENTE POPULAR DE RECONSTRUÇÃO MUNICIPAL</td>
      <td>PP / PDT / PT / PTB / PTN / PSB / PC do B</td>
      <td>4095</td>
    </tr>
    <tr>
      <th>155</th>
      <td>15/07/2016</td>
      <td>21:41:43</td>
      <td>2012</td>
      <td>1</td>
      <td>ELEIÇÃO MUNICIPAL 2012</td>
      <td>AC</td>
      <td>1074</td>
      <td>1074</td>
      <td>CRUZEIRO DO SUL</td>
      <td>4</td>
      <td>11</td>
      <td>15</td>
      <td>10000000460</td>
      <td>VAGNER JOSE SALES</td>
      <td>VAGNER SALES</td>
      <td>PREFEITO</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>1</td>
      <td>ELEITO</td>
      <td>15</td>
      <td>PMDB</td>
      <td>PARTIDO DO MOVIMENTO DEMOCRÁTICO BRASILEIRO</td>
      <td>10000000053</td>
      <td>UNIAO, FORCA E TRABALHO</td>
      <td>PRB / PP / PDT / PMDB / PSL / PTN / PSC / PPS ...</td>
      <td>18840</td>
    </tr>
  </tbody>
</table>
</div>


    resultado_2016.zip 40.2 MB
    


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
      <th>DT_GERACAO</th>
      <th>HH_GERACAO</th>
      <th>ANO_ELEICAO</th>
      <th>CD_TIPO_ELEICAO</th>
      <th>NM_TIPO_ELEICAO</th>
      <th>NR_TURNO</th>
      <th>CD_ELEICAO</th>
      <th>DS_ELEICAO</th>
      <th>DT_ELEICAO</th>
      <th>TP_ABRANGENCIA</th>
      <th>SG_UF</th>
      <th>SG_UE</th>
      <th>NM_UE</th>
      <th>CD_MUNICIPIO</th>
      <th>NM_MUNICIPIO</th>
      <th>NR_ZONA</th>
      <th>CD_CARGO</th>
      <th>DS_CARGO</th>
      <th>SQ_CANDIDATO</th>
      <th>NR_CANDIDATO</th>
      <th>NM_CANDIDATO</th>
      <th>NM_URNA_CANDIDATO</th>
      <th>NM_SOCIAL_CANDIDATO</th>
      <th>CD_SITUACAO_CANDIDATURA</th>
      <th>DS_SITUACAO_CANDIDATURA</th>
      <th>CD_DETALHE_SITUACAO_CAND</th>
      <th>DS_DETALHE_SITUACAO_CAND</th>
      <th>TP_AGREMIACAO</th>
      <th>NR_PARTIDO</th>
      <th>SG_PARTIDO</th>
      <th>NM_PARTIDO</th>
      <th>SQ_COLIGACAO</th>
      <th>NM_COLIGACAO</th>
      <th>DS_COMPOSICAO_COLIGACAO</th>
      <th>CD_SIT_TOT_TURNO</th>
      <th>DS_SIT_TOT_TURNO</th>
      <th>ST_VOTO_EM_TRANSITO</th>
      <th>QT_VOTOS_NOMINAIS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>23</th>
      <td>28/05/2019</td>
      <td>15:29:27</td>
      <td>2016</td>
      <td>2</td>
      <td>Eleição Ordinária</td>
      <td>1</td>
      <td>220</td>
      <td>ELEIÇÕES MUNICIPAIS 2016</td>
      <td>02/10/2016</td>
      <td>M</td>
      <td>AC</td>
      <td>1023</td>
      <td>PORTO ACRE</td>
      <td>1023</td>
      <td>PORTO ACRE</td>
      <td>10</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000001061</td>
      <td>15</td>
      <td>ABÍLIO RODRIGUES BARBOSA NETO</td>
      <td>ABÍLIO RODRIGUES</td>
      <td>#NULO#</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>Partido isolado</td>
      <td>15</td>
      <td>PMDB</td>
      <td>Partido do Movimento Democrático Brasileiro</td>
      <td>10000000101</td>
      <td>PARTIDO ISOLADO</td>
      <td>PMDB</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>970</td>
    </tr>
    <tr>
      <th>64</th>
      <td>28/05/2019</td>
      <td>15:29:27</td>
      <td>2016</td>
      <td>2</td>
      <td>Eleição Ordinária</td>
      <td>1</td>
      <td>220</td>
      <td>ELEIÇÕES MUNICIPAIS 2016</td>
      <td>02/10/2016</td>
      <td>M</td>
      <td>AC</td>
      <td>1066</td>
      <td>PORTO WALTER</td>
      <td>1066</td>
      <td>PORTO WALTER</td>
      <td>4</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000002596</td>
      <td>13</td>
      <td>MARCOS TAVARES DE ALMEIDA</td>
      <td>MARCOS TAVARES</td>
      <td>#NULO#</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>Coligação</td>
      <td>13</td>
      <td>PT</td>
      <td>Partido dos Trabalhadores</td>
      <td>10000000243</td>
      <td>FRENTE POPULAR DE PORTO WALTER</td>
      <td>PT / PSB / PC do B / PROS</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>1502</td>
    </tr>
    <tr>
      <th>105</th>
      <td>28/05/2019</td>
      <td>15:29:27</td>
      <td>2016</td>
      <td>2</td>
      <td>Eleição Ordinária</td>
      <td>1</td>
      <td>220</td>
      <td>ELEIÇÕES MUNICIPAIS 2016</td>
      <td>02/10/2016</td>
      <td>M</td>
      <td>AC</td>
      <td>1015</td>
      <td>CAPIXABA</td>
      <td>1015</td>
      <td>CAPIXABA</td>
      <td>8</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000003076</td>
      <td>40</td>
      <td>LIDIA FRANKLIM DA SILVA</td>
      <td>LIDIA</td>
      <td>#NULO#</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>Coligação</td>
      <td>40</td>
      <td>PSB</td>
      <td>Partido Socialista Brasileiro</td>
      <td>10000000288</td>
      <td>SIM, NÓS PODEMOS!</td>
      <td>PSB / PHS</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>501</td>
    </tr>
    <tr>
      <th>197</th>
      <td>28/05/2019</td>
      <td>15:29:27</td>
      <td>2016</td>
      <td>2</td>
      <td>Eleição Ordinária</td>
      <td>1</td>
      <td>220</td>
      <td>ELEIÇÕES MUNICIPAIS 2016</td>
      <td>02/10/2016</td>
      <td>M</td>
      <td>AC</td>
      <td>1392</td>
      <td>RIO BRANCO</td>
      <td>1392</td>
      <td>RIO BRANCO</td>
      <td>1</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000001231</td>
      <td>15</td>
      <td>ELIANE PEREIRA SINHASIQUE</td>
      <td>ELIANE SINHASIQUE</td>
      <td>#NULO#</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>Coligação</td>
      <td>15</td>
      <td>PMDB</td>
      <td>Partido do Movimento Democrático Brasileiro</td>
      <td>10000000125</td>
      <td>RIO BRANCO DO FUTURO</td>
      <td>PP / PTB / PMDB / PPS / DEM / PMN / PSD</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>19359</td>
    </tr>
    <tr>
      <th>240</th>
      <td>28/05/2019</td>
      <td>15:29:27</td>
      <td>2016</td>
      <td>2</td>
      <td>Eleição Ordinária</td>
      <td>1</td>
      <td>220</td>
      <td>ELEIÇÕES MUNICIPAIS 2016</td>
      <td>02/10/2016</td>
      <td>M</td>
      <td>AC</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>1120</td>
      <td>ACRELÂNDIA</td>
      <td>8</td>
      <td>11</td>
      <td>PREFEITO</td>
      <td>10000002509</td>
      <td>45</td>
      <td>ARISTON DE SOUZA JARDIM</td>
      <td>ARISTON</td>
      <td>#NULO#</td>
      <td>12</td>
      <td>APTO</td>
      <td>2</td>
      <td>DEFERIDO</td>
      <td>Partido isolado</td>
      <td>45</td>
      <td>PSDB</td>
      <td>Partido da Social Democracia Brasileira</td>
      <td>10000000237</td>
      <td>PARTIDO ISOLADO</td>
      <td>PSDB</td>
      <td>4</td>
      <td>NÃO ELEITO</td>
      <td>N</td>
      <td>627</td>
    </tr>
  </tbody>
</table>
</div>


## Auxiliary data
### population from IBGE
source: IBGE ftp://ftp.ibge.gov.br/


```python
picklename = 'pop2016.pickle'
filename = 'pop2016.xls'
if filename not in listdir(download_folder):
    wget.download('ftp://ftp.ibge.gov.br/Estimativas_de_Populacao/Estimativas_2016/estimativa_TCU_2016_20170614.xls', download_folder + filename)
print(filename,file_size(download_folder+filename))
pop2016 = pd.read_excel(download_folder+filename,'Municípios',header=1,
                        names=['uf','cod_uf','cod_mun','municipio','pop_2016'])
pop2016['mun_uf'] = pop2016.municipio.str.upper()+'-'+pop2016.uf.str.upper()
pop2016.loc[pop2016.mun_uf == 'JACAREACANGA-PA',    'pop_2016'] = 41487
pop2016.loc[pop2016.mun_uf == 'PORTO VELHO-RO',     'pop_2016'] = 494013
pop2016.loc[pop2016.mun_uf == 'CORONEL JOÃO SÁ-BA', 'pop_2016'] = 41487
pop2016.loc[pop2016.mun_uf == 'PORTO VELHO-RO',     'pop_2016'] = 494013
pop2016.loc[pop2016.mun_uf == 'CORONEL JOÃO SÁ-BA', 'pop_2016'] = 17422
pop2016.loc[pop2016.mun_uf == 'LIVRAMENTO-PB',      'pop_2016'] = 7248
pop2016.loc[pop2016.mun_uf == 'TAPEROÁ-PB',         'pop_2016'] = 15316
pop2016.dropna(inplace=True)
pop2016['cod_ibge_dv'] = (pop2016['cod_uf']*100000+pop2016['cod_mun']).astype(int)
pop2016['cod_ibge'] = (pop2016['cod_ibge_dv']/10).astype(int)
pop2016 = pop2016[['mun_uf','uf','cod_ibge_dv','cod_ibge','pop_2016']]
pop2016.to_pickle(data_folder+picklename)
pop2016.head()
```

    pop2016.xls 838.0 KB
    




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
      <th>mun_uf</th>
      <th>uf</th>
      <th>cod_ibge_dv</th>
      <th>cod_ibge</th>
      <th>pop_2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALTA FLORESTA D'OESTE-RO</td>
      <td>RO</td>
      <td>1100015</td>
      <td>110001</td>
      <td>25506</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ARIQUEMES-RO</td>
      <td>RO</td>
      <td>1100023</td>
      <td>110002</td>
      <td>105896</td>
    </tr>
    <tr>
      <th>2</th>
      <td>CABIXI-RO</td>
      <td>RO</td>
      <td>1100031</td>
      <td>110003</td>
      <td>6289</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CACOAL-RO</td>
      <td>RO</td>
      <td>1100049</td>
      <td>110004</td>
      <td>87877</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CEREJEIRAS-RO</td>
      <td>RO</td>
      <td>1100056</td>
      <td>110005</td>
      <td>17959</td>
    </tr>
  </tbody>
</table>
</div>



### IDHM
source: http://www.atlasbrasil.org.br/2013/pt/consulta/


```python
picklename = 'idhm.pickle'
filename = 'idhm.xls'
if filename not in listdir(download_folder):
    r = requests.get('http://www.atlasbrasil.org.br/2013/system/preconsultas/consultas/86022019cb76cac9f3eddb76b8d4af67.xlsx',
                 headers={'User-agent': 'Mozilla/5.0'})
    with open(download_folder+filename,'wb') as f:
        f.write(r.content)
print(filename,file_size(download_folder+filename))
idhm = pd.read_excel(download_folder+filename, 'Atlas Brasil',
                     names=['cod_ibge_dv','municipio','gini','pop2010','pop_rural','pop_urbana','mortalidade_infantil',
                            'pop_maior_18','idhm2010','idhm_renda','idhm_long','idhm_educ','pop_16_18','fundamental_maior_18',
                            'renda_per_capita', 'renda_per_capita_nao_nula','pobres','vulneraveis'])
idhm = idhm[idhm['municipio'] != 'Brasil']
idhm.to_pickle(data_folder+picklename)
idhm.head()
```

    idhm.xls 570.4 KB
    




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
      <th>cod_ibge_dv</th>
      <th>municipio</th>
      <th>gini</th>
      <th>pop2010</th>
      <th>pop_rural</th>
      <th>pop_urbana</th>
      <th>mortalidade_infantil</th>
      <th>pop_maior_18</th>
      <th>idhm2010</th>
      <th>idhm_renda</th>
      <th>idhm_long</th>
      <th>idhm_educ</th>
      <th>pop_16_18</th>
      <th>fundamental_maior_18</th>
      <th>renda_per_capita</th>
      <th>renda_per_capita_nao_nula</th>
      <th>pobres</th>
      <th>vulneraveis</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>5200050</td>
      <td>Abadia de Goiás</td>
      <td>0.42</td>
      <td>6876</td>
      <td>1795.0</td>
      <td>5081</td>
      <td>13.4</td>
      <td>4751</td>
      <td>0.708</td>
      <td>0.687</td>
      <td>0.830</td>
      <td>0.622</td>
      <td>385</td>
      <td>48.86</td>
      <td>574.96</td>
      <td>576.02</td>
      <td>6.18</td>
      <td>23.27</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3100104</td>
      <td>Abadia dos Dourados</td>
      <td>0.47</td>
      <td>6704</td>
      <td>2515.0</td>
      <td>4189</td>
      <td>14.8</td>
      <td>5046</td>
      <td>0.689</td>
      <td>0.693</td>
      <td>0.839</td>
      <td>0.563</td>
      <td>318</td>
      <td>39.36</td>
      <td>596.18</td>
      <td>601.18</td>
      <td>7.94</td>
      <td>27.40</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5200100</td>
      <td>Abadiânia</td>
      <td>0.43</td>
      <td>15757</td>
      <td>4979.0</td>
      <td>10778</td>
      <td>12.6</td>
      <td>11076</td>
      <td>0.689</td>
      <td>0.671</td>
      <td>0.841</td>
      <td>0.579</td>
      <td>832</td>
      <td>43.27</td>
      <td>519.87</td>
      <td>523.18</td>
      <td>8.45</td>
      <td>31.19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3100203</td>
      <td>Abaeté</td>
      <td>0.54</td>
      <td>22690</td>
      <td>2986.0</td>
      <td>19704</td>
      <td>14.0</td>
      <td>16733</td>
      <td>0.698</td>
      <td>0.720</td>
      <td>0.848</td>
      <td>0.556</td>
      <td>1104</td>
      <td>36.32</td>
      <td>707.24</td>
      <td>707.44</td>
      <td>6.69</td>
      <td>26.65</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1500107</td>
      <td>Abaetetuba</td>
      <td>0.53</td>
      <td>141100</td>
      <td>58102.0</td>
      <td>82998</td>
      <td>19.0</td>
      <td>87585</td>
      <td>0.628</td>
      <td>0.579</td>
      <td>0.798</td>
      <td>0.537</td>
      <td>10122</td>
      <td>43.24</td>
      <td>293.01</td>
      <td>297.38</td>
      <td>38.95</td>
      <td>65.93</td>
    </tr>
  </tbody>
</table>
</div>



## FINBRA
source: http://www.tesouro.fazenda.gov.br/pt/finbra-financas-municipais
			https://siconfi.tesouro.gov.br/siconfi/pages/public/consulta_finbra/finbra_list.jsf 


```python
for year in range(2013,2019):
    filename = 'finbra_'+str(year)+'.zip'
    df = pd.DataFrame([])
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]
    unzip_all(download_folder+filename,temp_dir)
    files = [download_folder+temp_dir+'\\'+x for x in listdir(download_folder+temp_dir) if x.split('.')[-1] != 'pdf']
    for f in files:
        df = pd.read_csv(f, sep=';', encoding = "ISO-8859-1",header=3)
    df['ano'] = year
    df['Valor'] = df['Valor'].str.replace(',','.').astype(float)
    df['Instituição'] = df['Instituição'].str.replace('Prefeitura Municipal de ','').str.upper().str.replace(' - ','-')
    display(df.head())
    df.to_pickle(data_folder+'finbra_'+str(year)+'.pickle')
```

    finbra_2013.zip 8.0 MB
    


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
      <th>Instituição</th>
      <th>Cod.IBGE</th>
      <th>UF</th>
      <th>População</th>
      <th>Coluna</th>
      <th>Conta</th>
      <th>Valor</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SULINA-PR</td>
      <td>4126652</td>
      <td>PR</td>
      <td>3366</td>
      <td>Despesas Empenhadas</td>
      <td>Despesas (Exceto Intra-Orçamentárias)</td>
      <td>13531296.34</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SULINA-PR</td>
      <td>4126652</td>
      <td>PR</td>
      <td>3366</td>
      <td>Despesas Empenhadas</td>
      <td>04 - Administração</td>
      <td>3089348.49</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>2</th>
      <td>SULINA-PR</td>
      <td>4126652</td>
      <td>PR</td>
      <td>3366</td>
      <td>Despesas Empenhadas</td>
      <td>04.122 - Administração Geral</td>
      <td>3089348.49</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SULINA-PR</td>
      <td>4126652</td>
      <td>PR</td>
      <td>3366</td>
      <td>Despesas Empenhadas</td>
      <td>08 - Assistência Social</td>
      <td>589100.32</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SULINA-PR</td>
      <td>4126652</td>
      <td>PR</td>
      <td>3366</td>
      <td>Despesas Empenhadas</td>
      <td>08.241 - Assistência ao Idoso</td>
      <td>8854.34</td>
      <td>2013</td>
    </tr>
  </tbody>
</table>
</div>


    finbra_2014.zip 8.0 MB
    


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
      <th>Instituição</th>
      <th>Cod.IBGE</th>
      <th>UF</th>
      <th>População</th>
      <th>Coluna</th>
      <th>Conta</th>
      <th>Valor</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ALTO FELIZ-RS</td>
      <td>4300570</td>
      <td>RS</td>
      <td>3017</td>
      <td>Despesas Empenhadas</td>
      <td>Despesas (Exceto Intraorçamentárias)</td>
      <td>12933349.02</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>1</th>
      <td>ALTO FELIZ-RS</td>
      <td>4300570</td>
      <td>RS</td>
      <td>3017</td>
      <td>Despesas Empenhadas</td>
      <td>01 - Legislativa</td>
      <td>237312.91</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ALTO FELIZ-RS</td>
      <td>4300570</td>
      <td>RS</td>
      <td>3017</td>
      <td>Despesas Empenhadas</td>
      <td>01.031 - Ação Legislativa</td>
      <td>237312.91</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>3</th>
      <td>ALTO FELIZ-RS</td>
      <td>4300570</td>
      <td>RS</td>
      <td>3017</td>
      <td>Despesas Empenhadas</td>
      <td>04 - Administração</td>
      <td>3451273.92</td>
      <td>2014</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ALTO FELIZ-RS</td>
      <td>4300570</td>
      <td>RS</td>
      <td>3017</td>
      <td>Despesas Empenhadas</td>
      <td>04.121 - Planejamento e Orçamento</td>
      <td>117572.14</td>
      <td>2014</td>
    </tr>
  </tbody>
</table>
</div>


    finbra_2015.zip 8.4 MB
    


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
      <th>Instituição</th>
      <th>Cod.IBGE</th>
      <th>UF</th>
      <th>População</th>
      <th>Coluna</th>
      <th>Conta</th>
      <th>Valor</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>SÃO VALÉRIO DO SUL-RS</td>
      <td>4319737</td>
      <td>RS</td>
      <td>2748</td>
      <td>Despesas Empenhadas</td>
      <td>Despesas (Exceto Intraorçamentárias)</td>
      <td>10644144.82</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>1</th>
      <td>SÃO VALÉRIO DO SUL-RS</td>
      <td>4319737</td>
      <td>RS</td>
      <td>2748</td>
      <td>Despesas Empenhadas</td>
      <td>01 - Legislativa</td>
      <td>401636.50</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>2</th>
      <td>SÃO VALÉRIO DO SUL-RS</td>
      <td>4319737</td>
      <td>RS</td>
      <td>2748</td>
      <td>Despesas Empenhadas</td>
      <td>01.031 - Ação Legislativa</td>
      <td>401636.50</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>3</th>
      <td>SÃO VALÉRIO DO SUL-RS</td>
      <td>4319737</td>
      <td>RS</td>
      <td>2748</td>
      <td>Despesas Empenhadas</td>
      <td>04 - Administração</td>
      <td>4109323.87</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>4</th>
      <td>SÃO VALÉRIO DO SUL-RS</td>
      <td>4319737</td>
      <td>RS</td>
      <td>2748</td>
      <td>Despesas Empenhadas</td>
      <td>04.122 - Administração Geral</td>
      <td>4109323.87</td>
      <td>2015</td>
    </tr>
  </tbody>
</table>
</div>


    finbra_2016.zip 7.8 MB
    


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
      <th>Instituição</th>
      <th>Cod.IBGE</th>
      <th>UF</th>
      <th>População</th>
      <th>Coluna</th>
      <th>Conta</th>
      <th>Valor</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CHIAPETTA-RS</td>
      <td>4305405</td>
      <td>RS</td>
      <td>4061</td>
      <td>Despesas Empenhadas</td>
      <td>Despesas (Exceto Intraorçamentárias)</td>
      <td>15743045.91</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CHIAPETTA-RS</td>
      <td>4305405</td>
      <td>RS</td>
      <td>4061</td>
      <td>Despesas Empenhadas</td>
      <td>01 - Legislativa</td>
      <td>442406.08</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>2</th>
      <td>CHIAPETTA-RS</td>
      <td>4305405</td>
      <td>RS</td>
      <td>4061</td>
      <td>Despesas Empenhadas</td>
      <td>01.031 - Ação Legislativa</td>
      <td>442406.08</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CHIAPETTA-RS</td>
      <td>4305405</td>
      <td>RS</td>
      <td>4061</td>
      <td>Despesas Empenhadas</td>
      <td>02 - Judiciária</td>
      <td>496720.66</td>
      <td>2016</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CHIAPETTA-RS</td>
      <td>4305405</td>
      <td>RS</td>
      <td>4061</td>
      <td>Despesas Empenhadas</td>
      <td>02.061 - Ação Judiciária</td>
      <td>496720.66</td>
      <td>2016</td>
    </tr>
  </tbody>
</table>
</div>


    finbra_2017.zip 8.5 MB
    


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
      <th>Instituição</th>
      <th>Cod.IBGE</th>
      <th>UF</th>
      <th>População</th>
      <th>Coluna</th>
      <th>Conta</th>
      <th>Valor</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>CANUDOS DO VALE-RS</td>
      <td>4304614</td>
      <td>RS</td>
      <td>1823</td>
      <td>Despesas Empenhadas</td>
      <td>Despesas Exceto Intraorçamentárias</td>
      <td>11649256.22</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>1</th>
      <td>CANUDOS DO VALE-RS</td>
      <td>4304614</td>
      <td>RS</td>
      <td>1823</td>
      <td>Despesas Empenhadas</td>
      <td>01 - Legislativa</td>
      <td>491193.96</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>2</th>
      <td>CANUDOS DO VALE-RS</td>
      <td>4304614</td>
      <td>RS</td>
      <td>1823</td>
      <td>Despesas Empenhadas</td>
      <td>01.031 - Ação Legislativa</td>
      <td>491193.96</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>3</th>
      <td>CANUDOS DO VALE-RS</td>
      <td>4304614</td>
      <td>RS</td>
      <td>1823</td>
      <td>Despesas Empenhadas</td>
      <td>04 - Administração</td>
      <td>2708869.64</td>
      <td>2017</td>
    </tr>
    <tr>
      <th>4</th>
      <td>CANUDOS DO VALE-RS</td>
      <td>4304614</td>
      <td>RS</td>
      <td>1823</td>
      <td>Despesas Empenhadas</td>
      <td>04.121 - Planejamento e Orçamento</td>
      <td>680045.59</td>
      <td>2017</td>
    </tr>
  </tbody>
</table>
</div>


    finbra_2018.zip 8.6 MB
    


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
      <th>Instituição</th>
      <th>Cod.IBGE</th>
      <th>UF</th>
      <th>População</th>
      <th>Coluna</th>
      <th>Conta</th>
      <th>Valor</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>BONFINÓPOLIS-GO</td>
      <td>5203559</td>
      <td>GO</td>
      <td>8876</td>
      <td>Despesas Empenhadas</td>
      <td>Despesas Exceto Intraorçamentárias</td>
      <td>22065362.39</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>1</th>
      <td>BONFINÓPOLIS-GO</td>
      <td>5203559</td>
      <td>GO</td>
      <td>8876</td>
      <td>Despesas Empenhadas</td>
      <td>01 - Legislativa</td>
      <td>797417.88</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>2</th>
      <td>BONFINÓPOLIS-GO</td>
      <td>5203559</td>
      <td>GO</td>
      <td>8876</td>
      <td>Despesas Empenhadas</td>
      <td>01.031 - Ação Legislativa</td>
      <td>797417.88</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>3</th>
      <td>BONFINÓPOLIS-GO</td>
      <td>5203559</td>
      <td>GO</td>
      <td>8876</td>
      <td>Despesas Empenhadas</td>
      <td>04 - Administração</td>
      <td>2282545.92</td>
      <td>2018</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BONFINÓPOLIS-GO</td>
      <td>5203559</td>
      <td>GO</td>
      <td>8876</td>
      <td>Despesas Empenhadas</td>
      <td>04.122 - Administração Geral</td>
      <td>1620112.75</td>
      <td>2018</td>
    </tr>
  </tbody>
</table>
</div>



```python
pd.DataFrame(df[df['Conta'] == '04.131 - Comunicação Social'].groupby(['UF','Instituição'])['Valor','População'].sum())
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
      <th></th>
      <th>Valor</th>
      <th>População</th>
    </tr>
    <tr>
      <th>UF</th>
      <th>Instituição</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="3" valign="top">AC</th>
      <th>CAPIXABA-AC</th>
      <td>62595.00</td>
      <td>32460</td>
    </tr>
    <tr>
      <th>SENADOR GUIOMARD-AC</th>
      <td>24590.40</td>
      <td>64107</td>
    </tr>
    <tr>
      <th>XAPURI-AC</th>
      <td>835617.36</td>
      <td>71576</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">AL</th>
      <th>ARAPIRACA-AL</th>
      <td>3458554.49</td>
      <td>1163355</td>
    </tr>
    <tr>
      <th>MACEIÓ-AL</th>
      <td>56423755.86</td>
      <td>4086836</td>
    </tr>
    <tr>
      <th>PALMEIRA DOS ÍNDIOS-AL</th>
      <td>20025.00</td>
      <td>222147</td>
    </tr>
    <tr>
      <th>RIO LARGO-AL</th>
      <td>608180.40</td>
      <td>227064</td>
    </tr>
    <tr>
      <th rowspan="4" valign="top">AM</th>
      <th>ITACOATIARA-AM</th>
      <td>540095.35</td>
      <td>394012</td>
    </tr>
    <tr>
      <th>JUTAÍ-AM</th>
      <td>94320.00</td>
      <td>48600</td>
    </tr>
    <tr>
      <th>MANAUS-AM</th>
      <td>272183124.09</td>
      <td>8377564</td>
    </tr>
    <tr>
      <th>PARINTINS-AM</th>
      <td>2544200.13</td>
      <td>338148</td>
    </tr>
    <tr>
      <th>AP</th>
      <th>MACAPÁ-AP</th>
      <td>23700737.18</td>
      <td>2327475</td>
    </tr>
    <tr>
      <th rowspan="18" valign="top">BA</th>
      <th>ALAGOINHAS-BA</th>
      <td>5109769.83</td>
      <td>466086</td>
    </tr>
    <tr>
      <th>ANDARAÍ-BA</th>
      <td>2538.00</td>
      <td>41196</td>
    </tr>
    <tr>
      <th>ANDORINHA-BA</th>
      <td>450754.41</td>
      <td>62204</td>
    </tr>
    <tr>
      <th>APUAREMA-BA</th>
      <td>805979.34</td>
      <td>30980</td>
    </tr>
    <tr>
      <th>ARACATU-BA</th>
      <td>107196.00</td>
      <td>56092</td>
    </tr>
    <tr>
      <th>ARACI-BA</th>
      <td>8428891.13</td>
      <td>278185</td>
    </tr>
    <tr>
      <th>ARAÇAS-BA</th>
      <td>3198444.04</td>
      <td>62475</td>
    </tr>
    <tr>
      <th>BARRA-BA</th>
      <td>413205.06</td>
      <td>218252</td>
    </tr>
    <tr>
      <th>BARREIRAS-BA</th>
      <td>97500.00</td>
      <td>622076</td>
    </tr>
    <tr>
      <th>BOM JESUS DA LAPA-BA</th>
      <td>1112370.00</td>
      <td>280360</td>
    </tr>
    <tr>
      <th>BOQUIRA-BA</th>
      <td>225134.43</td>
      <td>112240</td>
    </tr>
    <tr>
      <th>BOTUPORÃ-BA</th>
      <td>88115.16</td>
      <td>43828</td>
    </tr>
    <tr>
      <th>BUERAREMA-BA</th>
      <td>110500.80</td>
      <td>57807</td>
    </tr>
    <tr>
      <th>CACHOEIRA-BA</th>
      <td>81402.18</td>
      <td>105039</td>
    </tr>
    <tr>
      <th>CACULÉ-BA</th>
      <td>504598.77</td>
      <td>94740</td>
    </tr>
    <tr>
      <th>CAETITÉ-BA</th>
      <td>221130.00</td>
      <td>210784</td>
    </tr>
    <tr>
      <th>CAFARNAUM-BA</th>
      <td>8280.00</td>
      <td>56751</td>
    </tr>
    <tr>
      <th>CAMACAN-BA</th>
      <td>29700.00</td>
      <td>99771</td>
    </tr>
    <tr>
      <th>...</th>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th rowspan="25" valign="top">SP</th>
      <th>SERRANA-SP</th>
      <td>545196.57</td>
      <td>216465</td>
    </tr>
    <tr>
      <th>SOCORRO-SP</th>
      <td>1120493.58</td>
      <td>199480</td>
    </tr>
    <tr>
      <th>SÃO BERNARDO DO CAMPO-SP</th>
      <td>55828763.92</td>
      <td>3288968</td>
    </tr>
    <tr>
      <th>SÃO CAETANO DO SUL-SP</th>
      <td>6541297.04</td>
      <td>794125</td>
    </tr>
    <tr>
      <th>SÃO CARLOS-SP</th>
      <td>11995735.87</td>
      <td>1218825</td>
    </tr>
    <tr>
      <th>SÃO JOSÉ DO RIO PARDO-SP</th>
      <td>461490.79</td>
      <td>272815</td>
    </tr>
    <tr>
      <th>SÃO JOSÉ DO RIO PRETO-SP</th>
      <td>19943615.72</td>
      <td>2233245</td>
    </tr>
    <tr>
      <th>SÃO JOÃO DA BOA VISTA-SP</th>
      <td>2647498.82</td>
      <td>447820</td>
    </tr>
    <tr>
      <th>SÃO PAULO-SP</th>
      <td>9735238.53</td>
      <td>48152700</td>
    </tr>
    <tr>
      <th>SÃO PEDRO-SP</th>
      <td>636077.49</td>
      <td>103785</td>
    </tr>
    <tr>
      <th>SÃO SEBASTIÃO-SP</th>
      <td>2250753.25</td>
      <td>421470</td>
    </tr>
    <tr>
      <th>SÃO VICENTE-SP</th>
      <td>8741304.64</td>
      <td>1789945</td>
    </tr>
    <tr>
      <th>TABAPUÃ-SP</th>
      <td>193708.83</td>
      <td>48712</td>
    </tr>
    <tr>
      <th>TAIAÇU-SP</th>
      <td>138459.78</td>
      <td>18690</td>
    </tr>
    <tr>
      <th>TAIÚVA-SP</th>
      <td>262547.77</td>
      <td>28020</td>
    </tr>
    <tr>
      <th>TAMBAÚ-SP</th>
      <td>461895.65</td>
      <td>92964</td>
    </tr>
    <tr>
      <th>TAPIRAÍ-SP</th>
      <td>345554.98</td>
      <td>40050</td>
    </tr>
    <tr>
      <th>TAUBATÉ-SP</th>
      <td>13403674.77</td>
      <td>1525870</td>
    </tr>
    <tr>
      <th>UBATUBA-SP</th>
      <td>1908491.26</td>
      <td>436820</td>
    </tr>
    <tr>
      <th>VARGEM GRANDE PAULISTA-SP</th>
      <td>1115758.94</td>
      <td>247710</td>
    </tr>
    <tr>
      <th>VINHEDO-SP</th>
      <td>1128750.37</td>
      <td>369275</td>
    </tr>
    <tr>
      <th>VOTORANTIM-SP</th>
      <td>3092869.97</td>
      <td>594290</td>
    </tr>
    <tr>
      <th>VOTUPORANGA-SP</th>
      <td>2137292.58</td>
      <td>368128</td>
    </tr>
    <tr>
      <th>VÁRZEA PAULISTA-SP</th>
      <td>4457546.02</td>
      <td>588860</td>
    </tr>
    <tr>
      <th>ÁGUAS DE LINDÓIA-SP</th>
      <td>532184.16</td>
      <td>55236</td>
    </tr>
    <tr>
      <th rowspan="5" valign="top">TO</th>
      <th>JAÚ DO TOCANTINS-TO</th>
      <td>10500.00</td>
      <td>15164</td>
    </tr>
    <tr>
      <th>MIRANORTE-TO</th>
      <td>27000.00</td>
      <td>40089</td>
    </tr>
    <tr>
      <th>NATIVIDADE-TO</th>
      <td>894.48</td>
      <td>27903</td>
    </tr>
    <tr>
      <th>NOVA ROSALÂNDIA-TO</th>
      <td>15385.38</td>
      <td>16636</td>
    </tr>
    <tr>
      <th>PORTO NACIONAL-TO</th>
      <td>630396.00</td>
      <td>157530</td>
    </tr>
  </tbody>
</table>
<p>1024 rows × 2 columns</p>
</div>




```python
publicidade = df[(df['Conta'] == '04.131 - Comunicação Social') & (df['Coluna'] == 'Despesas Empenhadas')][['Instituição','Valor','População']].sort_values('Valor', ascending=False)
publicidade.set_index('Instituição', inplace=True)
publicidade[:30]['Valor'].plot(kind='bar', figsize=(15,10))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x193c01847f0>




![png](1-data_collection_files/1-data_collection_14_1.png)



```python
publicidade['pub_percapita'] = publicidade['Valor']/publicidade['População']
```


```python
publicidade[publicidade['População'] > 50000].sort_values('pub_percapita', ascending=False)['pub_percapita'][:30].plot(kind='bar', figsize=(15,10))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1935f32e588>




![png](1-data_collection_files/1-data_collection_16_1.png)


### TCE MG
source: https://dadosabertos.tce.mg.gov.br/view/xhtml/paginas/downloadArquivos.xhtml#


```python

```

## TCM BA
source: http://www.tcm.ba.gov.br/portal-da-cidadania/publicidade/


```python
for year in range(2013,2019):
    filename = 'tce_ba_'+str(year)+'.csv'
    if filename not in listdir(download_folder):
        wget.download('https://www.tce.ba.gov.br/images/transparencia/despesa-detalhada/Despesa_detalhada_Exercicio_'+str(year)+'.csv', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    df = pd.read_csv(download_folder+filename, sep=';', encoding = "utf-8")
    display(df.head())
    df.to_pickle(data_folder+'tce_ba_'+str(year)+'.pickle')


```

    tce_ba_2013.csv 1.6 MB
    


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
      <th>Unidade Orçamentária</th>
      <th>Modalidade de Licitação</th>
      <th>Ano do Instrumento</th>
      <th>Número do Instrumento</th>
      <th>Nome do Credor</th>
      <th>CNPJ/CPF do Credor</th>
      <th>Número do Empenho</th>
      <th>Data do Empenho</th>
      <th>Número do Processo do Pagamento</th>
      <th>Número do Pagamento Principal</th>
      <th>Data do Pagamento Principal</th>
      <th>Forma de Pagamento</th>
      <th>Regularização</th>
      <th>Resto a Pagar</th>
      <th>Situação da Liquidação</th>
      <th>Mês</th>
      <th>Função</th>
      <th>Categoria Econômica</th>
      <th>Grupo de Despesa</th>
      <th>Modalidade de Aplicação</th>
      <th>Subelemento de Despesa</th>
      <th>Unidade Gestora</th>
      <th>Histórico da Liquidação</th>
      <th>Secretaria/Órgão</th>
      <th>Valor do Empenho</th>
      <th>Retenções</th>
      <th>Pagamento com Retenções</th>
      <th>Pagamento Líquido ao Credor</th>
      <th>Unnamed: 28</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>09 - Inexigibilidade - art. 60</td>
      <td>0</td>
      <td>nan</td>
      <td>EMPRESA GRAFICA DA BAHIA</td>
      <td>15.257.819/0001-06</td>
      <td>210100011300007478</td>
      <td>18/07/2013</td>
      <td>727</td>
      <td>210100011300012059</td>
      <td>01/08/2013</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>8</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>86 - PUBLICIDADE LEGAL: 2457,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>Pagto proc 1030/13</td>
      <td>2020 - Comunicação Legal</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>60.000,00</td>
      <td>NaN</td>
      <td>2.457,00</td>
      <td>2.457,00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Ana Paula Costa Teixeira</td>
      <td>943.051.375-34</td>
      <td>210100011300008024</td>
      <td>31/07/2013</td>
      <td>780</td>
      <td>210100011300012040</td>
      <td>01/08/2013</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>8</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>02 - AUXÍLIO FUNERAL: 2034,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>Pagto proc 1029/13</td>
      <td>2009 - Encargos com Benefícios Especiais</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>2.034,00</td>
      <td>NaN</td>
      <td>2.034,00</td>
      <td>2.034,00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>002301 - Centro de Estudos e Desenvolvimento d...</td>
      <td>01 - Concorrência Pública</td>
      <td>0</td>
      <td>nan</td>
      <td>AVANSYS TECNOLOGIA LTDA</td>
      <td>04.181.950/0001-10</td>
      <td>230100011300002453</td>
      <td>18/06/2013</td>
      <td>5</td>
      <td>230100011300007180</td>
      <td>01/08/2013</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>8</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>38 - SERV INFO Ñ CARCT SUBST SERVIDOR: 2782,50;</td>
      <td>0001 - Centro de Estudos e Desenvolvimento de ...</td>
      <td>Prestação de serviços especializados de progra...</td>
      <td>7380 - Implementação de Solução Tecnológica de...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>35.000,00</td>
      <td>IRRF ESTADUAL: 41,74; ISS: 139,13;</td>
      <td>2.782,50</td>
      <td>2.601,63</td>
    </tr>
    <tr>
      <th>3</th>
      <td>002301 - Centro de Estudos e Desenvolvimento d...</td>
      <td>01 - Concorrência Pública</td>
      <td>0</td>
      <td>nan</td>
      <td>AVANSYS TECNOLOGIA LTDA</td>
      <td>04.181.950/0001-10</td>
      <td>230100011300002453</td>
      <td>18/06/2013</td>
      <td>5</td>
      <td>230100011300007210</td>
      <td>01/08/2013</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>8</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>38 - SERV INFO Ñ CARCT SUBST SERVIDOR: 795,00;</td>
      <td>0001 - Centro de Estudos e Desenvolvimento de ...</td>
      <td>Prestação de serviços especializados de progra...</td>
      <td>7380 - Implementação de Solução Tecnológica de...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>35.000,00</td>
      <td>IRRF ESTADUAL: 11,93; ISS: 39,75;</td>
      <td>795,00</td>
      <td>743,32</td>
    </tr>
    <tr>
      <th>4</th>
      <td>002301 - Centro de Estudos e Desenvolvimento d...</td>
      <td>01 - Concorrência Pública</td>
      <td>0</td>
      <td>nan</td>
      <td>AVANSYS TECNOLOGIA LTDA</td>
      <td>04.181.950/0001-10</td>
      <td>230100011300002453</td>
      <td>18/06/2013</td>
      <td>5</td>
      <td>230100011300007245</td>
      <td>01/08/2013</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>8</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>38 - SERV INFO Ñ CARCT SUBST SERVIDOR: 6757,50;</td>
      <td>0001 - Centro de Estudos e Desenvolvimento de ...</td>
      <td>Prestação de serviços especializados de progra...</td>
      <td>7380 - Implementação de Solução Tecnológica de...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>35.000,00</td>
      <td>IRRF ESTADUAL: 101,36; ISS: 337,88;</td>
      <td>6.757,50</td>
      <td>6.318,26</td>
    </tr>
  </tbody>
</table>
</div>


    tce_ba_2014.csv 1.7 MB
    


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
      <th>Unidade Orçamentária</th>
      <th>Modalidade de Licitação</th>
      <th>Ano do Instrumento</th>
      <th>Número do Instrumento</th>
      <th>Nome do Credor</th>
      <th>CNPJ/CPF do Credor</th>
      <th>Número do Empenho</th>
      <th>Data do Empenho</th>
      <th>Número do Processo do Pagamento</th>
      <th>Número do Pagamento Principal</th>
      <th>Data do Pagamento Principal</th>
      <th>Forma de Pagamento</th>
      <th>Regularização</th>
      <th>Resto a Pagar</th>
      <th>Situação da Liquidação</th>
      <th>Mês</th>
      <th>Função</th>
      <th>Categoria Econômica</th>
      <th>Grupo de Despesa</th>
      <th>Modalidade de Aplicação</th>
      <th>Subelemento de Despesa</th>
      <th>Unidade Gestora</th>
      <th>Histórico da Liquidação</th>
      <th>Secretaria/Órgão</th>
      <th>Valor do Empenho</th>
      <th>Retenções</th>
      <th>Pagamento com Retenções</th>
      <th>Pagamento Líquido ao Credor</th>
      <th>Unnamed: 28</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>002301 - Centro de Estudos e Desenvolvimento d...</td>
      <td>01 - Concorrência Pública</td>
      <td>2014</td>
      <td>230100011400000384.00</td>
      <td>AVANSYS TECNOLOGIA LTDA</td>
      <td>04.181.950/0001-10</td>
      <td>230100011400001009</td>
      <td>03/03/2014</td>
      <td>101</td>
      <td>230100011400008575</td>
      <td>15/09/2014</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>9</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>38 - SERV INFO Ñ CARCT SUBST SERVIDOR: 7137,18;</td>
      <td>0001 - Centro de Estudos e Desenvolvimento de ...</td>
      <td>Prestação de serviços especializados de progra...</td>
      <td>7380 - Implementação de Solução Tecnológica de...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>150.000,00</td>
      <td>IRRF ESTADUAL: 107,06; ISS: 356,86;</td>
      <td>7.137,18</td>
      <td>6.673,26</td>
    </tr>
    <tr>
      <th>1</th>
      <td>002301 - Centro de Estudos e Desenvolvimento d...</td>
      <td>01 - Concorrência Pública</td>
      <td>2014</td>
      <td>230100011400000384.00</td>
      <td>AVANSYS TECNOLOGIA LTDA</td>
      <td>04.181.950/0001-10</td>
      <td>230100011400001009</td>
      <td>03/03/2014</td>
      <td>101</td>
      <td>230100011400008605</td>
      <td>15/09/2014</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>9</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>38 - SERV INFO Ñ CARCT SUBST SERVIDOR: 3436,42;</td>
      <td>0001 - Centro de Estudos e Desenvolvimento de ...</td>
      <td>Prestação de serviços especializados de progra...</td>
      <td>7380 - Implementação de Solução Tecnológica de...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>150.000,00</td>
      <td>IRRF ESTADUAL: 51,55; ISS: 171,82;</td>
      <td>3.436,42</td>
      <td>3.213,05</td>
    </tr>
    <tr>
      <th>2</th>
      <td>002301 - Centro de Estudos e Desenvolvimento d...</td>
      <td>01 - Concorrência Pública</td>
      <td>2014</td>
      <td>230100011400000384.00</td>
      <td>AVANSYS TECNOLOGIA LTDA</td>
      <td>04.181.950/0001-10</td>
      <td>230100011400001009</td>
      <td>03/03/2014</td>
      <td>101</td>
      <td>230100011400008631</td>
      <td>15/09/2014</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>9</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>38 - SERV INFO Ñ CARCT SUBST SERVIDOR: 2960,61;</td>
      <td>0001 - Centro de Estudos e Desenvolvimento de ...</td>
      <td>Prestação de serviços especializados de progra...</td>
      <td>7380 - Implementação de Solução Tecnológica de...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>150.000,00</td>
      <td>IRRF ESTADUAL: 44,41; ISS: 148,03;</td>
      <td>2.960,61</td>
      <td>2.768,17</td>
    </tr>
    <tr>
      <th>3</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>11 - Pregão Presencial</td>
      <td>0</td>
      <td>nan</td>
      <td>Turismo Pinheiro LTDA</td>
      <td>14.706.238/0001-41</td>
      <td>210100011400002761</td>
      <td>31/03/2014</td>
      <td>55</td>
      <td>210100011400015683</td>
      <td>15/09/2014</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>9</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - PASSAGENS PAÍS TAX EMB SEGUROS: 907,22;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9293/14 FAT 204949</td>
      <td>2137 - Auditoria e Fiscalização Contábil, Fina...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>50.000,00</td>
      <td>NaN</td>
      <td>907,22</td>
      <td>907,22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Ana Amélia Ferreira</td>
      <td>163.257.535-34</td>
      <td>210100011400007526</td>
      <td>11/09/2014</td>
      <td>608</td>
      <td>210100011400015594</td>
      <td>15/09/2014</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>9</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>88 - ADIANTAMENTO PASSAGENS LOCOMOÇÃO: 4000,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9265/2014 RA 72/2014</td>
      <td>2137 - Auditoria e Fiscalização Contábil, Fina...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>4.000,00</td>
      <td>NaN</td>
      <td>4.000,00</td>
      <td>4.000,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_ba_2015.csv 1.4 MB
    


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
      <th>Unidade Orçamentária</th>
      <th>Modalidade de Licitação</th>
      <th>Ano do Instrumento</th>
      <th>Número do Instrumento</th>
      <th>Nome do Credor</th>
      <th>CNPJ/CPF do Credor</th>
      <th>Número do Empenho</th>
      <th>Data do Empenho</th>
      <th>Número do Processo do Pagamento</th>
      <th>Número do Pagamento Principal</th>
      <th>Data do Pagamento Principal</th>
      <th>Forma de Pagamento</th>
      <th>Regularização</th>
      <th>Resto a Pagar</th>
      <th>Situação da Liquidação</th>
      <th>Mês</th>
      <th>Função</th>
      <th>Categoria Econômica</th>
      <th>Grupo de Despesa</th>
      <th>Modalidade de Aplicação</th>
      <th>Subelemento de Despesa</th>
      <th>Unidade Gestora</th>
      <th>Histórico da Liquidação</th>
      <th>Secretaria/Órgão</th>
      <th>Valor do Empenho</th>
      <th>Retenções</th>
      <th>Pagamento com Retenções</th>
      <th>Pagamento Líquido ao Credor</th>
      <th>Unnamed: 28</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Cristina Maria Moura Ferreira</td>
      <td>345.775.725-91</td>
      <td>210100011500012516</td>
      <td>14/10/2015</td>
      <td>1</td>
      <td>210100011500024000.00</td>
      <td>19/10/2015</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>10</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - DIÁRIAS NO PAÍS - PESSOAL CIVIL: 784,70;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>Pgto Proc 9088/2015</td>
      <td>6987 - Gestão do Controle Externo das Contas P...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>784,70</td>
      <td>NaN</td>
      <td>784,70</td>
      <td>784,70</td>
    </tr>
    <tr>
      <th>1</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Ivonete Dionízio De Lima</td>
      <td>376.988.695-04</td>
      <td>210100011500012591</td>
      <td>14/10/2015</td>
      <td>190464</td>
      <td>210100011500024064.00</td>
      <td>19/10/2015</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>10</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - DIÁRIAS NO PAÍS - PESSOAL CIVIL: 1062,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 8312/2015</td>
      <td>5011 - Capacitação Técnico-profissional do Tri...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>1.062,00</td>
      <td>NaN</td>
      <td>1.062,00</td>
      <td>1.062,00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Itiel Souza Santos</td>
      <td>181.780.145-72</td>
      <td>210100011500012664</td>
      <td>15/10/2015</td>
      <td>1</td>
      <td>210100011500024000.00</td>
      <td>19/10/2015</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>10</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - DIÁRIAS NO PAÍS - PESSOAL CIVIL: 756,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>Pgto Proc 9126/2015</td>
      <td>6987 - Gestão do Controle Externo das Contas P...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>756,00</td>
      <td>NaN</td>
      <td>756,00</td>
      <td>756,00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Ivonete Dionízio De Lima</td>
      <td>376.988.695-04</td>
      <td>210100011500011005</td>
      <td>21/09/2015</td>
      <td>190464</td>
      <td>210100011500024064.00</td>
      <td>19/10/2015</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>10</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - DIÁRIAS NO PAÍS - PESSOAL CIVIL: 1593,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 8312/2015</td>
      <td>5011 - Capacitação Técnico-profissional do Tri...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>1.593,00</td>
      <td>NaN</td>
      <td>1.593,00</td>
      <td>1.593,00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>11 - Pregão Presencial</td>
      <td>0</td>
      <td>nan</td>
      <td>IMAGEM EQUIPAMENTOS PARA ESCRITORIO LTDA</td>
      <td>10.389.877/0001-70</td>
      <td>210100011500010238</td>
      <td>01/09/2015</td>
      <td>680</td>
      <td>210100011500023936.00</td>
      <td>19/10/2015</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Não</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>10</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>20 - SERV DIV IMP ENC REP GRÁFICA MICROFIL: 49...</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9082/2015 NF 2503</td>
      <td>2000 - Manutenção de Serviços Técnico e Admini...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>15.000,00</td>
      <td>ISS: 248,79;</td>
      <td>4.975,84</td>
      <td>4.727,05</td>
    </tr>
  </tbody>
</table>
</div>


    tce_ba_2016.csv 1.3 MB
    


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
      <th>Unidade Orçamentária</th>
      <th>Modalidade de Licitação</th>
      <th>Ano do Instrumento</th>
      <th>Número do Instrumento</th>
      <th>Nome do Credor</th>
      <th>CNPJ/CPF do Credor</th>
      <th>Número do Empenho</th>
      <th>Data do Empenho</th>
      <th>Número do Processo do Pagamento</th>
      <th>Número do Pagamento Principal</th>
      <th>Data do Pagamento Principal</th>
      <th>Forma de Pagamento</th>
      <th>Regularização</th>
      <th>Resto a Pagar</th>
      <th>Situação da Liquidação</th>
      <th>Mês</th>
      <th>Função</th>
      <th>Categoria Econômica</th>
      <th>Grupo de Despesa</th>
      <th>Modalidade de Aplicação</th>
      <th>Subelemento de Despesa</th>
      <th>Unidade Gestora</th>
      <th>Histórico da Liquidação</th>
      <th>Secretaria/Órgão</th>
      <th>Valor do Empenho</th>
      <th>Retenções</th>
      <th>Pagamento com Retenções</th>
      <th>Pagamento Líquido ao Credor</th>
      <th>Unnamed: 28</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Tribunal de Contas do Estado</td>
      <td>14.674.303/0001-02</td>
      <td>210100011600000119</td>
      <td>04/01/2016</td>
      <td>1</td>
      <td>210100011600002336.00</td>
      <td>29/02/2016</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>2</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>1 - Pessoal e Encargos Sociais</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - INDENIZ TRABALHISTA SERVIDORES: 1462829,81;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 842/2016</td>
      <td>2001 - Administração de Pessoal e Encargos</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>5.000.000,00</td>
      <td>NaN</td>
      <td>1.462.829,81</td>
      <td>1.462.829,81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Tribunal de Contas do Estado</td>
      <td>14.674.303/0001-02</td>
      <td>210100011600000070</td>
      <td>04/01/2016</td>
      <td>1</td>
      <td>210100011600002464.00</td>
      <td>29/02/2016</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>2</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>1 - Pessoal e Encargos Sociais</td>
      <td>90 - Aplicações Diretas</td>
      <td>08 - DESP VARIÁV PES CIVIL ESTATUTÁRIO: 9005,27;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 907/2016</td>
      <td>2001 - Administração de Pessoal e Encargos</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>50.000,00</td>
      <td>NaN</td>
      <td>9.005,27</td>
      <td>9.005,27</td>
    </tr>
    <tr>
      <th>2</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Fundo Financeiro da Previdência Social dos Ser...</td>
      <td>09.317.177/0001-90</td>
      <td>210100011600000038</td>
      <td>04/01/2016</td>
      <td>1</td>
      <td>210100011600002240.00</td>
      <td>29/02/2016</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>2</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>1 - Pessoal e Encargos Sociais</td>
      <td>91 - Aplicação Direta Decorrente de Operação e...</td>
      <td>01 - FUNPREV - PESSOAL CIVIL: 1495996,04;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 931/2016 FUNPREV</td>
      <td>6986 - Administração de Pessoal e Encargos do ...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>10.200.000,00</td>
      <td>NaN</td>
      <td>1.495.996,04</td>
      <td>1.495.996,04</td>
    </tr>
    <tr>
      <th>3</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Fundo Financeiro da Previdência Social dos Ser...</td>
      <td>09.317.177/0001-90</td>
      <td>210100011600000089</td>
      <td>04/01/2016</td>
      <td>1</td>
      <td>210100011600002240.00</td>
      <td>29/02/2016</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>2</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>1 - Pessoal e Encargos Sociais</td>
      <td>91 - Aplicação Direta Decorrente de Operação e...</td>
      <td>01 - FUNPREV - PESSOAL CIVIL: 352957,50;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 931/2016 FUNPREV</td>
      <td>2001 - Administração de Pessoal e Encargos</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>2.280.000,00</td>
      <td>NaN</td>
      <td>352.957,50</td>
      <td>352.957,50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Tribunal de Contas do Estado</td>
      <td>14.674.303/0001-02</td>
      <td>210100011600000021</td>
      <td>04/01/2016</td>
      <td>1</td>
      <td>210100011600002464.00</td>
      <td>29/02/2016</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>2</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>1 - Pessoal e Encargos Sociais</td>
      <td>90 - Aplicações Diretas</td>
      <td>08 - DESP VARIÁV PES CIVIL ESTATUTÁRIO: 8804,32;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 907/2016</td>
      <td>6986 - Administração de Pessoal e Encargos do ...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>60.000,00</td>
      <td>NaN</td>
      <td>8.804,32</td>
      <td>8.804,32</td>
    </tr>
  </tbody>
</table>
</div>


    tce_ba_2017.csv 1.6 MB
    


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
      <th>Unidade Orçamentária</th>
      <th>Modalidade de Licitação</th>
      <th>Ano do Instrumento</th>
      <th>Número do Instrumento</th>
      <th>Nome do Credor</th>
      <th>CNPJ/CPF do Credor</th>
      <th>Número do Empenho</th>
      <th>Data do Empenho</th>
      <th>Número do Processo do Pagamento</th>
      <th>Número do Pagamento Principal</th>
      <th>Data do Pagamento Principal</th>
      <th>Forma de Pagamento</th>
      <th>Regularização</th>
      <th>Resto a Pagar</th>
      <th>Situação da Liquidação</th>
      <th>Mês</th>
      <th>Função</th>
      <th>Categoria Econômica</th>
      <th>Grupo de Despesa</th>
      <th>Modalidade de Aplicação</th>
      <th>Subelemento de Despesa</th>
      <th>Unidade Gestora</th>
      <th>Histórico da Liquidação</th>
      <th>Secretaria/Órgão</th>
      <th>Valor do Empenho</th>
      <th>Retenções</th>
      <th>Pagamento com Retenções</th>
      <th>Pagamento Líquido ao Credor</th>
      <th>Unnamed: 28</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>11 - Pregão Presencial</td>
      <td>2016</td>
      <td>210100011600000416.00</td>
      <td>TELEMAR NORTE LESTE S/A_( RIO DE JANEIRO )</td>
      <td>33.000.118/0001-79</td>
      <td>210100011600010191</td>
      <td>27/09/2016</td>
      <td>2</td>
      <td>210100011700000448.00</td>
      <td>31/01/2017</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>05 - SERV COMUNIC TELECOMUNICAÇÃO: 849,76;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9255/2016 NF 127100 E 102 TELEMAR</td>
      <td>2018 - Encargos com Concessionárias de Serviço...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>3.500,00</td>
      <td>NaN</td>
      <td>849,76</td>
      <td>849,76</td>
    </tr>
    <tr>
      <th>1</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>09 - Inexigibilidade - art. 60</td>
      <td>2016</td>
      <td>210100011600004832.00</td>
      <td>YANNE CURSOS LTDA</td>
      <td>19.033.824/0001-96</td>
      <td>210100011600013504</td>
      <td>23/11/2016</td>
      <td>2</td>
      <td>210100011700000480.00</td>
      <td>31/01/2017</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>11 - APERF TREINAM CAPACIT PESSOAL: 3900,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 8396/2016 NF 328 YANNE</td>
      <td>5011 - Capacitação Técnico-profissional do Tri...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>3.900,00</td>
      <td>NaN</td>
      <td>3.900,00</td>
      <td>3.900,00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>09 - Inexigibilidade - art. 60</td>
      <td>2016</td>
      <td>210100011600001216.00</td>
      <td>SERVICO FEDERAL DE PROCESSAMENTO DE DADOS</td>
      <td>33.683.111/0006-03</td>
      <td>210100011600002545</td>
      <td>01/02/2016</td>
      <td>1</td>
      <td>210100011700000448.00</td>
      <td>31/01/2017</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>05 - SERV COMUNIC TELECOMUNICAÇÃO: 500,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9254/2016 NF 1278 SERPRO</td>
      <td>2002 - Manutenção de Serviços de Informática</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>4.800,00</td>
      <td>ISS: 25,00;</td>
      <td>500,00</td>
      <td>475,00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>INSTITUTO NACIONAL DO SEGURO SOCIAL</td>
      <td>29.979.036/0001-40</td>
      <td>210100011700000580</td>
      <td>02/01/2017</td>
      <td>1</td>
      <td>210100011700000416.00</td>
      <td>31/01/2017</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>01 - INSS CONTRIB INDV COOP TRAB E OUTROS: 541...</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 553/2017</td>
      <td>2000 - Manutenção de Serviços Técnico e Admini...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>5.000,00</td>
      <td>NaN</td>
      <td>541,70</td>
      <td>541,70</td>
    </tr>
    <tr>
      <th>4</th>
      <td>002101 - Diretoria Administrativa</td>
      <td>11 - Pregão Presencial</td>
      <td>2016</td>
      <td>210100011600000416.00</td>
      <td>TELEMAR NORTE LESTE S/A_( RIO DE JANEIRO )</td>
      <td>33.000.118/0001-79</td>
      <td>210100011600006656</td>
      <td>17/06/2016</td>
      <td>2</td>
      <td>210100011700000512.00</td>
      <td>31/01/2017</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>05 - SERV COMUNIC TELECOMUNICAÇÃO: 8879,94;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9260/2016 NF 127099 TELEMAR</td>
      <td>2018 - Encargos com Concessionárias de Serviço...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>70.000,00</td>
      <td>NaN</td>
      <td>8.879,94</td>
      <td>8.879,94</td>
    </tr>
  </tbody>
</table>
</div>


    tce_ba_2018.csv 850.6 KB
    


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
      <th>Unidade Orçamentária</th>
      <th>Modalidade de Licitação</th>
      <th>Ano do Instrumento</th>
      <th>Número do Instrumento</th>
      <th>Nome do Credor</th>
      <th>CNPJ/CPF do Credor</th>
      <th>Número do Empenho</th>
      <th>Data do Empenho</th>
      <th>Número do Processo do Pagamento</th>
      <th>Número do Pagamento Principal</th>
      <th>Data do Pagamento Principal</th>
      <th>Forma de Pagamento</th>
      <th>Regularização</th>
      <th>Resto a Pagar</th>
      <th>Situação da Liquidação</th>
      <th>Mês</th>
      <th>Função</th>
      <th>Categoria Econômica</th>
      <th>Grupo de Despesa</th>
      <th>Modalidade de Aplicação</th>
      <th>Subelemento de Despesa</th>
      <th>Unidade Gestora</th>
      <th>Histórico da Liquidação</th>
      <th>Secretaria/Órgão</th>
      <th>Valor do Empenho</th>
      <th>Retenções</th>
      <th>Pagamento com Retenções</th>
      <th>Pagamento Líquido ao Credor</th>
      <th>Unnamed: 28</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>06 - Pregão Eletrônico</td>
      <td>2017</td>
      <td>210100011700003584.00</td>
      <td>POSITIVA EMPREENDIMENTOS E SERVICOS LTDA - ME</td>
      <td>17.689.476/0001-84</td>
      <td>210100011700015960</td>
      <td>28/12/2017</td>
      <td>3</td>
      <td>210100011800000864.00</td>
      <td>31/01/2018</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>04 - TERCEIRIZAÇÃO DE MÃO-DE-OBRA: 274,11;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9585/2017 NF 411 POSITIVA</td>
      <td>2000 - Manutenção de Serviços Técnico e Admini...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>274,11</td>
      <td>PROVISÃO DE ENCARGOS TRABALHISTAS DA LEI ANTIC...</td>
      <td>274,11</td>
      <td>238,15</td>
    </tr>
    <tr>
      <th>1</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Maria Margarida Silva Santos</td>
      <td>173.839.785-87</td>
      <td>210100011800001183</td>
      <td>22/01/2018</td>
      <td>3</td>
      <td>nan</td>
      <td>31/01/2018</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>15 - Ressarcimento de Despesas: 22,05;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 200/2018 NF 41932</td>
      <td>2000 - Manutenção de Serviços Técnico e Admini...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>22,05</td>
      <td>ISS: 22,05;</td>
      <td>22,05</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>07 - Inaplicável</td>
      <td>0</td>
      <td>nan</td>
      <td>Maria Margarida Silva Santos</td>
      <td>173.839.785-87</td>
      <td>210100011800000969</td>
      <td>22/01/2018</td>
      <td>3</td>
      <td>210100011800000480.00</td>
      <td>31/01/2018</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Não</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>15 - Ressarcimento de Despesas: 418,95;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 200/2018</td>
      <td>2000 - Manutenção de Serviços Técnico e Admini...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>418,95</td>
      <td>NaN</td>
      <td>418,95</td>
      <td>418,95</td>
    </tr>
    <tr>
      <th>3</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>06 - Pregão Eletrônico</td>
      <td>2017</td>
      <td>210100011700004128.00</td>
      <td>CEK INFORMÁTICA EIRELI ME</td>
      <td>00.949.640/0001-42</td>
      <td>210100011700014727</td>
      <td>24/11/2017</td>
      <td>2</td>
      <td>210100011800000896.00</td>
      <td>31/01/2018</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>4 - DESPESA DE CAPITAL</td>
      <td>4 - Investimentos</td>
      <td>90 - Aplicações Diretas</td>
      <td>04 - SISTEMA PROCESSAMENTO DADOS: 29288,00;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 9569/2017 NF 1841</td>
      <td>1521 - Ampliação do Parque Computacional de Te...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>29.288,00</td>
      <td>NaN</td>
      <td>29.288,00</td>
      <td>29.288,00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>002101 - Diretoria Administrativa e Financeira</td>
      <td>06 - Pregão Eletrônico</td>
      <td>2017</td>
      <td>210100011700000416.00</td>
      <td>LINK CARD ADMINISTRADORA DE BENEFÍCIOS EIRELI-ME</td>
      <td>12.039.966/0001-11</td>
      <td>210100011700002389</td>
      <td>16/03/2017</td>
      <td>6</td>
      <td>210100011800000896.00</td>
      <td>31/01/2018</td>
      <td>01 - Nota de Ordem Bancária (NOB)</td>
      <td>Sim</td>
      <td>Sim</td>
      <td>1 - LIQ Normal</td>
      <td>1</td>
      <td>01 - Legislativa</td>
      <td>3 - DESPESA CORRENTE</td>
      <td>3 - Outras Despesas Correntes</td>
      <td>90 - Aplicações Diretas</td>
      <td>10 - REPARO ADAPT CONS MAN VEÍCULOS: 1924,07;</td>
      <td>0001 - Diretoria Administrativa e Financeira -...</td>
      <td>PGTO PROC 170/2018 NF 59786 LINK CARD</td>
      <td>2000 - Manutenção de Serviços Técnico e Admini...</td>
      <td>02 - Tribunal de Contas do Estado</td>
      <td>71.223,41</td>
      <td>ISS: 7,40;</td>
      <td>1.924,07</td>
      <td>1.916,67</td>
    </tr>
  </tbody>
</table>
</div>


## TCE SP
source: https://transparencia.tce.sp.gov.br/conjunto-de-dados


```python
for year in range(2013,2019):
    filename = 'tce_sp_'+str(year)+'.zip'
    if filename not in listdir(download_folder):
        wget.download('https://transparencia.tce.sp.gov.br/sites/default/files/conjunto-dados/despesas-'+str(year)+'.zip', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]
    #unzip_all(download_folder+filename,temp_dir)
    files = [download_folder+temp_dir+'\\'+x for x in listdir(download_folder+temp_dir) if x.split('.')[-1] != 'pdf']
    df = pd.read_csv(files[0], sep=';', encoding = "ISO-8859-1")
    df = df[(df['ds_subfuncao_governo'] == 'COMUNICAÇÃO SOCIAL') & (df['tp_despesa'] == 'Empenhado')]
    df['vl_despesa'] = df['vl_despesa'].str.replace(',','.').astype(float)
    display(df.head())
    df.to_pickle(data_folder+'tce_sp_'+str(year)+'.pickle')
    
```

    tce_sp_2013.zip 1006.8 MB
    


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
      <th>id_despesa_detalhe</th>
      <th>ano_exercicio</th>
      <th>ds_municipio</th>
      <th>ds_orgao</th>
      <th>mes_referencia</th>
      <th>mes_ref_extenso</th>
      <th>tp_despesa</th>
      <th>nr_empenho</th>
      <th>identificador_despesa</th>
      <th>ds_despesa</th>
      <th>dt_emissao_despesa</th>
      <th>vl_despesa</th>
      <th>ds_funcao_governo</th>
      <th>ds_subfuncao_governo</th>
      <th>cd_programa</th>
      <th>ds_programa</th>
      <th>cd_acao</th>
      <th>ds_acao</th>
      <th>ds_fonte_recurso</th>
      <th>ds_cd_aplicacao_fixo</th>
      <th>ds_modalidade_lic</th>
      <th>ds_elemento</th>
      <th>historico_despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>318248</th>
      <td>242087110</td>
      <td>2013</td>
      <td>Altinópolis</td>
      <td>PREFEITURA MUNICIPAL DE ALTINÓPOLIS</td>
      <td>1</td>
      <td>janeiro</td>
      <td>Empenhado</td>
      <td>574-2013</td>
      <td>CNPJ - PESSOA JURÍDICA - 04333099000102</td>
      <td>GOIABA SOM PROPAGANDAS VOLANTES LTDA ME</td>
      <td>10/01/2013</td>
      <td>440.00</td>
      <td>SAÚDE</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2252.00</td>
      <td>COMUNICACAO E PUBLICIDADE NA AREA DE SAUDE</td>
      <td>TESOURO</td>
      <td>0310 - SAÚDE - GERAL</td>
      <td>DISPENSA DE LICITAÇÃO</td>
      <td>33903988 - SERVIÇOS DE PUBLICIDADE E PROPAGANDA</td>
      <td>CONTRATACAO DE TRIO ELETRICO/EQUIP. SOM</td>
    </tr>
    <tr>
      <th>319923</th>
      <td>242086622</td>
      <td>2013</td>
      <td>Altinópolis</td>
      <td>PREFEITURA MUNICIPAL DE ALTINÓPOLIS</td>
      <td>1</td>
      <td>janeiro</td>
      <td>Empenhado</td>
      <td>831-2013</td>
      <td>CNPJ - PESSOA JURÍDICA - 11355107000179</td>
      <td>KIYA &amp; KIYA INFORMATICA LTDA - ME</td>
      <td>22/01/2013</td>
      <td>77.28</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>9004.00</td>
      <td>GESTAO DO PODER EXECUTIVO</td>
      <td>2251.00</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>PREGÃO</td>
      <td>33903917 - MANUTENÇÃO E CONSERVAÇÃO DE MÁQUINA...</td>
      <td>DESPESAS COM RECARGAS TONERS E CARTUCHOS</td>
    </tr>
    <tr>
      <th>320106</th>
      <td>242088625</td>
      <td>2013</td>
      <td>Altinópolis</td>
      <td>PREFEITURA MUNICIPAL DE ALTINÓPOLIS</td>
      <td>1</td>
      <td>janeiro</td>
      <td>Empenhado</td>
      <td>952-2013</td>
      <td>CNPJ - PESSOA JURÍDICA - 00402904000143</td>
      <td>MARCOS BARROS - LIVRARIA E PESQUISAS - ME</td>
      <td>30/01/2013</td>
      <td>4790.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251.00</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>DISPENSA DE LICITAÇÃO</td>
      <td>33903999 - OUTROS SERVIÇOS DE TERCEIROS - PESS...</td>
      <td>CONTRATACAO</td>
    </tr>
    <tr>
      <th>321979</th>
      <td>242754879</td>
      <td>2013</td>
      <td>Altinópolis</td>
      <td>PREFEITURA MUNICIPAL DE ALTINÓPOLIS</td>
      <td>2</td>
      <td>fevereiro</td>
      <td>Empenhado</td>
      <td>2233-2013</td>
      <td>CNPJ - PESSOA JURÍDICA - 04333099000102</td>
      <td>GOIABA SOM PROPAGANDAS VOLANTES LTDA ME</td>
      <td>28/02/2013</td>
      <td>800.00</td>
      <td>SAÚDE</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2252.00</td>
      <td>COMUNICACAO E PUBLICIDADE NA AREA DE SAUDE</td>
      <td>TESOURO</td>
      <td>0310 - SAÚDE - GERAL</td>
      <td>DISPENSA DE LICITAÇÃO</td>
      <td>33903947 - SERVIÇOS DE COMUNICAÇÃO EM GERAL</td>
      <td>DESPESA COM LOCACAO DE CARRO DE SOM</td>
    </tr>
    <tr>
      <th>323484</th>
      <td>242755442</td>
      <td>2013</td>
      <td>Altinópolis</td>
      <td>PREFEITURA MUNICIPAL DE ALTINÓPOLIS</td>
      <td>2</td>
      <td>fevereiro</td>
      <td>Empenhado</td>
      <td>1445-2013</td>
      <td>CNPJ - PESSOA JURÍDICA - 02365730000111</td>
      <td>KMR TELECOMUNICACOES LTDA</td>
      <td>01/02/2013</td>
      <td>4451.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251.00</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>DISPENSA DE LICITAÇÃO</td>
      <td>33909299 - OUTRAS DESPESAS CORRENTES</td>
      <td>SERVICO DE COMUNICACAO EM GERAL</td>
    </tr>
  </tbody>
</table>
</div>


    tce_sp_2014.zip 1.2 GB
    

    C:\ProgramData\Anaconda3\lib\site-packages\IPython\core\interactiveshell.py:3020: DtypeWarning: Columns (10) have mixed types. Specify dtype option on import or set low_memory=False.
      interactivity=interactivity, compiler=compiler, result=result)
    


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
      <th>id_despesa_detalhe</th>
      <th>ano_exercicio</th>
      <th>ds_municipio</th>
      <th>codigo_municipio_ibge</th>
      <th>ds_orgao</th>
      <th>mes_referencia</th>
      <th>mes_ref_extenso</th>
      <th>tp_despesa</th>
      <th>nr_empenho</th>
      <th>tp_identificador_despesa</th>
      <th>nr_identificador_despesa</th>
      <th>ds_despesa</th>
      <th>dt_emissao_despesa</th>
      <th>vl_despesa</th>
      <th>ds_funcao_governo</th>
      <th>ds_subfuncao_governo</th>
      <th>cd_programa</th>
      <th>ds_programa</th>
      <th>cd_acao</th>
      <th>ds_acao</th>
      <th>ds_fonte_recurso</th>
      <th>ds_cd_aplicacao_fixo</th>
      <th>ds_modalidade_lic</th>
      <th>ds_elemento</th>
      <th>historico_despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>174233</th>
      <td>266390443</td>
      <td>2014</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>542-2014</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>4384547000199</td>
      <td>N.B.B.K. PUBLICIDADE E PROPAGANDA LTDA</td>
      <td>16/01/2014</td>
      <td>150000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903905 - SERVIÇOS TÉCNICOS PROFISSIONAIS</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>174359</th>
      <td>266391178</td>
      <td>2014</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>75-2014</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>5133694000159</td>
      <td>DANIELLE DE GODOY &amp; FLORENCIO LTDA</td>
      <td>03/01/2014</td>
      <td>10604.27</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903905 - SERVIÇOS TÉCNICOS PROFISSIONAIS</td>
      <td>REEMPENHO DO EMPENHO 1316/2013 AGENCIA DE PUBL...</td>
    </tr>
    <tr>
      <th>174478</th>
      <td>266391599</td>
      <td>2014</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>236-2014</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>4384547000199</td>
      <td>N.B.B.K. PUBLICIDADE E PROPAGANDA LTDA</td>
      <td>03/01/2014</td>
      <td>10000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903905 - SERVIÇOS TÉCNICOS PROFISSIONAIS</td>
      <td>REEMPENHO DO EMPENHO 4559/2013 PUBLICACOES, PU...</td>
    </tr>
    <tr>
      <th>174601</th>
      <td>266392656</td>
      <td>2014</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>469-2014</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>8227188000117</td>
      <td>PATRIA DESIGNERS PUBLIC E PROPAGANDA LTDA.</td>
      <td>03/01/2014</td>
      <td>6000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>33903990 - SERVIÇOS DE PUBLICIDADE LEGAL</td>
      <td>PUBLICACAO DE EDITAIS DE CONVITE,PREGAO, TOMA ...</td>
    </tr>
    <tr>
      <th>174809</th>
      <td>266393620</td>
      <td>2014</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>416-2014</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>10867871000160</td>
      <td>DIARIO SERV. INTERM. EM PUBLICACOES LTDA EPP</td>
      <td>03/01/2014</td>
      <td>2970.63</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>33903990 - SERVIÇOS DE PUBLICIDADE LEGAL</td>
      <td>PUBLICACAO DE EDITAL DE REABERTURA  DAS TP 05,...</td>
    </tr>
  </tbody>
</table>
</div>


    tce_sp_2015.zip 1.2 GB
    


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
      <th>id_despesa_detalhe</th>
      <th>ano_exercicio</th>
      <th>ds_municipio</th>
      <th>codigo_municipio_ibge</th>
      <th>ds_orgao</th>
      <th>mes_referencia</th>
      <th>mes_ref_extenso</th>
      <th>tp_despesa</th>
      <th>nr_empenho</th>
      <th>tp_identificador_despesa</th>
      <th>nr_identificador_despesa</th>
      <th>ds_despesa</th>
      <th>dt_emissao_despesa</th>
      <th>vl_despesa</th>
      <th>ds_funcao_governo</th>
      <th>ds_subfuncao_governo</th>
      <th>cd_programa</th>
      <th>ds_programa</th>
      <th>cd_acao</th>
      <th>ds_acao</th>
      <th>ds_fonte_recurso</th>
      <th>ds_cd_aplicacao_fixo</th>
      <th>ds_modalidade_lic</th>
      <th>ds_elemento</th>
      <th>historico_despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>161481</th>
      <td>289917656</td>
      <td>2015</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>806-2015</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>19/01/2015</td>
      <td>6000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901699 - OUTRAS DESPESAS VARIÁVEIS - PESSOAL...</td>
      <td>ESTIMATIVO DE DESPESAS COM PESSOAL</td>
    </tr>
    <tr>
      <th>161591</th>
      <td>289917979</td>
      <td>2015</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>1030-2015</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>4384547000199</td>
      <td>N.B.B.K. PUBLICIDADE E PROPAGANDA LTDA</td>
      <td>23/01/2015</td>
      <td>37500.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903905 - SERVIÇOS TÉCNICOS PROFISSIONAIS</td>
      <td>SERVICOS DE COMUNICACAO/PUBLICIDADE E PROPAGAN...</td>
    </tr>
    <tr>
      <th>161801</th>
      <td>289918672</td>
      <td>2015</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>624-2015</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>19/01/2015</td>
      <td>155400.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901101 - VENCIMENTOS E SALÁRIOS</td>
      <td>ESTIMATIVO DE DESPESAS COM PESSOAL</td>
    </tr>
    <tr>
      <th>161989</th>
      <td>289919701</td>
      <td>2015</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>805-2015</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>19/01/2015</td>
      <td>600.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31900501 - SALÁRIO FAMÍLIA - ATIVO - PESSOAL C...</td>
      <td>ESTIMATIVO DE DESPESAS COM PESSOAL</td>
    </tr>
    <tr>
      <th>162259</th>
      <td>289920938</td>
      <td>2015</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>760-2015</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>133</td>
      <td>I.N.S.S.</td>
      <td>19/01/2015</td>
      <td>47000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901302 - CONTRIBUIÇÕES PREVIDENCIÁRIAS - INSS</td>
      <td>ESTIMATIVO DE CONTRIBUICAO</td>
    </tr>
  </tbody>
</table>
</div>


    tce_sp_2016.zip 1.2 GB
    


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
      <th>id_despesa_detalhe</th>
      <th>ano_exercicio</th>
      <th>ds_municipio</th>
      <th>codigo_municipio_ibge</th>
      <th>ds_orgao</th>
      <th>mes_referencia</th>
      <th>mes_ref_extenso</th>
      <th>tp_despesa</th>
      <th>nr_empenho</th>
      <th>tp_identificador_despesa</th>
      <th>nr_identificador_despesa</th>
      <th>ds_despesa</th>
      <th>dt_emissao_despesa</th>
      <th>vl_despesa</th>
      <th>ds_funcao_governo</th>
      <th>ds_subfuncao_governo</th>
      <th>cd_programa</th>
      <th>ds_programa</th>
      <th>cd_acao</th>
      <th>ds_acao</th>
      <th>ds_fonte_recurso</th>
      <th>ds_cd_aplicacao_fixo</th>
      <th>ds_modalidade_lic</th>
      <th>ds_elemento</th>
      <th>historico_despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>158004</th>
      <td>320626607</td>
      <td>2016</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>1035-2016</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>29/01/2016</td>
      <td>127300.08</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901101 - VENCIMENTOS E SALÁRIOS</td>
      <td>REFERENTE A FOLHA PAGAMENTO</td>
    </tr>
    <tr>
      <th>158011</th>
      <td>320626614</td>
      <td>2016</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>411-2016</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>4384547000199</td>
      <td>N.B.B.K. PUBLICIDADE E PROPAGANDA LTDA</td>
      <td>04/01/2016</td>
      <td>142315.27</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903905 - SERVIÇOS TÉCNICOS PROFISSIONAIS</td>
      <td>CONTRATACAO DE AGENCIA DE PUBLICIDADE REEMPENH...</td>
    </tr>
    <tr>
      <th>158075</th>
      <td>320627038</td>
      <td>2016</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>975-2016</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>29/01/2016</td>
      <td>14144.45</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901145 - FÉRIAS - ABONO CONSTITUCIONAL</td>
      <td>REFERENTE A FERIAS MENSAIS</td>
    </tr>
    <tr>
      <th>158273</th>
      <td>320627405</td>
      <td>2016</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>412-2016</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>4384547000199</td>
      <td>N.B.B.K. PUBLICIDADE E PROPAGANDA LTDA</td>
      <td>04/01/2016</td>
      <td>150000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903905 - SERVIÇOS TÉCNICOS PROFISSIONAIS</td>
      <td>CONTRATACAO DE AGENCIA DE PUBLICIDADE PLURIANUAL</td>
    </tr>
    <tr>
      <th>158278</th>
      <td>320627410</td>
      <td>2016</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>1012-2016</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>133</td>
      <td>I.N.S.S.</td>
      <td>29/01/2016</td>
      <td>28464.36</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901302 - CONTRIBUIÇÕES PREVIDENCIÁRIAS - INSS</td>
      <td>CONTRIBUICAO MENSAL</td>
    </tr>
  </tbody>
</table>
</div>


    tce_sp_2017.zip 1.3 GB
    


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
      <th>id_despesa_detalhe</th>
      <th>ano_exercicio</th>
      <th>ds_municipio</th>
      <th>codigo_municipio_ibge</th>
      <th>ds_orgao</th>
      <th>mes_referencia</th>
      <th>mes_ref_extenso</th>
      <th>tp_despesa</th>
      <th>nr_empenho</th>
      <th>tp_identificador_despesa</th>
      <th>nr_identificador_despesa</th>
      <th>ds_despesa</th>
      <th>dt_emissao_despesa</th>
      <th>vl_despesa</th>
      <th>ds_funcao_governo</th>
      <th>ds_subfuncao_governo</th>
      <th>cd_programa</th>
      <th>ds_programa</th>
      <th>cd_acao</th>
      <th>ds_acao</th>
      <th>ds_fonte_recurso</th>
      <th>ds_cd_aplicacao_fixo</th>
      <th>ds_modalidade_lic</th>
      <th>ds_elemento</th>
      <th>historico_despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>162529</th>
      <td>354250964</td>
      <td>2017</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>579-2017</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>134</td>
      <td>F.G.T.S.</td>
      <td>31/01/2017</td>
      <td>809.46</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901301 - FGTS</td>
      <td>CONTRIBUICAO MENSAL - JANEIRO</td>
    </tr>
    <tr>
      <th>162531</th>
      <td>354250966</td>
      <td>2017</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>683-2017</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>31/01/2017</td>
      <td>62.14</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31900501 - SALÁRIO FAMÍLIA - ATIVO - PESSOAL C...</td>
      <td>FOLHA DE PAGAMENTO REF. MES JANEIRO</td>
    </tr>
    <tr>
      <th>162533</th>
      <td>354250968</td>
      <td>2017</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>685-2017</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>31/01/2017</td>
      <td>230.89</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901137 - GRATIFICAÇÃO POR TEMPO DE SERVIÇO</td>
      <td>FOLHA DE PAGAMENTO REF. MES JANEIRO</td>
    </tr>
    <tr>
      <th>162535</th>
      <td>354250970</td>
      <td>2017</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>217-2017</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>02/01/2017</td>
      <td>156.27</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901101 - VENCIMENTOS E SALÁRIOS</td>
      <td>VERBAS RESCISORIAS CARLOS EDUARDO MENDONCA MEL...</td>
    </tr>
    <tr>
      <th>162652</th>
      <td>354251126</td>
      <td>2017</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>686-2017</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>31/01/2017</td>
      <td>110.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901199 - OUTRAS DESPESAS FIXAS - PESSOAL CIVIL</td>
      <td>FOLHA DE PAGAMENTO REF. MES JANEIRO</td>
    </tr>
  </tbody>
</table>
</div>


    tce_sp_2018.zip 1.9 GB
    


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
      <th>id_despesa_detalhe</th>
      <th>ano_exercicio</th>
      <th>ds_municipio</th>
      <th>codigo_municipio_ibge</th>
      <th>ds_orgao</th>
      <th>mes_referencia</th>
      <th>mes_ref_extenso</th>
      <th>tp_despesa</th>
      <th>nr_empenho</th>
      <th>tp_identificador_despesa</th>
      <th>nr_identificador_despesa</th>
      <th>ds_despesa</th>
      <th>dt_emissao_despesa</th>
      <th>vl_despesa</th>
      <th>ds_funcao_governo</th>
      <th>ds_subfuncao_governo</th>
      <th>cd_programa</th>
      <th>ds_programa</th>
      <th>cd_acao</th>
      <th>ds_acao</th>
      <th>ds_fonte_recurso</th>
      <th>ds_cd_aplicacao_fixo</th>
      <th>ds_modalidade_lic</th>
      <th>ds_elemento</th>
      <th>historico_despesa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>172686</th>
      <td>386404394</td>
      <td>2018</td>
      <td>Bariri</td>
      <td>3505203</td>
      <td>PREFEITURA MUNICIPAL DE BARIRI</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>132-2018</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>20491368000107</td>
      <td>AZURE PROPAGANDA LTDA EPP</td>
      <td>02/01/2018</td>
      <td>150000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>2.00</td>
      <td>Supervisao e Coordenacao Superior</td>
      <td>2063.00</td>
      <td>Publicidade e Propaganda</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>CONCORRÊNCIA</td>
      <td>33903990 - SERVIÇOS DE PUBLICIDADE LEGAL</td>
      <td>REFERENTE A PRESTACAO DE SERVICOS PARA PUBLICI...</td>
    </tr>
    <tr>
      <th>228866</th>
      <td>387320081</td>
      <td>2018</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>514-2018</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>134</td>
      <td>F.G.T.S.</td>
      <td>30/01/2018</td>
      <td>1219.47</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251.00</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901301 - FGTS</td>
      <td>CONTRIBUICAO COMPETENCIA JANEIRO 2018</td>
    </tr>
    <tr>
      <th>228867</th>
      <td>387320082</td>
      <td>2018</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>617-2018</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>30/01/2018</td>
      <td>63.42</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251.00</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31900501 - SALÁRIO FAMÍLIA - ATIVO - PESSOAL C...</td>
      <td>REFERENTE FOLHA DE PAGAMENTO JANEIRO</td>
    </tr>
    <tr>
      <th>229011</th>
      <td>387320562</td>
      <td>2018</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>65-2018</td>
      <td>CNPJ - PESSOA JURÍDICA</td>
      <td>4384547000199</td>
      <td>N.B.B.K. PUBLICIDADE E PROPAGANDA LTDA</td>
      <td>02/01/2018</td>
      <td>310000.00</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2303.00</td>
      <td>PUBLICACOES INSTITUCIONAIS</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>TOMADA DE PREÇOS</td>
      <td>33903988 - SERVIÇOS DE PUBLICIDADE E PROPAGANDA</td>
      <td>TERMO DE PRORROGACAO CONTRATACAO DE AGENCIA DE...</td>
    </tr>
    <tr>
      <th>229012</th>
      <td>387320563</td>
      <td>2018</td>
      <td>Barra Bonita</td>
      <td>3505302</td>
      <td>PREFEITURA MUNICIPAL DE BARRA BONITA</td>
      <td>1</td>
      <td>Janeiro</td>
      <td>Empenhado</td>
      <td>619-2018</td>
      <td>INSCRIÇÃO GENÉRICA-OUTROS</td>
      <td>33</td>
      <td>DIVERSOS SERVIDORES MUNICIPAIS</td>
      <td>30/01/2018</td>
      <td>230.89</td>
      <td>ADMINISTRAÇÃO</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>7004.00</td>
      <td>COMUNICACAO E PUBLICIDADE</td>
      <td>2251.00</td>
      <td>COORDENACAO E EXECUCAO ACOES DE COMUNICACAO DO...</td>
      <td>TESOURO</td>
      <td>0110 - GERAL</td>
      <td>OUTROS/NÃO APLICÁVEL</td>
      <td>31901137 - GRATIFICAÇÃO POR TEMPO DE SERVIÇO</td>
      <td>REFERENTE FOLHA DE PAGAMENTO JANEIRO</td>
    </tr>
  </tbody>
</table>
</div>


## TCE RS
source: http://dados.tce.rs.gov.br/dataset/despesa-orcamentaria-por-empenhos-2018


```python
for year in range(2013,2019):
    filename = 'tce_rs_'+str(year)+'.zip'
    if filename not in listdir(download_folder):
        wget.download('http://dados.tce.rs.gov.br/dados/municipal/empenhos/'+str(year)+'.csv.zip', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]
    #unzip_all(download_folder+filename,temp_dir)
    files = [download_folder+temp_dir+'\\'+x for x in listdir(download_folder+temp_dir) if x.split('.')[-1] != 'pdf']
    df = pd.read_csv(files[0], sep=',', encoding = "utf-8")
    df.columns = df.columns.str.upper()
    df['DS_SUBFUNCAO'] = df['DS_SUBFUNCAO'].str.upper().str.replace('Ç','C').str.replace('Ã','A')
    df = df[(df['DS_SUBFUNCAO'] == 'COMUNICACAO SOCIAL') & (df['TIPO_OPERACAO'] == 'E')]
    display(df.head())
    df.to_pickle(data_folder+'tce_rs_'+str(year)+'.pickle')
```

    tce_rs_2013.zip 807.8 MB
    


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
      <th>ANO_RECEBIMENTO</th>
      <th>BIMESTRE_RECEBIMENTO</th>
      <th>CD_ORGAO</th>
      <th>NOME_ORGAO</th>
      <th>CD_RECEBIMENTO</th>
      <th>CD_ORGAO_ORCAMENTARIO</th>
      <th>NOME_ORGAO_ORCAMENTARIO</th>
      <th>CD_UNIDADE_ORCAMENTARIA</th>
      <th>NOME_UNIDADE_ORCAMENTARIA</th>
      <th>TP_UNIDADE</th>
      <th>TIPO_OPERACAO</th>
      <th>ANO_EMPENHO</th>
      <th>ANO_OPERACAO</th>
      <th>DT_EMPENHO</th>
      <th>DT_OPERACAO</th>
      <th>NR_EMPENHO</th>
      <th>CD_FUNCAO</th>
      <th>DS_FUNCAO</th>
      <th>CD_SUBFUNCAO</th>
      <th>DS_SUBFUNCAO</th>
      <th>CD_PROGRAMA</th>
      <th>DS_PROGRAMA</th>
      <th>CD_PROJETO</th>
      <th>NM_PROJETO</th>
      <th>CD_ELEMENTO</th>
      <th>CD_RUBRICA</th>
      <th>DS_RUBRICA</th>
      <th>CD_RECURSO</th>
      <th>NM_RECURSO</th>
      <th>CD_CREDOR</th>
      <th>NM_CREDOR</th>
      <th>CNPJ_CPF</th>
      <th>CGC_TE</th>
      <th>HISTORICO</th>
      <th>VL_EMPENHO</th>
      <th>NR_LIQUIDACAO</th>
      <th>VL_LIQUIDACAO</th>
      <th>NR_PAGAMENTO</th>
      <th>VL_PAGAMENTO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>77858</th>
      <td>2013</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>119000</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2012</td>
      <td>2012</td>
      <td>2012-11-01</td>
      <td>2012-11-01</td>
      <td>201200010060</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131.00</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6.00</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120.00</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000.00</td>
      <td>OUTROS SERVICOS DE TERCEIROS-PESSOA JURIDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE IN- TER...</td>
      <td>300.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>77916</th>
      <td>2013</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>119000</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2012</td>
      <td>2012</td>
      <td>2012-11-30</td>
      <td>2012-11-30</td>
      <td>201200010060</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131.00</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6.00</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120.00</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000.00</td>
      <td>OUTROS SERVICOS DE TERCEIROS-PESSOA JURIDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE IN- TER...</td>
      <td>-134.80</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>78040</th>
      <td>2013</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>119000</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2012</td>
      <td>2012</td>
      <td>2012-12-03</td>
      <td>2012-12-03</td>
      <td>201200010920</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131.00</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6.00</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120.00</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000.00</td>
      <td>OUTROS SERVICOS DE TERCEIROS-PESSOA JURIDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE IN- TER...</td>
      <td>100.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>78211</th>
      <td>2013</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>119000</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2012</td>
      <td>2012</td>
      <td>2012-12-17</td>
      <td>2012-12-17</td>
      <td>201200011326</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131.00</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6.00</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120.00</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.30</td>
      <td>339030000000.00</td>
      <td>MATERIAL DE CONSUMO</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>122.00</td>
      <td>JORGE FELIPETTO</td>
      <td>91399410000102.00</td>
      <td>nan</td>
      <td>PAGAMENTO DE FOTOS DE PROGRAMACOES DE FINAL DE...</td>
      <td>600.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>78495</th>
      <td>2013</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>119000</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2012</td>
      <td>2012</td>
      <td>2012-12-24</td>
      <td>2012-12-24</td>
      <td>201200011662</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131.00</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6.00</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120.00</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000.00</td>
      <td>OUTROS SERVICOS DE TERCEIROS-PESSOA JURIDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>37.00</td>
      <td>FUNDACAO NAVEGANTES P.LUCENA</td>
      <td>90786765000191.00</td>
      <td>979000033.00</td>
      <td>PAGAMENTO DE 15 ANUNCIOS DE DIVULGACAO, PARA A...</td>
      <td>150.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
  </tbody>
</table>
</div>


    tce_rs_2014.zip 853.4 MB
    


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
      <th>ANO_RECEBIMENTO</th>
      <th>BIMESTRE_RECEBIMENTO</th>
      <th>CD_ORGAO</th>
      <th>NOME_ORGAO</th>
      <th>CD_RECEBIMENTO</th>
      <th>CD_ORGAO_ORCAMENTARIO</th>
      <th>NOME_ORGAO_ORCAMENTARIO</th>
      <th>CD_UNIDADE_ORCAMENTARIA</th>
      <th>NOME_UNIDADE_ORCAMENTARIA</th>
      <th>TP_UNIDADE</th>
      <th>TIPO_OPERACAO</th>
      <th>ANO_EMPENHO</th>
      <th>ANO_OPERACAO</th>
      <th>DT_EMPENHO</th>
      <th>DT_OPERACAO</th>
      <th>NR_EMPENHO</th>
      <th>CD_FUNCAO</th>
      <th>DS_FUNCAO</th>
      <th>CD_SUBFUNCAO</th>
      <th>DS_SUBFUNCAO</th>
      <th>CD_PROGRAMA</th>
      <th>DS_PROGRAMA</th>
      <th>CD_PROJETO</th>
      <th>NM_PROJETO</th>
      <th>CD_ELEMENTO</th>
      <th>CD_RUBRICA</th>
      <th>DS_RUBRICA</th>
      <th>CD_RECURSO</th>
      <th>NM_RECURSO</th>
      <th>CD_CREDOR</th>
      <th>NM_CREDOR</th>
      <th>CNPJ_CPF</th>
      <th>CGC_TE</th>
      <th>HISTORICO</th>
      <th>VL_EMPENHO</th>
      <th>NR_LIQUIDACAO</th>
      <th>VL_LIQUIDACAO</th>
      <th>NR_PAGAMENTO</th>
      <th>VL_PAGAMENTO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83436</th>
      <td>2014</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>127787</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2013</td>
      <td>2013</td>
      <td>2013-11-01</td>
      <td>2013-11-01</td>
      <td>201300010040</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS - PESSOA JURÍDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE INTERES...</td>
      <td>300.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>83575</th>
      <td>2014</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>127787</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2013</td>
      <td>2013</td>
      <td>2013-12-03</td>
      <td>2013-12-03</td>
      <td>201300011070</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS - PESSOA JURÍDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO E NOTICIAS DE IN TERES...</td>
      <td>300.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84101</th>
      <td>2014</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>127787</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2013</td>
      <td>2013</td>
      <td>2013-12-30</td>
      <td>2013-12-30</td>
      <td>201300011958</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS - PESSOA JURÍDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PARA COMPLEMENTO DO EMPENHO NO11070/2013..</td>
      <td>222.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84103</th>
      <td>2014</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>127787</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2013</td>
      <td>2013</td>
      <td>2013-12-30</td>
      <td>2013-12-30</td>
      <td>201300011959</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>6</td>
      <td>DIVULGACAO OFICIAL E INSTITUCIONAL</td>
      <td>2120</td>
      <td>Manutençao da Assessoria de Imprensa e Divulgaçao</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS - PESSOA JURÍDICA</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PARA COMPLEMENTO DO EMPENHO NO10040/2013..</td>
      <td>1134.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84359</th>
      <td>2014</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>127787</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2014</td>
      <td>2014</td>
      <td>2014-01-03</td>
      <td>2014-01-03</td>
      <td>201400000139</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgaçao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutençao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039920000</td>
      <td>SERVICOS DE PUBLICIDADE INSTITUCIONAL</td>
      <td>1.00</td>
      <td>LIVRE</td>
      <td>904.00</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACO DE NOTICIAS DE INTE RES...</td>
      <td>300.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
  </tbody>
</table>
</div>


    tce_rs_2015.zip 842.5 MB
    


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
      <th>ANO_RECEBIMENTO</th>
      <th>BIMESTRE_RECEBIMENTO</th>
      <th>CD_ORGAO</th>
      <th>NOME_ORGAO</th>
      <th>CD_RECEBIMENTO</th>
      <th>CD_ORGAO_ORCAMENTARIO</th>
      <th>NOME_ORGAO_ORCAMENTARIO</th>
      <th>CD_UNIDADE_ORCAMENTARIA</th>
      <th>NOME_UNIDADE_ORCAMENTARIA</th>
      <th>TP_UNIDADE</th>
      <th>TIPO_OPERACAO</th>
      <th>ANO_EMPENHO</th>
      <th>ANO_OPERACAO</th>
      <th>DT_EMPENHO</th>
      <th>DT_OPERACAO</th>
      <th>NR_EMPENHO</th>
      <th>CD_FUNCAO</th>
      <th>DS_FUNCAO</th>
      <th>CD_SUBFUNCAO</th>
      <th>DS_SUBFUNCAO</th>
      <th>CD_PROGRAMA</th>
      <th>DS_PROGRAMA</th>
      <th>CD_PROJETO</th>
      <th>NM_PROJETO</th>
      <th>CD_ELEMENTO</th>
      <th>CD_RUBRICA</th>
      <th>DS_RUBRICA</th>
      <th>CD_RECURSO</th>
      <th>NM_RECURSO</th>
      <th>CD_CREDOR</th>
      <th>NM_CREDOR</th>
      <th>CNPJ_CPF</th>
      <th>CGC_TE</th>
      <th>HISTORICO</th>
      <th>VL_EMPENHO</th>
      <th>NR_LIQUIDACAO</th>
      <th>VL_LIQUIDACAO</th>
      <th>NR_PAGAMENTO</th>
      <th>VL_PAGAMENTO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>81711</th>
      <td>2015</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>138347</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2014</td>
      <td>2014</td>
      <td>2014-12-22</td>
      <td>2014-12-22</td>
      <td>201400011016</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgaçao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutençao da Assessoria de Imprensa</td>
      <td>3.3.90.30</td>
      <td>339030000000</td>
      <td>MATERIAL DE CONSUMO</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>2483</td>
      <td>CIGI VIDEO PRODUCOES LTDA.</td>
      <td>7804762000190.00</td>
      <td>nan</td>
      <td>AQUISICAO DE 350 CARTOES DE ANIVERSARIO PARA F...</td>
      <td>700.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>81712</th>
      <td>2015</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>138347</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2014</td>
      <td>2014</td>
      <td>2014-12-22</td>
      <td>2014-12-22</td>
      <td>201400011017</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgaçao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutençao da Assessoria de Imprensa</td>
      <td>3.3.90.30</td>
      <td>339030000000</td>
      <td>MATERIAL DE CONSUMO</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>2143</td>
      <td>REI ARTES EM METAL LTDA</td>
      <td>73484875000180.00</td>
      <td>1100056022.00</td>
      <td>AQUISICAO DE 01 PLACA DE ALUMINIO 30X40 PA RA ...</td>
      <td>650.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>82155</th>
      <td>2015</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>138347</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2014</td>
      <td>2014</td>
      <td>2014-12-29</td>
      <td>2014-12-29</td>
      <td>201400011401</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgaçao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutençao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS - PESSOA JURÍDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>904</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PARA COMPLEMENTO DO EMPENHO NO10344/2014..</td>
      <td>239.70</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>82547</th>
      <td>2015</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>138347</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2015</td>
      <td>2015</td>
      <td>2015-01-06</td>
      <td>2015-01-06</td>
      <td>201500000141</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgaçao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutençao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039920000</td>
      <td>SERVICOS DE PUBLICIDADE INSTITUCIONAL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>904</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE IN TERE...</td>
      <td>500.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>82664</th>
      <td>2015</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>138347</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2015</td>
      <td>2015</td>
      <td>2015-01-07</td>
      <td>2015-01-07</td>
      <td>201500000168</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgaçao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutençao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039470000</td>
      <td>SERVICOS DE COMUNICACAO EM GERAL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>37</td>
      <td>FUNDACAO NAVEGANTES P.LUCENA</td>
      <td>90786765000191.00</td>
      <td>979000033.00</td>
      <td>PAGAMENTO DE PRESTADORA DE SERVICOS DE TRANSMI...</td>
      <td>7920.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
  </tbody>
</table>
</div>


    tce_rs_2016.zip 864.9 MB
    


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
      <th>ANO_RECEBIMENTO</th>
      <th>BIMESTRE_RECEBIMENTO</th>
      <th>CD_ORGAO</th>
      <th>NOME_ORGAO</th>
      <th>CD_RECEBIMENTO</th>
      <th>CD_ORGAO_ORCAMENTARIO</th>
      <th>NOME_ORGAO_ORCAMENTARIO</th>
      <th>CD_UNIDADE_ORCAMENTARIA</th>
      <th>NOME_UNIDADE_ORCAMENTARIA</th>
      <th>TP_UNIDADE</th>
      <th>TIPO_OPERACAO</th>
      <th>ANO_EMPENHO</th>
      <th>ANO_OPERACAO</th>
      <th>DT_EMPENHO</th>
      <th>DT_OPERACAO</th>
      <th>NR_EMPENHO</th>
      <th>CD_FUNCAO</th>
      <th>DS_FUNCAO</th>
      <th>CD_SUBFUNCAO</th>
      <th>DS_SUBFUNCAO</th>
      <th>CD_PROGRAMA</th>
      <th>DS_PROGRAMA</th>
      <th>CD_PROJETO</th>
      <th>NM_PROJETO</th>
      <th>CD_ELEMENTO</th>
      <th>CD_RUBRICA</th>
      <th>DS_RUBRICA</th>
      <th>CD_RECURSO</th>
      <th>NM_RECURSO</th>
      <th>CD_CREDOR</th>
      <th>NM_CREDOR</th>
      <th>CNPJ_CPF</th>
      <th>CGC_TE</th>
      <th>HISTORICO</th>
      <th>VL_EMPENHO</th>
      <th>NR_LIQUIDACAO</th>
      <th>VL_LIQUIDACAO</th>
      <th>NR_PAGAMENTO</th>
      <th>VL_PAGAMENTO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>82127</th>
      <td>2016</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>147453</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2015</td>
      <td>2015</td>
      <td>2015-10-16</td>
      <td>2015-10-16</td>
      <td>201500008216</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>500</td>
      <td>RADIO REGIONAL LTDA</td>
      <td>89096994000103.00</td>
      <td>1169000026.00</td>
      <td>PAGAMENTO DE SERVICOS DE DIVULGACAO DA 15A EXP...</td>
      <td>1500.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>82257</th>
      <td>2016</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>147453</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2015</td>
      <td>2015</td>
      <td>2015-12-01</td>
      <td>2015-12-01</td>
      <td>201500009727</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>904</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE INTERES...</td>
      <td>900.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>82542</th>
      <td>2016</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>147453</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2015</td>
      <td>2015</td>
      <td>2015-12-29</td>
      <td>2015-12-29</td>
      <td>201500010565</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>904</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PARA COMPLEMENTO DO EMPENHO NO9727/2015...</td>
      <td>761.25</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>82748</th>
      <td>2016</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>147453</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2016</td>
      <td>2016</td>
      <td>2016-01-04</td>
      <td>2016-01-04</td>
      <td>201600000094</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039920000</td>
      <td>SERVICOS DE PUBLICIDADE INSTITUCIONAL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>904</td>
      <td>EDITORA JORN. CORREIO SEMANAL LTDA</td>
      <td>4211446000116.00</td>
      <td>1160014725.00</td>
      <td>PAGAMENTO DE DIVULGACAO DE NOTICIAS DE INTERES...</td>
      <td>900.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84701</th>
      <td>2016</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>147453</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2016</td>
      <td>2016</td>
      <td>2016-01-29</td>
      <td>2016-01-29</td>
      <td>201600000760</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039470000</td>
      <td>SERVICOS DE COMUNICACAO EM GERAL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>37</td>
      <td>FUNDACAO NAVEGANTES P.LUCENA</td>
      <td>90786765000191.00</td>
      <td>979000033.00</td>
      <td>PAGAMENTO DE EMPRESA PRESTADORA DE SERVICO DE ...</td>
      <td>7920.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
  </tbody>
</table>
</div>


    tce_rs_2017.zip 863.5 MB
    


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
      <th>ANO_RECEBIMENTO</th>
      <th>BIMESTRE_RECEBIMENTO</th>
      <th>CD_ORGAO</th>
      <th>NOME_ORGAO</th>
      <th>CD_RECEBIMENTO</th>
      <th>CD_ORGAO_ORCAMENTARIO</th>
      <th>NOME_ORGAO_ORCAMENTARIO</th>
      <th>CD_UNIDADE_ORCAMENTARIA</th>
      <th>NOME_UNIDADE_ORCAMENTARIA</th>
      <th>TP_UNIDADE</th>
      <th>TIPO_OPERACAO</th>
      <th>ANO_EMPENHO</th>
      <th>ANO_OPERACAO</th>
      <th>DT_EMPENHO</th>
      <th>DT_OPERACAO</th>
      <th>NR_EMPENHO</th>
      <th>CD_FUNCAO</th>
      <th>DS_FUNCAO</th>
      <th>CD_SUBFUNCAO</th>
      <th>DS_SUBFUNCAO</th>
      <th>CD_PROGRAMA</th>
      <th>DS_PROGRAMA</th>
      <th>CD_PROJETO</th>
      <th>NM_PROJETO</th>
      <th>CD_ELEMENTO</th>
      <th>CD_RUBRICA</th>
      <th>DS_RUBRICA</th>
      <th>CD_RECURSO</th>
      <th>NM_RECURSO</th>
      <th>CD_CREDOR</th>
      <th>NM_CREDOR</th>
      <th>CNPJ_CPF</th>
      <th>CGC_TE</th>
      <th>HISTORICO</th>
      <th>VL_EMPENHO</th>
      <th>NR_LIQUIDACAO</th>
      <th>VL_LIQUIDACAO</th>
      <th>NR_PAGAMENTO</th>
      <th>VL_PAGAMENTO</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>83900</th>
      <td>2017</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>161503</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2016</td>
      <td>2016</td>
      <td>2016-01-29</td>
      <td>2016-01-29</td>
      <td>201600000760</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>37</td>
      <td>FUNDACAO NAVEGANTES P.LUCENA</td>
      <td>90786765000191.00</td>
      <td>979000033.00</td>
      <td>PAGAMENTO DE EMPRESA PRESTADORA DE SERVICO DE ...</td>
      <td>7920.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84139</th>
      <td>2017</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>161503</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2016</td>
      <td>2016</td>
      <td>2016-11-14</td>
      <td>2016-11-14</td>
      <td>201600009294</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>254</td>
      <td>RBS TV SANTA ROSA LTDA</td>
      <td>93088367000190.00</td>
      <td>nan</td>
      <td>PAGAMENTO DE 26 INSERCOES DE PUBLICIDADE DIVUL...</td>
      <td>6214.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84287</th>
      <td>2017</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>161503</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2016</td>
      <td>2016</td>
      <td>2016-12-14</td>
      <td>2016-12-14</td>
      <td>201600010404</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>3866</td>
      <td>ADRIANE MONTANHA 03602918050</td>
      <td>20207366000143.00</td>
      <td>nan</td>
      <td>PAGAMENTO DE 12 HORAS DE DIVULGACAO COM MOTO S...</td>
      <td>240.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84613</th>
      <td>2017</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>161503</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2016</td>
      <td>2016</td>
      <td>2016-12-30</td>
      <td>2016-12-30</td>
      <td>201600000760</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039000000</td>
      <td>OUTROS SERVICOS DE TERCEIROS - PESSOA JURIDICA</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>37</td>
      <td>FUNDACAO NAVEGANTES P.LUCENA</td>
      <td>90786765000191.00</td>
      <td>979000033.00</td>
      <td>PAGAMENTO DE EMPRESA PRESTADORA DE SERVICO DE ...</td>
      <td>-2400.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>84696</th>
      <td>2017</td>
      <td>6</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>161503</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2017</td>
      <td>2017</td>
      <td>2017-01-02</td>
      <td>2017-01-02</td>
      <td>201700000007</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039590000</td>
      <td>SERVICOS DE AUDIO, VIDEO E FOTO</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>122</td>
      <td>JORGE FELIPETTO</td>
      <td>91399410000102.00</td>
      <td>nan</td>
      <td>FILMAGEM DA CERIMONIA POSSE DO PREFEITO VICE P...</td>
      <td>250.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
    </tr>
  </tbody>
</table>
</div>


    tce_rs_2018.zip 965.7 MB
    


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
      <th>ANO_RECEBIMENTO</th>
      <th>MES_RECEBIMENTO</th>
      <th>CD_ORGAO</th>
      <th>NOME_ORGAO</th>
      <th>CD_RECEBIMENTO</th>
      <th>CD_ORGAO_ORCAMENTARIO</th>
      <th>NOME_ORGAO_ORCAMENTARIO</th>
      <th>CD_UNIDADE_ORCAMENTARIA</th>
      <th>NOME_UNIDADE_ORCAMENTARIA</th>
      <th>TP_UNIDADE</th>
      <th>TIPO_OPERACAO</th>
      <th>ANO_EMPENHO</th>
      <th>ANO_OPERACAO</th>
      <th>DT_EMPENHO</th>
      <th>DT_OPERACAO</th>
      <th>NR_EMPENHO</th>
      <th>CD_FUNCAO</th>
      <th>DS_FUNCAO</th>
      <th>CD_SUBFUNCAO</th>
      <th>DS_SUBFUNCAO</th>
      <th>CD_PROGRAMA</th>
      <th>DS_PROGRAMA</th>
      <th>CD_PROJETO</th>
      <th>NM_PROJETO</th>
      <th>CD_ELEMENTO</th>
      <th>CD_RUBRICA</th>
      <th>DS_RUBRICA</th>
      <th>CD_RECURSO</th>
      <th>NM_RECURSO</th>
      <th>CD_CREDOR</th>
      <th>NM_CREDOR</th>
      <th>CNPJ_CPF</th>
      <th>CGC_TE</th>
      <th>HISTORICO</th>
      <th>VL_EMPENHO</th>
      <th>NR_LIQUIDACAO</th>
      <th>VL_LIQUIDACAO</th>
      <th>NR_PAGAMENTO</th>
      <th>VL_PAGAMENTO</th>
      <th>ANO_LICITACAO</th>
      <th>NR_LICITACAO</th>
      <th>MOD_LICITACAO</th>
      <th>ANO_CONTRATO</th>
      <th>NR_CONTRATO</th>
      <th>TP_INSTRUMENTO_CONTRATUAL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>89324</th>
      <td>2018</td>
      <td>12</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>178088</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2017</td>
      <td>2017</td>
      <td>2017-12-04</td>
      <td>2017-12-04</td>
      <td>201700010368</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039630000000</td>
      <td>SERVICOS GRAFICOS E EDITORIAIS</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>2350</td>
      <td>WIELAND   SILVA LTDA.</td>
      <td>8824791000186.00</td>
      <td>1100102474.00</td>
      <td>CONFECCAO DE PLACA PREMIO GESTOR PUBLICO E 02 ...</td>
      <td>1260.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0</td>
      <td>0.00</td>
      <td>DPV</td>
      <td>nan</td>
      <td>nan</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89378</th>
      <td>2018</td>
      <td>12</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>178088</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2017</td>
      <td>2017</td>
      <td>2017-12-14</td>
      <td>2017-12-14</td>
      <td>201700010680</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.3.90.39</td>
      <td>339039470000000</td>
      <td>SERVICOS DE COMUNICACAO EM GERAL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>167</td>
      <td>EMPRESA JORNALIST.NOROESTE LTDA</td>
      <td>87687703000118.00</td>
      <td>1109000305.00</td>
      <td>PAGAMENTO REFERENTE A MENSAGENS DE NATAL E ANO...</td>
      <td>500.00</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0</td>
      <td>0.00</td>
      <td>DPV</td>
      <td>nan</td>
      <td>nan</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89413</th>
      <td>2018</td>
      <td>12</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>178088</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2017</td>
      <td>2017</td>
      <td>2017-12-15</td>
      <td>2017-12-15</td>
      <td>201700010823</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.1.91.13</td>
      <td>319113030100000</td>
      <td>CONTRIBUICOES PATRONAIS PARA O RPPS - ATIVO CIVIL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>39</td>
      <td>FUNDO FAS</td>
      <td>13937121000106.00</td>
      <td>nan</td>
      <td>PAGAMENTO F.A.P.S SOBRE O 13O SALARIO</td>
      <td>280.81</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0</td>
      <td>0.00</td>
      <td>DPV</td>
      <td>nan</td>
      <td>nan</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89438</th>
      <td>2018</td>
      <td>12</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>178088</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2017</td>
      <td>2017</td>
      <td>2017-12-15</td>
      <td>2017-12-15</td>
      <td>201700010848</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.1.91.13</td>
      <td>319113990100000</td>
      <td>AMORTIZACAO DO PASSIVO ATUARIAL COM O RPPS - A...</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>39</td>
      <td>FUNDO FAS</td>
      <td>13937121000106.00</td>
      <td>nan</td>
      <td>PAGAMENTO ALIQUOTA SUPLEMENTAR DOS FAPS CONF. ...</td>
      <td>236.72</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0</td>
      <td>0.00</td>
      <td>DPV</td>
      <td>nan</td>
      <td>nan</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>89540</th>
      <td>2018</td>
      <td>12</td>
      <td>40300</td>
      <td>PM DE ALECRIM</td>
      <td>178088</td>
      <td>10</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>1</td>
      <td>SECRETARIA GERAL DE GOVERNO</td>
      <td>PRM</td>
      <td>E</td>
      <td>2017</td>
      <td>2017</td>
      <td>2017-12-21</td>
      <td>2017-12-21</td>
      <td>201700011130</td>
      <td>4</td>
      <td>ADMINISTRACAO</td>
      <td>131</td>
      <td>COMUNICACAO SOCIAL</td>
      <td>8</td>
      <td>Divulgacao Oficial e Institucional</td>
      <td>2191</td>
      <td>Manutencao da Assessoria de Imprensa</td>
      <td>3.1.91.13</td>
      <td>319113030100000</td>
      <td>CONTRIBUICOES PATRONAIS PARA O RPPS - ATIVO CIVIL</td>
      <td>1</td>
      <td>LIVRE</td>
      <td>39</td>
      <td>FUNDO FAS</td>
      <td>13937121000106.00</td>
      <td>nan</td>
      <td>PAGAMENTO PARTE PATRONAL DO FAPS, CONFORME FOL...</td>
      <td>381.22</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0.00</td>
      <td>nan</td>
      <td>0</td>
      <td>0.00</td>
      <td>DPV</td>
      <td>nan</td>
      <td>nan</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>


## TCE PR
source: http://servicos.tce.pr.gov.br/TCEPR/Tribunal/Relacon/Dados/DadosConsulta/Consolidado


```python
for year in range(2013,2019):
    filename = 'tce_pr_'+str(year)+'.zip'
    if filename not in listdir(download_folder):
        wget.download('http://servicos.tce.pr.gov.br/TCEPR/Tribunal/Relacon/Arquivos/'+str(year)+'_PIT_TodosArquivos.zip', download_folder + filename)
    print(filename,file_size(download_folder+filename))
    despesas = [x for x in unzip_list(download_folder+filename) if x.endswith('Despesa.zip')]
    temp_dir = filename.split('.')[0]+'\\'
    df = pd.DataFrame([])
    for file_zip in despesas:
        try:
            unzip_file(file_zip,download_folder+filename,temp_dir)
            #print('file_zip',file_zip,file_size(download_folder+temp_dir+file_zip))
            empenho = [y for y in unzip_list(download_folder+temp_dir+file_zip) if y.endswith('Empenho.xml')]
            for empenho_file in empenho:
                unzip_file(empenho_file,download_folder+temp_dir+file_zip,temp_dir)
                df2 = read_xml(download_folder+temp_dir+empenho_file)
                df2 = df2[df2['dsDesdobramento'].str.contains('PUBLICIDADE')]
                #print(df.shape,df2.shape)
                df = df.append(df2, sort=False)
        except Exception as e:
            print(file_zip,empenho_file,e)
    df.to_pickle(data_folder+'tce_pr_'+str(year)+'.pickle')
 
```

    tce_pr_2013.zip 1.5 GB
    tce_pr_2014.zip 1.6 GB
    2014_411090_Despesa.zip 2014_411090_Empenho.xml reference to invalid character number: line 1, column 2677951
    2014_411295_Despesa.zip 2014_411295_Empenho.xml reference to invalid character number: line 1, column 547432
    2014_411700_Despesa.zip 2014_411700_Empenho.xml reference to invalid character number: line 1, column 556031
    tce_pr_2015.zip 1.7 GB
    2015_411090_Despesa.zip 2015_411090_Empenho.xml reference to invalid character number: line 1, column 6240005
    2015_411700_Despesa.zip 2015_411700_Empenho.xml reference to invalid character number: line 1, column 594934
    tce_pr_2016.zip 1.7 GB
    tce_pr_2017.zip 1.8 GB
    tce_pr_2018.zip 1.0 GB
    2018_411700_Despesa.zip 2018_411700_Empenho.xml reference to invalid character number: line 1, column 196260
    

## TCE ES
source: http://dados.es.gov.br/dataset/despesas-municipios


```python
from bs4 import BeautifulSoup
```


```python
url = 'http://dados.es.gov.br/dataset/despesas-municipios'
soup = BeautifulSoup(requests.get(url).content, 'html.parser')
links = [link['href'] for link in soup.find_all('a') if link['href'].startswith('http://dados.es.gov.br')]
links_dict = dict()
for link in links:
    year = link.split('-')[-1].split('.')[0]
    links_dict[year] = link
print(links_dict)
```

    {'2018': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/97a45d1f-a106-4163-8e6e-5e1c4a5977c6/download/municipios-despesas-2018.zip', '2017': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/335e9ab7-6b62-48bf-a39b-b4cc0a9cc529/download/municipios-despesas-2017.zip', '2016': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/a52bf323-72a2-4f81-b9a1-b821dc6fe8ca/download/municipios-despesas-2016.zip', '2015': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/69a8ad56-7442-4519-a6dc-d1e330edaab2/download/municipios-despesas-2015.zip', '2014': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/8dcef151-4c74-4271-b80e-0b6bf9e9c31d/download/municipios-despesas-2014.zip', '2013': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/032a55f0-fd4e-4ffe-9ab5-b7dfe20024fb/download/municipios-despesas-2013.zip', '2019': 'http://dados.es.gov.br/dataset/0c7f8c3a-af88-4d03-b906-426474cf8035/resource/dbaafe85-90c2-4c70-8027-ea22614ae5f4/download/municipios-despesas-2019.zip'}
    


```python
for year in links_dict.keys():
    filename = 'tce_es_'+str(year)+'.zip'
    if filename not in listdir(download_folder):
        print('downloading from: ',filename,links_dict[year])
        wget.download(links_dict[year], download_folder + filename)
    print(filename,file_size(download_folder+filename))
    temp_dir = filename.split('.')[0]+'\\'
    unzip_all(download_folder+filename,temp_dir)
    files = [download_folder+temp_dir+'\\'+x for x in listdir(download_folder+temp_dir)]
    for f in files:
        df = pd.DataFrame([])
        df = pd.read_csv(f, sep=',' ,encoding = 'utf-16',error_bad_lines=False)
        df = df[df['DescricaoSubFuncao'] == "COMUNICAÇÃO SOCIAL"]
        display(df.head())
        df.to_pickle(data_folder+'tce_es_'+str(year)+'.pickle')
```

    tce_es_2018.zip 11.3 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>12663</th>
      <td>2018</td>
      <td>10</td>
      <td>Prefeitura Municipal de Águia Branca</td>
      <td>Águia Branca</td>
      <td>0,00</td>
      <td>2000,00</td>
      <td>2000,00</td>
      <td>33504300</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>TRANSFERÊNCIAS A INSTITUIÇÕES PRIVADAS SEM FIN...</td>
      <td>SUBVENÇÕES SOCIAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>12679</th>
      <td>2018</td>
      <td>10</td>
      <td>Prefeitura Municipal de Águia Branca</td>
      <td>Águia Branca</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>335043--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>TRANSFERÊNCIAS A INSTITUIÇÕES PRIVADAS SEM FIN...</td>
      <td>SUBVENÇÕES SOCIAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>15085</th>
      <td>2018</td>
      <td>9</td>
      <td>Prefeitura Municipal de Águia Branca</td>
      <td>Águia Branca</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>335043--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>TRANSFERÊNCIAS A INSTITUIÇÕES PRIVADAS SEM FIN...</td>
      <td>SUBVENÇÕES SOCIAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>15093</th>
      <td>2018</td>
      <td>9</td>
      <td>Prefeitura Municipal de Águia Branca</td>
      <td>Águia Branca</td>
      <td>0,00</td>
      <td>2000,00</td>
      <td>2000,00</td>
      <td>33504300</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>TRANSFERÊNCIAS A INSTITUIÇÕES PRIVADAS SEM FIN...</td>
      <td>SUBVENÇÕES SOCIAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>18497</th>
      <td>2018</td>
      <td>2</td>
      <td>Prefeitura Municipal de Águia Branca</td>
      <td>Águia Branca</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>335043--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>TRANSFERÊNCIAS A INSTITUIÇÕES PRIVADAS SEM FIN...</td>
      <td>SUBVENÇÕES SOCIAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_es_2017.zip 9.7 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>33305</th>
      <td>2017</td>
      <td>8</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>319011--</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>VENCIMENTOS E VANTAGENS FIXAS – PESSOAL CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>33306</th>
      <td>2017</td>
      <td>8</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>8514,33</td>
      <td>8514,33</td>
      <td>8514,33</td>
      <td>31901101</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>VENCIMENTOS E VANTAGENS FIXAS – PESSOAL CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>33307</th>
      <td>2017</td>
      <td>8</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>319013--</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OBRIGAÇÕES PATRONAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>33308</th>
      <td>2017</td>
      <td>8</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>694,19</td>
      <td>1178,97</td>
      <td>31901302</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OBRIGAÇÕES PATRONAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>33309</th>
      <td>2017</td>
      <td>8</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339030--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>MATERIAL DE CONSUMO</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_es_2016.zip 9.5 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>37824</th>
      <td>2016</td>
      <td>12</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>1701,32</td>
      <td>1701,32</td>
      <td>33903908</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>37825</th>
      <td>2016</td>
      <td>12</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>11180,00</td>
      <td>11180,00</td>
      <td>33903999</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>37826</th>
      <td>2016</td>
      <td>12</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>917,32</td>
      <td>1677,32</td>
      <td>1677,32</td>
      <td>33903999</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>37827</th>
      <td>2016</td>
      <td>12</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339039--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>37828</th>
      <td>2016</td>
      <td>12</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>2500,00</td>
      <td>2500,00</td>
      <td>2500,00</td>
      <td>33903099</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>MATERIAL DE CONSUMO</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_es_2015.zip 9.5 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>38417</th>
      <td>2015</td>
      <td>10</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>319013--</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OBRIGAÇÕES PATRONAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>38418</th>
      <td>2015</td>
      <td>10</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>1848,43</td>
      <td>1848,43</td>
      <td>31901302</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OBRIGAÇÕES PATRONAIS</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>38419</th>
      <td>2015</td>
      <td>10</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339036--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA FÍSICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>38420</th>
      <td>2015</td>
      <td>10</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>10471,65</td>
      <td>10471,65</td>
      <td>10471,65</td>
      <td>31901101</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>VENCIMENTOS E VANTAGENS FIXAS – PESSOAL CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>38421</th>
      <td>2015</td>
      <td>10</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>319011--</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>VENCIMENTOS E VANTAGENS FIXAS – PESSOAL CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_es_2014.zip 8.7 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>31259</th>
      <td>2014</td>
      <td>11</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>-198,49</td>
      <td>0,00</td>
      <td>579,20</td>
      <td>33903607</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA FÍSICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>31260</th>
      <td>2014</td>
      <td>11</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339014--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>DIÁRIAS – CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>31261</th>
      <td>2014</td>
      <td>11</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>319011--</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>VENCIMENTOS E VANTAGENS FIXAS – PESSOAL CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>31262</th>
      <td>2014</td>
      <td>9</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>850,66</td>
      <td>4950,66</td>
      <td>33903999</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>31263</th>
      <td>2014</td>
      <td>11</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>-913,73</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>33903980</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_es_2013.zip 7.9 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>35839</th>
      <td>2013</td>
      <td>9</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339039--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>35840</th>
      <td>2013</td>
      <td>9</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>1064,00</td>
      <td>1064,00</td>
      <td>33903999</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>35841</th>
      <td>2013</td>
      <td>9</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339036--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA FÍSICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>35842</th>
      <td>2013</td>
      <td>9</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>542,40</td>
      <td>542,40</td>
      <td>33903607</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA FÍSICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
    <tr>
      <th>35843</th>
      <td>2013</td>
      <td>9</td>
      <td>Prefeitura Municipal de Alfredo Chaves</td>
      <td>Alfredo Chaves</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>319011--</td>
      <td>DESPESA CORRENTE</td>
      <td>PESSOAL E ENCARGOS SOCIAIS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>VENCIMENTOS E VANTAGENS FIXAS – PESSOAL CIVIL</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>0,00</td>
    </tr>
  </tbody>
</table>
</div>


    tce_es_2019.zip 3.2 MB
    


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
      <th>Ano</th>
      <th>Mes</th>
      <th>UnidadeGestora</th>
      <th>EsferaAdministrativa</th>
      <th>Empenhada</th>
      <th>Liquidada</th>
      <th>Paga</th>
      <th>CodigoCompleto</th>
      <th>CategoriaDespesa</th>
      <th>GrupoDespesa</th>
      <th>ModalidadeDespesa</th>
      <th>ElementoDespesa</th>
      <th>CodigoFuncao</th>
      <th>DescricaoFuncao</th>
      <th>CodigoSubFuncao</th>
      <th>DescricaoSubFuncao</th>
      <th>PrevisaoInicial</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9556</th>
      <td>2019</td>
      <td>1</td>
      <td>Prefeitura Municipal de Piúma</td>
      <td>Piúma</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339039--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>5000,00</td>
    </tr>
    <tr>
      <th>9633</th>
      <td>2019</td>
      <td>1</td>
      <td>Prefeitura Municipal de Piúma</td>
      <td>Piúma</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339039--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>5000,00</td>
    </tr>
    <tr>
      <th>9671</th>
      <td>2019</td>
      <td>1</td>
      <td>Prefeitura Municipal de Piúma</td>
      <td>Piúma</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339039--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>10000,00</td>
    </tr>
    <tr>
      <th>10184</th>
      <td>2019</td>
      <td>1</td>
      <td>Prefeitura Municipal de Piúma</td>
      <td>Piúma</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>339039--</td>
      <td>DESPESA CORRENTE</td>
      <td>OUTRAS DESPESAS CORRENTES</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS – PESSOA JURÍDICA</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>10000,00</td>
    </tr>
    <tr>
      <th>12911</th>
      <td>2019</td>
      <td>1</td>
      <td>Secretaria Municipal de Administração e Recurs...</td>
      <td>Serra</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>0,00</td>
      <td>449052--</td>
      <td>DESPESA DE CAPITAL</td>
      <td>INVESTIMENTOS</td>
      <td>APLICAÇÕES DIRETAS</td>
      <td>EQUIPAMENTOS E MATERIAL PERMANENTE</td>
      <td>4</td>
      <td>ADMINISTRAÇÃO</td>
      <td>131</td>
      <td>COMUNICAÇÃO SOCIAL</td>
      <td>20000,00</td>
    </tr>
  </tbody>
</table>
</div>


## TCE PE
source: https://www.tce.pe.gov.br/internet/index.php/dados-abertos/bases-de-dados-completas
https://www.tce.pe.gov.br/internet/index.php/dados-abertos/definicoes-da-api


```python
for year in range(2013,2019):
    url = 'https://sistemas.tce.pe.gov.br/DadosAbertos/Despesas!xml?ANOREFERENCIA='+str(year)+'&SUBFUNCAO=Comunica' 
    filename = 'tce_pe_'+str(year)+'.xml'
    print(filename)
    if filename not in listdir(download_folder):
        wget.download(url,download_folder+filename)
    with open(download_folder+filename, 'rb') as f:
        data = f.read()
        
    soup = BeautifulSoup(data, 'xml').find('conteudo')

    root = et.fromstring(soup.decode())
    dic = dict()
    for count,r in enumerate(root.iter('DespesasMunicipais')):
        dic2 = dict()
        for d in r:
            dic2[d.tag] = d.text
        dic[count] = dic2
    df = pd.DataFrame.from_dict(dic, orient='index')
    display(df.head())
    df.to_pickle(data_folder+'tce_pe_'+str(year)+'.pickle')
```

    tce_pe_2013.xml
    -1 / unknown


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
      <th>HISTORICO</th>
      <th>VALORLIQUIDADO</th>
      <th>CPF_CNPJ</th>
      <th>NOMEUNIDADEGESTORA</th>
      <th>VALORPAGO</th>
      <th>SUBFUNCAO</th>
      <th>UNIDADEORCAMENTARIA</th>
      <th>PROGRAMA</th>
      <th>ELEMENTODESPESA</th>
      <th>ESFERA</th>
      <th>FORNECEDOR</th>
      <th>ACAO</th>
      <th>DATAEMPENHO</th>
      <th>ID_UNIDADE_GESTORA</th>
      <th>FONTERECURSO</th>
      <th>NATUREZA</th>
      <th>MODALIDADE</th>
      <th>VALOREMPENHADO</th>
      <th>FUNCAO</th>
      <th>ANOREFERENCIA</th>
      <th>TIPOCREDOR</th>
      <th>NUMEROEMPENHO</th>
      <th>SUBELEMENTODESPESA</th>
      <th>CATEGORIA</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Emissão de Empenho Orçamentário. VALOR QUE SE ...</td>
      <td>27381.75</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>77870.20</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRAÇÃO SUPERIOR</td>
      <td>DIVULGAÇÃO INSTITUCIONAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICAÇÃO LTDA - EPP</td>
      <td>DIVULGAÇÃO INSTITUCIONAL, IMPRESSOS E PUBLICAÇ...</td>
      <td>2013-01-25 00:00:00.0</td>
      <td>649</td>
      <td>None</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>78100.00</td>
      <td>Administração</td>
      <td>2013</td>
      <td>2</td>
      <td>0000325</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Emissão de Empenho Orçamentário. VALOR QUE SE ...</td>
      <td>257.50</td>
      <td>10921252000107</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>257.50</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRAÇÃO SUPERIOR</td>
      <td>DIVULGAÇÃO INSTITUCIONAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>CEPE - COMPANHIA EDITORA DE PERNAMBUCO</td>
      <td>DIVULGAÇÃO INSTITUCIONAL, IMPRESSOS E PUBLICAÇ...</td>
      <td>2013-01-02 00:00:00.0</td>
      <td>649</td>
      <td>None</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>257.50</td>
      <td>Administração</td>
      <td>2013</td>
      <td>2</td>
      <td>0000001</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Emissão de Empenho Orçamentário. VALOR QUE SE ...</td>
      <td>412.00</td>
      <td>10921252000107</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>412.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRAÇÃO SUPERIOR</td>
      <td>DIVULGAÇÃO INSTITUCIONAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>CEPE - COMPANHIA EDITORA DE PERNAMBUCO</td>
      <td>DIVULGAÇÃO INSTITUCIONAL, IMPRESSOS E PUBLICAÇ...</td>
      <td>2013-01-02 00:00:00.0</td>
      <td>649</td>
      <td>None</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>412.00</td>
      <td>Administração</td>
      <td>2013</td>
      <td>2</td>
      <td>0000002</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Emissão de Empenho Orçamentário. VALOR QUE SE ...</td>
      <td>1390.50</td>
      <td>10921252000107</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>17921.50</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRAÇÃO SUPERIOR</td>
      <td>DIVULGAÇÃO INSTITUCIONAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>CEPE - COMPANHIA EDITORA DE PERNAMBUCO</td>
      <td>DIVULGAÇÃO INSTITUCIONAL, IMPRESSOS E PUBLICAÇ...</td>
      <td>2013-01-02 00:00:00.0</td>
      <td>649</td>
      <td>None</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>40000.00</td>
      <td>Administração</td>
      <td>2013</td>
      <td>2</td>
      <td>0000015</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Emissão de Empenho Orçamentário. VALOR EMPENHA...</td>
      <td>100.00</td>
      <td>10994697000117</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>100.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRAÇÃO SUPERIOR</td>
      <td>DIVULGAÇÃO INSTITUCIONAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>NAE NORDESTE ASSESSORIA EMPRESARIAL LTDA</td>
      <td>DIVULGAÇÃO INSTITUCIONAL, IMPRESSOS E PUBLICAÇ...</td>
      <td>2013-05-02 00:00:00.0</td>
      <td>649</td>
      <td>None</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>100.00</td>
      <td>Administração</td>
      <td>2013</td>
      <td>2</td>
      <td>0001259</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
  </tbody>
</table>
</div>


    tce_pe_2014.xml
    -1 / unknown


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
      <th>HISTORICO</th>
      <th>VALORLIQUIDADO</th>
      <th>CPF_CNPJ</th>
      <th>NOMEUNIDADEGESTORA</th>
      <th>VALORPAGO</th>
      <th>SUBFUNCAO</th>
      <th>UNIDADEORCAMENTARIA</th>
      <th>PROGRAMA</th>
      <th>ELEMENTODESPESA</th>
      <th>ESFERA</th>
      <th>FORNECEDOR</th>
      <th>ACAO</th>
      <th>DATAEMPENHO</th>
      <th>ID_UNIDADE_GESTORA</th>
      <th>FONTERECURSO</th>
      <th>NATUREZA</th>
      <th>MODALIDADE</th>
      <th>VALOREMPENHADO</th>
      <th>FUNCAO</th>
      <th>ANOREFERENCIA</th>
      <th>TIPOCREDOR</th>
      <th>NUMEROEMPENHO</th>
      <th>SUBELEMENTODESPESA</th>
      <th>CATEGORIA</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>REF- LOCA??O DE 02 ( DOIS) CARROS DE SOM COM M...</td>
      <td>21560.00</td>
      <td>08211304000100</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>21560.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>BRISA PROMO??ES E EVENTOS LTDA- ME</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2014-07-30 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>21560.00</td>
      <td>Administração</td>
      <td>2014</td>
      <td>2</td>
      <td>0002309</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>1</th>
      <td>REF. LOCA??O DE 02 ( DOIS ) CARROS DE SOM COM ...</td>
      <td>21450.00</td>
      <td>08211304000100</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>21450.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>BRISA PROMO??ES E EVENTOS LTDA- ME</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2014-01-02 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>21450.00</td>
      <td>Administração</td>
      <td>2014</td>
      <td>2</td>
      <td>0000169</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>2</th>
      <td>REF. LOCA??O DE 02 (DOIS) CARROS DE SOM COM MO...</td>
      <td>21230.00</td>
      <td>08211304000100</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>21230.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>BRISA PROMO??ES E EVENTOS LTDA- ME</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2014-06-11 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>21230.00</td>
      <td>Administração</td>
      <td>2014</td>
      <td>2</td>
      <td>0001811</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>3</th>
      <td>REF. LOCA??O DE 02 (DOIS) CARROS DE SOM COM MO...</td>
      <td>21010.00</td>
      <td>08211304000100</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>21010.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>BRISA PROMO??ES E EVENTOS LTDA- ME</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2014-05-12 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>21010.00</td>
      <td>Administração</td>
      <td>2014</td>
      <td>2</td>
      <td>0001432</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>4</th>
      <td>REF. LOCA??O DE 02 (DOIS) CARROS DE SOM COM MO...</td>
      <td>18150.00</td>
      <td>08211304000100</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>18150.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>BRISA PROMO??ES E EVENTOS LTDA- ME</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2014-01-24 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>18150.00</td>
      <td>Administração</td>
      <td>2014</td>
      <td>2</td>
      <td>0000469</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
  </tbody>
</table>
</div>


    tce_pe_2015.xml
    -1 / unknown


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
      <th>HISTORICO</th>
      <th>VALORLIQUIDADO</th>
      <th>CPF_CNPJ</th>
      <th>NOMEUNIDADEGESTORA</th>
      <th>VALORPAGO</th>
      <th>SUBFUNCAO</th>
      <th>UNIDADEORCAMENTARIA</th>
      <th>PROGRAMA</th>
      <th>ELEMENTODESPESA</th>
      <th>ESFERA</th>
      <th>FORNECEDOR</th>
      <th>ACAO</th>
      <th>DATAEMPENHO</th>
      <th>ID_UNIDADE_GESTORA</th>
      <th>FONTERECURSO</th>
      <th>NATUREZA</th>
      <th>MODALIDADE</th>
      <th>VALOREMPENHADO</th>
      <th>FUNCAO</th>
      <th>ANOREFERENCIA</th>
      <th>TIPOCREDOR</th>
      <th>NUMEROEMPENHO</th>
      <th>SUBELEMENTODESPESA</th>
      <th>CATEGORIA</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>REF. A CRIA??O E FINALIZA??O DE SPOTS DE 30 SE...</td>
      <td>0.00</td>
      <td>24130007000196</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>0.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MAKPLAN - MARKETING   PLANEJAMENTO LTDA</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2015-03-16 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>0.00</td>
      <td>Administração</td>
      <td>2015</td>
      <td>2</td>
      <td>0000821</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>1</th>
      <td>REF. AO 1? TERMO ADITIVO CONFORME PROCESSO DE ...</td>
      <td>1803491.62</td>
      <td>24130007000196</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>1573661.39</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MAKPLAN - MARKETING   PLANEJAMENTO LTDA</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2015-01-05 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>1803491.62</td>
      <td>Administração</td>
      <td>2015</td>
      <td>2</td>
      <td>0000037</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>2</th>
      <td>REF. CONFEC??O DE 200 ( DUZENTAS ) CAMISAS NO ...</td>
      <td>0.00</td>
      <td>24130007000196</td>
      <td>Prefeitura Municipal do Cabo de Santo Agostinho</td>
      <td>0.00</td>
      <td>Comunicação Social</td>
      <td>31102 - SECRETARIA EXECUTIVA DE COMUNICA??O SO...</td>
      <td>GEST?O GOVERNAMENTAL TRANSPARENTE</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MAKPLAN - MARKETING   PLANEJAMENTO LTDA</td>
      <td>DIVULGA??O DA A??O GOVERNAMENTAL</td>
      <td>2015-03-04 00:00:00.0</td>
      <td>115</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>0.00</td>
      <td>Administração</td>
      <td>2015</td>
      <td>2</td>
      <td>0000711</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATA??O D...</td>
      <td>38733.08</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>38733.08</td>
      <td>Comunicação Social</td>
      <td>Administracao Superior</td>
      <td>DIVULGA??O INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICA??O LTDA - EPP</td>
      <td>REALIZA??O DE ATIVIDADES DE DIVULGA??O INSTITU...</td>
      <td>2015-08-03 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>38733.08</td>
      <td>Administração</td>
      <td>2015</td>
      <td>2</td>
      <td>0001945</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>4</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATA??O D...</td>
      <td>46303.33</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>46303.33</td>
      <td>Comunicação Social</td>
      <td>Administracao Superior</td>
      <td>DIVULGA??O INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICA??O LTDA - EPP</td>
      <td>REALIZA??O DE ATIVIDADES DE DIVULGA??O INSTITU...</td>
      <td>2015-11-10 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>46303.33</td>
      <td>Administração</td>
      <td>2015</td>
      <td>2</td>
      <td>0002779</td>
      <td>SERVIÇOS DE COMUNICAÇÃO EM GERAL</td>
      <td>Despesa Corrente</td>
    </tr>
  </tbody>
</table>
</div>


    tce_pe_2016.xml
    -1 / unknown


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
      <th>HISTORICO</th>
      <th>VALORLIQUIDADO</th>
      <th>CPF_CNPJ</th>
      <th>NOMEUNIDADEGESTORA</th>
      <th>VALORPAGO</th>
      <th>SUBFUNCAO</th>
      <th>UNIDADEORCAMENTARIA</th>
      <th>PROGRAMA</th>
      <th>ELEMENTODESPESA</th>
      <th>ESFERA</th>
      <th>FORNECEDOR</th>
      <th>ACAO</th>
      <th>DATAEMPENHO</th>
      <th>ID_UNIDADE_GESTORA</th>
      <th>FONTERECURSO</th>
      <th>NATUREZA</th>
      <th>MODALIDADE</th>
      <th>VALOREMPENHADO</th>
      <th>FUNCAO</th>
      <th>ANOREFERENCIA</th>
      <th>TIPOCREDOR</th>
      <th>NUMEROEMPENHO</th>
      <th>SUBELEMENTODESPESA</th>
      <th>CATEGORIA</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>VALOR QUE SE EMPENHA E REFERENTE E REFERENTE A...</td>
      <td>4440.00</td>
      <td>***.***.164-53</td>
      <td>Prefeitura Municipal de São Benedito do Sul</td>
      <td>3600.00</td>
      <td>Comunicação Postais</td>
      <td>ADMINISTRA??O DISTRITAL DE IGARAPEBA</td>
      <td>GEST?O SUPERIOR DO MUNIC?PIO</td>
      <td>Outros Serviços de Terceiros ? Pessoa Física</td>
      <td>M</td>
      <td>JOAO ANTONIO DA SILVA</td>
      <td>Manutencao das Agencias de Correios Comunitari...</td>
      <td>2016-01-04 00:00:00.0</td>
      <td>587</td>
      <td>Transferências de Outros Convênios</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>4440.00</td>
      <td>Comunicações</td>
      <td>2016</td>
      <td>1</td>
      <td>0000011</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>1</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATA??O D...</td>
      <td>49510.44</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>49510.44</td>
      <td>Comunicação Social</td>
      <td>Administracao Superior</td>
      <td>DIVULGA??O INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICA??O LTDA - EPP</td>
      <td>REALIZA??O DE ATIVIDADES DE DIVULGA??O INSTITU...</td>
      <td>2016-06-13 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>49510.44</td>
      <td>Administração</td>
      <td>2016</td>
      <td>2</td>
      <td>0001924</td>
      <td>OUTROS SERVIÇOS DE TERCEIROS, PESSOA JURÍDICA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATA??O D...</td>
      <td>80000.00</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>80000.00</td>
      <td>Comunicação Social</td>
      <td>Administracao Superior</td>
      <td>DIVULGA??O INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICA??O LTDA - EPP</td>
      <td>REALIZA??O DE ATIVIDADES DE DIVULGA??O INSTITU...</td>
      <td>2016-01-04 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>80000.00</td>
      <td>Administração</td>
      <td>2016</td>
      <td>2</td>
      <td>0000080</td>
      <td>SERVIÇOS DE COMUNICAÇÃO EM GERAL</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATA??O D...</td>
      <td>100000.00</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>100000.00</td>
      <td>Comunicação Social</td>
      <td>Administracao Superior</td>
      <td>DIVULGA??O INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICA??O LTDA - EPP</td>
      <td>REALIZA??O DE ATIVIDADES DE DIVULGA??O INSTITU...</td>
      <td>2016-03-22 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>100000.00</td>
      <td>Administração</td>
      <td>2016</td>
      <td>2</td>
      <td>0000963</td>
      <td>SERVIÇOS DE COMUNICAÇÃO EM GERAL</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>4</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATA??O D...</td>
      <td>150000.00</td>
      <td>02250442000111</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>150000.00</td>
      <td>Comunicação Social</td>
      <td>Administracao Superior</td>
      <td>DIVULGA??O INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>MINDELLO E ASSOCIADOS COMUNICA??O LTDA - EPP</td>
      <td>REALIZA??O DE ATIVIDADES DE DIVULGA??O INSTITU...</td>
      <td>2016-05-27 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>150000.00</td>
      <td>Administração</td>
      <td>2016</td>
      <td>2</td>
      <td>0001654</td>
      <td>SERVIÇOS DE COMUNICAÇÃO EM GERAL</td>
      <td>Despesa Corrente</td>
    </tr>
  </tbody>
</table>
</div>


    tce_pe_2017.xml
    -1 / unknown


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
      <th>HISTORICO</th>
      <th>VALORLIQUIDADO</th>
      <th>CPF_CNPJ</th>
      <th>NOMEUNIDADEGESTORA</th>
      <th>VALORPAGO</th>
      <th>SUBFUNCAO</th>
      <th>UNIDADEORCAMENTARIA</th>
      <th>PROGRAMA</th>
      <th>ELEMENTODESPESA</th>
      <th>ESFERA</th>
      <th>FORNECEDOR</th>
      <th>ACAO</th>
      <th>DATAEMPENHO</th>
      <th>ID_UNIDADE_GESTORA</th>
      <th>FONTERECURSO</th>
      <th>NATUREZA</th>
      <th>MODALIDADE</th>
      <th>VALOREMPENHADO</th>
      <th>FUNCAO</th>
      <th>ANOREFERENCIA</th>
      <th>TIPOCREDOR</th>
      <th>NUMEROEMPENHO</th>
      <th>SUBELEMENTODESPESA</th>
      <th>CATEGORIA</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A PRESTACAO DE ...</td>
      <td>3000.00</td>
      <td>***.***.834-35</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>3000.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRACAO SUPERIOR</td>
      <td>DIVULGACAO INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Física</td>
      <td>M</td>
      <td>LUANA CRISTINA OLIVEIRA DE ASSIS</td>
      <td>REALIZACAO DE ATIVIDADES DE DIVULGACAO INSTITU...</td>
      <td>2017-03-27 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>3000.00</td>
      <td>Administração</td>
      <td>2017</td>
      <td>1</td>
      <td>0000698</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>1</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATACAO D...</td>
      <td>90000.00</td>
      <td>10229491000109</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>90000.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRACAO SUPERIOR</td>
      <td>DIVULGACAO INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>USINA DE FILMES LTDA</td>
      <td>REALIZACAO DE ATIVIDADES DE DIVULGACAO INSTITU...</td>
      <td>2017-05-02 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>90000.00</td>
      <td>Administração</td>
      <td>2017</td>
      <td>2</td>
      <td>0000895</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A PRESTACAO DE ...</td>
      <td>7800.00</td>
      <td>11364583000156</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>7800.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRACAO SUPERIOR</td>
      <td>DIVULGACAO INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>HELIO BONFIM PEREIRA ME</td>
      <td>REALIZACAO DE ATIVIDADES DE DIVULGACAO INSTITU...</td>
      <td>2017-02-09 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>7800.00</td>
      <td>Administração</td>
      <td>2017</td>
      <td>2</td>
      <td>0000290</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VALOR QUE SE EMPENHA REFERENTE AO DESENVOLVIME...</td>
      <td>7500.00</td>
      <td>11364583000156</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>7500.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRACAO SUPERIOR</td>
      <td>DIVULGACAO INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>HELIO BONFIM PEREIRA ME</td>
      <td>REALIZACAO DE ATIVIDADES DE DIVULGACAO INSTITU...</td>
      <td>2017-06-01 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>7500.00</td>
      <td>Administração</td>
      <td>2017</td>
      <td>2</td>
      <td>0001199</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>4</th>
      <td>VALOR QUE SE EMPENHA REFERENTE A CONTRATACAO D...</td>
      <td>7500.00</td>
      <td>17780647000186</td>
      <td>Prefeitura Municipal de Surubim</td>
      <td>7500.00</td>
      <td>Comunicação Social</td>
      <td>ADMINISTRACAO SUPERIOR</td>
      <td>DIVULGACAO INSTITUCIONAL E CERIMONIAL</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>EDMILSON JOSE DE LIMA</td>
      <td>REALIZACAO DE ATIVIDADES DE DIVULGACAO INSTITU...</td>
      <td>2017-05-05 00:00:00.0</td>
      <td>649</td>
      <td>Recursos Ordinários (Não vinculados)</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>7500.00</td>
      <td>Administração</td>
      <td>2017</td>
      <td>2</td>
      <td>0000973</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
  </tbody>
</table>
</div>


    tce_pe_2018.xml
    -1 / unknown


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
      <th>HISTORICO</th>
      <th>VALORLIQUIDADO</th>
      <th>CPF_CNPJ</th>
      <th>NOMEUNIDADEGESTORA</th>
      <th>VALORPAGO</th>
      <th>SUBFUNCAO</th>
      <th>UNIDADEORCAMENTARIA</th>
      <th>PROGRAMA</th>
      <th>ELEMENTODESPESA</th>
      <th>ESFERA</th>
      <th>FORNECEDOR</th>
      <th>ACAO</th>
      <th>DATAEMPENHO</th>
      <th>ID_UNIDADE_GESTORA</th>
      <th>FONTERECURSO</th>
      <th>NATUREZA</th>
      <th>MODALIDADE</th>
      <th>VALOREMPENHADO</th>
      <th>FUNCAO</th>
      <th>ANOREFERENCIA</th>
      <th>TIPOCREDOR</th>
      <th>NUMEROEMPENHO</th>
      <th>SUBELEMENTODESPESA</th>
      <th>CATEGORIA</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>VALOR REF. A PRESTACAO DE SERVICOS DE RADIODIF...</td>
      <td>5850.00</td>
      <td>09025560000175</td>
      <td>Prefeitura Municipal de Lagoa dos Gatos</td>
      <td>5850.00</td>
      <td>Comunicação Social</td>
      <td>ASSESSORIA DE COMUNICACAO E ARTICULACAO POLITICA</td>
      <td>DIVULGACAO INSTITUCIONAL DA ADMINISTRACAO</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>FM RADIO A VOZ DO AGRESTE LTDA ME</td>
      <td>DIVULGACAO INSTITUCIONAL, IMPRESSOS E PUBLICAC...</td>
      <td>2018-03-02 00:00:00.0</td>
      <td>354</td>
      <td>Recursos Ordinários</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>5850.00</td>
      <td>Administração</td>
      <td>2018</td>
      <td>2</td>
      <td>0000177</td>
      <td>SEM SUBELEMENTO</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>1</th>
      <td>VALOR QUE SE EMPENHA EM FAVOR DO CREDOR, REFER...</td>
      <td>300000.00</td>
      <td>17670503000177</td>
      <td>Prefeitura Municipal de Petrolina</td>
      <td>300000.00</td>
      <td>Comunicação Social</td>
      <td>ASSESSORIA DE GOVERNO</td>
      <td>PROMO??O E DIVULGA??O DAS A??ES DO GOVERNO MUN...</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>SALA 10 COMUNICA??O LTDA</td>
      <td>DIVULGA??O DAS ATIVIDADES DA PREFEITURA JUNTO ...</td>
      <td>2018-09-25 00:00:00.0</td>
      <td>471</td>
      <td>Recursos Ordinários</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>300000.00</td>
      <td>Comunicações</td>
      <td>2018</td>
      <td>2</td>
      <td>0003817</td>
      <td>SERVIÇOS DE PUBLICIDADE E PROPAGANDA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>2</th>
      <td>VALOR QUE SE EMPENHA EM FAVOR DO CREDOR, REFER...</td>
      <td>699782.31</td>
      <td>17670503000177</td>
      <td>Prefeitura Municipal de Petrolina</td>
      <td>699782.31</td>
      <td>Comunicação Social</td>
      <td>ASSESSORIA DE GOVERNO</td>
      <td>PROMO??O E DIVULGA??O DAS A??ES DO GOVERNO MUN...</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>SALA 10 COMUNICA??O LTDA</td>
      <td>DIVULGA??O DAS ATIVIDADES DA PREFEITURA JUNTO ...</td>
      <td>2018-08-22 00:00:00.0</td>
      <td>471</td>
      <td>Recursos Ordinários</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>699782.31</td>
      <td>Comunicações</td>
      <td>2018</td>
      <td>2</td>
      <td>0003303</td>
      <td>SERVIÇOS DE PUBLICIDADE E PROPAGANDA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>3</th>
      <td>VALOR QUE SE EMPENHA EM FAVOR DO CREDOR, REFER...</td>
      <td>1249845.04</td>
      <td>17670503000177</td>
      <td>Prefeitura Municipal de Petrolina</td>
      <td>1249845.04</td>
      <td>Comunicação Social</td>
      <td>ASSESSORIA DE GOVERNO</td>
      <td>PROMO??O E DIVULGA??O DAS A??ES DO GOVERNO MUN...</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>SALA 10 COMUNICA??O LTDA</td>
      <td>DIVULGA??O DAS ATIVIDADES DA PREFEITURA JUNTO ...</td>
      <td>2018-06-08 00:00:00.0</td>
      <td>471</td>
      <td>Recursos Ordinários</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>1249845.04</td>
      <td>Comunicações</td>
      <td>2018</td>
      <td>2</td>
      <td>0002300</td>
      <td>SERVIÇOS DE PUBLICIDADE E PROPAGANDA</td>
      <td>Despesa Corrente</td>
    </tr>
    <tr>
      <th>4</th>
      <td>VALOR QUE SE EMPENHA EM FAVOR DO CREDOR, REFER...</td>
      <td>0.00</td>
      <td>17670503000177</td>
      <td>Prefeitura Municipal de Petrolina</td>
      <td>0.00</td>
      <td>Comunicação Social</td>
      <td>ASSESSORIA DE GOVERNO</td>
      <td>PROMO??O E DIVULGA??O DAS A??ES DO GOVERNO MUN...</td>
      <td>Outros Serviços de Terceiros ? Pessoa Jurídica</td>
      <td>M</td>
      <td>SALA 10 COMUNICA??O LTDA</td>
      <td>DIVULGA??O DAS ATIVIDADES DA PREFEITURA JUNTO ...</td>
      <td>2018-05-14 00:00:00.0</td>
      <td>471</td>
      <td>Recursos Ordinários</td>
      <td>Outras Despesas Correntes</td>
      <td>Aplicações Diretas</td>
      <td>0.00</td>
      <td>Comunicações</td>
      <td>2018</td>
      <td>2</td>
      <td>0001937</td>
      <td>SERVIÇOS DE PUBLICIDADE E PROPAGANDA</td>
      <td>Despesa Corrente</td>
    </tr>
  </tbody>
</table>
</div>


## TCE TO
source: https://www.tce.to.gov.br/sitetce/sessoes/resultados-de-consultas


```python

```

## TCE RR
source: https://www.tce.rr.leg.br/portal/cidadao.php


```python

```

## TCM GO
source: https://www.tcm.go.gov.br/pentaho/api/repos/%3Apublic%3ATCM%3ADashBoards%3APortalCidadao%3ADespesas.wcdf/generatedContent?=undefined&bookmarkState=%7B%22impl%22%3A%22client%22%2C%22params%22%3A%7B%22paramAno%22%3A%222019%22%2C%22paramMunicipio%22%3A%22Itabera%C3%AD%22%7D%7D


```python

```

## TCE CE
source:
https://www.tce.ce.gov.br/despesas/execucao-orcamentaria ,
http://municipios.tce.ce.gov.br/tce-municipios/ ,
http://api.tce.ce.gov.br/


```python

```

## TCM PA
source:
https://www.tcm.pa.gov.br/ ,
https://www.tcm.pa.gov.br/portal-da-transparencia/tcm-pa-transparente/despesas/01/2013#portipo


```python

```
