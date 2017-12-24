---
title: The Story of SAM
date: 2017-12-24 14:51:55
banner: /the-story-of-sam/sam-story-thumbnail.png
thumbnail: /the-story-of-sam/sam-story-thumbnail.png
categories:
- Story
tags:
- SAM Platform
- Programming
---

{% blockquote Yuchen Zhong %}
Every line of code is a breakthrough of myself.
{% endblockquote %}

SAM, originally launched in 2014, was a campus homework platform built for students and teachers. From the time (Sept. 2016) I joined the Computerization, I have been working on its updates, bringing new functions to it. Last Semester, I worked with Jedi and used the whole semester on the new User Interface of old SAM. Eventually, we finished the project in June, just before the end of the semester. Although SAM looked very different from previous one, still few students and teachers used it. A collaboration system with a so small scope seems no longer in demand. 

#### Reconstruct, Restart, Revive
In June 2017, I took over the Computerization. At that time, the homework function was almost replaced by We-chat groups within the campus, and the program and code of SAM become more and more complex and hard to maintain. Therefore, I decided to re-construct the SAM platform on both function and technical hierarchy.

What function to work on becomes the problem I’m unable to answer, since I don’t know students and teachers’ demand. I tried hard to find a companion for our Computerization by collaborating with them and providing them powerful online features as well as solutions they want. The student union of WFLA becomes our first partner. In June 2017, it’s close to the end of the semester. It was also the time for annual student organization assessment. The assessment process requires participants of that organization to perform a presentation, and the teachers and students off the stage will assess their performance. How to effectively collect answer from these audience becomes a problem for student unions. Payton Guo, the minister of student organization department, discussed with me about the feasibility to build a data collection application for the assessment. She provided the detailed list of function needed, and it was already June 20, less than 10 days before the assessment activity.

Before the task, I have previously no experience on building such a large application. What’s more, I have never handle such an urgent task within 10 days. I start the project with anxiety and concerns. I spent two days reading documents of related development tools and skills, familiar with website coding techniques, and try to built sample units to examine the skills I learned. Then, June 23, 2017, marked the formal start of new SAM project, with its first function of data collecting, similar to voting. In June 24, 2017, after a whole day’s exploration in programming, I posted the first part of code of new SAM on github. It only contains one basic module, which is user login and register. For me, it is a breakthrough, because it is the first time when I have the ability to write HTML, which is user interface; PHP, which is background data processing logic; MySQL, which is database operation all three at a time. These are basic skills in website development, but it helped me later to build more powerful and amazing features. At the end, I spent two days to build the new SAM with voting function, another day to fix bug and to meet the requirements of Student Union. In June 26, 2017, I posted the last update and bug fix of the new SAM. Shortly after, all its code is deployed to a server and ready for use.

The challenge comes on the last day of the semester. For the whole afternoon, the students and teachers are using the platform for presentation rating. They send totally near one thousand scores to the platform in 3 hours. The platform is reliable during the whole process and receives positive feedback. SAM, Computerization and I are trusted by the student union from then on.

#### Further Cooperation
From then on, Computerization and I had built a solid relationship with student union. We have collaborated on three major projects later.

On August 12, 2017, the last day in my college trip, I received a message from minister of organization about their new demand on functions of SAM platform. She was working hard to arrange classrooms for all student organizations next semester. She wanted to let all student organizations choose their classrooms and places of activity by themselves, but quickly a problem encountered: How. How to let the classroom chosen finished without conflicts: what if two organizations want one classroom at the same time? A quick solution is to use an online reservation platform: the one who reserved his classroom early will get the classroom.

All details about the reservation function was clear. Because the module wasn’t complex, I finished the whole function in 12 hours – Yes, I’m working on it on my flight back. I deployed this function to server in August 14. This holds the fastest record for me to finish a project – within 4 days from receiving demand to giving out full application. The function was opened to all student organizations several days later. And it was proven to be reliable again.

On the beginning of this semester, a great opportunity of new SAM came with the annual organization recruiting season. The idea came that our Computerization needed a way other than We-chat public channel to publicize ourselves. Then I realized that I can help other organizations publicize with my effort. Therefore, I decided to create a new function called student organization. It consists of an organization home page and a recruiting page for every organization registered on our SAM platform. This function was mainly done on the first week of this semester, two weeks before the recruiting week.

Tons of new features are added to this update of SAM. Image uploading, Online Text Editing, Search Function, Portal Page…. In one side, I used up all my techniques in programming to build this update, and learned many skills on website programming. On the other side, SAM were becoming more and more powerful. It was no longer a collection of tiny functions, but rather a comprehensive student platform.

At the beginning of this semester, there were only one recruiting page on the platform – ourselves, Computerization. Luckily, the student union discovered this project and publicized it to all student organizations. On September 12, 2017, one week before recruiting days, over 50 student organizations uploaded their profile to our platform. SAM obtained tens of thousands of visits and thousands of visitors during the whole recruiting season. 

The platform helped new students looking for their ideal organizations and activities, but it rarely helped students connect with organizations. Most organizations didn’t open their recruiting channel on SAM while most student only viewed profile of organization on SAM, ignoring the ‘Join that organization’ button. In the whole recruiting week, only 15 apply request to organizations were sent through our platform. ‘SAM was not powerful enough’, I thought after the event. A student platform should not only aim at publicize activities and organizations, but also connect students, schools, competitions, organizations toward each other. The idea led to changes in plan of SAM. New features that empowered these connections were listed in the plan.

#### Challenges, Redefined
On the beginning of December, 2017, I received the most challenging task I have ever handle in SAM project. The annual event of our school – the 2017 Why Charity Event was coming. Ian Zheng, minister of student union discussed with me for a solution in a Charity Auction. In order to sell 5 paintings in charity, an auction system was required for the transaction. Its difficulties were similar to previous ones: new technology required, short preparation time. That’s not all strict requirements. Additionally, 500 hundred users would bid at the same time. Through rough calculation, SAM server will handle up to 10 requests in 1 second, which was a demanding requirement for performance of program.

In order to meet such requirements, I learned to write website in an unfamiliar programming language, Javascript, in one week’s time. This time, I was out of my ‘comfort zone’. New language meant that I had to debug all potential faults with no previous experience. The development process proved the above statement to be true. To fix a bug in real-time price broadcasting, I searched on the internet for hours, looking for similar cases and reading official documentations, and eventually fixed it with tremendous difficulties. What’s worse, similar faults (bugs) occur everywhere in the program, and each debug process was equivalently lengthy. It’s true that I was exhausted, but every bug I removed from the program was a breakthrough of myself.

After resolving all technical issues, I began discussing with student union about the user interface (UI). If SAM was a product, their role was similar to product managers, and I was similar to (actually was) programmers. Their demand on UI was always changing. To satisfy these changings, I modified code back and forth. The UI was increasingly elegant and fascinating.

The most challenge part in the development of auction system came on the day of Charity and Auction. On 3pm, December 23, 2017, 4 hours before the auction event, a serious problem was discovered in website, which causes every Apple computers and phones unable to refresh the price in auction. I was no longer able search for solutions on Internet because of its urgency. I rewrote part of program in two hours’ time, replaced every possible line of code which causes the fault, and went through necessary uploading and testing in another one hour.

The Why Charity 2017 started on time at 6 pm, December 23, 2017. I sat backstage and oversaw the status of server where SAM was hosted. Internet Traffic and server load burst out when one hundred people were visiting our website at the same time. Luckily, the server didn’t go down at last and the auction process was smooth going.

#### Future of SAM
Except for stories of myself building SAM, I was managing the Computerization and building a development team. When SAM was larger and larger in scale, it became increasingly difficult for me to maintain it by myself. I had also made future plans for SAM in next semester, with two extremely powerful features on TO-DO list. I’m sure that when SAM is equipped with these two features, it will become a platform connecting all high school students and organizations of Shanghai, not only WFLAers. Our development team will speed up the cadence of developing, and our product SAM will defy expectations.
