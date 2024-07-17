---
layout: post
category: Fundamentals
title: "Edge Cases (and Seeing Around Corners)"
---

I came across an edge case in a side project I've been working on, and that got me thinking about edge cases more generally. How are they defined? Is there a way to go about looking for them somewhat systematically? Let's look at some examples and see if we can start to answer those questions.

###### Classic edge case examples

Say you're implementing some frontend code to add a label next to all items in a store that are less than $10. One edge case, then, would be what happens when an item costs exactly $10.00. But you'd also want to check a very large number, and make sure to handle a negative number (though that may not make logical sense).

Say you're implementing code to sort all the integers in an array in-place, and return the largest one. If you start off with a `largest_num` variable with a value of 0, and set anything above that value to `largest_num`, you miss the edge case where all the numbers are negative, and would incorrectly return 0. And what would you return when the given array is actually empty?

Say you're implementing a linked list (citing [this quora article from 7 years ago](https://www.quora.com/What-is-an-edge-case-when-programming)). You may know what happens when you delete the fifth element of that list, but what about when you delete the first or the last element?

###### How to find edge cases

The key challenge is not in handling the edge cases, but in finding them. And that boils down, primarily, to checking input/output values. Before getting into the below types, check whether values are null, nil, or whatever other undefined value languages may have, and whether the values are in fact of the type you expect.

<span class="list  list--md one"><strong>Integers</strong>. Check a negative, 0, 1, and a positive number, as well as a very large number.</span>
<span class="list  list--md one"><strong>Floats</strong>. Check the same cases as Integers, and also keep the number of decimal places in mind. Also check numbers between -1 and 0, and 0 and 1.</span>
<span class="list  list--md one"><strong>Strings/characters</strong>. Check if they're empty. Check for weird or escape characters, or quotes.</span>
<span class="list  list--md one"><strong>Arrays/hashes</strong>. Check if they're empty, or sorted somehow - then check each value. Also check for duplicates.</span>
<span class="list  list--md one"><strong>Objects</strong>. Check each value in the object.</span>

In addition to this list, also check 'around the edges': if your code deals with even and odd numbers differently, for example, make sure to test each. Or if you're performing some operation on an element in a set, check how that operation works on the first, middle, and last element.

Finally, (and it's possible this is veering slightly outside the realm of edge cases) check other assumptions that you're making in your code. If you're expecting to rely on a particular value, just ask yourself under what circumstances that value may differ.

###### Edge case definition (kind of)

With those examples and the how-to, it's clear that edge cases are situations on the 'edge' of code logic, some simply for types of data, and some concerning the code's logic itself.

###### Seeing around corners

Seeing around corners is the extension of edge cases to project planning, and viewing a project as solving a problem. This phrase seems to be used mostly in business, but I picked it up from an engineer I worked with at my last job to mean more 'anticipating future technical hurdles and edge cases in the planning stage before they come up in implementation.' How do you see around corners to find all the edge cases and ensure the project works effectively for all users? Not a rhetorical question! I view this as a large part of a senior engineer's responsibility.

To me, seeing around corners is accomplished through familiarity with the problem space, experience, and asking questions. And especially that last one if less of the first two.

But to extend the edge case/seeing around corners analogy, seeing around corners involves checking input/output values for entire projects, not just types; that can include common challenges with particular language features, scaling difficulties with components of a tech stack, knowing best practices in several areas, or even just project management timeframe awareness. It's finding and handling edge cases, just on a larger scale.

###### Implications for the app I'm working on

In the test rails app I'm building, I set up tables for three European leagues and got exports working successfully for them (EPL, Serie A, La Liga). But I expected to run into an edge case before long, and I did with the Brasileirao: the line where we find the table in the parsed HTML file with `table = doc.xpath('//table[starts-with(.,results-2023-2024)]').first`. What if the league doesn't run 2023-2024, but only in 2024? A value I didn't initially expect. That's the next edge case to handle.