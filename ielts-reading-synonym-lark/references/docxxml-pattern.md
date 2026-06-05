# DocxXML Patterns

## Main Reading Section

```xml
<h1>Part 1</h1>
<p></p>
<h2>READING_TITLE</h2>
<blockquote><p>访问路径：SOURCE_PATH</p></blockquote>
<p></p>

<!-- Optional: only when repeated vocabulary exists -->
<h3>重复出现</h3>
<table>
  <thead>
    <tr>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">重复原词</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">含义</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">常见替换</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">替换词含义</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">出现形式</p></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td vertical-align="middle"><p align="center"><span background-color="light-red">source</span></p></td>
      <td vertical-align="middle"><p align="center">名词, ...</p></td>
      <td vertical-align="middle"><p align="center">replacement A / replacement B</p></td>
      <td vertical-align="middle"><p align="center">...</p></td>
      <td vertical-align="middle"><p align="center">A -> B/C</p></td>
    </tr>
  </tbody>
</table>
<p></p>

<table>
  <colgroup><col width="201"/><col width="201"/><col width="194"/><col width="142"/></colgroup>
  <thead>
    <tr>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">文章原词</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">含义</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">题目替换词</p></th>
      <th background-color="rgb(242,243,245)" vertical-align="middle"><p align="center">含义</p></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td vertical-align="middle"><p align="center">passage word or phrase</p></td>
      <td vertical-align="middle"><p align="center">含义或词性 + 含义</p></td>
      <td vertical-align="middle"><p align="center"><span background-color="light-red">question replacement</span></p></td>
      <td vertical-align="middle"><p align="center">含义或词性 + 含义</p></td>
    </tr>
  </tbody>
</table>
<p></p>
```

## High-Frequency Highlight Heuristics

Prefer light red for terms that are reusable across IELTS Reading:

- academic verbs: `introduce`, `increase`, `prevent`, `criticise`, `forecast`, `control`, `spread`
- result/failure words: `outcome`, `failure`, `flaw`
- nature/science categories: `species`, `native`, `predator`, `fauna`, `habitat`, `pesticide`
- scope/time words: `throughout`, `immediately`, `prior to`, `following`
- common category shifts: specific animal/plant/insect -> broader category

Avoid highlighting every English token. The highlight should tell the student what is worth memorizing.
