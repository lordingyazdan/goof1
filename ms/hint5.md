# Hint 5

To achieve catastropic backtracking, change the content string such that it’s very large and so has many combinations to back track through, and will never complete. The example we show below changes ‘minutes’ to ‘minutea’ This simple change means ms will never be able to parse the string for a time duration, but by heck will it try! You’ll see approximately a 10-15 second delay. Try to access the application from the browser and add other todos which your request is processing. You’ve just successfully exploited a ReDoS vulnerability.

```
$ echo 'content=Buy milk in '`printf %.0s5 {1..60000}`' minutea' | http --form http://localhost:3001/create -v
```
