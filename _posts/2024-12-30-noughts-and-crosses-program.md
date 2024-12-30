---
layout: post
title: Noughts and Crosses program
date: '2024-12-30 01:02:44 +0000'
---
<h3>Overview</h3> 
For my second programming assignment for university, I was challenged to create a noughts and crosses (tic tac toe) game in C++ the goals were to:
<ul>
<li>1.	Modular function methodology </li>
<li>2.	User interface displaying the game grid </li> 
<li>3.	Interaction with users </li>
<li>4.	Loops which allow a rematch and end when a winner is found </li>
<li>5.	Additional features  </li>
</ul>
[my noughts and crosses program](https://github.com/RynTurtle/Tic-Tac-Toe) included the specifications wanted and I was pretty happy with how it turned out, it definitely came with some challenges which were unique since I hadn’t made a game with an interface like this.

<h3>Additional features</h3> 

The first challenge I came across was getting the menus and game look how I wanted, I had to figure out the size of text and the window size I wanted, the spaces needed for the game grid not to break and how I wanted the menus to function. My first iteration of the game was very simple with a grid of singular lines and info text at the bottom however it was clear it could definitely be improved so I wrote down some ideas and drew a sort of mock example of how I would like it to look. After being happy with what I saw I began to create it, 
After having the main look down, I added colour to the grid created with colours based on the player which I thought was a nice touch. Also sound which was played during the menu screens, when a user won and when the bot won.
My main challenge was introducing the minimax algorithm for my “against bot” option, I had never implemented an algorithm like this before so this was probably the hardest challenge for me, I had to do a bunch of research to try and understand the recursive flow of how the function worked by calling itself, so I looked at many guides and informative videos, used some references to ensure I was doing it properly and managed to write it and implement it within my tic tac toe class successfully. 

<h3> Design documentation </h3>
The design documentation aspect of this assignment was also a first since I would normally just create a project I had in my mind, I had to create a function catalog explaining each function, a high level flow chart and test logs, I think the first two went fine and I managed to do them fairly easily but creating a flow chart was definitely a challenge to show the flow of the program in a simple way, my finished version did the job but I wasnt happy with how messy it looked, I know after doing more of these ill definitely improve though so I will post more about any future flow charts I create. 


<h3> Images </h3>


![example #1](/assets/img/tictac-menu.jpg){:height="200"}![example #2](/assets/img/tictac-game.jpg){:height="200"}
![example #3](/assets/img/tictac-leaderboard.jpg){:height="200"}