I reproduced a bug in pandas, a free tool I use in Python to work with tables of data. I traced the cause and opened a fix with a test to catch the same problem in the future.

Think of a daily stock summary showing the opening price, the highest and lowest prices, and the closing price. In pandas, that calculation worked with the numbers stored one way. When I stored the same numbers in a newer format, it crashed.

Finding an issue to work on took a few tries. Someone was already working on each of the first five I found, so I changed my search. On the next try, all five dropped out.

All 48 automated checks passed. My fix is still open and waiting for one of the people who maintain pandas to review it. As a data analyst, I spend a lot of time using tools like pandas. This time I got to look inside one and contribute a change myself.

https://github.com/pandas-dev/pandas/pull/72280
