---
layout: post
title: Product filters using jQuery
---

We were working on making changes in an e-commerce website where we needed to filter products based on the attributes they have, like colour, size, shape etc. In our case we were having products which have multiple attributes and values, so a product can be available in multiple colors in different sizes. Our major concern was how to identify the set of products to display by filtering them based on these multiple attributes. 

We would like to show an incremental solution we adopted to this problem. [**The jQuery filter method**](http://api.jquery.com/filter) was very helpful in approaching to a solution to this problem.

<strong>Problem (level 1) Single Value Single Attribute:</strong> We have a grid of products which have a single attribute (say colour) and a section of filter checkboxes which would filter out the products based on colour as per the checkboxes selected. This means that every product can have only one color.

<img class="alignnone  wp-image-3328" src="/images/image004.png" alt="image004" width="93" height="74" />  <img class="alignnone  wp-image-3327" src="/images/image003.png" alt="image003" width="96" height="76" />   <img class="alignnone  wp-image-3326" src="/images/image002.png" alt="image002" width="97" height="77" />   <img class="alignnone  wp-image-3325" src="/images/image001.png" alt="image001" width="95" height="74" />

Whenever a filter checkbox is clicked
<ol>
  <li> Hide all products </li>
  <li> Find all the checkboxes with a checked state </li>
        <li> Create a filter string matching the colors selected </li>
        <li> Filter products </li>
  <li> Show the products that fulfill the filteration criteria </li>
</ol>

Here is the working example:

<a class="jsbin-embed" href="http://jsbin.com/wabuvo/4/embed?output">Product Filter Level 1</a>

<strong>Problem (level 2) Multiple Attributes Single Value:</strong>  We have grid of products which have more than one attribute (say colour , size , shape) with a single value and a section of filter checkboxes which would filter out the products based on these attributes as per the checkboxes selected. Every product in this case can have only one color, one shape and one size.

<img class="alignnone size-medium wp-image-3329" src="/images/image008.png" alt="image008" width="129" height="86" />     <img class="alignnone size-medium wp-image-3330" src="/images/image009.png" alt="image009" width="84" height="86" />     <img class="alignnone size-medium wp-image-3332" src="/images/image011.png" alt="image011" width="86" height="88" />    <img class="alignnone size-medium wp-image-3331" src="/images/image010.png" alt="image010" width="102" height="93" />

Suppose we want to filter by color ‘Red’ and size ‘Large’ When more than one checkbox is selected from each attribute then we would need all the possible combinations. Let’s take an example: Selected checkboxes from colours – Red , Blue.  Selected checkboxes from size – Medium, Small. Now, the possible combinations would be
<ol>
  <li>Red Medium</li>
  <li>Red Small</li>
  <li>Blue Medium</li>
  <li>Blue Small</li>
</ol>
But what if the data set grows. Say we have 10 different colours and 15 different sizes. How can we find the combinations if say 4 colors and 3 sizes are selected? What if we add another attribute shape? The data set would keep growing and thus making it difficult to find the possible combinations. In order to tackle this problem, what we did was whenever any checkbox was selected we sorted the original set of products based on all the attributes present. 

On each checkbox click:
<ol>
  <li> Hide all products </li>
  <li> Iterate over an attribute (say color)</li>
        <li> Find the checkboxes selected for that attribute (say red and blue) </li>
        <li> Create filter string ([data-color='red'], [data-color='blue'])</li>
  <li> Filter the original set of products and store these </li>
  <li> Continue the same process on the filtered set of products iteratively for the remaining attributes(size, shape) </li>
</ol>

Then show the final set of filtered products

<a class="jsbin-embed" href="http://jsbin.com/hewax/3/embed?output">Product Filter Level 2</a>

<strong>Problem (level 3) Multiple Attributes Multiple Value Combinations:</strong> We have grid of products which have more than one attribute (say colour , size) with multiple value combinations and a section of filter checkboxes which would filter out the products based on these attributes as per the checkboxes selected. Here, the problem is a product can be available in more than one color-size combination. For example, Product 1 has the following combinations (or variants) available: Red and L, Red and M,  Green and L.

In order to tackle this problem, we can consider a product as a large box with hidden small product boxes( or combinations) having the attributes (multiple attributes single value i.e. product level 2). We can filter the hidden small boxes but show and hide their parent large boxes.

Let’s do it with an example. Consider two products:

Product 1 and Product 2 (large boxes)

Variants/Small Boxes for Product 1- Red L, Red M, Green L, Blue S
Variants/Small Boxes for Product 2- Red S, Yellow M, Green L, Blue XL

<img class="alignnone size-full wp-image-3343" src="/images/image023.png" alt="image023" width="220" height="166" />       <img class="alignnone size-full wp-image-3344" src="/images/image024.png" alt="image024" width="202" height="166" />

Here, whenever a filter is selected the small hidden boxes are filtered and their parent bigger product boxes are shown/hidden.
<table>
<tbody>
<tr>
<td width="142"><strong>Selection</strong></td>
<td width="142"><strong>Product 1</strong></td>
<td width="142"><strong>Product 2</strong></td>
</tr>
<tr>
<td width="142">Red</td>
<td width="142">Visible</td>
<td width="142">Visible</td>
</tr>
<tr>
<td width="142">Yellow</td>
<td width="142">Hidden</td>
<td width="142">Visible</td>
</tr>
<tr>
<td width="142">S</td>
<td width="142">Visible</td>
<td width="142">Visible</td>
</tr>
<tr>
<td width="142">Green L</td>
<td width="142">Visible</td>
<td width="142">Visible</td>
</tr>
<tr>
<td width="142">Blue S</td>
<td width="142">Hidden</td>
<td width="142">Hidden</td>
</tr>
<tr>
<td width="142">Red L</td>
<td width="142">Visible</td>
<td width="142">Hidden</td>
</tr>
</tbody>
</table>

<a class="jsbin-embed" href="http://jsbin.com/zisaqa/4/embed?output">Product Filter Level 3</a><script src="http://static.jsbin.com/js/embed.js"></script>

This solved our problem.

Please share your suggestions to improve this implementation we would be happy to try it out. 