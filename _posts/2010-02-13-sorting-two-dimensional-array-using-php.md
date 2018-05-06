---
id: 115
title: Sorting two dimensional array using PHP
date: 2010-02-13T09:18:21+00:00
author: Omkar
excerpt: How to sort multi-dimensional or 2d array in PHP
layout: post
guid: http://omkarslab.co.cc/?p=115
permalink: /2010/02/13/sorting-two-dimensional-array-using-php/
sfw_comment_form_password:
  - fJwerFeNsSMy
sfw_pwd:
  - itafjHRy1JCS
categories:
  - php
tags:
  - 2d
  - array
  - asort
  - multi-dimensional
  - php
---
Usually when displaying search results it is quite important to sort them according to the relevant field of the records. A search result containing records with just one field can be easily sorted by storing them in an array and using the asort() or arsort() functions in PHP.

 `"Adam", '2' => "Jessie", '3' => "Harvey");<br />
asort($results);<br />
print_r($results);<br />
?><br />
// OUTPUT<br />
Array ( [1] => Adam [3] => Harvey [2] => Jessie )<br />
` 

But things get a little complicated while dealing with multidimensional arrays as shown below

`<br />
array('name' => "Adam", 'age' => "32"),<br />
'2' => array('name' => "Jessie", 'age' => "19"),<br />
'3' => array('name' => "John", 'age' => "24"),<br />
);<br />
?><br />
` 

In this case we are not only storing the names but also the age of the users. This compels us to use a multidimensional array which could hold the name and age data together. There is a simple solution to this issue.

Let me demonstrate this in an example. In the above case we intend to sort the users according to their age (and not their name). So the array key we need to consider while sorting is &#8220;age&#8221;. Now&#8230;

Step 1: We need to store all the elements of the sub-array into a temporary array for the purpose of sorting.
  
Step 2: We then sort this temp array using our conventional method of asort();
  
Step 3: We will take advantage of the tendency of this function, of not disturbing the key values. Precisely we will get the array with its keys rearranged. In this case &#8220;2, 3, 1&#8221;.
  
Step 4: Now we store the record of the user under key=2 in the 1st place, 3 in the 2nd and so on until the last.
  
Step 5: Recline in the chair and give a smile to the sorted array 😉

Here is the function for this method&#8230;
  
`<br />
function subval_sort($a,$subkey) {<br />
foreach($a as $k=>$v) {<br />
$b[$k] = strtolower($v[$subkey]);<br />
}<br />
asort($b);<br />
print_r($b);<br />
foreach($b as $key=>$val) {<br />
$c[] = $a[$key];<br />
}<br />
return $c;<br />
}`

`$results = subval_sort($results,"age");<br />
print_r($results);</p>
<p>`

`//OUTPUT<br />
Array ( [0] => Array ( [name] => Jessie [age] => 19 ) [1] => Array ( [name] => John [age] => 24 ) [2] => Array ( [name] => Adam [age] => 32 ) )<br />
` 

I was desperately looking for such a method and found it on <a href="http://firsttube.com/read/sorting-a-multi-dimensional-array-with-php/" target="_blank">FirstTube.com</a> but it lacked the explanation.

Hope you find it useful! 🙂