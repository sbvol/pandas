
# Loading dependents
***


```python
import pandas as pd
import numpy as np
```

# Reading files
***


```python
school_data = 'Downloads/school_data.csv'
student_data = 'Downloads/student_data.csv'
```


```python
school_data_df = pd.read_csv(school_data, encoding = 'ISO-8859-1')
student_data_df = pd.read_csv(student_data, encoding = 'ISO-8859-1')
```

# Performing district report calculations
***


```python
school_data_df = school_data_df.rename(columns={'name':'school'})
total_school_budget = school_data_df['budget'].sum()
number_schools = school_data_df['school'].count()
number_students = student_data_df['name'].count()
avg_math_score = student_data_df['math_score'].mean()
avg_reading_score = student_data_df['reading_score'].mean()
number_passing_math = student_data_df[student_data_df.loc[:, 'math_score'] > 70].count()['name']
number_passing_reading = student_data_df[student_data_df.loc[:, 'reading_score'] > 70].count()['name']
pct_students_passing_math = number_passing_math / number_students
pct_students_passing_reading = number_passing_reading / number_students
overall_passing_rate = (pct_students_passing_math + pct_students_passing_reading) / 2
```

# District report variable formatting
***


```python
pct_students_passing_math = '{:,.2%}'.format(pct_students_passing_math)
pct_students_passing_reading = '{:,.2%}'.format(pct_students_passing_reading)
overall_passing_rate = '{:,.2%}'.format(overall_passing_rate)
avg_math_score = '%.1f' % avg_math_score
avg_reading_score = '%.1f' % avg_reading_score
```

# District report output/formating
***


```python
#Output to screen
district_report = pd.DataFrame({'Total Schools': [number_schools],
                                'Total Students':[number_students],
                                'Total Budget':[total_school_budget],
                                'Avg Math Score':[avg_math_score],
                                'Avg Reading Score':[avg_reading_score],
                                '% Passing Math':[pct_students_passing_math],
                                '% Passing Reading':[pct_students_passing_reading],
                                'Overall Passing Rate':[overall_passing_rate]})
district_report = district_report[['Total Schools',
                                   'Total Students',
                                   'Total Budget',
                                   'Avg Math Score',
                                   'Avg Reading Score',
                                   '% Passing Math',
                                   '% Passing Reading',
                                   'Overall Passing Rate']]
district_report
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Avg Math Score</th>
      <th>Avg Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39170</td>
      <td>24649428</td>
      <td>79.0</td>
      <td>81.9</td>
      <td>72.39%</td>
      <td>82.97%</td>
      <td>77.68%</td>
    </tr>
  </tbody>
</table>
</div>



# Writing output to an excel file
***


```python
# Export to excel and remove index
district_report.to_excel("Downloads/District_report.xlsx", index=False)
```

# School Summary Report - Calculations
***


```python
sorted_school_data = school_data_df.sort_values('school')
sorted_school_data['per student budget'] = sorted_school_data['budget'] / sorted_school_data['size']
```


```python
temp_school_report = sorted_school_data[['school', 'type', 'size', 'budget', 'per student budget']]
```


```python
avg_math_score = student_data_df.groupby(by='school', as_index=False)['math_score'].mean()
```


```python
temp_school_report = pd.merge(temp_school_report, avg_math_score, on='school')
```


```python
temp_school_report = temp_school_report.rename(columns={'math_score':'avg math score'})
```


```python
avg_reading_score = student_data_df.groupby(by='school', as_index=False)['reading_score'].mean()
```


```python
temp_school_report = pd.merge(temp_school_report, avg_reading_score, on='school')
```


```python
temp_school_report = temp_school_report.rename(columns={'reading_score':'avg reading score'})
```


```python
passing_math = student_data_df[student_data_df['math_score'] > 70]
passing_math = passing_math.groupby(by='school', as_index=False)['math_score'].count()
passing_math = passing_math.rename(columns={'math_score':'# passing math'})
```


```python
temp_school_report = pd.merge(temp_school_report, passing_math, on='school')
temp_school_report['% passing math'] = temp_school_report['# passing math'] / temp_school_report['size']
```


```python
passing_reading = student_data_df[student_data_df['reading_score'] > 70]
passing_reading = passing_reading.groupby(by='school', as_index=False)['reading_score'].count()
passing_reading = passing_reading.rename(columns={'reading_score':'# passing reading'})
```


```python
temp_school_report = pd.merge(temp_school_report, passing_reading, on='school')
temp_school_report['% passing reading'] = temp_school_report['# passing reading'] / temp_school_report['size']
```


```python
temp_school_report['% overall passing'] = (temp_school_report['% passing math'] + temp_school_report['% passing reading']) / 2
```

# School Report Output
***


```python
final_school_report = temp_school_report[['school',
                                           'type',
                                           'size',
                                           'budget',
                                           'per student budget',
                                           'avg math score',
                                           'avg reading score',
                                           '% passing math',
                                           '% passing reading',
                                           '% overall passing']]
final_school_report
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.048432</td>
      <td>81.033963</td>
      <td>0.646302</td>
      <td>0.793006</td>
      <td>0.719654</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.061895</td>
      <td>83.975780</td>
      <td>0.895587</td>
      <td>0.938644</td>
      <td>0.917115</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.711767</td>
      <td>81.158020</td>
      <td>0.637504</td>
      <td>0.784334</td>
      <td>0.710919</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.102592</td>
      <td>80.746258</td>
      <td>0.657539</td>
      <td>0.775100</td>
      <td>0.716320</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.351499</td>
      <td>83.816757</td>
      <td>0.897139</td>
      <td>0.933924</td>
      <td>0.915531</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.289752</td>
      <td>80.934412</td>
      <td>0.647465</td>
      <td>0.781877</td>
      <td>0.714671</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.803279</td>
      <td>83.814988</td>
      <td>0.906323</td>
      <td>0.927400</td>
      <td>0.916862</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.629414</td>
      <td>81.182722</td>
      <td>0.633185</td>
      <td>0.788138</td>
      <td>0.710662</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.072464</td>
      <td>80.966394</td>
      <td>0.638521</td>
      <td>0.782819</td>
      <td>0.710670</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.839917</td>
      <td>84.044699</td>
      <td>0.916840</td>
      <td>0.922037</td>
      <td>0.919439</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.842711</td>
      <td>80.744686</td>
      <td>0.640660</td>
      <td>0.777444</td>
      <td>0.709052</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.359455</td>
      <td>83.725724</td>
      <td>0.898921</td>
      <td>0.926178</td>
      <td>0.912550</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.418349</td>
      <td>83.848930</td>
      <td>0.902141</td>
      <td>0.929052</td>
      <td>0.915596</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.274201</td>
      <td>83.989488</td>
      <td>0.909330</td>
      <td>0.932545</td>
      <td>0.920937</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.682222</td>
      <td>83.955000</td>
      <td>0.902778</td>
      <td>0.934444</td>
      <td>0.918611</td>
    </tr>
  </tbody>
</table>
</div>



# Formating entire table with percents and decimals
***


```python
final_school_report['avg math score'] = pd.Series(round(val, 1) for val in final_school_report.loc[:, 'avg math score'])
final_school_report['avg reading score'] = pd.Series(round(val, 1) for val in final_school_report.loc[:, 'avg reading score'])
final_school_report['% passing math'] = pd.Series("{0:.1f}%".format(val * 100) for val in final_school_report.loc[:, '% passing math'])
final_school_report['% passing reading'] = pd.Series("{0:.1f}%".format(val * 100) for val in final_school_report.loc[:, '% passing reading'])
final_school_report['% overall passing'] = pd.Series("{0:.1f}%".format(val * 100) for val in final_school_report.loc[:, '% overall passing'])
final_school_report
```

    /Users/jeffmoore/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """Entry point for launching an IPython kernel.
    /Users/jeffmoore/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      
    /Users/jeffmoore/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      This is separate from the ipykernel package so we can avoid doing imports until
    /Users/jeffmoore/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      after removing the cwd from sys.path.
    /Users/jeffmoore/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>77.0</td>
      <td>81.0</td>
      <td>64.6%</td>
      <td>79.3%</td>
      <td>72.0%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>89.6%</td>
      <td>93.9%</td>
      <td>91.7%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>63.8%</td>
      <td>78.4%</td>
      <td>71.1%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>77.1</td>
      <td>80.7</td>
      <td>65.8%</td>
      <td>77.5%</td>
      <td>71.6%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>89.7%</td>
      <td>93.4%</td>
      <td>91.6%</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.3</td>
      <td>80.9</td>
      <td>64.7%</td>
      <td>78.2%</td>
      <td>71.5%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.8</td>
      <td>83.8</td>
      <td>90.6%</td>
      <td>92.7%</td>
      <td>91.7%</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>63.3%</td>
      <td>78.8%</td>
      <td>71.1%</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>63.9%</td>
      <td>78.3%</td>
      <td>71.1%</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>91.7%</td>
      <td>92.2%</td>
      <td>91.9%</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>64.1%</td>
      <td>77.7%</td>
      <td>70.9%</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.4</td>
      <td>83.7</td>
      <td>89.9%</td>
      <td>92.6%</td>
      <td>91.3%</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>90.2%</td>
      <td>92.9%</td>
      <td>91.6%</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>90.9%</td>
      <td>93.3%</td>
      <td>92.1%</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.7</td>
      <td>84.0</td>
      <td>90.3%</td>
      <td>93.4%</td>
      <td>91.9%</td>
    </tr>
  </tbody>
</table>
</div>



# Best Performing Schools (by % overall passing)
***


```python
top_5_schools = final_school_report.sort_values('% overall passing', ascending=False)
top_5_schools.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>90.9%</td>
      <td>93.3%</td>
      <td>92.1%</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>91.7%</td>
      <td>92.2%</td>
      <td>91.9%</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.7</td>
      <td>84.0</td>
      <td>90.3%</td>
      <td>93.4%</td>
      <td>91.9%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>89.6%</td>
      <td>93.9%</td>
      <td>91.7%</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.8</td>
      <td>83.8</td>
      <td>90.6%</td>
      <td>92.7%</td>
      <td>91.7%</td>
    </tr>
  </tbody>
</table>
</div>



# Worst Performing Schools (by % overall passing)
***


```python
bottom_5_schools = final_school_report.sort_values('% overall passing', ascending=False)
bottom_5_schools.tail(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>77.3</td>
      <td>80.9</td>
      <td>64.7%</td>
      <td>78.2%</td>
      <td>71.5%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>63.8%</td>
      <td>78.4%</td>
      <td>71.1%</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>63.3%</td>
      <td>78.8%</td>
      <td>71.1%</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>63.9%</td>
      <td>78.3%</td>
      <td>71.1%</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>64.1%</td>
      <td>77.7%</td>
      <td>70.9%</td>
    </tr>
  </tbody>
</table>
</div>



# Math Scores by Grade (by School)
***


```python
math_scores = student_data_df.groupby(['school','grade'], as_index=False)['math_score'].mean()
math_scores = math_scores.pivot(index='school', columns='grade')['math_score']
math_scores = math_scores[['9th', '10th', '11th', '12th']]
math_scores['9th'] = math_scores['9th'].map('{:,.1f}'.format)
math_scores['10th'] = math_scores['10th'].map('{:,.1f}'.format)
math_scores['11th'] = math_scores['11th'].map('{:,.1f}'.format)
math_scores['12th'] = math_scores['12th'].map('{:,.1f}'.format)
math_scores
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77.1</td>
      <td>77.0</td>
      <td>77.5</td>
      <td>76.5</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.1</td>
      <td>83.2</td>
      <td>82.8</td>
      <td>83.3</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.4</td>
      <td>76.5</td>
      <td>76.9</td>
      <td>77.2</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.4</td>
      <td>77.7</td>
      <td>76.9</td>
      <td>76.2</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>82.0</td>
      <td>84.2</td>
      <td>83.8</td>
      <td>83.4</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.4</td>
      <td>77.3</td>
      <td>77.1</td>
      <td>77.2</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.8</td>
      <td>83.4</td>
      <td>85.0</td>
      <td>82.9</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>77.0</td>
      <td>75.9</td>
      <td>76.4</td>
      <td>77.2</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77.2</td>
      <td>76.7</td>
      <td>77.5</td>
      <td>76.9</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.6</td>
      <td>83.4</td>
      <td>84.3</td>
      <td>84.1</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.9</td>
      <td>76.6</td>
      <td>76.4</td>
      <td>77.7</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.4</td>
      <td>82.9</td>
      <td>83.4</td>
      <td>83.8</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.6</td>
      <td>83.1</td>
      <td>83.5</td>
      <td>83.5</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.1</td>
      <td>83.7</td>
      <td>83.2</td>
      <td>83.0</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.3</td>
      <td>84.0</td>
      <td>83.8</td>
      <td>83.6</td>
    </tr>
  </tbody>
</table>
</div>



# Reading Scores by Grade (by School)
***


```python
reading_scores = student_data_df.groupby(['school','grade'], as_index=False)['reading_score'].mean()
reading_scores = reading_scores.pivot(index='school', columns='grade')['reading_score']
reading_scores = reading_scores[['9th', '10th', '11th', '12th']]
reading_scores['9th'] = reading_scores['9th'].map('{:,.1f}'.format)
reading_scores['10th'] = reading_scores['10th'].map('{:,.1f}'.format)
reading_scores['11th'] = reading_scores['11th'].map('{:,.1f}'.format)
reading_scores['12th'] = reading_scores['12th'].map('{:,.1f}'.format)
reading_scores
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>grade</th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81.3</td>
      <td>80.9</td>
      <td>80.9</td>
      <td>80.9</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.7</td>
      <td>84.3</td>
      <td>83.8</td>
      <td>84.3</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.2</td>
      <td>81.4</td>
      <td>80.6</td>
      <td>81.4</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>80.6</td>
      <td>81.3</td>
      <td>80.4</td>
      <td>80.7</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.4</td>
      <td>83.7</td>
      <td>84.3</td>
      <td>84.0</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.9</td>
      <td>80.7</td>
      <td>81.4</td>
      <td>80.9</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.7</td>
      <td>83.3</td>
      <td>83.8</td>
      <td>84.7</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.3</td>
      <td>81.5</td>
      <td>81.4</td>
      <td>80.3</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81.3</td>
      <td>80.8</td>
      <td>80.6</td>
      <td>81.2</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.8</td>
      <td>83.6</td>
      <td>84.3</td>
      <td>84.6</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>81.0</td>
      <td>80.6</td>
      <td>80.9</td>
      <td>80.4</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>84.1</td>
      <td>83.4</td>
      <td>84.4</td>
      <td>82.8</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.7</td>
      <td>84.3</td>
      <td>83.6</td>
      <td>83.8</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.9</td>
      <td>84.0</td>
      <td>83.8</td>
      <td>84.3</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.8</td>
      <td>83.8</td>
      <td>84.2</td>
      <td>84.1</td>
    </tr>
  </tbody>
</table>
</div>



# Spending analysis vs. % overall passing
***


```python
bin_labels = ['Lowest', 'Low', 'Med', 'High']
bins=[575,600,625,650,660]
final_school_report['spending'] = pd.cut(final_school_report['per student budget'], bins=bins,labels=bin_labels, right=False)
```

    /Users/jeffmoore/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:3: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      This is separate from the ipykernel package so we can avoid doing imports until



```python
spend_report = final_school_report.loc[:, ['school', 'per student budget', 'avg math score', 'avg reading score', '% passing math','% passing reading', '% overall passing', 'spending']]
spend_report.sort_values('% overall passing', ascending=False)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
      <th>spending</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>90.9%</td>
      <td>93.3%</td>
      <td>92.1%</td>
      <td>Lowest</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>91.7%</td>
      <td>92.2%</td>
      <td>91.9%</td>
      <td>Low</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>583.0</td>
      <td>83.7</td>
      <td>84.0</td>
      <td>90.3%</td>
      <td>93.4%</td>
      <td>91.9%</td>
      <td>Lowest</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>89.6%</td>
      <td>93.9%</td>
      <td>91.7%</td>
      <td>Lowest</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>581.0</td>
      <td>83.8</td>
      <td>83.8</td>
      <td>90.6%</td>
      <td>92.7%</td>
      <td>91.7%</td>
      <td>Lowest</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>625.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>89.7%</td>
      <td>93.4%</td>
      <td>91.6%</td>
      <td>Med</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>638.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>90.2%</td>
      <td>92.9%</td>
      <td>91.6%</td>
      <td>Med</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>600.0</td>
      <td>83.4</td>
      <td>83.7</td>
      <td>89.9%</td>
      <td>92.6%</td>
      <td>91.3%</td>
      <td>Low</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>628.0</td>
      <td>77.0</td>
      <td>81.0</td>
      <td>64.6%</td>
      <td>79.3%</td>
      <td>72.0%</td>
      <td>Med</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>644.0</td>
      <td>77.1</td>
      <td>80.7</td>
      <td>65.8%</td>
      <td>77.5%</td>
      <td>71.6%</td>
      <td>Med</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>652.0</td>
      <td>77.3</td>
      <td>80.9</td>
      <td>64.7%</td>
      <td>78.2%</td>
      <td>71.5%</td>
      <td>High</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>63.8%</td>
      <td>78.4%</td>
      <td>71.1%</td>
      <td>Med</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>63.3%</td>
      <td>78.8%</td>
      <td>71.1%</td>
      <td>High</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>63.9%</td>
      <td>78.3%</td>
      <td>71.1%</td>
      <td>High</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>64.1%</td>
      <td>77.7%</td>
      <td>70.9%</td>
      <td>Med</td>
    </tr>
  </tbody>
</table>
</div>



# Spending vs. School Size
***


```python
bin_labels = ['Small', 'Medium', 'Large']
bins=[399,1000,3000,5001]
final_school_report['school size'] = pd.cut(final_school_report.loc[:, 'size'], bins=bins,labels=bin_labels, right=False)
```


```python
size_report = final_school_report.loc[:, ['school','per student budget', 'avg math score', 'avg reading score', '% passing math','% passing reading', '% overall passing', 'spending','school size']]
size_report.sort_values('% overall passing', ascending=False)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
      <th>spending</th>
      <th>school size</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>90.9%</td>
      <td>93.3%</td>
      <td>92.1%</td>
      <td>Lowest</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>91.7%</td>
      <td>92.2%</td>
      <td>91.9%</td>
      <td>Low</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>583.0</td>
      <td>83.7</td>
      <td>84.0</td>
      <td>90.3%</td>
      <td>93.4%</td>
      <td>91.9%</td>
      <td>Lowest</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>89.6%</td>
      <td>93.9%</td>
      <td>91.7%</td>
      <td>Lowest</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>581.0</td>
      <td>83.8</td>
      <td>83.8</td>
      <td>90.6%</td>
      <td>92.7%</td>
      <td>91.7%</td>
      <td>Lowest</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>625.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>89.7%</td>
      <td>93.4%</td>
      <td>91.6%</td>
      <td>Med</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>638.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>90.2%</td>
      <td>92.9%</td>
      <td>91.6%</td>
      <td>Med</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>600.0</td>
      <td>83.4</td>
      <td>83.7</td>
      <td>89.9%</td>
      <td>92.6%</td>
      <td>91.3%</td>
      <td>Low</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>628.0</td>
      <td>77.0</td>
      <td>81.0</td>
      <td>64.6%</td>
      <td>79.3%</td>
      <td>72.0%</td>
      <td>Med</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>644.0</td>
      <td>77.1</td>
      <td>80.7</td>
      <td>65.8%</td>
      <td>77.5%</td>
      <td>71.6%</td>
      <td>Med</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>652.0</td>
      <td>77.3</td>
      <td>80.9</td>
      <td>64.7%</td>
      <td>78.2%</td>
      <td>71.5%</td>
      <td>High</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>63.8%</td>
      <td>78.4%</td>
      <td>71.1%</td>
      <td>Med</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>63.3%</td>
      <td>78.8%</td>
      <td>71.1%</td>
      <td>High</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>63.9%</td>
      <td>78.3%</td>
      <td>71.1%</td>
      <td>High</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>64.1%</td>
      <td>77.7%</td>
      <td>70.9%</td>
      <td>Med</td>
      <td>Large</td>
    </tr>
  </tbody>
</table>
</div>



# Spending vs. School Type
***


```python
school_type = size_report.iloc[:, :9]
school_type['type'] = final_school_report.iloc[:, 1:2]
school_type.sort_values('% overall passing', ascending=False)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>school</th>
      <th>per student budget</th>
      <th>avg math score</th>
      <th>avg reading score</th>
      <th>% passing math</th>
      <th>% passing reading</th>
      <th>% overall passing</th>
      <th>spending</th>
      <th>school size</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>13</th>
      <td>Wilson High School</td>
      <td>578.0</td>
      <td>83.3</td>
      <td>84.0</td>
      <td>90.9%</td>
      <td>93.3%</td>
      <td>92.1%</td>
      <td>Lowest</td>
      <td>Medium</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>609.0</td>
      <td>83.8</td>
      <td>84.0</td>
      <td>91.7%</td>
      <td>92.2%</td>
      <td>91.9%</td>
      <td>Low</td>
      <td>Small</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Wright High School</td>
      <td>583.0</td>
      <td>83.7</td>
      <td>84.0</td>
      <td>90.3%</td>
      <td>93.4%</td>
      <td>91.9%</td>
      <td>Lowest</td>
      <td>Medium</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Cabrera High School</td>
      <td>582.0</td>
      <td>83.1</td>
      <td>84.0</td>
      <td>89.6%</td>
      <td>93.9%</td>
      <td>91.7%</td>
      <td>Lowest</td>
      <td>Medium</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Holden High School</td>
      <td>581.0</td>
      <td>83.8</td>
      <td>83.8</td>
      <td>90.6%</td>
      <td>92.7%</td>
      <td>91.7%</td>
      <td>Lowest</td>
      <td>Small</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>625.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>89.7%</td>
      <td>93.4%</td>
      <td>91.6%</td>
      <td>Med</td>
      <td>Medium</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Thomas High School</td>
      <td>638.0</td>
      <td>83.4</td>
      <td>83.8</td>
      <td>90.2%</td>
      <td>92.9%</td>
      <td>91.6%</td>
      <td>Med</td>
      <td>Medium</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Shelton High School</td>
      <td>600.0</td>
      <td>83.4</td>
      <td>83.7</td>
      <td>89.9%</td>
      <td>92.6%</td>
      <td>91.3%</td>
      <td>Low</td>
      <td>Medium</td>
      <td>Charter</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Bailey High School</td>
      <td>628.0</td>
      <td>77.0</td>
      <td>81.0</td>
      <td>64.6%</td>
      <td>79.3%</td>
      <td>72.0%</td>
      <td>Med</td>
      <td>Large</td>
      <td>District</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Ford High School</td>
      <td>644.0</td>
      <td>77.1</td>
      <td>80.7</td>
      <td>65.8%</td>
      <td>77.5%</td>
      <td>71.6%</td>
      <td>Med</td>
      <td>Medium</td>
      <td>District</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hernandez High School</td>
      <td>652.0</td>
      <td>77.3</td>
      <td>80.9</td>
      <td>64.7%</td>
      <td>78.2%</td>
      <td>71.5%</td>
      <td>High</td>
      <td>Large</td>
      <td>District</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Figueroa High School</td>
      <td>639.0</td>
      <td>76.7</td>
      <td>81.2</td>
      <td>63.8%</td>
      <td>78.4%</td>
      <td>71.1%</td>
      <td>Med</td>
      <td>Medium</td>
      <td>District</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Huang High School</td>
      <td>655.0</td>
      <td>76.6</td>
      <td>81.2</td>
      <td>63.3%</td>
      <td>78.8%</td>
      <td>71.1%</td>
      <td>High</td>
      <td>Medium</td>
      <td>District</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Johnson High School</td>
      <td>650.0</td>
      <td>77.1</td>
      <td>81.0</td>
      <td>63.9%</td>
      <td>78.3%</td>
      <td>71.1%</td>
      <td>High</td>
      <td>Large</td>
      <td>District</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Rodriguez High School</td>
      <td>637.0</td>
      <td>76.8</td>
      <td>80.7</td>
      <td>64.1%</td>
      <td>77.7%</td>
      <td>70.9%</td>
      <td>Med</td>
      <td>Large</td>
      <td>District</td>
    </tr>
  </tbody>
</table>
</div>



# Observations
***

4 of the 5 best performing schools spend the least per student. This is counter intuitive as most would think that spending more per student would mean that performance would be best.

All five of the top performing schools are charter school that are medium or small in size. This supports a notion that public schools don't offer the best education - leading many to select a charter school (semi-private school) vs. supporting public schools fully.

3 of the worst performing schools are large, public schools which supports the notion that parents should seek to place their child in a smaller school if possible.  Also noticed in the analysis is that these large schools often had the largest per student budget.
