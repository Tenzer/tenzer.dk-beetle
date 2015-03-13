title: Python Output Format
date: 2015-03-13
type: blog
grouping: blog
---
I use [Sublime Text](http://www.sublimetext.com/) as my editor of choice, and I have loved how simple but effective the [Pretty JSON](https://packagecontrol.io/packages/Pretty%20JSON) plugin has been at making minimized JSON chunks into easy readable, neatly indented blocks of code with just one key press.

Since I mainly do my programming in Python I found that I for debugging often print out variables from Python code, in order to get an easier glimpse of what data I am working on or what I have produced thus far. When you print out structures like lists, dictionaries and such, they will come out in a format that more or less resembles minimized JSON - not really the easiest thing to parse with your eyes, so I thought I would take a go at making a Pretty JSON plugin but for Python `print()` output.

I found the [tokenize](https://docs.python.org/3/library/tokenize.html) module in the standard library which is able to tokenize Python code, and put it back together again based on tokens, which would make the parsing much easier for me than having to write regular expressions for each different type of value that I would run into.

Back in [August 2014](https://github.com/Tenzer/PythonOutputFormat/commit/94253e5d885bb1bdc94f66a2313d06c73b43c9d8) I wrote a working prototype of the plugin and was happily using it just myself, along with a couple of Python-coding colleagues at work I showed it to. Then nothing really happened to it for a long while until a colleague complained that the plugin wasn't available in [Package Control](https://packagecontrol.io/) (the Sublime Text plugin installer) for easy installation. So I sat down and went over the code once more, made the plugin a bit more flexible and helpful, ie. by changing the syntax of the document to Python if it's currently set to plain-text, and then I released it [as a plugin](https://packagecontrol.io/packages/Python%20Output%20Format).

If you want an example of what the plugin does before you go and install it, then look in the README file on the [GitHub repository](https://github.com/Tenzer/PythonOutputFormat).

[Let me know](https://twitter.com/Tenzer) if you experience any problems with it or just want to tell me how much easier it has made your life :)
