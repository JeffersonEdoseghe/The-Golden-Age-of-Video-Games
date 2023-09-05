# The-Golden-Age-of-Video-Games

***

# Introduction
To complete the Data Analyst in SQL track on Datacamp, I had to undertake a project.
In this project, I analyzed video game critic and user scores as well as sales data for the top 400 video games released between 1977 and 2020. 
My aim was to pinpoint the era in which video games reached their peak satisfaction among users and critics. 
Additionally, I explored the business side of gaming by looking at game sales data.

***

# Background
Video games are big business - the global gaming market is projected to be worth more than $300 billion by 2027 according to Mordor Intelligence. 
With so much money at stake, the major game publishers are hugely incentivized to create the next big hit. 
But are games getting better, or has the golden age of video games already passed?

***

# Exploratory Data Analysis
I explored the tables and answered several questions to gain insights into the golden age of video games. 
Here is a preview of the information schema of the two tables in the database.

![image](https://github.com/JeffersonEdoseghe/The-Golden-Age-of-Video-Games/assets/139655810/d1548a56-125b-4d7a-a18b-5cbeacff7555)

These questions formed the project tasks:
1. Finding the ten best-selling video games
2. Determining how many games were missing reviews
3. Finding the years with the highest average critic score
4. Finding game critics' ten favorite years. This time with the condition that a year must have more than four games released to be considered
5. Finding the years that dropped off the critics' favorites list as a result of the condition above
6. Determining years video game players loved
7. Determining the years that both players and critics loved
8. Lastly, determining the sales in the best video game years

***

# Solutions
1. Finding the ten best-selling video games

SELECT *

FROM game_sales

ORDER BY games_sold DESC

LIMIT 10;

**Insight:** The best-selling video games were released between 1985 to 2017.

***

2. Determining how many games were missing reviews

SELECT COUNT(*)

FROM game_sales

LEFT JOIN reviews

USING(game)

WHERE critic_score IS NULL

AND user_score IS NULL;

**Insight:** A little less than ten percent of the games on the game_sales table don't have any review data.

***

3. Finding the years with the highest average critic score

SELECT year, ROUND(AVG(critic_score), 2) AS avg_critic_score

FROM game_sales

INNER JOIN reviews

USING(game)

GROUP BY year

ORDER BY avg_critic_score DESC

LIMIT 10;

**Insight:** The range of great years according to critic reviews goes from 1982 until 2020. But were these years really that great?

***

4. Finding game critics' ten favorite years. This time with the condition that a year must have more than four games released to be considered

SELECT year, ROUND(AVG(critic_score), 2) AS avg_critic_score, COUNT(game) AS num_games

FROM game_sales

INNER JOIN reviews

USING(game)

GROUP BY year

HAVING COUNT(game) > 4

ORDER BY avg_critic_score DESC

LIMIT 10;

**Insight**: The num_games column convinces us that our new list of the critics' top games reflects years that had quite a few well-reviewed games rather than just one or two hits.

It might be a good idea to identify years that dropped off the list due to having four or fewer reviewed games so that someday we can track down more game reviews for those years and determine whether they might rightfully be considered excellent years for video game releases.

***

5. Finding the years that dropped off the critics' favorites list as a result of the condition above

Two tables were created with the results of our previous two queries. Here is a preview of their information schema.

![image](https://github.com/JeffersonEdoseghe/The-Golden-Age-of-Video-Games/assets/139655810/eb2d4c51-6b44-4c00-865b-a2462fe4370f)

SELECT year, avg_critic_score

FROM top_critic_years

WHERE year NOT IN
 
(SELECT year
               
FROM top_critic_years_more_than_four_games)
               
ORDER BY avg_critic_score DESC;

**Insight:** It looks like the early 1990s might merit consideration as the golden age of video games based on critic_score alone, but we would need to gather more games and review data to do further analysis.

***

6. Determining the years video game players loved

SELECT year, ROUND(AVG(user_score), 2) AS avg_user_score, COUNT(game) AS num_games

FROM game_sales

INNER JOIN reviews

USING(game)

GROUP BY year

HAVING COUNT(game) > 4

ORDER BY avg_user_score DESC

LIMIT 10;

**Insight:** We have a list of the top ten years according to both critic reviews and user reviews.

![image](https://github.com/JeffersonEdoseghe/The-Golden-Age-of-Video-Games/assets/139655810/016112ad-8f71-4930-802a-63bd8c16af3d)

***

7. Determining the years that both players and critics loved

SELECT year

FROM top_critic_years_more_than_four_games

INTERSECT

SELECT year

FROM top_user_years_more_than_four_games;

**Insight:** We have got the three years that both users and critics agreed were in the top ten - 1998, 2002 and 2008.

***

8. Lastly, determining the sales in the best video game years

SELECT year, SUM(games_sold) AS total_games_sold

FROM game_sales

WHERE year IN

(SELECT year

FROM top_critic_years_more_than_four_games

INTERSECT

SELECT year

FROM top_user_years_more_than_four_games)

GROUP BY year

ORDER BY total_games_sold DESC;

**Insight:** We are able to see if sales were good in years critics and users liked.

![image](https://github.com/JeffersonEdoseghe/The-Golden-Age-of-Video-Games/assets/139655810/4b8430e0-cb8a-4a49-ba51-80bc2a04b5b1)
