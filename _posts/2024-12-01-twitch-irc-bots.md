---
layout: post
title: Twitch IRC Bots
date: 2024-12-01 19:47 +0000
---
<h3> start of my programming journey </h3>
I began learning how to program whilst being heavily involved in the twitch streaming community and so it was natural to try to make an IRC chatbot to show off cool commands and have fun, as I got more involved into programming it became a very big reward to see my commands being used and enjoyed by other users on the platform. The bot I created was called SnappingBot which was hosted on a raspberry pi 4, it had various of fun [commands](https://github.com/RynTurtle/Snappingbot-Commands) As you can imagine since I was learning whilst creating the code was pretty bad but I did try to improve everyday finally ending with four versions of the bot each being better and cleaner than the previous. 

<h3> GUI application turtlerino </h3>
Whilst working on SnappingBot I thought “wouldn’t it be fun if users could use fun commands for themselves” and that’s when [turtlerino]( https://github.com/RynTurtle/turtlerino) was made, the name came from a very popular chat client [Chatterino](https://chatterino.com/) used by twitch users to enhance their chatting experience, turtlerino could do some cool things like rainbow usernames by typing /color {hexcode} after each message to show off to other users, a ratelimit to allow users to spam very fast based on twitch’s limits and custom commands that a user can invoke for a combination of messages being sent. 
I used the GUI library tkinter and wanted to improve the look so I changed to PyQt5 and found it very interesting and fun to mess with. The IRC section was imported from a lot of the things I did whilst making SnappingBot so I had a nice head start which allowed me to focus on creating interesting unique commands. 

<h3> End of twitch IRC bots </h3>
The bot was enjoyed by 339 different channels, whilst becoming more popular I had to improve each time to handle all the data being received and handled which became a difficult and fun challenge. Sadly after multiple iterations of the bot and the constant challenges I became burnt out from all stress of trying to do better and therefore lost of motivation for the project and terminated the bot and added turtlerino to the archive, about a year later twitch implemented API restrictions to need more permissions for a lot of the commands I used so after time I moved onto other new and more exciting projects. 
