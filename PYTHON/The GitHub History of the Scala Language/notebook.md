## 1. Scala's real-world project repository data
<p>With almost 30k commits and a history spanning over ten years, Scala is a mature programming language. It is a general-purpose programming language that has recently become another prominent language for data scientists.</p>
<p>Scala is also an open source project. Open source projects have the advantage that their entire development histories -- who made changes, what was changed, code reviews, etc. -- are publicly available. </p>
<p>We're going to read in, clean up, and visualize the real world project repository of Scala that spans data from a version control system (Git) as well as a project hosting site (GitHub). We will find out who has had the most influence on its development and who are the experts.</p>
<p>The dataset we will use, which has been previously mined and extracted from GitHub, is comprised of three files:</p>
<ol>
<li><code>pulls_2011-2013.csv</code> contains the basic information about the pull requests, and spans from the end of 2011 up to (but not including) 2014.</li>
<li><code>pulls_2014-2018.csv</code> contains identical information, and spans from 2014 up to 2018.</li>
<li><code>pull_files.csv</code> contains the files that were modified by each pull request.</li>
</ol>


```python
# Importing pandas
import pandas as pd
# ... YOUR CODE FOR TASK 1 ...

# Loading in the data
pulls_one = pd.read_csv("datasets/pulls_2011-2013.csv")
pulls_two = pd.read_csv("datasets/pulls_2014-2018.csv")
pull_files = pd.read_csv("datasets/pull_files.csv") 
```

## 2. Preparing and cleaning the data
<p>First, we will need to combine the data from the two separate pull DataFrames. </p>
<p>Next, the raw data extracted from GitHub contains dates in the ISO8601 format. However, <code>pandas</code> imports them as regular strings. To make our analysis easier, we need to convert the strings into Python's <code>DateTime</code> objects. <code>DateTime</code> objects have the important property that they can be compared and sorted.</p>
<p>The pull request times are all in UTC (also known as Coordinated Universal Time). The commit times, however, are in the local time of the author with time zone information (number of hours difference from UTC). To make comparisons easy, we should convert all times to UTC.</p>


```python
# Append pulls_one to pulls_two
pulls = pulls_one.append(pulls_two)
# pulls
# Convert the date for the pulls object
pulls['date'] = pd.to_datetime(pulls['date'], utc=True)

```

## 3. Merging the DataFrames
<p>The data extracted comes in two separate files. Merging the two DataFrames will make it easier for us to analyze the data in the future tasks.</p>


```python
# Merge the two DataFrames
data = pulls.merge(pull_files, on='pid')
```

## 4. Is the project still actively maintained?
<p>The activity in an open source project is not very consistent. Some projects might be active for many years after the initial release, while others can slowly taper out into oblivion. Before committing to contributing to a project, it is important to understand the state of the project. Is development going steadily, or is there a drop? Has the project been abandoned altogether?</p>
<p>The data used in this project was collected in January of 2018. We are interested in the evolution of the number of contributions up to that date.</p>
<p>For Scala, we will do this by plotting a chart of the project's activity. We will calculate the number of pull requests submitted each (calendar) month during the project's lifetime. We will then plot these numbers to see the trend of contributions.</p>
<ul>
<li><p>A helpful reminder of how to access various components of a date can be found in <a href="https://campus.datacamp.com/courses/data-manipulation-with-pandas/slicing-and-indexing?ex=12">this exercise of Data Manipulation with pandas</a></p></li>
<li><p>Additionally, recall that you can group by multiple variables by passing a list to <code>groupby()</code>. This video from <a href="https://campus.datacamp.com/courses/data-manipulation-with-pandas/aggregating-data-ad6d4643-0e95-470c-8299-f69cc4c83de8?ex=9">Data Manipulation with pandas</a> should help!</p></li>
</ul>


```python
%matplotlib inline

# Create a column that will store the month
data['month'] = data['date'].dt.month

# Create a column that will store the year
data['year'] = data['date'].dt.year

# Group by the month and year and count the pull requests
counts = data.groupby(['month', 'year']).count()

# Plot the results
counts.plot(kind='bar', figsize = (12,4))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7fae11a416a0>




    
![png](output_7_1.png)
    


## 5. Is there camaraderie in the project?
<p>The organizational structure varies from one project to another, and it can influence your success as a contributor. A project that has a very small community might not be the best one to start working on. The small community might indicate a high barrier of entry. This can be caused by several factors, including a community that is reluctant to accept pull requests from "outsiders," that the code base is hard to work with, etc. However, a large community can serve as an indicator that the project is regularly accepting pull requests from new contributors. Such a project would be a good place to start.</p>
<p>In order to evaluate the dynamics of the community, we will plot a histogram of the number of pull requests submitted by each user. A distribution that shows that there are few people that only contribute a small number of pull requests can be used as in indicator that the project is not welcoming of new contributors. </p>


```python
# Required for matplotlib
%matplotlib inline
# data
# Group by the submitter
by_user = data.groupby(['user']).agg({'pid':'count'})

# Plot the histogram
# ... YOUR CODE FOR TASK 5 ...
by_user.hist()
```




    array([[<matplotlib.axes._subplots.AxesSubplot object at 0x7faddede2940>]],
          dtype=object)




    
![png](output_9_1.png)
    


## 6. What files were changed in the last ten pull requests?
<p>Choosing the right place to make a contribution is as important as choosing the project to contribute to. Some parts of the code might be stable, some might be dead. Contributing there might not have the most impact. Therefore it is important to understand the parts of the system that have been recently changed. This allows us to pinpoint the "hot" areas of the code where most of the activity is happening. Focusing on those parts might not the most effective use of our times.</p>


```python
pull_files
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
      <th>pid</th>
      <th>file</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>163314316</td>
      <td>test/files/pos/t5638/Among.java</td>
    </tr>
    <tr>
      <th>1</th>
      <td>163314316</td>
      <td>test/files/pos/t5638/Usage.scala</td>
    </tr>
    <tr>
      <th>2</th>
      <td>163314316</td>
      <td>test/files/pos/t9291.scala</td>
    </tr>
    <tr>
      <th>3</th>
      <td>163314316</td>
      <td>test/files/run/t8348.check</td>
    </tr>
    <tr>
      <th>4</th>
      <td>163314316</td>
      <td>test/files/run/t8348/TableColumn.java</td>
    </tr>
    <tr>
      <th>5</th>
      <td>163314316</td>
      <td>test/files/run/t8348/TableColumnImpl.java</td>
    </tr>
    <tr>
      <th>6</th>
      <td>163314316</td>
      <td>test/files/run/t8348/Test.scala</td>
    </tr>
    <tr>
      <th>7</th>
      <td>163061502</td>
      <td>src/library/scala/concurrent/Lock.scala</td>
    </tr>
    <tr>
      <th>8</th>
      <td>163057333</td>
      <td>src/compiler/scala/tools/nsc/profile/AsyncHelp...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>163057333</td>
      <td>src/compiler/scala/tools/nsc/profile/Profiler....</td>
    </tr>
    <tr>
      <th>10</th>
      <td>162985594</td>
      <td>src/compiler/scala/tools/nsc/backend/jvm/BCode...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>162985594</td>
      <td>src/compiler/scala/tools/nsc/symtab/classfile/...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>162985594</td>
      <td>src/reflect/scala/reflect/internal/pickling/By...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>162838837</td>
      <td>src/compiler/scala/reflect/reify/phases/Calcul...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>162765232</td>
      <td>src/library/scala/Predef.scala</td>
    </tr>
    <tr>
      <th>15</th>
      <td>162765232</td>
      <td>test/files/neg/leibniz-liskov.check</td>
    </tr>
    <tr>
      <th>16</th>
      <td>162765232</td>
      <td>test/files/neg/leibniz-liskov.scala</td>
    </tr>
    <tr>
      <th>17</th>
      <td>162765232</td>
      <td>test/files/pos/leibniz-liskov.scala</td>
    </tr>
    <tr>
      <th>18</th>
      <td>162765232</td>
      <td>test/files/pos/leibniz_liskov.scala</td>
    </tr>
    <tr>
      <th>19</th>
      <td>162643301</td>
      <td>src/compiler/scala/tools/nsc/typechecker/Conte...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>162643301</td>
      <td>test/files/pos/parallel-classloader.scala</td>
    </tr>
    <tr>
      <th>21</th>
      <td>162643301</td>
      <td>test/files/pos/t10568/Converter.java</td>
    </tr>
    <tr>
      <th>22</th>
      <td>162643301</td>
      <td>test/files/pos/t10568/Impl.scala</td>
    </tr>
    <tr>
      <th>23</th>
      <td>162256029</td>
      <td>src/compiler/scala/tools/nsc/backend/jvm/PostP...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>162256029</td>
      <td>src/compiler/scala/tools/nsc/backend/jvm/analy...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>162197834</td>
      <td>LICENSE</td>
    </tr>
    <tr>
      <th>26</th>
      <td>162197834</td>
      <td>doc/LICENSE.md</td>
    </tr>
    <tr>
      <th>27</th>
      <td>162197834</td>
      <td>doc/License.rtf</td>
    </tr>
    <tr>
      <th>28</th>
      <td>162197834</td>
      <td>project/VersionUtil.scala</td>
    </tr>
    <tr>
      <th>29</th>
      <td>162197834</td>
      <td>src/library/scala/util/Properties.scala</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>85558</th>
      <td>551651</td>
      <td>test/files/run/enums.scala</td>
    </tr>
    <tr>
      <th>85559</th>
      <td>551505</td>
      <td>src/library/scala/Enumeration.scala</td>
    </tr>
    <tr>
      <th>85560</th>
      <td>551505</td>
      <td>test/files/run/enums.check</td>
    </tr>
    <tr>
      <th>85561</th>
      <td>551505</td>
      <td>test/files/run/enums.scala</td>
    </tr>
    <tr>
      <th>85562</th>
      <td>551474</td>
      <td>src/library/scala/collection/BitSetLike.scala</td>
    </tr>
    <tr>
      <th>85563</th>
      <td>551375</td>
      <td>src/compiler/scala/tools/nsc/doc/html/page/Tem...</td>
    </tr>
    <tr>
      <th>85564</th>
      <td>550508</td>
      <td>test/files/neg/t3481.check</td>
    </tr>
    <tr>
      <th>85565</th>
      <td>550508</td>
      <td>test/files/neg/t3481.scala</td>
    </tr>
    <tr>
      <th>85566</th>
      <td>548900</td>
      <td>src/compiler/scala/tools/nsc/util/SourceFile.s...</td>
    </tr>
    <tr>
      <th>85567</th>
      <td>548838</td>
      <td>test/disabled/scalacheck/redblack.scala</td>
    </tr>
    <tr>
      <th>85568</th>
      <td>548838</td>
      <td>test/files/scalacheck/redblack.scala</td>
    </tr>
    <tr>
      <th>85569</th>
      <td>548744</td>
      <td>src/library/scala/util/matching/Regex.scala</td>
    </tr>
    <tr>
      <th>85570</th>
      <td>548694</td>
      <td>src/library/scala/sys/process/package.scala</td>
    </tr>
    <tr>
      <th>85571</th>
      <td>547941</td>
      <td>src/compiler/scala/reflect/internal/Importers....</td>
    </tr>
    <tr>
      <th>85572</th>
      <td>547941</td>
      <td>src/compiler/scala/reflect/internal/Types.scala</td>
    </tr>
    <tr>
      <th>85573</th>
      <td>547941</td>
      <td>src/compiler/scala/tools/nsc/transform/LiftCod...</td>
    </tr>
    <tr>
      <th>85574</th>
      <td>547941</td>
      <td>test/pending/run/t5230.scala</td>
    </tr>
    <tr>
      <th>85575</th>
      <td>547941</td>
      <td>test/pending/run/t5230_nolift.scala</td>
    </tr>
    <tr>
      <th>85576</th>
      <td>547354</td>
      <td>src/compiler/scala/tools/nsc/util/BitSet.scala</td>
    </tr>
    <tr>
      <th>85577</th>
      <td>547354</td>
      <td>src/library/scala/collection/BitSet.scala</td>
    </tr>
    <tr>
      <th>85578</th>
      <td>547354</td>
      <td>src/library/scala/collection/BitSetLike.scala</td>
    </tr>
    <tr>
      <th>85579</th>
      <td>547354</td>
      <td>src/library/scala/collection/SortedSet.scala</td>
    </tr>
    <tr>
      <th>85580</th>
      <td>547354</td>
      <td>src/library/scala/collection/immutable/BitSet....</td>
    </tr>
    <tr>
      <th>85581</th>
      <td>547354</td>
      <td>src/library/scala/collection/immutable/SortedS...</td>
    </tr>
    <tr>
      <th>85582</th>
      <td>547354</td>
      <td>src/library/scala/collection/mutable/BitSet.scala</td>
    </tr>
    <tr>
      <th>85583</th>
      <td>547354</td>
      <td>test/files/run/bitsets.check</td>
    </tr>
    <tr>
      <th>85584</th>
      <td>547354</td>
      <td>test/files/run/bitsets.scala</td>
    </tr>
    <tr>
      <th>85585</th>
      <td>547150</td>
      <td>src/compiler/scala/tools/nsc/transform/LiftCod...</td>
    </tr>
    <tr>
      <th>85586</th>
      <td>547150</td>
      <td>src/library/scala/reflect/api/Trees.scala</td>
    </tr>
    <tr>
      <th>85587</th>
      <td>547003</td>
      <td>README.rst</td>
    </tr>
  </tbody>
</table>
<p>85588 rows × 2 columns</p>
</div>




```python
# Identify the last 10 pull requests
last_10 = pulls.sort_values('date', ascending=False).iloc[0:10,:]

# Join the two data sets
joined_pr = last_10.merge(pull_files, on='pid', how='left')

# Identify the unique files
files = set(joined_pr['file'])

# Print the results
files
```




    {'LICENSE',
     'doc/LICENSE.md',
     'doc/License.rtf',
     'project/VersionUtil.scala',
     'src/compiler/scala/reflect/reify/phases/Calculate.scala',
     'src/compiler/scala/tools/nsc/backend/jvm/BCodeHelpers.scala',
     'src/compiler/scala/tools/nsc/backend/jvm/PostProcessor.scala',
     'src/compiler/scala/tools/nsc/backend/jvm/analysis/BackendUtils.scala',
     'src/compiler/scala/tools/nsc/profile/AsyncHelper.scala',
     'src/compiler/scala/tools/nsc/profile/Profiler.scala',
     'src/compiler/scala/tools/nsc/symtab/classfile/ClassfileParser.scala',
     'src/compiler/scala/tools/nsc/typechecker/Contexts.scala',
     'src/library/scala/Predef.scala',
     'src/library/scala/concurrent/Lock.scala',
     'src/library/scala/util/Properties.scala',
     'src/reflect/scala/reflect/internal/pickling/ByteCodecs.scala',
     'src/reflect/scala/reflect/internal/tpe/GlbLubs.scala',
     'src/scaladoc/scala/tools/nsc/doc/html/page/Entity.scala',
     'src/scalap/decoder.properties',
     'test/files/neg/leibniz-liskov.check',
     'test/files/neg/leibniz-liskov.scala',
     'test/files/pos/leibniz-liskov.scala',
     'test/files/pos/leibniz_liskov.scala',
     'test/files/pos/parallel-classloader.scala',
     'test/files/pos/t10568/Converter.java',
     'test/files/pos/t10568/Impl.scala',
     'test/files/pos/t10686.scala',
     'test/files/pos/t5638/Among.java',
     'test/files/pos/t5638/Usage.scala',
     'test/files/pos/t9291.scala',
     'test/files/run/t8348.check',
     'test/files/run/t8348/TableColumn.java',
     'test/files/run/t8348/TableColumnImpl.java',
     'test/files/run/t8348/Test.scala'}



## 7. Who made the most pull requests to a given file?
<p>When contributing to a project, we might need some guidance. We might find ourselves needing some information regarding the codebase. It is important direct any questions to the right person. Contributors to open source projects generally have other day jobs, so their time is limited. It is important to address our questions to the right people. One way to identify the right target for our inquiries is by using their contribution history.</p>
<p>We identified <code>src/compiler/scala/reflect/reify/phases/Calculate.scala</code> as being recently changed. We are interested in the top 3 developers who changed that file. Those developers are the ones most likely to have the best understanding of the code.</p>


```python
# This is the file we are interested in:
file = 'src/compiler/scala/reflect/reify/phases/Calculate.scala'

# Identify the commits that changed the file
file_pr = data[data['file'] == file]

# Count the number of changes made by each developer

## 1
# author_counts = file_pr.groupby('user').count()
# author_counts.nlargest(3, 'file')

## 2h
author_counts = file_pr.groupby('user').agg({'pid':'count'})
author_counts.sort_values('pid', ascending=False)[:3]#.nlargest(3, 'file')
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
      <th>pid</th>
    </tr>
    <tr>
      <th>user</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>xeno-by</th>
      <td>11</td>
    </tr>
    <tr>
      <th>retronym</th>
      <td>5</td>
    </tr>
    <tr>
      <th>soc</th>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



## 8. Who made the last ten pull requests on a given file?
<p>Open source projects suffer from fluctuating membership. This makes the problem of finding the right person more challenging: the person has to be knowledgeable <em>and</em> still be involved in the project. A person that contributed a lot in the past might no longer be available (or willing) to help. To get a better understanding, we need to investigate the more recent history of that particular part of the system. </p>
<p>Like in the previous task, we will look at the history of  <code>src/compiler/scala/reflect/reify/phases/Calculate.scala</code>.</p>


```python
file = 'src/compiler/scala/reflect/reify/phases/Calculate.scala'

# Select the pull requests that changed the target file
file_pr = pull_files[pull_files['file'] == file]

# Merge the obtained results with the pulls DataFrame
joined_pr = file_pr.merge(pulls, on='pid')

# Find the users of the last 10 most recent pull requests
users_last_10 = set(joined_pr.nlargest(10, 'date')['user'])

# Printing the results
users_last_10
```




    {'bjornregnell', 'retronym', 'soc', 'starblood', 'xeno-by', 'zuvizudar'}



## 9. The pull requests of two special developers
<p>Now that we have identified two potential contacts in the projects, we need to find the person who was most involved in the project in recent times. That person is most likely to answer our questions. For each calendar year, we are interested in understanding the number of pull requests the authors submitted. This will give us a high-level image of their contribution trend to the project.</p>


```python
pulls.head()
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
      <th>pid</th>
      <th>user</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>11166973</td>
      <td>VladimirNik</td>
      <td>2013-12-31 23:10:55+00:00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>11161892</td>
      <td>Ichoran</td>
      <td>2013-12-31 16:55:47+00:00</td>
    </tr>
    <tr>
      <th>2</th>
      <td>11153894</td>
      <td>Ichoran</td>
      <td>2013-12-31 02:41:13+00:00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11151917</td>
      <td>rklaehn</td>
      <td>2013-12-30 23:45:47+00:00</td>
    </tr>
    <tr>
      <th>4</th>
      <td>11131244</td>
      <td>qerub</td>
      <td>2013-12-29 17:21:01+00:00</td>
    </tr>
  </tbody>
</table>
</div>




```python
%matplotlib inline

# The developers we are interested in
authors = ['xeno-by', 'soc']

# Get all the developers' pull requests
by_author = pulls[pulls['user'].isin(authors)]

# Count the number of pull requests submitted each year
counts = by_author.groupby(['user', by_author['date'].dt.year]).agg({'pid': 'count'}).reset_index()

# Convert the table to a wide format
counts_wide = counts.pivot_table(index='date', columns='user', values='pid', fill_value=0)

# Plot the results
counts_wide.plot(kind='bar')
# ... YOUR CODE FOR TASK 9 ...
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7faddf8a8240>




    
![png](output_19_1.png)
    


## 10. Visualizing the contributions of each developer
<p>As mentioned before, it is important to make a distinction between the global expertise and contribution levels and the contribution levels at a more granular level (file, submodule, etc.) In our case, we want to see which of our two developers of interest have the most experience with the code in a given file. We will measure experience by the number of pull requests submitted that affect that file and how recent those pull requests were submitted.</p>


```python
print(pulls.head())
print(pull_files.head())
```

            pid         user                      date
    0  11166973  VladimirNik 2013-12-31 23:10:55+00:00
    1  11161892      Ichoran 2013-12-31 16:55:47+00:00
    2  11153894      Ichoran 2013-12-31 02:41:13+00:00
    3  11151917      rklaehn 2013-12-30 23:45:47+00:00
    4  11131244        qerub 2013-12-29 17:21:01+00:00
             pid                                   file
    0  163314316        test/files/pos/t5638/Among.java
    1  163314316       test/files/pos/t5638/Usage.scala
    2  163314316             test/files/pos/t9291.scala
    3  163314316             test/files/run/t8348.check
    4  163314316  test/files/run/t8348/TableColumn.java



```python
authors = ['xeno-by', 'soc']
file = 'src/compiler/scala/reflect/reify/phases/Calculate.scala'

# Select the pull requests submitted by the authors, from the `data` DataFrame
by_author = data[data['user'].isin(authors)]

# Select the pull requests that affect the file
by_file = by_author[by_author['file'] == file]

# Group and count the number of PRs done by each user each year
grouped = by_file.groupby(['user', by_file['date'].dt.year]).count()['pid'].reset_index()

# Transform the data into a wide format
by_file_wide = grouped.pivot_table(values='pid',index='date', columns='user')

# Plot the results
by_file_wide.plot(kind='bar')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7faddf804470>




    
![png](output_22_1.png)
    

