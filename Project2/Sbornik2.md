```python
import os
import pandas as pd
import numpy as np

from sqlalchemy import create_engine
```


```python
path_to_db_local = 'telecomm_csi.db'
path_to_db_platform = '/datasets/telecomm_csi.db'
path_to_db = None

if os.path.exists(path_to_db_local):
    path_to_db = path_to_db_local
elif os.path.exists(path_to_db_platform):
    path_to_db = path_to_db_platform
else:
    raise Exception('Файл с базой данных SQLite не найден!')

if path_to_db:
    engine = create_engine(f'sqlite:///{path_to_db}', echo=False)
```


```python
query = """
SELECT *
    FROM user;
"""
```


```python
query = """
SELECT user_id, lt_day, 
CASE
    WHEN lt_day <= 365 THEN 'new'
    WHEN lt_day > 365 THEN 'old'
END AS is_new, 
age, gender_segment, os_name, cpe_type_name, loc.country, loc.city, a.title AS age_segment, 
tr.title AS traffic_segment, lt.title AS lifetime_segment, nps_score,
CASE
    WHEN nps_score > 0 AND nps_score <= 6 THEN 'detractors'
    WHEN nps_score > 6 AND nps_score <= 8 THEN 'passives'
    WHEN nps_score > 8 AND nps_score <= 10 THEN 'promoters'
END AS nps_group
FROM user AS u LEFT JOIN (SELECT country, city, location_id
                       FROM location)
AS loc ON u.location_id = loc.location_id
LEFT JOIN (SELECT title, age_gr_id
        FROM age_segment)
AS a ON u.age_gr_id = a.age_gr_id
LEFT JOIN (SELECT title, tr_gr_id
        FROM traffic_segment)
AS tr ON u.tr_gr_id = tr.tr_gr_id
LEFT JOIN (SELECT title, lt_gr_id
        FROM lifetime_segment)
AS lt ON u.lt_gr_id = lt.lt_gr_id
"""
```


```python
df = pd.read_sql(query,engine)
df.head(20)
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
      <th>user_id</th>
      <th>lt_day</th>
      <th>is_new</th>
      <th>age</th>
      <th>gender_segment</th>
      <th>os_name</th>
      <th>cpe_type_name</th>
      <th>country</th>
      <th>city</th>
      <th>age_segment</th>
      <th>traffic_segment</th>
      <th>lifetime_segment</th>
      <th>nps_score</th>
      <th>nps_group</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A001A2</td>
      <td>2320</td>
      <td>old</td>
      <td>45.0</td>
      <td>1.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Уфа</td>
      <td>05 45-54</td>
      <td>04 1-5</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A001WF</td>
      <td>2344</td>
      <td>old</td>
      <td>53.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Киров</td>
      <td>05 45-54</td>
      <td>04 1-5</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A003Q7</td>
      <td>467</td>
      <td>old</td>
      <td>57.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Москва</td>
      <td>06 55-64</td>
      <td>08 20-25</td>
      <td>06 13-24</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A004TB</td>
      <td>4190</td>
      <td>old</td>
      <td>44.0</td>
      <td>1.0</td>
      <td>IOS</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>РостовнаДону</td>
      <td>04 35-44</td>
      <td>03 0.1-1</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A004XT</td>
      <td>1163</td>
      <td>old</td>
      <td>24.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Рязань</td>
      <td>02 16-24</td>
      <td>05 5-10</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>5</th>
      <td>A005O0</td>
      <td>5501</td>
      <td>old</td>
      <td>42.0</td>
      <td>1.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Омск</td>
      <td>04 35-44</td>
      <td>05 5-10</td>
      <td>08 36+</td>
      <td>6</td>
      <td>detractors</td>
    </tr>
    <tr>
      <th>6</th>
      <td>A0061R</td>
      <td>1236</td>
      <td>old</td>
      <td>45.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Уфа</td>
      <td>05 45-54</td>
      <td>06 10-15</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>7</th>
      <td>A009KS</td>
      <td>313</td>
      <td>new</td>
      <td>35.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Москва</td>
      <td>04 35-44</td>
      <td>13 45-50</td>
      <td>05 7-12</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>8</th>
      <td>A00AES</td>
      <td>3238</td>
      <td>old</td>
      <td>36.0</td>
      <td>1.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>СанктПетербург</td>
      <td>04 35-44</td>
      <td>04 1-5</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>9</th>
      <td>A00F70</td>
      <td>4479</td>
      <td>old</td>
      <td>54.0</td>
      <td>1.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Волгоград</td>
      <td>05 45-54</td>
      <td>07 15-20</td>
      <td>08 36+</td>
      <td>9</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>10</th>
      <td>A00HL5</td>
      <td>5297</td>
      <td>old</td>
      <td>39.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Тольятти</td>
      <td>04 35-44</td>
      <td>09 25-30</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>11</th>
      <td>A00NDN</td>
      <td>1374</td>
      <td>old</td>
      <td>21.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Рязань</td>
      <td>02 16-24</td>
      <td>06 10-15</td>
      <td>08 36+</td>
      <td>2</td>
      <td>detractors</td>
    </tr>
    <tr>
      <th>12</th>
      <td>A00P46</td>
      <td>179</td>
      <td>new</td>
      <td>27.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Казань</td>
      <td>03 25-34</td>
      <td>13 45-50</td>
      <td>04 4-6</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>13</th>
      <td>A00SA7</td>
      <td>667</td>
      <td>old</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>TABLET</td>
      <td>Россия</td>
      <td>Казань</td>
      <td>06 55-64</td>
      <td>05 5-10</td>
      <td>06 13-24</td>
      <td>5</td>
      <td>detractors</td>
    </tr>
    <tr>
      <th>14</th>
      <td>A00U8A</td>
      <td>2908</td>
      <td>old</td>
      <td>44.0</td>
      <td>1.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>СанктПетербург</td>
      <td>04 35-44</td>
      <td>05 5-10</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>15</th>
      <td>A0116Q</td>
      <td>2517</td>
      <td>old</td>
      <td>34.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Самара</td>
      <td>03 25-34</td>
      <td>07 15-20</td>
      <td>08 36+</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>16</th>
      <td>A015CX</td>
      <td>4073</td>
      <td>old</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Самара</td>
      <td>05 45-54</td>
      <td>12 40-45</td>
      <td>08 36+</td>
      <td>9</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>17</th>
      <td>A017L3</td>
      <td>372</td>
      <td>old</td>
      <td>37.0</td>
      <td>0.0</td>
      <td>IOS</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Красноярск</td>
      <td>04 35-44</td>
      <td>05 5-10</td>
      <td>06 13-24</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
    <tr>
      <th>18</th>
      <td>A017QF</td>
      <td>6155</td>
      <td>old</td>
      <td>42.0</td>
      <td>1.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Екатеринбург</td>
      <td>04 35-44</td>
      <td>05 5-10</td>
      <td>08 36+</td>
      <td>8</td>
      <td>passives</td>
    </tr>
    <tr>
      <th>19</th>
      <td>A018WZ</td>
      <td>709</td>
      <td>old</td>
      <td>43.0</td>
      <td>0.0</td>
      <td>ANDROID</td>
      <td>SMARTPHONE</td>
      <td>Россия</td>
      <td>Калуга</td>
      <td>04 35-44</td>
      <td>08 20-25</td>
      <td>06 13-24</td>
      <td>10</td>
      <td>promoters</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.to_csv('telecomm_csi_tableau.csv', index=False)
```

[Tableu дашборды](https://public.tableau.com/views/NPSresearch_17073679811040/Story1?:language=en-GB&:display_count=n&:origin=viz_share_link)

[Презентация](https://drive.google.com/file/d/1jIaPNvAC1n3q40lb_SVHIgsTgz1IWWMT/view?usp=sharing)
