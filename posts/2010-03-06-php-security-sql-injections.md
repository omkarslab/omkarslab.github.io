---
id: 142
title: 'PHP Security: SQL Injections'
date: 2010-03-06T16:46:15+00:00
author: Omkar
layout: post
guid: http://www.omkarslab.co.cc/?p=142
permalink: /2010/03/06/php-security-sql-injections/
sfw_comment_form_password:
  - csEK8B4TFYmE
sfw_pwd:
  - xF5DZ3cNdPTX
categories:
  - php
  - PHP Security
tags:
  - application
  - attack
  - comment
  - database
  - escape
  - hack
  - injection
  - mysql
  - php
  - sql
  - sql injection
---
In our previous post &#8220;[Writing secure codes in PHP](http://www.omkarslab.co.cc/2010/02/22/writing-secure-codes-in-php/)&#8221; we discussed about the most basic tips on creating secure PHP applications. Here we provide more insight to one of the threats discussed.

Lets beging with, What is an SQL Injection? In simple terms SQL Injection is an input given to any PHP application through forms or links, which manages to manipulate the SQL statement in the original code and generates an undesirable output. SQL Injections are generally used to get unauthorised access to the database. Even if the attacker is not possible to retrieve any content, there are chances of corrupting the database and the file system. In order to stop such attacks it is very much necessary to understand these attacks.

So here is an example:

Here is my so very trust worthy code to facilitate my users to change their passwords.
  
This is the form to be filled by the user.

 `</p>

<p>` 

The above form will look something like this when you login as user 1:
  
Your User ID:1
  
Change Password:<input name="password" type="text" /><input type="submit" value="Change pass" />

Now here is what I write in the cpass.php that will do the real job of changing the database record for the user.
  
`<br />
<?php
include "sqlconn.php"; /* File containing the SQL connection details with $sqlconnection*/

$user = $_SESSION['id']; /* We take the user ID from the session */
$newpass = $_POST['password']; /* Take the new password */

/* The code below just updates the password field from the table with given ID*/
$query="UPDATE users
SET password='$newpass'
WHERE id='$user'";

mysql_query($query,$sqlconnection);
echo " Your password has been changed!";
?><br />
` 

Suppose the users table looks like this.

<table class="style1" style="width: 100%;" cellspacing="1">
  <tr>
    <td>
      <strong>id</strong>
    </td>
    
    <td>
      <strong>username</strong>
    </td>
    
    <td>
      <strong>password</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      goodjohn
    </td>
    
    <td>
      doom
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      badfred
    </td>
    
    <td>
      rattle
    </td>
  </tr>
</table>

What could probably go wrong here? Well to check, lets see a situation.
  
Lets say the user &#8216;badfred&#8217; with id:2 logs in to change password and enters the text below in the password field.
  
`hacked' WHERE id=1; -- //There is a space after -- which is very important`

After this process, the user table will look like this. As the field input included SQL commands, the query below was run.
  
`<br />
UPDATE users<br />
SET password='hacked' WHERE id=1; -- ' WHERE id=2;<br />
` 
  
and the text beyond &#8212; gets commented as it is a &#8216;comment tag&#8217;.

<table class="style1" style="width: 100%;" cellspacing="1">
  <tr>
    <td>
      <strong>id</strong>
    </td>
    
    <td>
      <strong>username</strong>
    </td>
    
    <td>
      <strong>password</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      1
    </td>
    
    <td>
      goodjohn
    </td>
    
    <td>
      hacked
    </td>
  </tr>
  
  <tr>
    <td>
      2
    </td>
    
    <td>
      badfred
    </td>
    
    <td>
      rattle
    </td>
  </tr>
</table>

What we did here is, we injected a SQL string in an input field. This is popularly know as SQL Injections.
  
The above illustrations is one the most simplest form of SQL injections. A more hybrid attack would involve downloading the whole database or corrupting the database structure.

This is a misconception that PHP has this security glitch. Well, the program just did what you asked it to do. In order to prevent your applications from such attacks it is necessary to filter the inputs that are going to be used in any SQL statment. This can be done as follows.

**<a href="http://in2.php.net/mysql_real_escape_string" target="_blank">mysql_real_escape_string():</a>** This function will escape all the unnecessary characters present in a string to make it safe to use in a MySQL query.

**<a href="http://in2.php.net/manual/en/function.addslashes.php" target="_blank">addslashes():</a>** Helps in adding slashes to characters that need to be commented before use in a SQL Statement.

Note: It is very much necessary to understand the above techniques in order to build a PHP application secure from SQL Injections. But this is not all it takes. There are other attack techniques that need to be dealt with. I repeat this over and over again, that it is the whole and sole responsibility of the programmer to prevent the application from taking any malicious input.

Happy Coding 🙂