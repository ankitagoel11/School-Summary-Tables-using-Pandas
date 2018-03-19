

```python
import pandas as pd
import os
import numpy as np
csvpath1 = os.path.join("schools_complete.csv")
csvpath2 = os.path.join("students_complete.csv")
district = pd.read_csv(csvpath1)
students = pd.read_csv(csvpath2)
schools_df = pd.DataFrame(district)
students_df = pd.DataFrame(students)
```

***District Summary Code***


```python
number_of_schools = schools_df['name'].count() 
number_of_students =  schools_df['size'].sum()
total_budget =  schools_df['budget'].sum()
average_math_score=students_df['math_score'].mean()
average_reading_score=students_df['reading_score'].mean()

# Defining the passing requirement as minimum score of 70
    
passing_reading_score_df = pd. DataFrame(students_df[students_df.reading_score > 70])
a = len(passing_reading_score_df)
passing_reading_percentage = a*100/number_of_students
 # Defining the passing requirement as minimum score of 70

passing_math_score_df = pd. DataFrame(students_df[students_df.math_score > 70])
b = len(passing_math_score_df)
passing_math_percentage = b*100/number_of_students


c = (passing_math_percentage + passing_reading_percentage)/2

#Placing all the required parameters in a dataframe

final_output_df = pd.DataFrame({ 'Total Schools' : [number_of_schools], 'Total Students': number_of_students ,
                                'Average Math Score' : average_math_score , 'Average Reading Score' : average_reading_score, 
                                ' % Passing Math' : passing_math_percentage,
                                ' % Passing Reading' : passing_reading_percentage, 
                                'Overall Passing Rate': c}) 
final_output_df = final_output_df [['Total Schools' , 'Total Students', 'Average Math Score', 'Average Reading Score', 
                                    ' % Passing Math', ' % Passing Reading', 'Overall Passing Rate' ]]
```

***District Summary Output***


```python
final_output_df
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
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
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
      <td>78.985371</td>
      <td>81.87784</td>
      <td>72.392137</td>
      <td>82.971662</td>
      <td>77.681899</td>
    </tr>
  </tbody>
</table>
</div>



***School Summary Code***


```python
schools_df.rename(columns = {'school' : 'School Name', 
                             'type' : 'School Type', 
                             'size' : 'Total Students',
                             'budget' : 'Total School budget'}, inplace = True)
schools_df['Per_Student_budget'] = schools_df['Total School budget']/schools_df['Total Students']

students_df.rename(columns = {'school' : "School Name"}, inplace = True)
schools_df.rename(columns = {'name' : "School Name"}, inplace = True)

#Reading Score Summary by School

a = students_df.groupby('School Name').reading_score.mean()
reading_score_summary_df = pd.DataFrame(a)
reading_score_summary_df.rename(columns = {'reading_score' :'Average_Reading_Score' }, inplace = True)
reading_score_summary_df = reading_score_summary_df.reset_index(drop = False)


#Math Score Summary by School
b = students_df.groupby('School Name').math_score.mean()
math_score_summary_df = pd.DataFrame(b)
math_score_summary_df.rename(columns = {'math_score' :'Average_Math_Score' }, inplace = True)
math_score_summary_df = math_score_summary_df.reset_index(drop = False)


merge_table = pd.merge(schools_df, reading_score_summary_df , on="School Name",how="left")

# Reading Passing %

d = passing_reading_score_df.groupby('school').reading_score.count()

e = students_df.groupby('School Name').name.count()

passing_reading_pecent_per_school = d*100/e

passing_reading_pecent_per_school_df = pd. DataFrame(passing_reading_pecent_per_school).reset_index(drop = False)

passing_reading_pecent_per_school_df.columns = ['School Name', 'Reading Passing %']

# Math Passing %

f = passing_math_score_df.groupby('school').math_score.count()

passing_math_pecent_per_school = f*100/e

passing_math_pecent_per_school_df = pd. DataFrame(passing_math_pecent_per_school).reset_index(drop = False)

passing_math_pecent_per_school_df.columns = ['School Name', 'Math Passing %']

merge_percentage_table = pd.merge(passing_reading_pecent_per_school_df, passing_math_pecent_per_school_df ,
                                  on="School Name",how="left")
# Overall passing %

merge_percentage_table['Overall Passing %'] = (merge_percentage_table['Reading Passing %'] +
                                                     merge_percentage_table['Math Passing %'])/2


#Merging all the results

School_Summary_final_output = merge_table.merge(math_score_summary_df,on='School Name').merge(merge_percentage_table,on='School Name')

del School_Summary_final_output['School ID']

#making a copy for later use

School_Summary_final_output_v1 = School_Summary_final_output

#displaying final output

School_Summary_final_output
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School budget</th>
      <th>Per_Student_budget</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>78.813850</td>
      <td>63.318478</td>
      <td>71.066164</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>78.433367</td>
      <td>63.750424</td>
      <td>71.091896</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>92.617831</td>
      <td>89.892107</td>
      <td>91.254969</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>78.187702</td>
      <td>64.746494</td>
      <td>71.467098</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>93.392371</td>
      <td>89.713896</td>
      <td>91.553134</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>93.254490</td>
      <td>90.932983</td>
      <td>92.093736</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>93.864370</td>
      <td>89.558665</td>
      <td>91.711518</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>81.033963</td>
      <td>77.048432</td>
      <td>79.300643</td>
      <td>64.630225</td>
      <td>71.965434</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>92.740047</td>
      <td>90.632319</td>
      <td>91.686183</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>92.203742</td>
      <td>91.683992</td>
      <td>91.943867</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>93.444444</td>
      <td>90.277778</td>
      <td>91.861111</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>77.744436</td>
      <td>64.066017</td>
      <td>70.905226</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>78.281874</td>
      <td>63.852132</td>
      <td>71.067003</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>80.746258</td>
      <td>77.102592</td>
      <td>77.510040</td>
      <td>65.753925</td>
      <td>71.631982</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.848930</td>
      <td>83.418349</td>
      <td>92.905199</td>
      <td>90.214067</td>
      <td>91.559633</td>
    </tr>
  </tbody>
</table>
</div>



**Top Performing Schools (By Passing Rate)**


```python
Schools_by_passing_rate_performance = School_Summary_final_output.sort_values('Overall Passing %', ascending=False)
```


```python
Schools_by_passing_rate_performance.head(5)
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School budget</th>
      <th>Per_Student_budget</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>93.254490</td>
      <td>90.932983</td>
      <td>92.093736</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>92.203742</td>
      <td>91.683992</td>
      <td>91.943867</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>93.444444</td>
      <td>90.277778</td>
      <td>91.861111</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>93.864370</td>
      <td>89.558665</td>
      <td>91.711518</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>92.740047</td>
      <td>90.632319</td>
      <td>91.686183</td>
    </tr>
  </tbody>
</table>
</div>



**Bottom 5 Performing Schools (By Passing Rate)**


```python
Schools_by_passing_rate_performance.tail(5)
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School budget</th>
      <th>Per_Student_budget</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>78.187702</td>
      <td>64.746494</td>
      <td>71.467098</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>78.433367</td>
      <td>63.750424</td>
      <td>71.091896</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>78.281874</td>
      <td>63.852132</td>
      <td>71.067003</td>
    </tr>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>78.813850</td>
      <td>63.318478</td>
      <td>71.066164</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>77.744436</td>
      <td>64.066017</td>
      <td>70.905226</td>
    </tr>
  </tbody>
</table>
</div>



**Math Scores by Grade**


```python
students_df.head()
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
      <th>Student ID</th>
      <th>name</th>
      <th>gender</th>
      <th>grade</th>
      <th>School Name</th>
      <th>reading_score</th>
      <th>math_score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Paul Bradley</td>
      <td>M</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>66</td>
      <td>79</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Victor Smith</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>94</td>
      <td>61</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Kevin Rodriguez</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>90</td>
      <td>60</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Dr. Richard Scott</td>
      <td>M</td>
      <td>12th</td>
      <td>Huang High School</td>
      <td>67</td>
      <td>58</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Bonnie Ray</td>
      <td>F</td>
      <td>9th</td>
      <td>Huang High School</td>
      <td>97</td>
      <td>84</td>
    </tr>
  </tbody>
</table>
</div>




```python
y = students_df.groupby(['School Name', 'grade']).math_score.mean().unstack(level=1)

y.columns = ['9th','10th','11th','12th']

y
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
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>76.996772</td>
      <td>77.515588</td>
      <td>76.492218</td>
      <td>77.083676</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83.154506</td>
      <td>82.765560</td>
      <td>83.277487</td>
      <td>83.094697</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>76.539974</td>
      <td>76.884344</td>
      <td>77.151369</td>
      <td>76.403037</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>77.672316</td>
      <td>76.918058</td>
      <td>76.179963</td>
      <td>77.361345</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>84.229064</td>
      <td>83.842105</td>
      <td>83.356164</td>
      <td>82.044010</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77.337408</td>
      <td>77.136029</td>
      <td>77.186567</td>
      <td>77.438495</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.429825</td>
      <td>85.000000</td>
      <td>82.855422</td>
      <td>83.787402</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>75.908735</td>
      <td>76.446602</td>
      <td>77.225641</td>
      <td>77.027251</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>76.691117</td>
      <td>77.491653</td>
      <td>76.863248</td>
      <td>77.187857</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.372000</td>
      <td>84.328125</td>
      <td>84.121547</td>
      <td>83.625455</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>76.612500</td>
      <td>76.395626</td>
      <td>77.690748</td>
      <td>76.859966</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>82.917411</td>
      <td>83.383495</td>
      <td>83.778976</td>
      <td>83.420755</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83.087886</td>
      <td>83.498795</td>
      <td>83.497041</td>
      <td>83.590022</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>83.724422</td>
      <td>83.195326</td>
      <td>83.035794</td>
      <td>83.085578</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>84.010288</td>
      <td>83.836782</td>
      <td>83.644986</td>
      <td>83.264706</td>
    </tr>
  </tbody>
</table>
</div>



**Reading Scores by Grade**


```python
x  = students_df.groupby(['School Name', 'grade']).reading_score.mean().unstack(level=1)

x.columns = ['9th','10th','11th','12th']

x
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
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>School Name</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>80.907183</td>
      <td>80.945643</td>
      <td>80.912451</td>
      <td>81.303155</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>84.253219</td>
      <td>83.788382</td>
      <td>84.287958</td>
      <td>83.676136</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81.408912</td>
      <td>80.640339</td>
      <td>81.384863</td>
      <td>81.198598</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>81.262712</td>
      <td>80.403642</td>
      <td>80.662338</td>
      <td>80.632653</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>83.706897</td>
      <td>84.288089</td>
      <td>84.013699</td>
      <td>83.369193</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>80.660147</td>
      <td>81.396140</td>
      <td>80.857143</td>
      <td>80.866860</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83.324561</td>
      <td>83.815534</td>
      <td>84.698795</td>
      <td>83.677165</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>81.512386</td>
      <td>81.417476</td>
      <td>80.305983</td>
      <td>81.290284</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>80.773431</td>
      <td>80.616027</td>
      <td>81.227564</td>
      <td>81.260714</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83.612000</td>
      <td>84.335938</td>
      <td>84.591160</td>
      <td>83.807273</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>80.629808</td>
      <td>80.864811</td>
      <td>80.376426</td>
      <td>80.993127</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83.441964</td>
      <td>84.373786</td>
      <td>82.781671</td>
      <td>84.122642</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>84.254157</td>
      <td>83.585542</td>
      <td>83.831361</td>
      <td>83.728850</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>84.021452</td>
      <td>83.764608</td>
      <td>84.317673</td>
      <td>83.939778</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>83.812757</td>
      <td>84.156322</td>
      <td>84.073171</td>
      <td>83.833333</td>
    </tr>
  </tbody>
</table>
</div>



**Scores by School Spending**


```python
School_Summary_final_output['bin'] = pd.cut(School_Summary_final_output['Per_Student_budget'], 
                                            [0,585, 615, 645, 675], labels=['<585', '585-615', '615-645', '645+'])
```


```python
School_Summary_final_output
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School budget</th>
      <th>Per_Student_budget</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
      <th>bin</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>78.813850</td>
      <td>63.318478</td>
      <td>71.066164</td>
      <td>645+</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>78.433367</td>
      <td>63.750424</td>
      <td>71.091896</td>
      <td>615-645</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>92.617831</td>
      <td>89.892107</td>
      <td>91.254969</td>
      <td>585-615</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>78.187702</td>
      <td>64.746494</td>
      <td>71.467098</td>
      <td>645+</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>93.392371</td>
      <td>89.713896</td>
      <td>91.553134</td>
      <td>615-645</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>93.254490</td>
      <td>90.932983</td>
      <td>92.093736</td>
      <td>&lt;585</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>93.864370</td>
      <td>89.558665</td>
      <td>91.711518</td>
      <td>&lt;585</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>81.033963</td>
      <td>77.048432</td>
      <td>79.300643</td>
      <td>64.630225</td>
      <td>71.965434</td>
      <td>615-645</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>92.740047</td>
      <td>90.632319</td>
      <td>91.686183</td>
      <td>&lt;585</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>92.203742</td>
      <td>91.683992</td>
      <td>91.943867</td>
      <td>585-615</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>93.444444</td>
      <td>90.277778</td>
      <td>91.861111</td>
      <td>&lt;585</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>77.744436</td>
      <td>64.066017</td>
      <td>70.905226</td>
      <td>615-645</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>78.281874</td>
      <td>63.852132</td>
      <td>71.067003</td>
      <td>645+</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>80.746258</td>
      <td>77.102592</td>
      <td>77.510040</td>
      <td>65.753925</td>
      <td>71.631982</td>
      <td>615-645</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.848930</td>
      <td>83.418349</td>
      <td>92.905199</td>
      <td>90.214067</td>
      <td>91.559633</td>
      <td>615-645</td>
    </tr>
  </tbody>
</table>
</div>




```python
Per_student_budget_bin = School_Summary_final_output.groupby('bin').mean()
Per_student_budget_bin.reset_index(drop = False, inplace=True)
Final_Student_bins = Per_student_budget_bin.drop(Per_student_budget_bin.columns[[1, 2, 3]], axis=1)

Final_Student_bins
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
      <th>bin</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;585</td>
      <td>83.933814</td>
      <td>83.455399</td>
      <td>93.325838</td>
      <td>90.350436</td>
      <td>91.838137</td>
    </tr>
    <tr>
      <th>1</th>
      <td>585-615</td>
      <td>83.885211</td>
      <td>83.599686</td>
      <td>92.410786</td>
      <td>90.788049</td>
      <td>91.599418</td>
    </tr>
    <tr>
      <th>2</th>
      <td>615-645</td>
      <td>81.891436</td>
      <td>79.079225</td>
      <td>83.214343</td>
      <td>73.021426</td>
      <td>78.117884</td>
    </tr>
    <tr>
      <th>3</th>
      <td>645+</td>
      <td>81.027843</td>
      <td>76.997210</td>
      <td>78.427809</td>
      <td>63.972368</td>
      <td>71.200088</td>
    </tr>
  </tbody>
</table>
</div>



**Scores by School Size**


```python


School_Summary_final_output_v1['bin'] = pd.cut(School_Summary_final_output_v1['Total Students'], 
                                            [0,2000, 3000, 5000], labels=['Small', 'Medium', 'Large'])

School_Summary_final_output_v1
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
      <th>School Name</th>
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School budget</th>
      <th>Per_Student_budget</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
      <th>bin</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Huang High School</td>
      <td>District</td>
      <td>2917</td>
      <td>1910635</td>
      <td>655.0</td>
      <td>81.182722</td>
      <td>76.629414</td>
      <td>78.813850</td>
      <td>63.318478</td>
      <td>71.066164</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2949</td>
      <td>1884411</td>
      <td>639.0</td>
      <td>81.158020</td>
      <td>76.711767</td>
      <td>78.433367</td>
      <td>63.750424</td>
      <td>71.091896</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1761</td>
      <td>1056600</td>
      <td>600.0</td>
      <td>83.725724</td>
      <td>83.359455</td>
      <td>92.617831</td>
      <td>89.892107</td>
      <td>91.254969</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4635</td>
      <td>3022020</td>
      <td>652.0</td>
      <td>80.934412</td>
      <td>77.289752</td>
      <td>78.187702</td>
      <td>64.746494</td>
      <td>71.467098</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1468</td>
      <td>917500</td>
      <td>625.0</td>
      <td>83.816757</td>
      <td>83.351499</td>
      <td>93.392371</td>
      <td>89.713896</td>
      <td>91.553134</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2283</td>
      <td>1319574</td>
      <td>578.0</td>
      <td>83.989488</td>
      <td>83.274201</td>
      <td>93.254490</td>
      <td>90.932983</td>
      <td>92.093736</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1858</td>
      <td>1081356</td>
      <td>582.0</td>
      <td>83.975780</td>
      <td>83.061895</td>
      <td>93.864370</td>
      <td>89.558665</td>
      <td>91.711518</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4976</td>
      <td>3124928</td>
      <td>628.0</td>
      <td>81.033963</td>
      <td>77.048432</td>
      <td>79.300643</td>
      <td>64.630225</td>
      <td>71.965434</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>248087</td>
      <td>581.0</td>
      <td>83.814988</td>
      <td>83.803279</td>
      <td>92.740047</td>
      <td>90.632319</td>
      <td>91.686183</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>585858</td>
      <td>609.0</td>
      <td>84.044699</td>
      <td>83.839917</td>
      <td>92.203742</td>
      <td>91.683992</td>
      <td>91.943867</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1800</td>
      <td>1049400</td>
      <td>583.0</td>
      <td>83.955000</td>
      <td>83.682222</td>
      <td>93.444444</td>
      <td>90.277778</td>
      <td>91.861111</td>
      <td>Small</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3999</td>
      <td>2547363</td>
      <td>637.0</td>
      <td>80.744686</td>
      <td>76.842711</td>
      <td>77.744436</td>
      <td>64.066017</td>
      <td>70.905226</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4761</td>
      <td>3094650</td>
      <td>650.0</td>
      <td>80.966394</td>
      <td>77.072464</td>
      <td>78.281874</td>
      <td>63.852132</td>
      <td>71.067003</td>
      <td>Large</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Ford High School</td>
      <td>District</td>
      <td>2739</td>
      <td>1763916</td>
      <td>644.0</td>
      <td>80.746258</td>
      <td>77.102592</td>
      <td>77.510040</td>
      <td>65.753925</td>
      <td>71.631982</td>
      <td>Medium</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1635</td>
      <td>1043130</td>
      <td>638.0</td>
      <td>83.848930</td>
      <td>83.418349</td>
      <td>92.905199</td>
      <td>90.214067</td>
      <td>91.559633</td>
      <td>Small</td>
    </tr>
  </tbody>
</table>
</div>




```python
school_size_bin = School_Summary_final_output_v1.groupby('bin').mean()
school_size_bin.reset_index(drop = False, inplace=True)
Final_school_size_bins = school_size_bin.drop(school_size_bin.columns[[1, 2, 3]], axis=1)

Final_school_size_bins
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
      <th>bin</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Small</td>
      <td>83.883125</td>
      <td>83.502373</td>
      <td>93.024001</td>
      <td>90.281832</td>
      <td>91.652916</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Medium</td>
      <td>81.769122</td>
      <td>78.429493</td>
      <td>82.002937</td>
      <td>70.938952</td>
      <td>76.470945</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Large</td>
      <td>80.919864</td>
      <td>77.063340</td>
      <td>78.378664</td>
      <td>64.323717</td>
      <td>71.351190</td>
    </tr>
  </tbody>
</table>
</div>



**Scores by School Type**


```python
school_type_bins = School_Summary_final_output.groupby('School Type').mean()
school_type_bins.reset_index(drop = False, inplace=True)
Final_school_type_bins = school_type_bins.drop(school_type_bins.columns[[1, 2, 3]], axis=1)

Final_school_type_bins
school_type_bins
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
      <th>School Type</th>
      <th>Total Students</th>
      <th>Total School budget</th>
      <th>Per_Student_budget</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Charter</td>
      <td>1524.250000</td>
      <td>9.126881e+05</td>
      <td>599.500000</td>
      <td>83.896421</td>
      <td>83.473852</td>
      <td>93.052812</td>
      <td>90.363226</td>
      <td>91.708019</td>
    </tr>
    <tr>
      <th>1</th>
      <td>District</td>
      <td>3853.714286</td>
      <td>2.478275e+06</td>
      <td>643.571429</td>
      <td>80.966636</td>
      <td>76.956733</td>
      <td>78.324559</td>
      <td>64.302528</td>
      <td>71.313543</td>
    </tr>
  </tbody>
</table>
</div>




```python
Final_school_type_bins
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
      <th>School Type</th>
      <th>Average_Reading_Score</th>
      <th>Average_Math_Score</th>
      <th>Reading Passing %</th>
      <th>Math Passing %</th>
      <th>Overall Passing %</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Charter</td>
      <td>83.896421</td>
      <td>83.473852</td>
      <td>93.052812</td>
      <td>90.363226</td>
      <td>91.708019</td>
    </tr>
    <tr>
      <th>1</th>
      <td>District</td>
      <td>80.966636</td>
      <td>76.956733</td>
      <td>78.324559</td>
      <td>64.302528</td>
      <td>71.313543</td>
    </tr>
  </tbody>
</table>
</div>


