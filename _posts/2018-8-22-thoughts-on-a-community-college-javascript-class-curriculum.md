---
layout: post
title: Thoughts On A Community College JavaScript Class Curriculum
comments: true
tags: [javascript]
icon: javascript.png
---

I had an opportunity to teach a Computer Science class at our local community college for this upcoming fall semester and was also given the tasks of picking a topic/language and developing my own curriculum. Teaching has always been something I'm interested in so I was really looking forward to this experience. After spending a good bit of time this summer developing curriculum, I have to admit that having never done it before it's been harder than I expected (but still fun!). Unfortunately, after several weeks of planning and with about 1 week left before the start of the semester the class ended up being canceled. That was certainly a big disappointment, but there is a chance that class will run again next semester. I wanted to share my thought process as well as the general outline of the class topics, and would appreciate any feedback from any readers.

Here are some general thoughts and information about the class and how I decided to approach things:

* This is a 190 level class and the students are just starting the CS education. They have not done any in-depth programming yet. Also, this class was to be 100% online.
* I debated on whether to do a boot-camp style course with a focus on more real-world type problems, or a more 'academic' approach to keep the focus on learning JavaScript language and programming fundamentals (I realize these are not mutually exclusive, but it helped to focus my planning). I chose to lean towards the 'academic' approach.
* After deciding to keep things focused on the JavaScript language I decided I did not want to include any browser-related stuff (no DOM or web-page interaction). There are other courses dedicated to web development I thought would be more appropriate for these topics.
* I initially chose [repl.it](https://repl.it) for my runtime environment. It looks really cool, and I think I will definitely use it in the future at some point, but decided that I wanted to keep things as simple as possible for now. So I then decided that I was going to use the browser and just console.log() everything we do. Finally, a little over a week before class was to start, I decided I was going to use Node.js. It was after watching some of the [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/javascript-fundamentals-for-absolute-beginners-14194) videos and liking the workflow they used that I decided to use Node.js.
* Lessons were designed to use a video presentation to introduce concepts and definitions and/or online reading materials (relying heavily on content from the Mozilla Developer Network) followed by a coding screencast that I recorded.
* No additional costs for this class! No textbook or additional software.
* There would be graded homework nearly every week, plus mid-term and final exams.
* Extra credit for attending and contributing at one of our local developer group meetups - [Queen City Hacks](https://twitter.com/queencityhacks)

Lesson sequence:

<table class="table table-condensed">
  <colgroup>
    <col width="30%">
    <col width="70%">
  </colgroup>
  <thead>
    <tr>
      <th>Week</th>
      <th>Topic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Class introduction and getting started overview</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Variables / Scope / Operators</td>
    </tr>
    <tr>
      <td>3</td>
      <td>Functions</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Control Structures and Error Handling</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Loops</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Strings, Numbers, and Dates</td>
    </tr>
    <tr>
      <td>7</td>
      <td>Objects (JSON)</td>
    </tr>
    <tr>
      <td>8</td>
      <td>Arrays Pt. 1</td>
    </tr>
    <tr>
      <td>9</td>
      <td>Arrays Pt. 2</td>
    </tr>
    <tr>
      <td>10</td>
      <td>Copying: Value vs Reference</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Advanced Functions</td>
    </tr>
    <tr>
      <td>12</td>
      <td>Intro to OOP in JavaScript</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Object Prototypes</td>
    </tr>
    <tr>
      <td>14</td>
      <td>JavaScript Ecosystem</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Classes</td>
    </tr>
  </tbody>
</table>

I can go into more detail about the content of particular lessons if anyone is interested. But to touch on a few things:

* I introduce functions earlier than a lot of other beginner tutorials I see. I think it would help with subsuquent lessons to have a basic understanding of functions.
* The Advanced Functions lesson is where we get into more advanced topics such as parameter destructuring, default values, arguments[], rest parameters, etc.
* There is a lot to cover with arrays! Part 1 is basic array syntax, push/pop, slice, etc. Part 2 we get into iteration methods such as .map(), .forEach(), .filter(), etc.
* The JavaScript Ecosystem lesson may seem out of place. That is Thanksgiving week and I wasn't going to assign any homework. Instead, I thought we would switch gears for a bit and I'd do a brief presentation on a lot of buzzwords and what they mean (frameworks, build tools, etc).

So, whatcha think? is there anything you would change or do different?
