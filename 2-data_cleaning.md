

```python
import pandas as pd
import numpy as np
from os import listdir

pd.set_option('display.max_columns', 500)
pd.options.display.float_format = '{:.2f}'.format

data_folder = 'd:\\publicidade_oficial\\data\\'
clean_folder = 'd:\\publicidade_oficial\\clean_data\\'
```


```python
files = listdir(data_folder)
print(files)
```

    ['candidato_2008.pickle', 'candidato_2012.pickle', 'candidato_2016.pickle', 'finbra_2013.pickle', 'finbra_2014.pickle', 'finbra_2015.pickle', 'finbra_2016.pickle', 'finbra_2017.pickle', 'finbra_2018.pickle', 'idhm.pickle', 'pop2016.pickle', 'prestacao_2012.pickle', 'prestacao_2016.pickle', 'resultado_2012.pickle', 'resultado_2016.pickle', 'tce_es_2013.pickle', 'tce_es_2014.pickle', 'tce_es_2015.pickle', 'tce_es_2016.pickle', 'tce_es_2017.pickle', 'tce_es_2018.pickle', 'tce_es_2019.pickle', 'tce_pe_2013.pickle', 'tce_pe_2014.pickle', 'tce_pe_2015.pickle', 'tce_pe_2016.pickle', 'tce_pe_2017.pickle', 'tce_pe_2018.pickle', 'tce_pr_2013.pickle', 'tce_pr_2014.pickle', 'tce_pr_2015.pickle', 'tce_pr_2016.pickle', 'tce_pr_2017.pickle', 'tce_pr_2018.pickle', 'tce_rs_2013.pickle', 'tce_rs_2014.pickle', 'tce_rs_2015.pickle', 'tce_rs_2016.pickle', 'tce_rs_2017.pickle', 'tce_rs_2018.pickle', 'tce_sp_2013.pickle', 'tce_sp_2014.pickle', 'tce_sp_2015.pickle', 'tce_sp_2016.pickle', 'tce_sp_2017.pickle', 'tce_sp_2018.pickle']
    


```python
finbra = pd.DataFrame([])
for year in range(2013,2019):
    finbra_temp = pd.read_pickle(data_folder + 'finbra_'+str(year)+'.pickle')
    finbra = finbra.append(finbra_temp)
finbra = finbra[(finbra['Conta'] == '04.131 - Comunicação Social') & (finbra['Coluna'] == 'Despesas Empenhadas')]
finbra.to_pickle(clean_folder+'finbra.pickle')
finbra.head()
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
      <th>674</th>
      <td>SÃO LEOPOLDO-RS</td>
      <td>4318705</td>
      <td>RS</td>
      <td>225520</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>176863.60</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1279</th>
      <td>BELMONTE-BA</td>
      <td>2903409</td>
      <td>BA</td>
      <td>23471</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>9190.00</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>2224</th>
      <td>COTIPORÃ-RS</td>
      <td>4305959</td>
      <td>RS</td>
      <td>4019</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>10480.00</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>3519</th>
      <td>VENÂNCIO AIRES-RS</td>
      <td>4322608</td>
      <td>RS</td>
      <td>69154</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>442098.34</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>4048</th>
      <td>DAVID CANABARRO-RS</td>
      <td>4306304</td>
      <td>RS</td>
      <td>4834</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>56817.34</td>
      <td>2013</td>
    </tr>
  </tbody>
</table>
</div>




```python
idhm = pd.read_pickle(data_folder+'idhm.pickle')
```


```python
idhm.head()
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
      <td>1795.00</td>
      <td>5081</td>
      <td>13.40</td>
      <td>4751</td>
      <td>0.71</td>
      <td>0.69</td>
      <td>0.83</td>
      <td>0.62</td>
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
      <td>2515.00</td>
      <td>4189</td>
      <td>14.80</td>
      <td>5046</td>
      <td>0.69</td>
      <td>0.69</td>
      <td>0.84</td>
      <td>0.56</td>
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
      <td>4979.00</td>
      <td>10778</td>
      <td>12.60</td>
      <td>11076</td>
      <td>0.69</td>
      <td>0.67</td>
      <td>0.84</td>
      <td>0.58</td>
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
      <td>2986.00</td>
      <td>19704</td>
      <td>14.00</td>
      <td>16733</td>
      <td>0.70</td>
      <td>0.72</td>
      <td>0.85</td>
      <td>0.56</td>
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
      <td>58102.00</td>
      <td>82998</td>
      <td>19.00</td>
      <td>87585</td>
      <td>0.63</td>
      <td>0.58</td>
      <td>0.80</td>
      <td>0.54</td>
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




```python
finbra.columns=['mun_uf','cod_ibge_dv','uf','pop_finbra','coluna','conta','publicidade','ano']
```


```python
finbra.head()
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
      <th>mun_uf</th>
      <th>cod_ibge_dv</th>
      <th>uf</th>
      <th>pop_finbra</th>
      <th>coluna</th>
      <th>conta</th>
      <th>publicidade</th>
      <th>ano</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>674</th>
      <td>SÃO LEOPOLDO-RS</td>
      <td>4318705</td>
      <td>RS</td>
      <td>225520</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>176863.60</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>1279</th>
      <td>BELMONTE-BA</td>
      <td>2903409</td>
      <td>BA</td>
      <td>23471</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>9190.00</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>2224</th>
      <td>COTIPORÃ-RS</td>
      <td>4305959</td>
      <td>RS</td>
      <td>4019</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>10480.00</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>3519</th>
      <td>VENÂNCIO AIRES-RS</td>
      <td>4322608</td>
      <td>RS</td>
      <td>69154</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>442098.34</td>
      <td>2013</td>
    </tr>
    <tr>
      <th>4048</th>
      <td>DAVID CANABARRO-RS</td>
      <td>4306304</td>
      <td>RS</td>
      <td>4834</td>
      <td>Despesas Empenhadas</td>
      <td>04.131 - Comunicação Social</td>
      <td>56817.34</td>
      <td>2013</td>
    </tr>
  </tbody>
</table>
</div>




```python
finbra_wide = finbra.pivot_table(index=['cod_ibge_dv','mun_uf','uf'], columns='ano', values='publicidade').reset_index()
```


```python
finbra_wide.columns = ['cod_ibge_dv','mun_uf','uf','pub_finbra_2013','pub_finbra_2014','pub_finbra_2015','pub_finbra_2016','pub_finbra_2017','pub_finbra_2018']
finbra_wide.head()
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
      <th>cod_ibge_dv</th>
      <th>mun_uf</th>
      <th>uf</th>
      <th>pub_finbra_2013</th>
      <th>pub_finbra_2014</th>
      <th>pub_finbra_2015</th>
      <th>pub_finbra_2016</th>
      <th>pub_finbra_2017</th>
      <th>pub_finbra_2018</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1100023</td>
      <td>ARIQUEMES-RO</td>
      <td>RO</td>
      <td>nan</td>
      <td>nan</td>
      <td>431626.93</td>
      <td>129269.20</td>
      <td>nan</td>
      <td>9008.57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1100205</td>
      <td>PORTO VELHO-RO</td>
      <td>RO</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>1904398.23</td>
      <td>5189988.64</td>
      <td>nan</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1100320</td>
      <td>SÃO MIGUEL DO GUAPORÉ-RO</td>
      <td>RO</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>560.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1200179</td>
      <td>CAPIXABA-AC</td>
      <td>AC</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>20865.00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1200203</td>
      <td>CRUZEIRO DO SUL-AC</td>
      <td>AC</td>
      <td>296894.60</td>
      <td>277372.19</td>
      <td>249416.80</td>
      <td>270710.00</td>
      <td>235202.30</td>
      <td>nan</td>
    </tr>
  </tbody>
</table>
</div>




```python
finbra_idhm = pd.merge(finbra_wide,idhm, on='cod_ibge_dv', how='outer',suffixes=('a','b')).sort_values('cod_ibge_dv')
```


```python
finbra_idhm[finbra_idhm['pop2010'] > 1000000]
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
      <th>cod_ibge_dv</th>
      <th>mun_uf</th>
      <th>uf</th>
      <th>pub_finbra_2013</th>
      <th>pub_finbra_2014</th>
      <th>pub_finbra_2015</th>
      <th>pub_finbra_2016</th>
      <th>pub_finbra_2017</th>
      <th>pub_finbra_2018</th>
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
      <th>10</th>
      <td>1302603</td>
      <td>MANAUS-AM</td>
      <td>AM</td>
      <td>40676129.72</td>
      <td>68158844.05</td>
      <td>83072618.35</td>
      <td>53606380.97</td>
      <td>85352443.72</td>
      <td>90727708.03</td>
      <td>Manaus</td>
      <td>0.61</td>
      <td>1802014.00</td>
      <td>9133.00</td>
      <td>1792881.00</td>
      <td>14.24</td>
      <td>1181978.00</td>
      <td>0.74</td>
      <td>0.74</td>
      <td>0.83</td>
      <td>0.66</td>
      <td>106588.00</td>
      <td>67.93</td>
      <td>790.27</td>
      <td>792.86</td>
      <td>12.90</td>
      <td>33.50</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1501402</td>
      <td>BELÉM-PA</td>
      <td>PA</td>
      <td>11471943.74</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>Belém</td>
      <td>0.61</td>
      <td>1393399.00</td>
      <td>11924.00</td>
      <td>1381475.00</td>
      <td>16.06</td>
      <td>992891.00</td>
      <td>0.75</td>
      <td>0.75</td>
      <td>0.82</td>
      <td>0.67</td>
      <td>76555.00</td>
      <td>69.19</td>
      <td>853.82</td>
      <td>856.28</td>
      <td>13.04</td>
      <td>33.26</td>
    </tr>
    <tr>
      <th>144</th>
      <td>2111300</td>
      <td>SÃO LUÍS-MA</td>
      <td>MA</td>
      <td>15225692.07</td>
      <td>25074953.18</td>
      <td>15374678.43</td>
      <td>2785039.43</td>
      <td>19640622.54</td>
      <td>nan</td>
      <td>São Luís</td>
      <td>0.61</td>
      <td>1014837.00</td>
      <td>56315.00</td>
      <td>958522.00</td>
      <td>18.10</td>
      <td>716183.00</td>
      <td>0.77</td>
      <td>0.74</td>
      <td>0.81</td>
      <td>0.75</td>
      <td>58471.00</td>
      <td>73.45</td>
      <td>805.36</td>
      <td>811.56</td>
      <td>13.81</td>
      <td>35.27</td>
    </tr>
    <tr>
      <th>2874</th>
      <td>2304400</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>Fortaleza</td>
      <td>0.61</td>
      <td>2452185.00</td>
      <td>nan</td>
      <td>2452185.00</td>
      <td>15.76</td>
      <td>1762036.00</td>
      <td>0.75</td>
      <td>0.75</td>
      <td>0.82</td>
      <td>0.69</td>
      <td>136540.00</td>
      <td>65.83</td>
      <td>846.36</td>
      <td>847.35</td>
      <td>12.14</td>
      <td>32.88</td>
    </tr>
    <tr>
      <th>455</th>
      <td>2611606</td>
      <td>RECIFE-PE</td>
      <td>PE</td>
      <td>2208411.37</td>
      <td>31185237.48</td>
      <td>43261563.05</td>
      <td>26433536.84</td>
      <td>31924962.14</td>
      <td>64976703.99</td>
      <td>Recife</td>
      <td>0.68</td>
      <td>1537704.00</td>
      <td>nan</td>
      <td>1537704.00</td>
      <td>15.56</td>
      <td>1139613.00</td>
      <td>0.77</td>
      <td>0.80</td>
      <td>0.82</td>
      <td>0.70</td>
      <td>75194.00</td>
      <td>66.35</td>
      <td>1144.26</td>
      <td>1145.56</td>
      <td>13.20</td>
      <td>32.91</td>
    </tr>
    <tr>
      <th>638</th>
      <td>2927408</td>
      <td>SALVADOR-BA</td>
      <td>BA</td>
      <td>589904.25</td>
      <td>366226.40</td>
      <td>129384.00</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>Salvador</td>
      <td>0.63</td>
      <td>2675656.00</td>
      <td>733.00</td>
      <td>2674923.00</td>
      <td>14.92</td>
      <td>1992945.00</td>
      <td>0.76</td>
      <td>0.77</td>
      <td>0.83</td>
      <td>0.68</td>
      <td>130991.00</td>
      <td>69.72</td>
      <td>973.00</td>
      <td>973.97</td>
      <td>11.35</td>
      <td>30.24</td>
    </tr>
    <tr>
      <th>690</th>
      <td>3106200</td>
      <td>BELO HORIZONTE-MG</td>
      <td>MG</td>
      <td>40296606.67</td>
      <td>41919201.32</td>
      <td>36088691.86</td>
      <td>26892082.71</td>
      <td>45341402.51</td>
      <td>47037260.62</td>
      <td>Belo Horizonte</td>
      <td>0.60</td>
      <td>2375151.00</td>
      <td>nan</td>
      <td>2375151.00</td>
      <td>12.95</td>
      <td>1815703.00</td>
      <td>0.81</td>
      <td>0.84</td>
      <td>0.86</td>
      <td>0.74</td>
      <td>107820.00</td>
      <td>70.15</td>
      <td>1497.29</td>
      <td>1497.46</td>
      <td>3.80</td>
      <td>13.89</td>
    </tr>
    <tr>
      <th>951</th>
      <td>3304557</td>
      <td>RIO DE JANEIRO-RJ</td>
      <td>RJ</td>
      <td>81051702.86</td>
      <td>91930000.00</td>
      <td>133680360.89</td>
      <td>69780407.30</td>
      <td>58104060.52</td>
      <td>42014345.61</td>
      <td>Rio de Janeiro</td>
      <td>0.62</td>
      <td>6320446.00</td>
      <td>nan</td>
      <td>6320446.00</td>
      <td>13.02</td>
      <td>4812491.00</td>
      <td>0.80</td>
      <td>0.84</td>
      <td>0.84</td>
      <td>0.72</td>
      <td>275422.00</td>
      <td>72.19</td>
      <td>1492.63</td>
      <td>1493.25</td>
      <td>5.01</td>
      <td>16.41</td>
    </tr>
    <tr>
      <th>986</th>
      <td>3509502</td>
      <td>CAMPINAS-SP</td>
      <td>SP</td>
      <td>10007419.53</td>
      <td>25406889.42</td>
      <td>29206403.96</td>
      <td>14376719.65</td>
      <td>18207015.20</td>
      <td>17283444.17</td>
      <td>Campinas</td>
      <td>0.56</td>
      <td>1080113.00</td>
      <td>18573.00</td>
      <td>1061540.00</td>
      <td>11.81</td>
      <td>823500.00</td>
      <td>0.81</td>
      <td>0.83</td>
      <td>0.86</td>
      <td>0.73</td>
      <td>48202.00</td>
      <td>67.71</td>
      <td>1390.83</td>
      <td>1391.53</td>
      <td>3.16</td>
      <td>11.39</td>
    </tr>
    <tr>
      <th>1009</th>
      <td>3518800</td>
      <td>GUARULHOS-SP</td>
      <td>SP</td>
      <td>6328109.19</td>
      <td>19357470.86</td>
      <td>750191.68</td>
      <td>4412220.49</td>
      <td>787176.48</td>
      <td>nan</td>
      <td>Guarulhos</td>
      <td>0.51</td>
      <td>1221979.00</td>
      <td>nan</td>
      <td>1221979.00</td>
      <td>13.34</td>
      <td>858748.00</td>
      <td>0.76</td>
      <td>0.75</td>
      <td>0.83</td>
      <td>0.72</td>
      <td>61689.00</td>
      <td>63.85</td>
      <td>829.91</td>
      <td>830.29</td>
      <td>6.50</td>
      <td>20.63</td>
    </tr>
    <tr>
      <th>1076</th>
      <td>3550308</td>
      <td>SÃO PAULO-SP</td>
      <td>SP</td>
      <td>6374213.84</td>
      <td>113576319.72</td>
      <td>6000000.00</td>
      <td>5992267.78</td>
      <td>3317374.61</td>
      <td>3402926.72</td>
      <td>São Paulo</td>
      <td>0.62</td>
      <td>11253503.00</td>
      <td>101159.00</td>
      <td>11152344.00</td>
      <td>13.15</td>
      <td>8407202.00</td>
      <td>0.81</td>
      <td>0.84</td>
      <td>0.85</td>
      <td>0.72</td>
      <td>498886.00</td>
      <td>67.68</td>
      <td>1516.21</td>
      <td>1517.04</td>
      <td>4.27</td>
      <td>14.69</td>
    </tr>
    <tr>
      <th>1131</th>
      <td>4106902</td>
      <td>CURITIBA-PR</td>
      <td>PR</td>
      <td>10738515.56</td>
      <td>11201101.94</td>
      <td>19022662.90</td>
      <td>46500.00</td>
      <td>10467879.51</td>
      <td>18042000.00</td>
      <td>Curitiba</td>
      <td>0.55</td>
      <td>1751907.00</td>
      <td>nan</td>
      <td>1751907.00</td>
      <td>11.91</td>
      <td>1319777.00</td>
      <td>0.82</td>
      <td>0.85</td>
      <td>0.85</td>
      <td>0.77</td>
      <td>82326.00</td>
      <td>73.96</td>
      <td>1581.04</td>
      <td>1581.33</td>
      <td>1.73</td>
      <td>7.86</td>
    </tr>
    <tr>
      <th>1367</th>
      <td>4314902</td>
      <td>PORTO ALEGRE-RS</td>
      <td>RS</td>
      <td>nan</td>
      <td>12443759.50</td>
      <td>12907433.53</td>
      <td>4099494.76</td>
      <td>1962091.99</td>
      <td>3980241.22</td>
      <td>Porto Alegre</td>
      <td>0.60</td>
      <td>1409351.00</td>
      <td>nan</td>
      <td>1409351.00</td>
      <td>11.60</td>
      <td>1083537.00</td>
      <td>0.81</td>
      <td>0.87</td>
      <td>0.86</td>
      <td>0.70</td>
      <td>60414.00</td>
      <td>74.78</td>
      <td>1758.27</td>
      <td>1758.86</td>
      <td>3.82</td>
      <td>12.51</td>
    </tr>
    <tr>
      <th>1538</th>
      <td>5208707</td>
      <td>GOIÂNIA-GO</td>
      <td>GO</td>
      <td>14969896.38</td>
      <td>10072058.76</td>
      <td>10750938.60</td>
      <td>5174835.66</td>
      <td>17214405.66</td>
      <td>11245032.92</td>
      <td>Goiânia</td>
      <td>0.58</td>
      <td>1302001.00</td>
      <td>4925.00</td>
      <td>1297076.00</td>
      <td>13.11</td>
      <td>966427.00</td>
      <td>0.80</td>
      <td>0.82</td>
      <td>0.84</td>
      <td>0.74</td>
      <td>66389.00</td>
      <td>71.44</td>
      <td>1348.55</td>
      <td>1348.70</td>
      <td>3.09</td>
      <td>12.70</td>
    </tr>
    <tr>
      <th>2109</th>
      <td>5300108</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>nan</td>
      <td>Brasília</td>
      <td>0.63</td>
      <td>2570160.00</td>
      <td>87950.00</td>
      <td>2482210.00</td>
      <td>14.01</td>
      <td>1829600.00</td>
      <td>0.82</td>
      <td>0.86</td>
      <td>0.87</td>
      <td>0.74</td>
      <td>130855.00</td>
      <td>72.32</td>
      <td>1715.11</td>
      <td>1716.52</td>
      <td>4.93</td>
      <td>16.00</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
