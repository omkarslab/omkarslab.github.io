---
id: 133
title: Writing secure codes in PHP
date: 2010-02-22T17:35:54+00:00
author: Omkar
excerpt: basic techniques to secure your PHP applications
layout: post
guid: http://omkarslab.co.cc/?p=133
permalink: /2010/02/22/writing-secure-codes-in-php/
sfw_comment_form_password:
  - JavPJakXAfsO
sfw_pwd:
  - 0dvSVn0c25f1
categories:
  - php
  - PHP Security
  - Security
tags:
  - attack
  - hack
  - injection
  - php
  - Security
  - sql
  - xss
---
During the era of a rapidly growing Internet market security has to be one of the prime concerns of a web developer. Today the content online includes something more than plain text. Personal details, bank transactions, socialising details are few of the many things stored on the Internet by users who blindly trust service providers for securing it.  As a beginner in any web-development area, a question arises. How shall I secure my application?

Its always good to believe that there are n number of ways to mess up with your application. This is not just applicable for PHP but to any programming language. Writing a secure code is the whole and sole responsibility of the programmer, and this is the point missed out by many.

PHP being a widely used programming language on the Internet is considered more vulnerable to exploits for no proper reason. The only reason that justifies this statement is, that more beginners use PHP and happen to miss out the security essentials. Always remember, the program does what you ask it to do. Nothing less or nothing more.

**Good habits while writing a code.**

  * **Filter both Input and Output:** It is a good practice to filter each and every input taken from the user from Forms, or URLs and do the same while displaying it. You should never trust the user on the type of input he will be sending. This will help your website reduce attacks like SQL Injections and XSS (Cross-site scripting).
  * **Accept only the allowed:** While filtering data, instead of checking for unwanted characters, check if it contains only allowed characters. This reduces the risk of any bypassing of the filters.
  * **Disable Register Globals:** In PHP register_globals should always be OFF. Since PHP 5.3.0 this feature has been deprecated, and removed from PHP 6. 0 .0. For more info check PHP manual for <a href="http://php.net/manual/en/security.globals.php" target="_blank">Using register globals</a>.
  * **Avoid use of REQUEST:** It is always good to avoid the use of REQUEST, and instead use an appropriate superglobal to access contents submitted by user. REQUEST takes in contents from GET,POST and COOKIE. This reduces the efforts of the attackers while forging submitted data.
  * **Use only Cookies for sessions:** Set the php.ini to use only cookies for sessions as nearly all browsers today support cookies. Setting it OFF avails to users being vulnerable to attackers who use URLs for session fixation.

Most the above settings might be accessible only to your hosting provider. It is suggested that you bring the vulnerable settings to your providers notice and get them changed.

**Special PHP functions that help keep code secure.**

  * **<a href="http://php.net/manual/en/function.addslashes.php" target="_blank">addslashes</a>:** It adds a slash (\) before characters that need to be quoted in database queries, Prevents: SQL Injections.
  * **<a href="http://in2.php.net/manual/en/function.htmlentities.php" target="_blank">htmlentities</a>:** It is useful while displaying/echoing data, and bypass any stray HTML tags or characters. Prevents: XSS.
  * **<a href="http://in2.php.net/manual/en/function.strip-tags.php" target="_blank">strip_tags</a>:** As the name suggests it simply strips the string of any HTML and PHP tags.

There are a lot more functions in PHP that can help in filtering data like preg_match,ctype and more. It is always suggested to keep track of the PHP manual found on <a href="www.php.net" target="_blank">www.php.net</a> as it provides a vast resource of ready made PHP codes from users and updates.

_All the techniques mentioned above are not enough to secure your application completely, but ensures the basic level of security. Always create your code considering your application vulnerable to the worlds ugliest hacker even if you don&#8217;t expect many visitors. This in itself will help you stay cautious with your code._

More details about XSS, SQL Injections and proper use of filter functions will be discussed in my future articles independently.

Source: The above content has been derived from PHP Security session at <a href="http://gnunify.in" target="_blank">GNUnify</a> 2010 by Aditya Mooley from <a href="http://www.sanisoft.com/" target="_blank">SANIsoft</a>, Nagpur.