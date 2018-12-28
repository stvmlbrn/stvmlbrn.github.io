---
layout: post
title: Join Database Tables On Multiple Fields
comments: true
tags: [database]
icon: database.png
---

If you've spent much time on back-end development you probably have at least a basic understanding of database joins - what they are and how they work. At a basic level, joins are a mechanism to utilize primary and foreign keys to fetch related information from multiple tables. In most cases we use a single field from each table to make the connection, but what about a situation where a table may have multiple references to another table? Let's look at a scenario where that may be required and how we can work with it.

The example here is an employee evaluation application. We have a table that contains our company (Pied Piper) employees, and another table that contains the results of our staff evaluations. The evaluation results needs to contain that staff member who is being evaluated as well as the staff member (supervisor) who performed the evaluation. When querying the evalaution table we want to include the name of both the staff member and supervisor, requiring us to join the 'Evaluations' table to the 'Employees' table on 2 different fields.

Here is a simple 'Employees' table in a database that contains information on Pied Piper employees.
<table>
  <thead>
    <tr>
      <th>employeeID</th>
      <th>firstName</th>
      <th>lastName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Erlich</td>
      <td>Bachman</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Richard</td>
      <td>Hendricks</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Dinesh</td>
      <td>Chugtai</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Bertram</td>
      <td>Gilfoyle</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Nelson</td>
      <td>Bighetti</td>
    </tr>
  </tbody>
</table>

Now let's look at the 'Evaluations' table. The 'staffID' field will reference that 'Employees' table as the person who is being evaluated. The 'supervisorID' field will also reference the 'Employees' table as the person performing the evaluation.
<table>
  <thead>
    <tr>
      <th>evaluationID</th>
      <th>staffID</th>
      <th>supervisorID</th>
      <th>evalDate</th>
      <th>score</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>4</td>
      <td>2</td>
      <td>2018-12-01</td>
      <td>Excellent</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3</td>
      <td>2</td>
      <td>2018-12-01</td>
      <td>Excellent</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>4</td>
      <td>2018-12-15</td>
      <td>Poor</td>
    </tr>
  </tbody>
</table>

From looking at this information we can tell the first evaluation was for  Bertram Gilfoyle and was performed by Richard Hendrix. How can we write a query that will include the evaluation results along with the names of both the staff member being evaluated and the supervisor?

The trick here is to use _aliases_. An alias is a way to refer to a database object (field, table, etc) using a different name. In our example we'll be using an alias on the _firstName_ and _lastName_ fields so we can indicate which name is the staff member and which name is the supervisor. We'll also need to join the 'Employees' table to the 'Evaluations' table twice, each time using a different alias.

<pre class="prettyprint lang-sql">
  <code class="language-sql">
    select
      evalDate,
      score,
      staff.firstName as staffFName,
      staff.lastName as staffLName,
      supervisor.firstName as supervisorFName,
      supervisor.lastName as supervisorLName
    from Evaluations
      inner join Employees as staff on
        staffID = staff.employeeID
      inner join Employees as supervisor on
        supervisorID = supervisor.employeeID
    where evaluationID = 1
  </code>
</pre>

The results for this query will look like the following:
<table>
  <thead>
    <tr>
      <th>evalDate</th>
      <th>score</th>
      <th>staffFName</th>
      <th>staffLName</th>
      <th>supervisorFName</th>
      <th>supervisorLName</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>2018-12-01</td>
      <td>Excellent</td>
      <td>Bertram</td>
      <td>Gilfoyle</td>
      <td>Richard</td>
      <td>Hendrix</td>
    </tr>
  </tbody>
</table>
